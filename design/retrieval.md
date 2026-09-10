# Retrieval, the Entroptics way — the guide

**Source of truth:** `entroptics/src/entroptics/proximity.py`

This exists because I built retrieval three times against my own intuitions and it was wrong each
time — thresholds, budgets, a stemmer, cosine. Every one of those was already answered in the
library. What follows is the answer, written down, so it is followed rather than rediscovered.

---

## 1. The one rule

> **Nothing is chosen. Every number is a function of the frame.**

`proximity.py` states it: *"There is no width, no rank, no rate, no tolerance, no grid and no
learned constant in this module."* The only external input anywhere in the construction is α, a
false-alarm level, and by Neyman–Pearson it cannot come from the data because it encodes the
reader's cost of a false alarm against a miss. **α enters no read.**

If a number is being picked, the design is wrong. Not the number — the design.

## 2. What is banned, and why each was tried

| Banned | Why it fails |
|---|---|
| **Cosine similarity** | A chosen metric. Assumes the space is isotropic and that angle is nearness. Nothing derives it. |
| **Score thresholds** (`_MIN_TOP_SCORE`) | Fitted to a corpus; drifts when the corpus does. Failed in both directions within one day. |
| **Top-k / candidate budgets** | Cutting 25→15 lost 3 of 5 answers. The aperture decides extent, not a constant. |
| **Counts as a digest** (`k_signal`) | *"A count is a threshold, and a threshold flips."* Measured on 60 BeIR frames: a 5% row edit moves the count on 30% of them, and the count takes only 4 distinct values across the corpus — it cannot separate 60 records. |
| **Stemmers, stopword lists** | Hand-built rule tables. A forcing, and English-only. |
| **Zero-padding a short read** | Measured: recall@1 0.717 vs 0.983 under 5% row deletion. A record has no value at a mode it does not have — writing 0 asserts a measurement never taken. |
| **Normalised / relative distance** | Divides out exactly the magnitude the construction was arranged to keep. |

## 3. The read

**`mp_deviation(M)` — the digest of one record.**

For every direction a frame resolves, how far does it stand from where the frame's *own noise
law* says it should? Marchenko–Pastur gives the bulk; the deviation from it is the signal. The
noise floor is derived from the frame, not assumed.

    v_j     = mean_i (A - med(A))_ij^2        centred per-channel second moment
    F_eff   = (sum v)^2 / sum v^2             the bulk's effective width         f(v)
    sigma^2 = median_i ||A_i||^2 / (F_eff * c_Feff * dof)                        f(A, N, F_eff)
    mu      = (sqrt(N-1) + sqrt(F_eff))^2     MP centring                        f(N, F_eff)
    s_MP(k) = sqrt(F_eff * sigma^2 * x_k)     the predicted spectrum
    dev_k   = (s_k - s_MP(k)) / sqrt(N)       the read

Continuous, per mode, nothing in or out. **Nothing can flip.**

**`spectral_distance(x, y)`** — plain L2 on the common prefix. Absolute, deliberately.

**`SpectrumProbe(spectra).nearest(query, k)`** — exact k-NN. The radius is *derived*, not
supplied: for any two vectors and any index `j`, `|x_j - y_j| <= ||x - y||`, so scanning the key
interval is lossless. Same answers as a full scan; only the work differs.

It came from here. *"Migrated from `agience-mantle/src/mantle/search/beacon/proximity.py`"* —
this is Mantle's own math, moved to where a second consumer could reach it. And it was measured
on **60 BeIR frames** — a retrieval benchmark. This is not adjacent work.

## 4. The shape — and the mistake currently in the tree

**`mp_deviation` reads a frame: an `(N, F)` matrix. Not a vector.**

A record's frame is its **chunks stacked** — `(n_chunks, dim)`. The spectrum is what the
document's own internal variation resolves into. One vector cannot form a frame; a 1×256 matrix
has no spectrum to speak of.

**What is built today is the wrong shape.** `mantle_common.store_artifact` embeds
`title + description + content` as one vector per artifact and sends it as `vector`. That is a
cosine-shaped input, and it is why the semantic arm returned flat cosines (0.29–0.42) and one
hit per query. I then nearly blamed model2vec for a failure that belongs to the metric and the
shape.

    now       artifact -> one 256-vector -> cosine
    correct   artifact -> chunks -> (n, 256) frame -> mp_deviation -> spectrum -> spectral_distance

Mantle already chunks for the vector arm, so the chunk structure exists: `search/ingest/chunking`
does the chunking and `cell.py` upserts by `(artifact_id, chunk_id)`. What is missing is a vector
per chunk and the digest over them.

## 5. The composition that works — measured 2026-08-14

Each layer does its own job and neither does the other's.

    query -> embed (model2vec, deterministic)
          -> Mantle recall WITH vector + space_id      -> ordering = semantic   (the server ranks)
          -> prism.adaptive_cut.cut(scores, frame=…)   -> how many to keep      (the client cuts)

**The server orders.** Cosine is Mantle's own decision inside its own arm, and it is entitled to
it — `search/types.py` draws the line at the store not computing a metric *it invented about
content*; ranking within a caller-supplied space is the vector arm's stated purpose.

**The client cuts.** `adaptive_cut` composes two reads. The aperture's `K_signal` says whether
there is structure, by Marchenko–Pastur and parameter-free; a scale-invariant largest relative
gap says where it breaks. *"Composed, the pair needs no constant at all."* The frame it reads
is the candidates' own features in score order, which its docstring notes no caller had
supplied.

**The quiet gate comes free.** Measured: `thanks, looks good` returns **zero hits** from the
semantic arm. No threshold, no null, no false-alarm level. A conversational turn has nothing near
it in the space, so the arm returns nothing — which is the behaviour six separate constructions
failed to produce, including a calibrated constant that drifted within hours and a null drawn
from the candidate page.

### Wiring notes that cost time to find

- **`EMBER_ADAPTIVE_MODE=on`.** The default is `off` and `cut()` silently returns the baseline.
- **Load `ember/optics.py` by path.** `import ember.optics` pulls `ember/__init__.py` and its
  tree — over 600s. The module itself imports only stdlib, numpy and `prism.rounding`, and loads
  in ~1.05s. Register it in `sys.modules` **before** `exec_module` or its dataclasses raise.
- **Never reach past `ember.optics` to entroptics directly.** The wrapper exists because the
  front door applies an entropy fold guard that destroys a sparse carrier — 256 channels folded
  to `F_eff = 1`, reported as `K_signal = 1`, indistinguishable from one real mode at the call
  site. Ontology coordinates are sparse.
- **Hold it all in a resident daemon.** `embedder.py` loads model2vec, prism and optics once and
  serves `/embed` and `/cut` on loopback. Hooks pay 19–41 ms; a fresh import would pay ~7s.

### What still had to be done

**Every artifact needs a vector.** The arm ranks only what it can see, and artifacts written
before the embedding wiring carry none — which is why the first semantic recalls returned `n=1`
and `n=2` and answered with commits rather than the README. `store_artifact` attaches one on
every write; existing artifacts need one pass to backfill.

## 6. Standing checks

Before writing any number into retrieval code:

> **What derives this?**
> "I measured this corpus" → a calibration. Stop.
> "The reader's tolerance for error" → α. Legitimate, and it enters no read.
> A resource ceiling → only if nothing about correctness depends on it. `_CANDIDATE_BUDGET`
> failed this: tuning it lost answers.

And: **is this a frame, or a vector?** If the code is comparing two vectors, it is doing cosine
with extra steps.

## 7. The derived path measured — and a wrong diagnosis corrected

With the anchor set fully derived (16 anchors, `matches_cells: true`, 104/104 cells rewritten):

| arm | hit@1 | hit@3 | mean latency |
|---|---|---|---|
| text only | 1/7 | 2/7 | 0.80s |
| **text + vector** | **4/7** | **6/7** | **0.65s** |

**The vector arm wins decisively and is also faster.** This reverses a reading of "semantic 2/6
vs lexical 4/4" taken against the mixed 37-anchor set (21 fitted + 16 derived) through a broken
harness. Neither number was measuring what it claimed.

**The arms are complementary, not redundant.** Their misses do not coincide — text alone
takes "why does it keep forgetting" at #1 where vector misses it entirely, and vector takes the
other six. Their union is 7/7. Supplying a vector currently orders on the vector, so the one
text-only win is lost; recovering it is the next real gain and needs no constant.

### The first diagnosis was wrong — and how it went wrong

It read: commits are 70% of the corpus, take 55% of top-3, and are burying the answers;
condense them with a junction colimit. Every number in it was real. **The conclusion was still
wrong, because four of the six questions asked about files that are not in the store.**

`embedder.py`, `store_file.py` and `mantle_common.py` live in `~/.claude/hooks/`, which
`store_file._EPHEMERAL_MARKERS` excludes on purpose — `.claude` is the other memory lane and
capturing it writes every fact to both stores. So the ranker was asked six questions, was
missing the answer to four, and returned the nearest thing it had. **Commits won those slots
because nothing better existed, not because they crowd anything out.**

Two independent checks agreed and both were ignored until the traffic was actually read:

- The scores on those queries were flat — 0.396 / 0.394 / 0.392. A flat spread is the signature
  of "nothing here matches," which is precisely what `adaptive_cut` reads. It was reported and
  not believed.
- `tekton_colimit.py --dry-run` over the same corpus found **0 junctions in 90 records**. If 75
  commits were near-duplicate mass, the colimit built for exactly that case would have said so.

### The rules that come out of it

> **Before concluding a ranker failed, verify the answer is in the corpus.** A retrieval eval
> whose ground truth was never stored measures nothing, and it will produce a confident,
> well-evidenced, entirely wrong diagnosis — with tables.

> **A flat score spread is a reading, not a null result.** It says the corpus has nothing for
> this query. Treat it as evidence.

And the harness bug underneath: `recall` takes **`query_text`**, not `query`. Passing `query`
returns `400: query_text or vector is required` — so a text-only call silently measured zero
while a text+vector call silently measured vector-only, with the query discarded.

## 8. The union, measured — 7/7 hit@3 with no fusion constant

The arms miss different things, so the fix is to stop discarding one. Measured on the same
seven questions:

| arm | hit@1 | hit@3 |
|---|---|---|
| text only | 1/7 | 2/7 |
| vector only | 4/7 | 6/7 |
| **union** | **4/7** | **7/7** |
| union, after `adaptive_cut` decides the extent | — | **5/7 delivered** |

**The rule, and it introduces nothing:**

    both arms contribute CANDIDATES
    the vector arm ORDERS them          (one metric, applied to everything, not a blend)
    text-only survivors append in their own order
    adaptive_cut decides HOW MANY       (derived, per query)

No weight, no interleave ratio, no RRF `k`. Nothing is discarded, and no score is compared
across two scales — which is the thing a fusion constant exists to paper over.

**Where it still loses: the cut, not the ranking.** The two delivered misses are both cases
where the answer was retrieved and then cut off — `keep=1` with the target at #2, `keep=2` with
the target at #3. The ordering found them; the extent dropped them. That is the next thing to
look at, and it is a question about the frame `adaptive_cut` reads, not about adding a floor.

The server already supports this shape natively: `artifacts_router` has a `candidates` mode
that returns the raw unranked set "for a flavor to rank within", which is exactly the client
side where the embedder and the cut already live. That union was assembled from two calls;
one candidates call would do it in one round trip.

## 9. The frame the cut reads — title vs content, 5/7 → 7/7

§8 ended with two misses that were the extent, not the ordering: the answer at #2 with `keep=1`,
and at #3 with `keep=2`. Both were the frame.

`adaptive_cut` reads "the candidates' own features in score order". It was being handed each
candidate's **title** — a handful of words, so the frame was a stack of nearly empty rows and
the read collapsed. `recall` already returns each hit's **entropy-cut densest span**
(`search/beacon/density.py`), a real frame, and it was sitting unused in the reply.

| frame the cut reads | delivered after the cut |
|---|---|
| title | 5/7 |
| **content** | **7/7** |

### The control that makes this a result rather than a loosening

**A cut that simply keeps more scores better on any recall test while meaning nothing.** So the
question was never "did it deliver more" but "did it stop discriminating". It did not:

| conversational turn | candidates | keep (title) | keep (content) |
|---|---|---|---|
| `thanks, looks good` | **0** | — | — |
| `ok` / `yes continue` / `nice` | **0** | — | — |
| `go on` | 23 | 3 | **3** |
| `sounds right` | 1 | 1 | **1** |
| `this is good.. continue` | 11 | 1 | **2** |

Four of seven chatter prompts retrieve **nothing at all** — the gate still arrives from the arms
rather than from any number. Where candidates exist the keep is unchanged or one larger. The
content frame changed the answer only where the frame was genuinely richer, which is what a
better read looks like as opposed to a slackened one.

**Still open, stated plainly:** an off-corpus question ("what is the capital of Peru") returns
21 candidates and the cut keeps 3. The arms return nearest neighbours from a corpus that has no
answer, and nothing downstream can tell that apart from a real hit without exactly the kind of
constant this path removes. This is the same shape as §7's flat-score case and it is unsolved.

## 10. Why the arms are complementary — the offer/body split

§8 recorded that the arms miss different things and treated it as luck. It is not luck, it is
the indexing design, and knowing the mechanism is what makes the union principled rather than
lucky.

**The lexical arm indexes the offer and never the body.** `pipeline_unified._OFFER_FIELDS` is
`("title", "description", "tags")` and `_sse_index_artifact` filters `fields` down to exactly
those before writing a posting. The reason is a hard cost measurement on a store of
2.9M vertices:

    raw SQLite insert into the 9.7 GB store   0.008s   <- the substrate was never the problem
    POST, no content and no name              0.2s
    POST, no content, ONE name               14.6s
    POST, 4KB of real prose                  16.4s
    POST, 4KB of `'x '` (one distinct term)   3.5s     <- cost is TERMS, not bytes

Posting lists are read-modify-write: `sse/posting.py` runs `get_posting` → decrypt all →
linear-scan `upsert_entry` → re-encrypt all → `put_posting`. One term therefore costs
O(artifacts already carrying it), and a body contributes thousands of distinct terms.
**An artifact's offer is bounded by the artifact; its body is bounded by nothing** — indexing
the body made write cost a function of corpus size, which is the one thing §2 rule 4 forbids.

### So the two arms answer different questions

| arm | indexes | answers well |
|---|---|---|
| lexical | title · description · tags | "the thing whose name is this" |
| vector | the body, through cells | "the thing that says this" |

`WHY-IT-KEEPS-FORGETTING.md` is won by the lexical arm at #1 and missed by the vector arm
entirely — because the query repeats its title. Six other questions are won by the vector arm,
because they ask about what a document says. **The union is not two tries at one job; it is one
try at each of two jobs**, which is why it is 7/7 where each alone is 2/7 and 6/7.

**This retro-labels the text-only measurements in §7 and §8.** "Text-only 2/7" is not "BM25 is
weak" — it is BM25 being asked to match body prose it was never given. Any future comparison
must say which arm indexes what, or it will keep concluding the lexical arm is broken when it is
doing its job.

**It also broke a test silently**, which is worth more than the fix: a deletion test wrote a
fixture carrying only `content`, which now filters to empty, returns `ARM_SKIPPED`, and indexes
nothing — so the security assertion it exists to make (delete removes the postings) passed its
own precondition check and never ran. Narrowing what gets indexed silently narrows what gets
tested.

## 11. The optimisation that measured well and was wrong — where the gate lives

Server-side latency, measured 2026-08-15 (medians, warm):

    no-match query        103 ms      <- fixed overhead floor
    vector only           201 ms
    text only             355 ms
    text + vector         498 ms      <- roughly additive: the arms run IN SERIES
    size=1 / 5 / 20       376 / 346 / 368 ms
    size=100             2362 ms      <- hydration is linear in results RETURNED

Two real wins came out of it. The arms are independent, so running the hook's two calls
concurrently costs 739 ms instead of 1204 ms — same calls, same results. And all client-side
work (embed 0-2 ms, cut 2-4 ms) is ~6 ms, so nothing on that side is worth optimising.

### The trap

Since text+vector is additive, sending the vector without `query_text` should remove duplicated
server work — and it does. It halved end-to-end latency (1213→619, 1472→639, 2241→1174 ms) and
delivered **the same 6/7** on the eval. Every number said ship it.

It was wrong, and only a control caught it:

| prompt | text only | pure vector | text + vector |
|---|---|---|---|
| `thanks, looks good` | 0 | **3** | 0 |
| `ok` / `nice` | 0 | **2–3** | 0 |
| `yes continue` | 0 | **5** | 0 |

**The quiet gate is the lexical arm.** Nearest neighbours always exist, so a pure vector query
can never answer "nothing here" — asked about `thanks, looks good` it returns the three closest
things in the store. The lexical arm answers nothing when no offer term matches. **`text+vector`
means "lexical supplies the candidates, the vector orders them"**, and that composition is what
makes a conversational turn inject nothing with no threshold anywhere.

So §5's "the quiet gate comes free" is now explained rather than just observed, and the extra
latency is its price. Reverted; the reasoning is at the call site so it is not re-optimised away.

### The rule

> **An eval scored on questions cannot see a gate, because it never asks a question that has no
> answer.** Every retrieval change needs a control set of prompts that should return nothing.
> Without one, the metric rewards deleting the gate.

This is the same failure as §7 from the other direction: there, missing answers made a fine
ranker look broken; here, a strong score hid a deleted safety property. Both were caught by
looking at what the system did on inputs the eval did not contain.

## 12. Why a recall costs what it costs — measured, and it is architectural

**The cost model, profiled on a store of 202 owners and ~107 artifacts:**

    file opens per recall  =  query_terms  x  authorized_principals  x  tokens_per_term
                           =      10       x        202              x      2.24        =  4,520

~20 ms per query token, end to end. Three measurements pin it and rule out the alternatives:

| probe | result | what it rules out |
|---|---|---|
| 10 nonsense words (no posting exists) | 245 ms — same as 10 real | it is not fetching/decrypting data |
| 10 repeats of one word | 251 ms — same as 10 distinct | query terms are not deduplicated |
| size 1 / 5 / 20 | 376 / 346 / 368 ms | it does not scale with results |
| size 100 | 2362 ms | hydration alone is linear in results returned |

**Every artifact is its own SSE principal** — authorization is the encryption, so each owner is
separately keyed and one term must be blinded and probed once per owner. Principals therefore
track artifacts, and **recall cost is linear in corpus size**, which §2 rule 4 forbids. This is a
property of the keying design, not a defect in the read path.

### The fix the code sanctions does NOT work — measured

`PostingStore`'s own docstring says `get_posting` is thread-safe, that the probes "are
independent blobs, so nothing about them forces a serial read", and that the thread-pool reader
"went with the BM25 path". Restoring that fan-out (one task per owner) made it **worse: 492 ms
vs 217 ms serial.**

The profile says why. Of a 10-term query, only **0.128 s is `open()`** against roughly **0.22 s of
Python-level path construction** — 452,821 `list.append`, 49,720 `ntpath.splitroot`, 9,040
`encode_component`. **It is not I/O-bound, it is GIL-bound**, so threads bought contention, not
overlap. Reverted.

If a docstring proposes an optimisation, that is a hypothesis, not a measurement. This one had
been true when it was written (with a different reader) and was false for the current code.

### What did work — 15-20%, contained, behaviour-identical

| | before | after |
|---|---|---|
| 1 term | 44.0 ms | 35.3 ms |
| 10 terms | 217.7 ms | 184.2 ms |

- **`encode_component` fast path.** It ran a 64-iteration per-character loop to return its own
  argument: a blind token is 64 hex chars and a principal id is a lowercase UUID, and every one
  of those characters is already in `_SAFE`. `str.strip` against the same alphabet is one C pass
  and answers the identical question. Verified over 4,000 fuzzed strings plus Unicode, reserved
  device names (`con`, `nul`, `com1`) and traversal attempts (`..`, `a/b`).
- **`_posting_dir` memoised per principal.** It depends on the owner alone and was rebuilt for
  every one of that owner's ~22 probes.

### What getting to milliseconds actually requires

Not tuning. The fan-out itself has to change — **one probe per owner instead of one per
(owner x term x field x prefix)** — e.g. a per-owner token set read once per recall. That changes
how the encrypted index is keyed and is a design decision, not an optimisation.

## 13. The owner index — one read per owner instead of one per probe

§12 ended with the fan-out as the only remaining lever and called it a design decision. It was
decided in favour of efficiency: re-encryption and a data reload are an acceptable price.

### The shape

    before   one open() per (owner x term x field x prefix)   = 4,520 for a ten-term query
    after    one read  per owner                              = 194,  whatever the query says

A third key tree beside `posting:` and `manifest:` — **`ownerindex:`**, its own AAD prefix,
holding that owner's whole `token -> entries` map in one encrypted blob. It carries no id in its
HKDF `info` because there is exactly one per owner and the SSE key already identifies the owner.

**Why an owner index is the right unit:** each artifact is its own principal, and indexing is
offer-only since §10 — title, description, tags — so an owner's whole token map is small.
Measured: 111 of 194 owner directories are artifact ids, identical to their root ids. The thing
that was expensive to probe piecemeal is cheap to hold whole.

### What makes it safe to ship before any reindex

- **The per-token postings remain the source of truth** and are still written. This is a
  read-side accelerator over the same data, not a replacement for it — so deletion, re-key and
  mesh paths are untouched.
- **Three failures collapse into one answer.** `narrowing._load_owner_index` reaches the store
  with `getattr` and treats a missing method, a missing blob and an unreadable blob identically:
  fall back to probing. An owner without an index is slower and never wrong.
- **The other posting writer does not collide.** `collection_proximity` stores digest slots keyed
  by `digest_slot_token(key, collection_id)` and reads them back through `get_posting` directly.
  Narrowing only ever asks for term tokens, so the two namespaces share a store and never meet.
- **The flush is once per artifact operation, not per token.** Writing it inside the token loop
  would rewrite the whole blob for each of the artifact's tokens — quadratic in the owner's
  vocabulary, which is the §10 lesson repeating itself one layer up.

### The guard file is the real deliverable

`tests/test_owner_index_is_only_ever_faster.py`. **An accelerator that returns a different answer
is not an accelerator, it is silent loss of search results** — and it would present as "that
document just stopped coming back", which no one reports as a bug. So it asserts the same
property from six angles:

| guard | what it would catch |
|---|---|
| identical answers with/without the index, over 6 query shapes | any divergence at all |
| a reindex that drops a term stops matching it | a stale index — the dangerous direction |
| removing an artifact empties the index | a deletion that reports success and removes nothing |
| a corrupt blob still answers | an accelerator that fails a recall the slow path would serve |
| one owner's blob will not open under another's key | a derivation slip crossing principals |
| **0 posting probes with the index, >0 without** | that it does anything at all |

That last one matters because "it got faster on my machine" is not a property a test can hold;
the probe count is.

### Measured, after the migration (115 owner blobs, 10,508 token slots)

| query | before | after | |
|---|---|---|---|
| 1 term | 35.3 ms | 45.5 ms | **0.8x — slower** |
| 3 terms | 74.5 ms | 48.3 ms | 1.5x |
| 10 terms | 184.2 ms | **79.2 ms** | **2.3x** |
| hook, end to end | 816-1462 ms | **541-1041 ms** | results identical, gate intact |

**The shape changed, which matters more than the ratio.** Latency was 35 → 75 → 184 ms as the
query grew; it is now 46 → 48 → 79. **Query length has almost stopped mattering**, which is the
defect §12 identified. Real prompts run 7-12 terms, so that is the case that pays.

**A one-term query got worse and the reason is structural, not a bug.** The old path opened two
small files per owner; the new one reads that owner's whole blob. Below ~2 terms the per-token
probe is genuinely the cheaper read. Making the reader choose between them on a term count would
be a fitted constant of exactly the kind §2 bans, so it is not done — the flat curve is the
property worth having.

**Still linear in owners, and that is the remaining barrier.** Cost is now
`owners x blob_size` instead of `owners x terms x tokens_per_term`. One factor is gone, not the
one §2 rule 4 names. A store whose owners each hold a large vocabulary would read a lot per
recall — the dry run found one owner with 12,408 token slots and another with 59,489, both
legacy from when content was indexed. Offer-only indexing (§10) is what keeps the blobs small
now, and the two changes depend on each other.

### The migration, and what it proved about the model

`src/mantle/system/manage_owner_index.py` — inverts existing posting lists rather than
re-tokenizing, so its output cannot disagree with the source it derives from, and it is re-runnable
on a live store.

**It cannot outrank the grant ledger, and the first run proved it.** Run as the platform system
principal it refused all 198 owners with `GrantDenied` — system identity is provenance, not
authority. Run as the identity that already reads this corpus it built 115 and skipped 83, which
are precisely the owners that identity cannot read. **A migration over an encrypted index is
bounded by the same grants as a query over it**, and an accelerator that could be built for owners
you cannot read would be a way to extract what the light cone refuses.

A no-op rewrite does NOT populate it: 115 artifacts "reindexed" in 3 s wrote zero blobs, because
the ingest path correctly skips unchanged content. The migration has to be its own pass.

## 14. Never cut across two scales — the bug that hid behind a healthy arm

`recall()` unioned both arms and handed `adaptive_cut` one score list: cosine (~0.28) from the
vector arm, then BM25 (~6.0) from the text arm. **The largest relative gap in that list is the
scale boundary**, so the cut landed exactly there and kept only the vector block.

It measured fine for as long as the vector arm was strong, and failed the moment it was not.
With the vector arm returning 1-2 hits the cut kept 1-2 and discarded every text hit — including
the right answer sitting at text rank #2. Delivered went 7/7 -> 3/7 with nothing about the
ranking having changed.

    before   cut(vector_scores + text_scores)     one read across two incomparable scales
    after    cut(vector_scores) ∪ cut(text_scores)   each arm read against its own

**A cut is a reading of one measurement.** The same fact that makes a fusion weight
unnecessary (§8 — the arms' scores are not comparable) makes a shared cut *wrong*. Reading them
together was smuggling a comparison back in through the aperture instead of through a weight.
Fixing it: 3/7 -> 5/7, no constant added.

### What is still open, stated plainly

| | delivered | injected per prompt |
|---|---|---|
| both arms cut | **5/7** | ~4 |
| text arm kept whole | 6/7 | ~20 |

The extra answer costs five times the context, so the cut stays. The two remaining misses are
`README.md` and `CLAUDE.md` — both FILE artifacts whose titles share no term with the question,
so the lexical arm structurally cannot reach them (§10: it indexes the offer) and only the vector
arm can. It currently returns 1-3 hits where it returned 3-9 before the migration, and that is
the next thing to look at.

**BM25 scores are coarse integers** (4, 3, 3, 2, 2 …), so a largest-relative-gap read on them
puts the break at rank 1 far too often. The vector arm's continuous scores do not have this
problem. That is a property of the frame the text arm supplies, not of the cut.

## 15. The gate and the reach are different jobs — separate the calls

§11 established that `text+vector` gives the quiet gate free, and it does. What it also does, and
what only showed once indexing became offer-only, is cap the vector arm: the server ranks the
vector arm within the lexical candidates, and the lexical arm sees only title, description and
tags. So an artifact whose title shares no term with the question is unreachable **however close
its meaning is**, because it never becomes a candidate for the vector arm to rank.

    how does authorization work in the lattice   ->  README.md   UNREACHABLE
    when should I use mantle vs local memory     ->  CLAUDE.md   UNREACHABLE

**Both used to work, on postings that no longer exist.** They were indexed before the switch to
offer-only, so they still carried content postings; the migration re-indexed everything under the
current rules and those went away. The 7/7 baseline was partly resting on index entries the write
path had already stopped producing — a benchmark can measure a capability the system no longer
has, and keep passing, until something forces a rewrite.

### The composition

    text call    (query_text only)         -> candidates AND the gate
    vector call  (vector only, no text)    -> the whole corpus, by meaning
    if the TEXT arm is silent              -> inject nothing
    otherwise                              -> cut each arm on its own scores, union the survivors

The gate is evaluated on the text arm alone, which is what stops the vector arm's unconditional
nearest-neighbours from opening it. §11's warning still stands and is now load-bearing in a
different place: a pure-vector recall can never say "nothing here", so it must never be what
decides whether to speak.

| | delivered | injected | gate |
|---|---|---|---|
| combined call, shared cut | 3/7 | ~2 | held |
| combined call, per-arm cut (§14) | 5/7 | ~4 | held |
| **separated, per-arm cut** | **6/7** | **~3** | **held** |

Better than the old 7/7 in the way that matters: it stands on the index the current write path
actually produces, not on legacy entries.

## 16. The gate's cost, measured on both sides — and why it stays

Rebuilt from scratch, 29 real documents, ground truth verified per case: the answer is checked to
be present in the target file before the ranker is scored, for the reason §7 records.

| | hit@1 | hit@3 | chatter silent | injected per chatter turn |
|---|---|---|---|---|
| **gate on** (text arm decides) | 6/15 | 10/15 | **8/8** | **0.0** |
| gate off | 7/15 | 11/15 | 0/8 | 1.5 |

**One extra hit costs the whole gate.** Noise on every conversational turn, forever, to recover a
single question in fifteen. The gate stays.

### What the gate actually costs, stated exactly

The text arm indexes the offer (§10), so a question whose words appear only in bodies finds no
lexical candidate, the gate closes, and the recall returns nothing — not a bad ranking, an empty
result. `what did we decide about stemming` is the clean example. Three of four misses are this
shape. **The gate cannot tell "this is not a question" from "this question is about body text";
both are lexical silence.**

### Three ways out, all measured, all rejected

| attempt | result |
|---|---|
| **Gate on the vector arm's score structure** | No separation. Questions top out 0.29–0.58, chatter 0.04–0.38, and they overlap — `yes continue` scores 0.382 against a real question's 0.286. Spread, ratio and the derived cut separate nothing either. This is the seventh score-shaped gate to fail. |
| **Widen the offer with the entropy cut** (`density.dense_excerpt`) | Too narrow to carry vocabulary: 2.1% of content, 27 terms per document, and 1 of 10 question terms. It selects one dense span for a preview; it is not a summary of what a document is about. |
| **Widen the offer with headings** | Better but not enough: 80 terms per document, 3 of 10 question terms (and 1 of 9 chatter terms). A document's headings do not name most of what it discusses. |

**The conclusion is structural, not a tuning failure.** To answer questions about body text the
lexical arm has to index body text — and that both restores the 1,024-posting-files-per-artifact
cost (measured: offer-only is 20) and weakens the gate, because ordinary words like "good" and
"looks" do appear in bodies. There is no bounded summary that carries enough body vocabulary; two
were tried and measured.

So the four misses are the price of a gate that is otherwise perfect, and the price is worth
paying. What would change the answer is a gate that is not lexical silence — and nothing measured
so far is one.
