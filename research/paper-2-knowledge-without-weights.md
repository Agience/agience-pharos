# KNOWLEDGE WITHOUT WEIGHTS

## Grounding, identity and consolidation read off a corpus, with no trained model in the answer path

**Agience · Ikailo Inc. (Toronto / Ontario, Canada) · John Sessford**

**AGIENCE** and **CREATE YOUR AGENCY** are trademarks of Ikailo Inc., registered in Canada.

---

## Abstract

A knowledge store in which **every value is either observed — ingested with provenance — or derived
by a verified operator from other artifacts**, and no trained weights appear in the answer path.
The measuring instrument is the screened spectral aperture of the companion paper
(`paper-1-the-instrument.md`); this paper is what happens when it is pointed at a corpus.

Four things are done without a model. **Grounding** is read off the corpus rather than tagged: a
token's grammatical role comes from its own sense counts, with no verb list, no interrogative list
and no part-of-speech tagger (§57). **The act** — apply, preimage, or infer — is selected by where
the ungrounded hole falls in the ordering, with no `is_question` flag anywhere in the system (§58).
**Identity** between two records of one concept is a **conservation residual**, not a similarity
threshold, with a tolerance computed from the frame rather than typed (§72–73). **Refusal** is not a
branch: when nothing rises above the floor, nothing comes out (§63).

**Propagation has no step limit and no routing table.** A walk ends at a **computed null** — the
weight an unrelated pair scores on this corpus, 0.026284 (§66) — never at a hop cap, which would
change reach by three orders of magnitude. Where the taxonomy stops, a charged vertex **crosses one
gap on the charge it holds**: a per-vertex reach budget $\operatorname{horizon}(\xi, \text{gap}, w_v)$
over the same Jiang–Conrath distance, one pass, no recharge (§66.1). And where a query carries an
operator as well as a context, a row's amplitude is the **geometric mean of the two fields**,
$\sqrt{e_{\text{ctx}} \cdot e_{\text{op}}}$ — zero unless both signals reach the vertex, which is what
makes the operator steer the answer by geometry rather than by a branch on the question word (§62).

**The results.** Identity derived at **45.5%** against a **68.2%** byte-identical-gloss baseline over
600 synsets, disagreeing in *both* directions and reported as a full $2\times2$ with negative
controls — the tolerance computed from the frame, $\mathrm{tol} = \varepsilon_{\mathrm{machine}}
\cdot \|F\| \cdot d$, never typed (§73). Per-screen decay measured live on five streams, with mode
weights published so the extrapolation is reproducible (§61). A computed propagation floor of
**0.026284** — the weight an unrelated pair scores on this corpus — replacing every step limit, where
a hop cap of 2 would have changed reach by three orders of magnitude (§66). A corpus basis reported
**with its interval**: $k = 280$, certified $[123, 956]$, `certain = False`, because the evidence
admits 834 resolutions (§59).

**Two measurements set the method's direction, and both are null results.**

- **A coordinate keyed on a name cannot establish identity across sources.** Two records of one
  concept in two id families share no path name, so they share no feature index, and
  $\cos = 0.000000$ — orthogonal by construction (§70). **This is why identity is derived from
  edges instead**, which is what §71–§73 build and measure. The negative result is the reason the
  positive one exists.
- **Sparse ontology coordinates do not support a fitted propagator.** `resolved_modes` $= 0$ on the
  is-a descent frames, and on the turn axis a connected chain is indistinguishable from the same
  twelve concepts scrambled ($t = 0.13$) (§65). **So the system places the field directly and reads
  it** — no fit, no rollout, no horizon — and direct placement beats the propagator on every query.
  The aperture refusing these frames exactly as it refuses noise is the instrument working:
  *"I cannot resolve this"* and *"there is one thing here"* must never be the same answer.

**Two limits are carried openly.** One imported prior remains — a Brown-corpus information-content
table, load-bearing for every meaning distance, of which **42.7%** of synsets carry the
zero-frequency sentinel (§98); the resolution is a recount over the ingested corpus. And **§114's
language baseline has not been run**: three corpus-internal arms, a fixed question set, an oracle per
measure, and the falsifiers stated in advance, all specified so the run cannot be steered by its
outcome. Until it is run, §62–§63's live answers are demonstrations rather than evidence.

---

## How to read this

| Part | What it gives you |
|---|---|
| **A** | What an artifact is: the record, the content/context/operator triple, and provenance as a graded rung derived server-side. The substrate the rest of the paper sits on. |
| **VIII** | Language: grounding, act selection, the coordinate and its basis, energy as information, measured decay, formation, the corpus's own words, categories by compression — and §65, what the operator does **not** resolve. |
| **IX** | The category: the corpus as a category, the cross-source identity failure, the derived diagram, the colimit and its universal property checked numerically, and the measured identity criterion. |
| **XI** | The corpus itself: the curriculum, retrieval on demand, the fleet inventory with its imported-prior admission, and the five faults in the order that fixes them. |
| **XV** | Acceptance and what is owed: the falsifiable tests, and the language baseline specified before it is run. |

**§70 and §65 are the two hinges.** Each is a null result that redirected the method — the first from
coordinates to edges, the second from a fitted propagator to direct placement — and each is followed
immediately by what replaced it. Read them with the sections that answer them (§71–§73 and §66)
rather than on their own.

---

## A note on numbering

**Section numbers are the originals and are never reused.** These documents were split from a
single source, and renumbering would have broken several hundred cross-references, so the numbering
runs continuously across the set: a reference of the form §N means the same section wherever it
appears.

| sections | document |
|---|---|
| §1–7, §22–27, §28–56, §109–111, §113, §115, glossary | `architecture.md` — the specification |
| **§8–18, §100–105** | **`paper-1-the-instrument.md`** |
| **§19–21.1, §57–77, §96–99, §112, §114** | **`paper-2-knowledge-without-weights.md`** |
| §78–95 | `the-economy.md` |

---
# PART A — WHAT AN ARTIFACT IS

*The substrate the rest of this paper sits on. These four sections are shared with `architecture.md`, which carries the remainder of the universal model (§22–§27).*

## 19. Everything is an object; objects carry edges

One model underneath everything: an **object** with **edges**. A person, a grant, an identity, an operator, a crystal, a capability, a policy: all objects, their relationships edges; the lattice's `vertex`/`edge` tables *are* this model. An **artifact** is the content-bearing case — content plus context, addressed by its own hash.

An artifact carries:

```
id                 per-version identity
root_id            stable identity across versions (first version: id == root_id)
content_type       MIME string — the type identity
content            the raw payload, by content_ref
context            type-owned structured JSON (the kernel never parses it)
lemmas             keyed terms — the retrieval surface
state              committed | archived — a retention marker, not a gate
created_by / created_time      provenance — set on every write, immutable
modified_by / modified_time
name / description
```

Addressing is always by identity; human-readable slugs are type-owned data inside `context`, never the address. Load-bearing choices:

- **Container is a graph property, not a type.** Any artifact with outbound containment edges is a container. A workspace, a collection, and a folder are the same mechanism.
- **Types own their structure.** Content plus context is a platform concern — always stored, versioned, searchable, generically browsable — while the view and the tools live on personas.
- **Generic before specific.** A generic browser renders any artifact through common inheritance; a type-specific viewer is an enhancement, never a prerequisite.
- **The operator is an edge, not a field.** An artifact's operator is another artifact, linked by an edge with `relationship: "operator"`.
- **`state` is a retention marker.** `committed` is the default and grants nothing; `archived` marks an artifact for a retention policy. Neither value gates visibility and neither promotes: promotion is mass (§23 of `architecture.md`), and access is a grant (§51 of `architecture.md`). A retention sweep writes `state`; nothing else reads it.
- **Edges carry a propagation mask.** Each edge is an **origin edge** — the creation chain, through which grants inherit — or a **link edge**, a structural reference with no grant flow. Each carries a `propagate` mask over the **CRUDEASIO** permission set of §51 of `architecture.md`; that set holds nine permissions, so the mask is nine bits wide. Effective inherited access is the intersection of the grant on the parent and the edge's mask.

**The hash is the coordinate.** A `content_ref = cas/<sha>` lives *inside* the versioned artifact, so a body change *is* a version change: new bytes to new sha to new `content_ref` to new `_seq`, the Merkle leaf moves, and it replicates. Agreeing on the index *is* agreeing on the content.

## 20. The content / context / operator triple

Every artifact is **Content** (what it is) + **Context** (where it fits: ontology, provenance, trust, identity) + **Operator** (what produced or acts on it). Given any two, the third is determined:

$$
\begin{aligned}
\text{content} + \text{operator} &\to \text{context}
    && \textbf{apply} && \text{compute the result} \\
\text{operator} + \text{context} &\to \text{content}
    && \textbf{preimage} && \text{solve, invert the arrow — \emph{novel thought}} \\
\text{content} + \text{context} &\to \text{operator}
    && \textbf{infer} && \text{which arrow maps these — \emph{learn the operator}}
\end{aligned}
$$

$\text{context} + \text{operator} \to \text{content}$ is the reconstruction guarantee that makes categorical consolidation lossless: store the colimit and the morphisms, and the sources are derivable.

**An operator is an algebra (pull: $\text{input} \to \text{output}$). A source is its dual — a coalgebra (push/unfold: $\text{the world} \to \text{a stream of observations}$).** Folder watchers, URL fetchers, and dataset streams are sources; `poll()` returns what is new since the last poll and remembers what it has seen. **The pull side composes and the push side cannot**, which is why ingest gets a poll-and-remember protocol and reasoning gets an algebra.

## 21. Provenance is a rung

Trust is graded, highest to lowest:

$$
\text{HUMAN\_VALIDATED} \succ \text{OBSERVED} \succ \text{SPAN\_CITED} \succ \text{ONTOLOGY\_PROPOSAL} \succ \text{HYPOTHESIS} \succ \text{UNKNOWN} \succ \text{ASSERTION}
$$

`ASSERTION` — a caller claimed it — is a marker for an inbound claim, not a storable rung: it is **never allowed to mint content**, and the server rewrites it to `UNKNOWN`, the lowest rung an artifact can actually hold. An unauthenticated write therefore lands at `UNKNOWN`; an authenticated but untrusted client is capped at `HYPOTHESIS`.

**The rung a write actually earns is derived server-side and a client cannot forge it.** Provenance rides in client-supplied context, which is stored blind, so a raw request can *say* `human_validated` — a rung that requires a human principal. Anything above a principal's ceiling is **quietly demoted to what it earned rather than rejected**: a rejected write loses the observation; a demoted one keeps it at the strength it can support.

The derivation is enforced on the signal-ingress path; every point at which a write can enter routes through it, and the lattice's own write path carries provenance as an audit field.

**Mass is stored verified-energy.** Three couplings bind mass to dynamics:

- **Corroboration has diminishing returns** — $(\text{ceiling} - \text{base}) \cdot (1 - 0.6^{n})$ after $n$ witnesses — so the ceiling is approached without ever being typed as a cap. `base` and `ceiling` are bounds the claim's own rung imposes: what a single unwitnessed claim at that rung supports, and what that rung supports however many witnesses arrive. The factor $0.6$ is fixed by the observed agreement variance among independent witnesses of one claim — how fast a further witness stops carrying information.
- **Revision requires inertia.** A refutation lands only if it carries at least the claim's mass (`may_revise`), the same test that decides whether a write takes head. A client's forged `human_validated` is demoted *before* the comparison, so the write becomes a proposal rather than a replacement.
- **Mass is the switch between propagating and changing state.** Below the cut a signal only propagates; at or above it, it transforms state.

### 21.1 Mass is derived; the cut is a coupling constant

**There is no $\text{rung} \to \text{mass}$ table, and a builder must not write one.** Mass is **derived server-side from the provenance quadruple** — who is speaking, which authority attests them, what agreement has accrued on the claim, and what that authority can support — and a client's assertion is an input to that derivation, never a result of it.

**The rung is ordinal, not numeric.** The ladder of §21 bounds what a claim may earn: a claim cannot be weighed above the ceiling of the rung its evidence supports, and a principal cannot present a rung above its own.

**The cut between propagating and transforming is a coupling constant.** It belongs to the same class as the correlation length $\xi$ and the propagation floor — the $\alpha$'s of this universe (§83 of `the-economy.md`), which are to be *measured*, not chosen. Any value carried for it today is a **seam**, listed among the open seams of §113 of `architecture.md`.

**What fixes it is a computed null** over the corpus's own mass distribution: the mass an uncorroborated signal scores by chance here, computed exactly the way the propagation floor is computed over propagation weights (§66).

---
# PART VIII — LANGUAGE

**Recognition** is a signal placed on a screen — a token's grammatical role read off the corpus rather than tagged, the act selected by where the ungrounded hole falls in the ordering, the concepts that fire those the propagation energises. **Formation** is what survives projection in both directions — inbound onto the corpus basis, outbound through the aperture that reads the answer back.

## 57. Grounding is measured, not tagged

Every token is grounded by asking the corpus what it *can be*. Three properties, read off the lexicon:

$$
\begin{aligned}
\text{concept-eligible} &\iff \text{the token has a noun sense}
    && \text{a meaning coordinate exists} \\
\text{relation-eligible} &\iff \text{its relational reading dominates}
    && \text{verb count} > \text{noun count} \\
\text{a HOLE} &\iff \text{the geometry affords neither}
    && \text{no coordinate at all}
\end{aligned}
$$

The dominance test has two tiers, both corpus properties: sense **frequency** where the corpus carries lemma counts, and the noun-versus-verb **sense-count ratio** where it does not.

Measured: `says` 11:1 and `eat` 6:0 read verb-dominant. `dog` 1:7 and `woof` 0:1 read concept.

**There is no verb list, no auxiliary list, no interrogative list, and no part-of-speech tagger.**

## 58. The act is selected by order

Which role a token *plays* is decided by position:

```
rel   = the first verb-dominant token that has a concept BEFORE it
subj  = the last concept before rel
obj   = the first concept after rel
```

A verb-dominant token with no concept to its left is an auxiliary or a subject-inversion marker. A relation with nothing before it at all is an imperative. **There is no `is_question` flag anywhere in the system.**

The hole's position then selects the act:

$$
\begin{aligned}
\textbf{response} &= \text{content} + \text{operator} \to \text{context}
    && \textbf{apply} && \text{compute the result} \\
\textbf{thought} &= \text{context} + \text{operator} \to \text{content}
    && \textbf{preimage} && \text{invert the arrow} \\
\textbf{learn} &= \text{content} + \text{context} \to \text{operator}
    && \textbf{infer} && \text{induce the relation}
\end{aligned}
$$

The three acts are the three faces of the triple of §20; the naming there governs.

**Write-safety is derived.** A question cannot be learned, because a question contains an ungrounded token by construction: the hole is present, so the statement branch is never reached.

**Act selection requires `rel`.** All three acts need an operator term, so where no verb-dominant token has a concept before it, `rel` is undefined and no act is selected; the composite path takes over, placing the whole active field on the screen and condensing it. Both worked queries take it: in *what is a cat* the only verb-dominant candidate is `is`, preceded by the hole `what`, so it reads as the inversion marker rather than as `rel`, the field is `cat`'s, and §62 reads one mode; in *capital of france* no token is verb-dominant, the field is $\text{capital} \oplus \text{france}$, and §62 reads two.

The composite is the sum concept: *A dog is coming towards us* settles to $\text{dog} \oplus \text{approach} \oplus \text{toward} \oplus \text{us}$; *A dog is running* to $\text{dog} \oplus \text{motion}$, a different path.

## 59. The coordinate, and the basis that makes it readable

Concepts carry a Jiang–Conrath tree coordinate signed-hashed into $D = 2048$ features, then projected onto a corpus basis derived from the noun cloud's own correlation spectrum — orthonormal to **2.3e-15**, so lifting is an exact isometry and only the down-projection is lossy.

The coordinate is built by walking the hypernym path:

```
coordinate(synset):
    A = the ancestor-or-self set of the synset along its hypernym paths
        (a synset with several parents contributes the union of its paths)
    x = zeros(D = 2048)
    for a in A:
        i, s = signed_hash(name(a))        # i in [0, D), s in {-1, +1}
        x[i] = x[i] + s * w(a)
    return normalise(x)
```

The key hashed per ancestor is the ancestor's **name**, so the coordinate survives §70's cross-source identity problem only as far as names agree. The per-ancestor weight `w` is the Jiang–Conrath reading against the seed, the information-content distance

$$
d_{\mathrm{JC}}(a, b) = \operatorname{IC}(a) + \operatorname{IC}(b) - 2 \operatorname{IC}(\operatorname{lcs}(a, b))
$$

A synset carries about eight ancestors, so a coordinate carries about eight nonzeros in $D = 2048$ — the sparsity §13 of `paper-1-the-instrument.md` measures, and the check a rebuild must reproduce. The signed-hash function with its seed, the exact form of `w`, and the normalisation are **declared inputs** (§105 of `paper-1-the-instrument.md`), published with the basis artifact: a peer that hashes differently is not in the same basis and cannot couple (§47 of `architecture.md`).

The basis is measured **with its interval**:

$$
k = 280 \qquad k_{\mathrm{certified\_lo}} = 123 \qquad k_{\mathrm{certified\_hi}} = 956 \qquad k_{\mathrm{certain}} = \text{False}
$$

$k$ is the point estimate of the read that produced the interval — the count of eigenvalues above the Marchenko–Pastur bulk edge,

$$
k = \#\{\, i : \lambda_i > \lambda_+ \,\}, \qquad \lambda_+ = \left(1 + \sqrt{F/T}\right)^{2} \qquad \text{(§10 of paper-1-the-instrument.md)}
$$

— and the read reports it uncertain because the evidence admits **834 resolutions** between the two edges. Everything downstream inherits the estimate with its interval. The read is recorded with the content-addressed basis generation, so a peer on another corpus computes its own $k$ from its own spectrum rather than copying this one.

Over nine live queries the corpus basis retains **0.449–0.620** of frame energy (mean 0.542), and $BB^{\top} - I = 2.33\mathrm{e}{-15}$ establishes the remainder as out-of-span energy rather than error.

The same concepts, fitted both ways, $T \approx 63$:

| stream | raw 2048-dim | in the corpus basis (280), $d = 1$ |
|---|---|---|
| dog | $n_{\text{modes}} = 1$ | **7 modes**, $\tau$ 0.340 / 91.04, ratio 268 |
| water | $n_{\text{modes}} = 1$ | **6 modes**, $\tau$ 0.640 / 14.24, ratio 22 |
| physicist | $n_{\text{modes}} = 1$ | **5 modes**, $\tau$ 0.285 / 81.56, ratio 286 |

$F/T = 32$ in the raw coordinate is rank-deficient; $F/T = 4.4$ in the basis resolves five to seven modes with a clean fast/slow separation.

**The fit runs at $d = 1$, without a delay lift — where the language path departs from the instrument's rule.** §17 of `paper-1-the-instrument.md` sets the delay depth from the signal's own integral correlation length and refuses a depth it cannot measure; here $d = 1$ comes from a sweep of $d = 1\ldots 8$ across three seeds, where the modes sit at $d = 1$ and at $d \ge 3$ the slow timescale goes **negative** ($-288$, $-1381$, $-2789$) — growth, which the reader refuses as unreadable. On the language path $d = 1$ is a **declared input and an open seam** (§103 of `paper-1-the-instrument.md`), in the constants sweep of §113 of `architecture.md`, item 1, until the integral correlation length of the language screens retires it; everywhere else the autocorrelation-derived depth of §17 runs. A lifted coordinate is shared with nothing: a signed coupling requires a shared basis, so an operator fitted in a per-fit lifted width can neither couple across turns nor splice across peers, and the coupling read raises rather than return a magnitude-only statistic in a mismatched basis.

**The basis is content-addressed.** Generations are minted through the store's revision path and overwrite is refused, so a Screen that pooled one width can never find a different one under the same id. Both grids — the $D = 2048$ signed-hash grid and the corpus basis derived from it — are shared **measurement** conventions, pinned as artifacts and required for splicing. What stays frame-relative is the **meaning** placed on them, measured pairwise at each coupling (§27 of `architecture.md`).

## 60. Energy is information

A token that appears everywhere carries near-zero surprisal, so whatever it fires arrives at near-zero amplitude, so coupling ignores it. **There is no stop-list and no length test.**

**A rule is replaced by an amplitude:**

| the rule | the amplitude that replaces it |
|---|---|
| "the far endpoint fired this turn" | its measured salience — an unfired concept is an all-zero row |
| "this is a stopword" | surprisal — a token carrying no information cannot energise anything |
| "this snap is weak" | the coupling itself, carried as energy |
| "this trace is cold" | its amplitude on the measured decay curve |
| "these two glosses match" | the conservation residual |

Whenever a rule appears, the question is **which measured quantity it was standing in for**.

**Evidence, not distance, gates sense activation.** Measured on *"what is a star"*: `is` strips to `i`, and `a` is itself a lemma. Both are letters as nouns, letters sit near printing characters, and two function words therefore **coherently** drag `star` toward its `asterisk` sense — noise that *agrees with the wrong sense* uses the mechanism meant to resolve ambiguity. The question is whether `is` may activate the letter at all — an **evidence** question: *has this surface form ever been observed realising that sense?* The corpus has never seen that word mean that; if a corpus arrives where it has, the reading counts. It is also the measured case where agreement is not accuracy: several observers converged tightly on the wrong sense because their evidence was correlated — the counterexample the convergence test of §92 of `the-economy.md` must survive.

## 61. The decay is measured per screen

A screen is a per-`(node, subject)` accumulator identified by a content address. It fits one operator on its own observation history and reads off $\tau_{\text{fast}}$, $\tau_{\text{slow}}$, and the whole curve $C(\tau) = \sum_k P_k \mu_k^{\tau}$. A trace's amplitude is $\text{energy} \times C(dt)$ — one curve, every resolved mode, **no crossover and no bands**.

Live, per screen:

$$
\begin{aligned}
\text{dog} & \quad \tau_{\text{fast}}\ 0.340 & \quad \tau_{\text{slow}}\ 91.04 & \quad \text{ratio}\ 268 & \\
\text{water} & \quad \tau_{\text{fast}}\ 0.640 & \quad \tau_{\text{slow}}\ 14.24 & \quad \text{ratio}\ 22 & \\
\text{physicist} & \quad \tau_{\text{fast}}\ 0.285 & \quad \tau_{\text{slow}}\ 81.56 & \quad \text{ratio}\ 286 & \\
\text{animals} & \quad \tau_{\text{fast}}\ 0.323 & \quad \tau_{\text{slow}}\ 13.02 & \quad \text{ratio}\ 40 & \quad \text{(a live 8-turn conversation)} \\
\text{science} & \quad \tau_{\text{fast}}\ 0.589 & \quad \tau_{\text{slow}}\ 14.85 & \quad \text{ratio}\ 25 & \quad \text{(a live 8-turn conversation)}
\end{aligned}
$$

The curve is monotone non-increasing over its whole 201-lag reconstruction and stays in $[0,1]$. The fit costs 0.02–0.11 s.

An amplitude at a given lag is $\sum_k P_k \mu_k^{\tau}$, so it depends on the **mode weights** as well as the timescales: a reconstruction reading 1.6e-11 at lag 20 requires nearly all the weight on the fast modes, which a screen carrying $\tau_{\text{slow}} = 91.04$ shows only if $P_{\text{slow}}$ is small. Each screen therefore publishes its $P_k$ beside its $\tau$ values, and any quoted extrapolation names its screen; without the weights the trace rule $\text{energy} \times C(dt)$ is not reproducible.

The tense reading — *is* against *was* — is a cut on the screen's own ordered amplitudes, taken by the instrument that answers every other "how many of these are signal" question, and **it returns everything when nothing separates**.

**A rate is measured or nothing decays.** No typed floor, no crossover point, no amplitude cutoff, and no constant asserted equal to another by a comment: every number on the decay path is read off the screen's own fit.

## 62. Formation — a relation is said because it couples

1. The store's incident edges are **read**.
2. The answer's **band** is its leads' own coupling subspace.
3. The incident signal is one ordered $(T,F)$ frame, one row per edge, at the far endpoint's **measured salience** — amplitude $\sqrt{\text{energy}}$, so a row's squared norm is that row's energy.
4. `absorb_transmit(frame, basis=band)` splits it, conservation exact. **What is absorbed is stated; the residual is not.**

**The junction — where two signals meet.** Step 3 reads a row's amplitude off *one* field: the far
endpoint's salience under the context signal. Where the query also carries an operator — a
verb-dominant token, with its own concepts resolved the same way any other token's are (§57) — the
operator is spread as a **second** field, and the row's amplitude is the geometric mean of the two:

$$
\text{amplitude} = \sqrt{e_{\text{ctx}} \cdot e_{\text{op}}}
$$

which is **zero unless both signals reach the vertex.** A concept the context reaches and the
operator does not contributes an all-zero row, exactly as an unfired concept does, and so does the
converse. Nothing is added and no weight is chosen: a product of two energies, square-rooted back
into amplitude, so a row's squared norm is still that row's energy and the conservation identity of
step 4 is unchanged.

**Measured.** It is what first made two queries over the same concept differ:

| query | relations stated |
|---|---|
| *what does a cow say* | `cow -related_to-> beef` · `cow -hypernym-> cattle` · **`cow -related_to-> emit`** |
| *what is a cow* | `cow -related_to-> beef` · `cow -hypernym-> cattle` · `beef -hypernym-> cattle` · `cattle -hypernym-> bovine` |

The operator steers, and it steers by geometry rather than by a branch on the question word —
consistent with §58's rule that there is no `is_question` flag anywhere in the system.

⚠ **It halts one node short of the target, and the reason is the junction itself.** The corpus holds
the answer, correctly typed: `moo -manner_of->` *"express audibly; utter sounds"* and
`cn-cow -capable_of-> cn-low_by_mooing`. That sense carries the lemmas `emit`, `let loose`,
`let out` and `utter`, and `moo` is its only `manner_of` member. The stated answer reaches `emit`
and stops before `moo`, because `moo` carries charge from `cow` and **none** from `say` — so the
geometric mean puts it below
`beef`. Closing it means the operator's own discharge (§66.1) reaching `moo`, not a change to the
junction.

A concept the propagation never fired contributes an **all-zero row**: there is nothing of it to absorb. Admissibility is not a condition anyone checks.

Measured on the live store, absorbed-energy $> 0$ is *exactly* the set an explicit "fired this turn" predicate would admit, for every probe query: cat 6/15 rows, dog 19/155, einstein 42/940, france 2/34.

**The projection runs in both directions.**

$$
\text{signal} \to \text{projection} \to \text{reasoning} \to \text{projection} \to \text{signal}
$$

The absorbed rows are themselves a frame — one row per relation, at the energy it absorbed — so the outbound read is the same instrument handed the ordered absorbed energies: the resolution read answers when it can certify, the derived statistics when it cannot.

Measured, with the outbound leg closed:

| query | relations stated |
|---|---|
| cat | **2** — domestic cat, wildcat |
| water | **3** |
| physicist | **6** |
| bank | **2**, both senses retained |

The mode-energy series the cut acts on, live:

$$
\begin{aligned}
\text{what is a cat} & \quad [56.57, 19.55, 1.06, 0.99] & \quad \to\ 1\ \text{reading} & \quad \text{cat} \\
\text{who was einstein} & \quad [4.48, 3.09, 3.05, 2.38, 2.34] & \quad \to\ 1\ \text{reading} & \quad \text{einstein} \\
\text{what is a bat} & \quad [6.02, 4.04, 1.23, \ldots] & \quad \to\ 2\ \text{readings} & \quad \text{bat(club), bat(animal)} \\
\text{what is a bank} & \quad [97.28, 62.58, 6.54, \ldots] & \quad \to\ 2\ \text{readings} & \quad \text{bank, financial institution} \\
\text{capital of france} & \quad [2.97, 1.69] & \quad \to\ 2\ \text{readings} & \quad \text{both Paris entries}
\end{aligned}
$$

The table counts relations rendered; the series counts readings the outbound cut kept, which is why `cat` reads 2 and 1 respectively. `bank` returns two readings because the field carries two modes.

**A condenser resolves where a subsumer exists, and selects by measured energy where one does not.** The reached set is a ranked candidate list and also a **diagram** in the sense of §71 — the artifacts the evidence cannot separate from the seed. Where the diagram has a subsumer the answer is its **colimit**: the one object the whole diagram maps into, branches surviving only where they carry what it does not. Over the taxonomy the colimit is exact — intersect the reached names' ancestor-or-self closures, take the maximum-information-content element.

This condenser is distinct from the tekton that condenses by absorbing a band (§32 of `architecture.md`) — same spelling, different role.

## 63. The words are the corpus's

A resolved concept renders by exact keyed lookup: its own cited gloss and its true immediate hypernym. Relations render as the store's own labels — `cat -hypernym-> feline` — on their own line. **Layout, not language**: a connective would assert a reading of the label, while repeating the label asserts only what is stored.

Live answers with citations:

```
what is a dog       A member of the genus Canis (probably descended from the common wolf) that has
                    been domesticated by man since prehistoric times; occurs in many breeds.
                    dog -hypernym-> canine; dog -hypernym-> domestic animal;
                    hunting dog -hypernym-> dog; working dog -hypernym-> dog.
                    [6 citations]

capital of France   The capital and largest city of France; and international center of culture
                    and commerce.
                    paris -instance_of-> national capital; paris -part_of-> france.
                    [4 citations]

zzqxwv plorbnak     ""                                          [0 citations — the computed null]
```

The bracketed count is what the **stated** answer cites for that full query string; §65's per-path counts measure citation *reach* on the bare seed term, before condensing, and the two are not comparable.

**The null is no outgoing signal.** When nothing rises above the floor, nothing comes out — not an apology, not a placeholder, not a canned reply.

**The floor read is the implementation**, and the lexical rule falls out of it. A query all of whose words fire no synset *and* carry zero document frequency has an empty field, so it is silent by construction — a **sufficient** condition, not a second test: a query whose words do fire synsets is also silent whenever what fires clears no floor. No stop-list, no threshold.

A mixed query settles the quantifier: one known word beside one junk word is **not** silent. The junk word contributes an all-zero row and absorbs nothing (§62), the known word's field stands on its own, and the answer is what that field condenses to. Silence needs the whole field empty, not one dead token in it.

The word "refusal" is itself the bias: a refusal is a category the machine invents and attaches to its own silence.

## 64. Categories form by compression

Generalisation is a compression decision. A clump of $n$ members under region $r$ costs $\sum_m \operatorname{IC}(m) - (n-1)\cdot\operatorname{IC}(r)$ to describe, against $\sum_m \operatorname{IC}(m)$ to name each member separately, so a clump saves exactly

$$
\operatorname{gain}(\text{clump}) = (n - 1) \cdot \operatorname{IC}(\text{region})
$$

Bigger clumps and *more specific* regions save more, so the objective has an interior maximum: climb too high and the region's information content collapses toward the root; stay too low and a region covers one point. **The geometry's own information content decides the generality level.**

Multiplicity is free: an outlier does not drag a clump up to the root, because two tight clumps beat one diluted clump under the same objective. A polysemous footprint therefore lights several templates at once, and **matching returns a ranked set, never a single winner.**

The same objective carries the sample floor: $\operatorname{gain} > 0$ makes the singleton fall out for free, $n - 1 = 0$, and excludes two points sharing nothing but the root, which compress by nothing — **a typed minimum sample count decides, before any geometry is consulted, how much evidence is allowed to count, and it decides wrong.**

Categories *resolve from the mass by compression* — never a forced taxonomy, and no level of generality chosen in advance.

## 65. What the operator resolves on language, and what it does not

Measured on the live corpus, the is-a descent frames a propagator would be fitted to:

| seed | descent rows | F | resolved modes |
|---|---|---|---|
| `dog.n.01` | 8 | 2048 | **0** |
| `physicist.n.01` | 5 | 2048 | **0** |
| `bank.n.01` | 5 | 2048 | **0** |

**$\texttt{resolved\_modes} = 0$ — no modes at all.** The aperture refuses these frames exactly as it refuses noise: *"I cannot resolve this"* and *"there is one thing here"* must never be the same answer.

The same wall reads from three directions: a model-free embedder returns the mean, because near-orthogonal hash vectors give the document screen no coherent modes; a one-hot frame fills the feature basis at $\varphi_F = 0.94$ with $K_{\mathrm{signal}} = 0$ and a `coherence` of 0.10 — the aperture's permutation z-score of §9 of `paper-1-the-instrument.md`, so *indistinguishable from noise*; and the is-a descent frames resolve nothing. **Ontology coordinates are sparse, and a sparse frame reads as noise.**

The measured consequence for capacity: planted $K=1$ gives 1600 spots; **pure noise gives 3200** — noise has the *most* spots, because it fills the basis. **Capacity is not content.**

**Conversation is not a compact trajectory.** One row per turn, propagator fitted in the trajectory's own resolved subspace, scored as one-step-ahead prediction on **held-out** turns. 32 predictions, four arms, three nulls:

| arm | operator | persistence | mean | shuffled |
|---|---|---|---|---|
| animals (unrelated queries) | 0.0016 | **0.6132** | 0.6176 | 0.1098 |
| science (unrelated queries) | $-0.0583$ | **0.1994** | 0.2728 | 0.0599 |
| **drift** (a connected chain) | 0.0243 | **0.1193** | 0.2857 | 0.0282 |
| **jumble** (the same 12 concepts, scrambled) | 0.0333 | **0.1148** | 0.2983 | $-0.0328$ |

The operator does not beat *persistence* — "the next turn is the same as this one" — in any arm, and scores at the **shuffled** null. `drift` is built to give a propagator something to find: $\text{dog} \to \text{wolf} \to \text{pack} \to \text{hunt} \to \text{prey} \to \text{deer} \to \text{forest} \to \text{tree} \to \text{leaf} \to \text{plant} \to \text{soil} \to \text{earth}$; `jumble` is the same twelve concepts scrambled, identical vocabulary and topical spread, progression removed. **They are indistinguishable.**

Re-run with nothing collapsed — the screen's full state, $70 \to 517$ traces over 12 turns, scored on the **change** rather than the state:

| arm | $\Delta$ operator | $\Delta$ shuffled | paired diff | **t** |
|---|---|---|---|---|
| drift | +0.1833 | +0.1680 | $+0.0153 \pm 0.1177$ | **0.13** |
| jumble | +0.3351 | +0.1571 | $+0.1780 \pm 0.1004$ | **1.77** |

**The turn axis carries no temporal structure a propagator finds**, on either row construction.

**Certification is unreachable there by arithmetic.** $\texttt{resolved\_modes} = 2$ stably from $T=4$ to $T=12$, with the certified band staying $[0, 280]$ — the entire range. The width goes as $\sqrt{F/T} + F/T$, which approaches $1/\sqrt{T}$ only for $T \gg F$, so at $F = 280$ it needs hundreds of turns. **A conversation cannot certify before it is over.**

What survives, and does not need a turn trajectory: **measured per-mode decay on the screen.**

**So the system places the field directly and reads it** — no fit, no rollout, no horizon, no snap floor. A propagator rolled forward over a concept trajectory produces $\text{dog} \to \text{hunting dog, terrier, hound}$ at a **snap similarity** of 0.72–0.86, similarity to the snapped target rather than the permutation z-score `coherence` names elsewhere. The aperture meanwhile reads $K_{\mathrm{signal}} = 0$ on every frame it was fitted to: directions from a rank-deficient fit, snapped onto the seed's own graph neighbourhood. Direct placement beats it on every query: citations dog $4 \to 28$, water $2 \to 17$, physicist $2 \to 7$, bank $3 \to 5$, the computed null unchanged — citation **reach** per path on the bare seed term, not the §63 counts.

## 66. What replaces a step limit on a propagation

A contribution propagates while it can still clear the corpus's own **propagation floor** — **0.026284** here, a computed null: the weight an *unrelated* pair scores on this corpus. It is derived exhaustively rather than sampled — measured over all **481,846** nouns as $\text{gap} = \exp(-d_{\max}/\xi)$ with $d_{\max} = 1.690949$ and $\xi = 0.464698$, so that $\operatorname{horizon}(\xi, \text{gap})$ returns the corpus diameter exactly. Below the floor a contribution does not propagate weakly; **it does not propagate at all.** This floor is not the **mass gap** of §15 of `paper-1-the-instrument.md`, which is $-\log|\mu_1|$, a decay rate read off a fitted Koopman operator and carrying no corpus number.

The floor is a statistic of a null distribution, published with the procedure that produced it — **how an unrelated pair is sampled, how many pairs are drawn, which weight function is scored, and which statistic of the distribution the number is.** A peer that resamples on its own corpus gets its own floor.

The only finitude is the aperture's, one measured envelope. On this node it holds **~468,000 members** — 7.2 GiB available divided by 16,504 B per member, both read, neither typed, no headroom factor: the envelope *is* the quotient. The graph exhausts at **~74,400**, so the walk ends because the *corpus* ran out, not the box.

**Corpus-side bounds change reach by three orders of magnitude.** A hop cap of 2 holds `dog` to **77** members against **74,217**, and `physicist` to **29** against **73,862** — which is why the floor, and only the floor, ends a walk.

## 66.1 The discharge — how a signal crosses a gap

The propagation floor of §66 ends a walk. It says nothing about how a signal gets from one region of the taxonomy
to a neighbouring one that **no hypernym path connects** — and the taxonomic climb alone cannot,
because the relation that carries the answer is frequently not an is-a relation at all.

Signal accumulates along the taxonomy, each hop attenuated by the single decay kernel over the
Jiang–Conrath tree distance. Where the taxonomy stops, the signal does not stop with it: **a charged
vertex crosses one gap, once, on the charge it is holding.**

$$
\text{reach}(v) = \operatorname{horizon}\!\left(\xi,\ \text{gap},\ \text{weight} = w_v\right)
$$

with $\xi$ the correlation length and $w_v$ **the charge at that vertex**. Five properties, none of
them chosen:

- **The budget is per-vertex and local.** A vertex holding more charge crosses further; one holding
  too little to cross anything is skipped rather than given a floor. Nothing about the corpus as a
  whole enters the bound.
- **The distance is the same distance.** $d_{\mathrm{JC}}$ between the two concepts, against the same
  information-content reading every other reach uses (§59). **A gap is not a special metric.**
- **One pass, no recharge.** What arrives across a gap does not itself discharge again. The jump is a
  single crossing, not a second walk, so reach cannot compound.
- **Nearest reach wins**, exactly as in the taxonomic climb: the arriving amplitude is
  $w_v \cdot \operatorname{similarity}(d)$, kept only where it exceeds what the far vertex already
  holds.
- **Both directions.** The edge is read from either end. A relation the corpus happened to record in
  one direction is not directional evidence about reach.

**Why the bound must be local.** The implementation this replaced bounded the crossing **globally** —
one frontier for the whole field. On the live store that admitted **74,410** members; the per-vertex
bound admits **1,223** on the same query. The difference is not a tighter threshold, it is a
different question. A global frontier asks *how far can this query reach*; the local one asks *how
far can this vertex, holding this much charge, cross* — which is the question a capacitor answers,
and the reason the same measured envelope of §66 still bounds the whole.

Two disambiguations, because three quantities in this work are called something like a horizon. This
$\operatorname{horizon}$ is a **reach budget in distance units**, derived from $\xi$ and the local
charge. It is not the **forecast horizon** $\ell(m)$ that §65 refuses (a step count off a fitted
propagator), and it is not the **propagation floor** of §66 (a computed null that terminates a walk).
The three are never substituted for one another.

⚠ **What is not yet measured.** The discharge has no isolated before-and-after table. Its effect is
reported jointly with the junction in §62, and **separating the two contributions is owed** — as is a
null control showing that a vertex holding no charge crosses nothing, which the code asserts by
construction but which is not measured against a shuffled field.

## 67. The event-driven crystal — the shape the measurements force

A text fallback runs beside this route today (§99 F4); item 6 of §113 of `architecture.md` closes it.

**There must be exactly one route from surface to answer, and it has a name — the capacitor flow:**

```
   surface  --entry-->  BEAM  -->  crystal
                                     |
                         tekton absorbs its coupled band  --> organon (the real-world act)
                                     |
                             residual PROPAGATES to the next coupling
                                     |
   surface  <--inverse--  emit  <----+
```

**A facet's `entry` IS the frame constructor.** Text is the surface, the $(T, F)$ frame is the signal, and a "text path" beside a "beam path" does not exist — text is what a facet converts. Every mechanism downstream — basis derivation, streaming aperture update, the spectral accumulator, absorb/transmit and coupling-based routing, frame encoding, the crystal's conduct/condense/emit — is reachable only through this route, because each requires a signal originated in the form the instrument expects.

**The screen accumulates and fires condensation events.**

```
place(signal)  ->  the screen ACCUMULATES
                        |
                   read the projection            band tightens as √(F/T) + F/T
                        |
                   something CONDENSES  ---------> EVENT: {crystal, tekton, band, k, energy, certified}
                        |
                   the residual propagates        (the next coupling, local or peer)
```

Why this shape:

- **An answer is what condensed** — an event the screen emits when a mode resolves, not a query-time argmax.
- **It makes T grow.** Request/response cannot accumulate: each request builds and discards a frame. Events over an accumulating screen are the only shape where the certified band tightens.
- **Peers compose without shipping data.** Merging pools two nodes' spectra by exchanging covariance, not frames.

**A condensation event is an artifact**, so it is signed at the tekton boundary, lands in the lattice, and is subject to grants.

**Activation, not orchestration.** A tekton fires because a signal couples to its offer, never because a caller decided. **A scheduler calls; an originator places.** Origination is where energy enters from outside: a person asks, time passes, the disk changes, a file appears, a peer speaks.

**The three kinds — the test is what it *touches*, not what it is called:**

| kind | is | touches | needs a PRISM capability |
|---|---|---|---|
| **FACET** | a view — a conduit in/out | representations | no |
| **TEKTON** | a tool — pure; signal in, artifact out | nothing outside | no |
| **ORGANON** | the hands — real-world only | the world | **yes** |

Every invocation is INVOKE-gated by the light-cone (§51 of `architecture.md`), whatever its kind, with a closed default. What distinguishes an organon is the **additional** prism-capability gate and a real-world side effect; "touches nothing outside" is a purity claim, not an authorization one.

Every real-world act splits into a tekton that **decides** and an organon that **does** — which is what lets the system test *would this evict the wrong thing?* without deleting anything.

---
---
# PART IX — THE CATEGORY

## 68. The corpus is a category, and learning is compactification

> *The store is a category $C$: objects = artifacts, morphisms = operators. Consolidation is the search for the smallest category equivalent to $C$ on the queries we care about.*

Two commitments make that literal:

- **Artifacts are observations.** Every vertex is something the system has seen.
- **Operators are observers.** Every operator is a morphism $f : A \to B$ that resolves structure from input to output: it *makes* an observation, recorded as an edge.

An edge is a record that some observer looked and found a relation. Provenance is therefore a property of the morphism rather than metadata beside it, and a merged object's rung is the **minimum** over its members: **an observation composed with a hypothesis is a hypothesis.**

**Compactification is the corpus's own learning step**: *fewer objects, more mass each, morphisms carried and composed.* Mass concentrates where evidence concentrates.

**The compression metric.** Per collection, $\rho = \text{bytes}(\text{generators} + \text{morphisms}) / \text{bytes}(\text{reconstructible corpus})$. The universe is *cooling* when $\rho$ falls while query-coverage holds; publishing $\rho$ is the target of §111 of `architecture.md`. Until it is published there is no $\rho$ to read, and a consumer reads the published measure, never a statistic invented beside it.

**Selection meets category.** VARY — propose new operators by inference and composition. GATE — verify the reconstruction on held-out artifacts. SELECT — adopt iff it *raises* fitness and *lowers* $\rho$. A consolidation that loses a query it used to answer is refuted and reverted.

## 69. The reasoning algebra is generated, not enumerated

The content/context/operator triple **is** a morphism and its two endpoints, so knowing any two determines the third. Composition then shortens the operator set rather than lengthening it: $\text{subtract} = \text{add} \circ (\text{id}, \text{negate})$; $\text{divide} = \text{multiply} \circ (\text{id}, \text{invert})$. **The arrow algebra is four generators plus an identity** — `add`, `negate`, `multiply`, `invert`, `identity` — and $g \circ f$ is a real construction. `infer` returns nothing on a cost tie rather than guessing.

**A derived composition is to be an artifact.** `composed_of` is declared as an edge type; the target is that a derived composition is minted as an artifact carrying morphisms to its factors, so the object carrying the fitness counter is the composition itself — see §76 and §111 of `architecture.md`. A dictionary assembled at import time earns no fitness, smooths no chain, and cannot be selected against $\rho$.

**Store every operator that has a compositional expression as that composition.** Operators reached today as flat calls — floor division, modulo, primality, factorization, square root, factorial, gcd, lcm — are expressed in the generators wherever the generators reach them; the ones that genuinely do not decompose are declared as new generators with their cost. One dispatcher, one algebra: an operator set that bypasses the category module is a second algebra the corpus cannot compress.

## 70. The coordinate cannot see cross-source identity

The corpus holds ~**75,000** concepts twice — two id families over one concept. Measured on 600 random OEWN synsets: **86.5%** have a same-lemma PWN synset, **68.2%** have a byte-identical gloss.

```
wn-einstein.n.01      "physicist born in Germany who formulated…"   instance_of -> wn-physicist.n.01
wn-oewn-10974490-n    "physicist born in Germany who formulated…"   instance_of -> wn-oewn-10447768-n
                                                                     ^ no edge joins them
```

The obvious test returns exactly zero:

$$
\cos\big(\,\mathrm{dense\_vec}(\texttt{wn-einstein.n.01}),\ \mathrm{dense\_vec}(\texttt{wn-oewn-10974490-n})\,\big) = 0.000000
$$

The coordinate's feature key per ancestor is **the name of the node on the hypernym path** — the ancestor's identity, not its position — so two records of one concept in two id families share no path name and therefore no feature index. The full construction is §59's. The subspaces are **orthogonal by construction**, and absorb/transmit transmits 100% residual for every cross-source pair.

**The coordinate is keyed on the identity the measurement is supposed to establish.** It can only confirm an identity it already had, so every geometric test reports "unrelated".

This bounds the claim of §8 of `paper-1-the-instrument.md` that a direction on the screen is a concept: it holds when both lenses enter on a shared basis, which under the current coordinate means within one id family. Across two source-keyed families identity is derived from **edges** instead — what §71 and §72 build.

**PWN, OEWN and ConceptNet are three disjoint universes** sharing only lemma anchors. `wn-dog.n.01` and `cn-dog` are joined by nothing.

A render-time string-equality filter hides the duplication per query and writes nothing back: **the corpus never learns that the two are one.** Derive identity from evidence and write it to the store.

## 71. The diagram is derived, never passed

A diagram supplied by a caller is a merge someone already decided on, and the operator is then only a writer, minting a pointer stub with no positions and no mass. **The diagram is derived from the concept.**

The operator takes **one concept as its subject** — no diagram, no candidate set, no pair:

```python
derive_diagram(store, aid, *, label_keyed, include_unanchored, anchor_cache=None)
```

> *The diagram of a concept — the artifacts the evidence cannot separate from it.*

`store` is the substrate it reads and `anchor_cache` is a memo; `label_keyed` and `include_unanchored` select which arm is measured, and §73 reports both arms.

*Diagram* is the categorical sense: objects and the morphisms between them, over which a colimit can be taken. A reached candidate set (§62) is one too — membership decided by reach ranking there, evidence inseparability here — but only the inseparability diagram is admissible as input to a colimit.

Four steps, none of them a rule:

| step | what it is | why it is not a rule |
|---|---|---|
| **candidacy** | shared anchor vertices — a lemma carrying lexical edges into both records | an observation already in the lattice, not a comparison anyone performs |
| **evidence** | each record's incident edges, expanded into their shared anchors, over the **exact union** of the pair's feature keys | no hash, no width, no collision |
| **band** | the same offer-basis construction the coupling read uses | the coupling subspace, not a chosen projection |
| **verdict** | the conservation residual, measured **in both directions** | one direction alone is *subsumption*, not identity |

**Association comes from the edges, not the coordinate.**

**The candidacy anchor never re-enters as an evidence row.** The anchor is what *generated* the candidacy, so feeding it back is circular. Satellite adjectives carry no relation edges, so for them that row would be the entire frame and unrelated senses would merge on a shared anchor alone.

## 72. The colimit, and its universal property checked numerically

A colimit is **not deletion**: it is the universal object with morphisms *from* every member. The members remain and stay reconstructible.

What the object must carry follows from the universal property:

- **every member's evidence, as the union** — every member's evidence must **factor through it**, and the smallest object with that property has incidence exactly the union of the members'. Carried as per-member `(count, sum)` rows: commutative, associative, therefore order-free and mergeable.
- **every member's provenance, whole** — with the rung at the **minimum**.
- **every member's position, summed** — not averaged. The mean-position control fails at exactly 0.5.
- **a morphism from each member** — written as `consolidates`, $\text{colimit} \to \text{member}$.
- **an identity that is a function of the diagram** — a hash over the sorted member ids, so the same diagram yields the same object and re-derivation is idempotent. An identity minted from the clock produces two artifacts for one merge.

**Recovery of a member is a projection, not an inversion.** A sum of signed-hashed sparse vectors is not invertible in general, so recovery is a projection: the summed coordinate onto the member's own band, residual measured against the rest. The per-member `(count, sum)` rows make that projection well-posed — each member's band is reconstructed from its own retained row, never from the sum. For every member, the residual of its band's projection out of the summed coordinate must fall within the derived float tolerance of §73 — computed from the frame, never typed. Exceed it and the member is lost and the merge refused.

**Conservation is the acceptance test.** The members' rows are stacked into one incident frame and split against the colimit's band, with the ledger left un-emitted; the apply refuses an unbalanced certificate. *If the colimit did not receive everything, the residual is non-zero.*

## 73. Measured: identity is a conservation residual

**Residual zero implies one object.** No threshold anywhere, no "close enough", no top-N.

**The tolerance is a function of the frame, never a typed constant.** It is machine epsilon scaled by the frame's own energy norm and its dimension factor:

$$
\mathrm{tol} = \varepsilon_{\mathrm{machine}} \cdot \|F\| \cdot d
$$

On the frame that produced the published figure, $\|F\| \cdot d$ evaluates to 20, so at double precision $\mathrm{tol} = 20 \cdot 2.22\text{e-}16 = 4.44\text{e-}15$. A different frame carries a different tolerance, computed the same way.

Measured against the string baseline on the same 600 synsets: **45.5% derived** against 68.2% gloss-identical. As a $2 \times 2$ over all 600, one denominator:

```
261 agree to merge · 12 derived-only · 148 gloss-identical but REFUSED · 179 agree to refuse
```

The four cells sum to 600. `unmeasurable` is not a fifth cell: it counts the **68** pairs carrying no frame on one side, reported alongside the matrix because a refusal with no frame is an absence of evidence, not evidence of difference.

It disagrees in both directions:

- The **12** the string test misses are typos (`in a give period`, `plies back and forth`), capitalisation (`Southern Hemisphere`), and pronoun modernisation (`he bid` → `they bid`).
- The **148** it refuses are genuine evidence differences — one source carries lemmas the other lacks (`caucasian`/`white_person`), the other carries species lemmas the first lacks. **A byte-identical gloss is not indistinguishable evidence.**

Keying the evidence by edge label collapses the rate to **0.2%**, because one source records converses that the other records once and the seed edge types declare no inverse pairs — nothing justifies assuming they are converses, so **both arms are measured and reported rather than one chosen**.

Negative controls: `bank` the slope against `bank` the institution — residual **1.0**, no merge. And two objects with nothing recorded about them do not merge, because **an empty frame is not a matching frame**.

## 74. The cycle, and why smoothing is free

```
   the corpus reads its own evidence
            |   two records nothing can separate
            v
   COLIMIT the diagram          one object carrying every morphism
            |
            v
   COMPACTIFY                   fewer objects, more MASS each
            |                   (mass IS inertia — a heavier concept is harder to move,
            |                    which is the corpus LEARNING rather than merely growing)
            v
   THE MORPHISMS SMOOTH         parallel facts collapse; what was two paths is one
            |
            +--------------> new evidence -> repeat
```

**Morphism smoothing costs nothing.** $\text{edge\_key} = \operatorname{blake2b}(\text{src} \| \text{dst} \| \text{label})$ with idempotent upsert means two parallel facts — one from each member — hash identically and land as **one row**. After a merge, `a --instance_of--> b` and `a' --instance_of--> b'` become one edge with no deduplication pass anywhere.

**Stability is certification, and the criterion is exact equality.** The cycle ends when a pass over the corpus mints no colimit and writes no edge — object count and edge count identical to the previous pass, digit for digit. No tolerance is admitted, because the quantity compared is a count and not a ratio.

**The sweep is bounded by one thing.** **The aperture has only a single resource envelope, and nothing else limits the aperture flow.** A pass over 2.15M vertices gets no batch size, no candidate cap, no diagram-size limit, no sampling rate. It takes what the envelope allows and continues. What is not yet processed is **not-yet-processed, not dropped** — and the cursor may never skip.

**The payoff.** ~75,000 concepts stop being two things, so the render-time string filter can go. The three universes stop being disjoint — the precondition for another node's prose being reachable as *the same concepts* rather than a fourth island.

## 75. Placed, never called — and the missing dream

**A scheduler calls; an originator places.** The colimit is not a nightly job, not a scheduled task, not a loop.

**A scalar cannot be placed** — a number is not a coordinate and couples to nothing. **Place the divergent nodes, energised by their divergence.** The consolidate operator then fires because the signal lies in its band, and **no `if` exists anywhere.** Place on the node's own Screen for that subject: a per-`(node, subject)` accumulator identified by a content address, so which Screen is meant is decided by the pair, not by a global.

**The dream phase fires on idle, so idle is a measured condition** — "nothing is being asked" is a reading of the node's own demand signal, the query-time placement rate over the measured envelope, published like any other stat.

**What runs the reader is an origination event, not a timer.** The placement rate is compared against its own computed null, and the crossing of that null is itself a placement: it puts a signal on the node's Screen. The placer couples to it and places the divergent nodes. Nothing polls and nothing sleeps.

## 76. What the categorical layer does not have

The built half is colimits over concept artifacts, derived from evidence and certified by conservation. The rest is work in front.

**Composition written to the store.** §69's target — $a \circ b$ minted as an artifact carrying morphisms to its factors — buys **chain smoothing**: $a \circ b \circ c$ collapses to one morphism, so a path the corpus walks often becomes a single arrow with its own fitness and its own mass. Concept edges keep their own edge type.

**Limits alongside colimits.** Everything categorical today is a *co*limit — observations arrive and are glued. The target is the dual: pullback, equalizer, product, and a **limit reading on the Screen**. It buys *intersection* as a first-class operation — the common part of two frames, the objects two constraints both admit, the agreement between observers.

**Functors between collections, and the Kan extension.** `describe` is a functor $\text{content-category} \to \text{context-category}$; a translation is a functor between language collections; consolidation across a functor is a **Kan extension**, the machinery that fills in a *missing image* — the concept one collection records and the other has never named, constructed as the best approximation the source's structure supports. It buys cross-collection learning without a merge.

**Analogy as a commuting square.** $a : b :: c : d$ holds when the square of morphisms commutes — checkable, exact, and refusable. It is falsifiable against the vector-offset baseline, so build it first: run both on the same pairs and report both arms.

**One category, or a functor between two.** The canon takes objects as artifacts and morphisms as operators; the colimit runs over concept artifacts with `consolidates` and the lexical/relational edges as its morphisms — two coherent categories. The target is the functor between them, written and checked, so a result proved in one transfers to the other.

**Observer-relative head resolution.** §77's version-DAG, whose head is a function of the observer, is the same machinery.

**Formalisation.** The mass-gap development — the spectral quantity $-\log|\mu_1|$ of §15 of `paper-1-the-instrument.md` — carries 44 sorry-free Lean theorems. The target is the categorical laws in the same form: the universal property, the idempotence of re-derivation, the minimum-rung law.

**Cross-kind association, measured.** The 45.5% is measured within one node's own lexicon. The target is the same derived criterion run from a prose article to a synset, with the number published; a $\texttt{title} = \texttt{word}$ string match is the shape that criterion exists to replace.

**The 148 refusals, resolved.** They are one concept with genuinely different recorded lexicalisations: a colimit is supposed to *carry* that union while the criterion reports the evidence separates them. Reconciling the two — a colimit whose band admits the union while the residual still refuses noise — is the open piece of the criterion.

**The live-store pass.** Applying a colimit to 2.15M vertices is an operation whose failure mode is a corpus, so it runs behind the conservation certificate, one diagram at a time, with the cursor that may never skip.

## 77. Disjoint-frame reconciliation is a different problem

The single-observer case is built. The multi-observer case is specified.

**Heads are observer-relative.** Two people may edit one artifact in disjoint frames, and the resolution is **one artifact whose head is observer-relative** — $\mathrm{head}(O)$ is the tip of what $O$ has authored and incorporated, and two observers can hold different heads. Reconciliation is therefore **lazy**: the versions coexist, and a colimit is computed only when one shared canonical answer is needed.

Three pieces carry that into the store: a parent pointer on each version as a version-DAG edge; head resolution as a function of the observer; reconciliation as an *optional* operator a caller invokes when it needs one answer.

The semantic colimit collapses duplicate *observations of the same thing*; disjoint-frame reconciliation reconciles divergent *versions authored by different observers*. The machinery of the first is the basis for the second.

---
---
# PART XI — THE CORPUS

## 96. The curriculum — order of ingestion

The order is **developmental**: a lexicon before grammar, grammar before world-facts, facts before formal reasoning, reasoning before a self-model, a self-model before following instructions.

Each stage is a **collection**, and its promotion condition is *read*, not set: a stage promotes when its coverage stops improving as more of its own sources load — a plateau read by the stability criterion compaction uses (§74) — and when the ingestion door checks have passed on every admitted record. No coverage number is chosen: the promotion point is a property of the ingestion curve in hand.

| stage | what | approx | why here |
|---|---|---|---|
| **0 — Lexicon & relations** | Open English WordNet, Princeton WordNet, interlingual ids, ConceptNet, Open Multilingual Wordnet | ~0.5 GB | the only fully-verified, hand-built, low-entropy layer, and it defines the lemma space all later keyed retrieval indexes into. **No prose is admitted before the words that index it exist.** |
| **1 — Grammar & simple world** | Simple encyclopedic prose, age-graded public-domain readers, a balanced reference corpus | ~2 GB | establishes sentence structure and the highest-frequency world facts on top of the lexicon, with minimal noise |
| **2 — Concepts & entities** | full encyclopedia, targeted entity data, scholarly abstracts | ~15–25 GB | concrete world knowledge and a category lattice — the scaffolding reasoning domains hang on |
| **3 — Reasoning & verification** | formal-proof libraries (the typechecker is the point), math corpora with LaTeX preserved, code *with content*, PR/issue diffs, voted Q&A | ~40–60 GB | the only domains with a ground-truth checker, hence the only ones where the system can generate rather than collect |
| **4 — Self & metacognition** | the system's own source, keyed as symbols; the operator catalog; the fitness record; the metrics trend | ~5 GB | a self-model requires the categorical machinery of stage 3 to represent operators-about-operators, and it is the prerequisite for following instructions *about oneself* |
| **5 — Instructions & pragmatics** | instruction/response pairs, reasoning trajectories | ~2 GB | an instruction is only meaningful against a world-model, a reasoning capacity, and a self it can act upon. Loaded earlier it is noise |

**Ingestion mechanics.** Each source mints a **Source operator** (a coalgebra that unfolds the stream) and a **Citation vertex** (card, license, snapshot id, retrieval date). The source operator advertises its offer and **accrues fitness like any operator**: a source yielding low-verification, low-demand artifacts is selected against.

**The universal acquisition rule: pull the index, decide against the index, fetch only what survived.** Metadata is cheap and separable from content; fetching first and filtering second blows the budget.

Each surviving record becomes an Observation: content encrypted into the object store, artifact minted with its content reference, lemmas, a citation edge, stage and source membership, its producing operator, and provenance at OBSERVED. **Describe at first observation.** **Verify at the door for stage 3 and above:** code must compile, proofs must typecheck, a Q&A pair must have an accepted answer. Failing records are dropped or parked, never promoted.

**The citation edge is enforced at that door**, so nothing minted on the ingest path exists without one. That is an ingress rule, not a property of the whole store: the bulk vertices of §99 F3 predate it, carry neither rung nor citation, and are **quarantined from the answer path** until Phase 3 repairs them.

## 97. Operator-based retrieval — data on demand

- **Phase A — keyed retrieval over the base.** Inverted index plus graph plus aperture rerank over the ingested, consolidated corpus. Fast, offline, deterministic.
- **Phase B — operator retrieval on demand.** A need the base cannot satisfy dispatches to a **retrieval operator** that *materializes* the answer: fetch a blob by identifier, range-request an archive offset by URL, pull one paper's source, GET a documentation page. The result is described, verified, cached — promoted into a collection — and cited. **Retrieval is itself an operator, fitness-selected**: a source that keeps being demanded and keeps verifying earns mass, and the working set grows toward *demand*, never toward the whole corpus.
- **Observation follows demand.** Ingest the generators and the *indices* — the cheap, separable metadata layer — and let operators fetch leaf content when a query needs it. **Edges are observations *made*; make them when demanded.**

A and B are the same need-to-offer dispatch: the router prefers a cheap keyed hit and falls back to a more expensive, cached materializing operator.

**A miss is "absorb what you can, propagate the remainder."** A resident operator absorbs what it can serve; what it cannot absorb *is* the NEED, already framed and already narrowed. The answer is assembled from partial absorptions across local plus N peers.

The single emission predicate is the floor read of §63. A frame nobody absorbs arrives whole at the last hop, and that hop writes the **terminal residual artifact** the $0 \to 1 \to 0$ certificate already requires, absorbed-or-emitted, referencing the NEED. A requester therefore waits for the *presence* of one artifact rather than for an absence, and a propagation that terminated with nothing is distinguishable from one still in flight.

## 98. What is actually held — the fleet inventory

| | |
|---|---|
| Total corpus vertices across the fleet | **~18.6 M** |
| Vertices reachable by the running chat | **2.15 M** — one holding only, **12%** |
| Largest single holding | the foundation holder, 71.6 GB / **7.42 M** vertices |
| Non-corpus scientific data | **70 GB** — lattice gauge configurations and cosmology |

| holding | size | vertices | edges |
|---|---|---|---|
| **the lexicon holder** (serves the chat) | 5.7 GB | **2,151,729** | ~4.9 M |
| **the content holder** | **30.1 GB** | **6,493,317** | 3,460,050 |
| **the foundation holder** | **71.6 GB** | **7,421,604** | 9,812,572 |
| **edge node** | 3.8 GB | 1,282,831 | 3,394,707 |
| **edge node** | 3.8 GB | 1,282,829 | 3,394,705 |

**The holdings are NOT subsets of one another — they are disjoint in kind**, and the edge nodes carry only a stage-0 lexicon *subset*.

| | lexicon | content | foundation | edge |
|---|---|---|---|---|
| ConceptNet | 1,165,110 | 1,165,110 | — | — |
| WordNet | **676,225** | 117,659 | — | — |
| encyclopedic | 310,003 | **5,186,578** | — | — |
| OEWN / OMW / ILI | **unmeasured** | **0** | — | — |
| foundation collection | — | — | **6,254,351** | — |
| markdown content ‡ | — | — | **6,114,748** | — |
| stage-0 lexicon † | 1,841,336 | — | — | 1,282,769 |

† **stage-0 lexicon is a collection membership spanning the ConceptNet and WordNet rows, not a disjoint kind.** ‡ **markdown content is a subset of the foundation collection.** Rows carrying a mark are overlays over rows above them in the same column and must not be summed with them: the three disjoint kinds in the lexicon column total 2,151,338 against a holding of 2,151,729, and the foundation column's two rows overlap by construction against a holding of 7,421,604. The OEWN / OMW / ILI row records presence on the lexicon holder and absence on the content holder; its count there is **unmeasured**, to be published with the count and never estimated.

Total edges on the lexicon holder: **4,924,621**, a typed idempotent table with observer provenance.

**WordNet is human-authored: no gradient descent, no weights.** Two sources that disagree, each carrying its own operator, is signal. **A source whose operator you cannot name is the one to refuse.**

**One imported prior remains, the single exception to the closure claim of §3 of `architecture.md`.** A Brown-corpus Information Content table, counted elsewhere over a corpus never ingested here, is the numeric spine of every meaning distance: not provenanced, not derived by a verified operator from anything in the store, load-bearing for every Jiang–Conrath coordinate and for the compression objective of §64. Of 117,659 synsets, **50,278 (42.7%)** carry the zero-frequency sentinel and 65.3% of nouns a single degenerate value; where the sentinel stands an IC-weighted compression gain evaluates to zero, so category formation is inert over that fraction of the taxonomy. **The resolution is a recount:** count Information Content over the ingested corpus itself, mint the counts as artifacts with citations and a producing operator, and retire the imported table.

**The training-artifact gap.** A synset carries the full triple: content = the gloss, context = its stage and source collections, operator = the source operator, cited at rung OBSERVED — a format proven at **117,659 artifacts**. **The third leg is the gap:** all 117k share one operator, and it is a *provenance* operator rather than a *transform*. Since $\text{context} + \text{operator} \to \text{content}$ is where novel thought comes from, **operator inference cannot be learned from a corpus where the operator is constant.** The arithmetic domain is the only place with real transform operators. **If training is the goal, widen the operators — not the lexical volume.**

## 99. The five faults, and the order that fixes them

**F1 · The merge must happen.** Corpora disjoint in kind must converge, or the node that answers questions holds the smallest one and sees neither the encyclopedia nor the foundation content.

**F2 · The sync path must be verified by keyed counts.** Merkle anti-entropy is the *only* sync mechanism, so a consumer that has consumed nothing must raise an alert. A "segments behind" gauge reading **2** while **2,687 segments (~7.7 GB)** stand unconsumed is the failure this prevents: convergence is proven by keyed counts, never by that gauge.

**F3 · Bulk content requires provenance.** Of the **5,186,578** encyclopedic vertices on the content holder, **5,186,296** carry no provenance rung and no citation source, so they cannot be cited and cannot ground an answer even once reachable. That figure — the vertices lacking a rung, not the holding total — is what Phase 3 covers.

**F4 · Structure must reach the answer.** A holding of **1,674,562** relatedness edges, 221,203 is-a, 182,535 hypernym and 171,552 synonym is inert if a bare-gloss answer uses none of them. Every OEWN concept returning an **all-zero coordinate** makes half of every query's frame phantom rows; some queries form **no frame at all** and are answered off a keyed lookup with the beam uninvolved. That second route is the text fallback, and it disappears when the need originates the frame in a facet's `entry` (§113 of `architecture.md`).

**F5 · The fleet must be running.** A quiesced holder answers nothing, a downed supervisor on the largest holding removes it from the mesh, and every node must carry a runtime the substrate supports.

**The ordering principle: make what is already held answer well before adding more.**

- **Phase 1 — make the local corpus reachable (F4).** Give every concept a coordinate, put the edge graph in the answer, and consume the residual — ~62% of each turn's energy is transmitted and absorbed by nothing. *Done when* zero all-zero rows, an answer cites a relation rather than only a gloss, and the $0 \to 1 \to 0$ certificate closes on a real conversation.
- **Phase 2 — converge the fleet (F1, F2, F5).** Fix the sync gauge *before* syncing: a run validated by a lying gauge is worse than no sync. Drain the unconsumed segments, prove convergence by keyed counts, restore the largest holder, and merge the content and foundation corpora into the reachable set, provenance-first.
- **Phase 3 — repair provenance (F3).** Assign a rung and a citation source to the **5,186,296** bulk vertices, or accept that they can never ground an answer. Where bulk was ingested without recording origins, **provenance is re-ingestable but not recoverable.**
- **Phase 4 — classify the 70 GB of scientific data.** Physics and observational data, plausibly instrument-validation material and a published dataset. It is not lexicon and must not be ingested as though it were.

**The architecture this forces: a bounded cache, activated rather than orchestrated.**

| tier | holds | bounded |
|---|---|---|
| **ACTIVE** | the working set — filtered, corrected, answering | **yes, by free disk** |
| **DURABLE (the pair)** | the large shard plus the content store | no |
| **AUTHORITY** | identity, grants, keys — no corpus by design | — |
| **HOLDERS** | large partial corpora, quiesced — **sources to drain, not targets to trust** | — |

**Write-back splits by kind:** identity to the authority, structure to the durable shard, content to the object store. **The durable pair is the eviction test**: the active node may drop an artifact iff its structure is on the shard *and* its content is in the object store; anything else is **pinned**.

---


---
# PART XV — ACCEPTANCE, AND WHAT IS OWED

*§112 and §114 are the acceptance criteria and the unrun protocol for the work in this paper. The remainder of the original Part XIV is in `architecture.md`.*

## 112. Acceptance — the falsifiable tests

| # | test | passing condition |
|---|---|---|
| **A1 · GROUNDED** | every answer carries $\ge 1$ citation resolving to a real artifact | asserted on every run, at the record granularity A1 requires |
| **A2 · RELATIONAL** | answers use the edge graph, not just a gloss | asserted, and screened on the way out |
| **A3 · UNFABRICATED** | nothing asserted that was not measured; the null is empty | asserted every run |
| **A4 · WEIGHTLESS** | no trained model in the shipped answer path: AST-enforced against imports, egress-enforced against remote inference endpoints | both legs asserted every run. The failure mode the import leg cannot see is an outbound HTTPS call to a hosted model, so the egress leg fails on any call to an inference endpoint; a bring-your-own-key remote call fails the test exactly as a local weight file does |
| **A5 · CONSERVED** | `0-1-0` closes on a live conversation | prefix identity holds at every hop — absorbed-plus-residual energy over the first $k$ hops equals hop 0's incident energy, for every $k$ — and the terminal residual is absorbed or emitted, within the frame's derived float noise |
| **A6 · REACHES** | a miss is served from a peer, fills the cache, is a hit next time | second query on the same term resolves locally |
| **A7 · CERTIFIED** | the Weyl interval collapses onto `resolved_modes` as $T$ grows | demonstrated on an axis where $T$ can grow; the turn axis cannot certify before a conversation ends, by arithmetic |
| **A8 · PROPAGATES** | an improvement on one node appears on the others | anti-entropy converges both nodes to the same root |
| **A9 · INSTALLABLE** | a bundle installs on a clean machine and answers | every member crystal's content hash resolves at install time, and the bundle hash recomputes as the hash over the sorted member hashes |
| **A10 · BOUNDED** | the cache evicts inside the measured envelope; nothing unpropagated is evicted | eviction under a measured envelope, with the unpropagated set held |

**A1's granularity requirement.** Citations reach an answer from two legs. The **relation leg** adds the far endpoint's **own artifact id** — that identifies the record; the **lead leg** must do the same. A corpus-level provenance anchor is a real artifact, so a citation to it resolves and satisfies A1's wording, but **what it resolves to is the source, not the record**: it identifies *the corpus*, not which of 117,659 synsets the sentence was read from. **The renderer already holds the artifact — it fetches it to read the gloss — so it cites the record it actually read.**


## 114. What is still owed

Two measurements are specified rather than reported. **Fix the protocols in advance — question set, arms, oracle, and failure condition — so that the run cannot be steered by its outcome.**

**The language baseline.** Every comparative claim here has a baseline except the one the system is named for: physics is measured against a trained network, boundary reads against cosine similarity, identity against byte-identical glosses, eviction against recency, the confined phase against the Coulomb floor. Until the answers have one, the live answers are demonstrations rather than evidence.

Three arms, all corpus-internal — **no trained model enters the evaluation, including as a judge**:

| arm | what it is |
|---|---|
| **A — the system** | the live answer path |
| **B — first-sense gloss lookup** | head noun to its first noun sense to that gloss |
| **C — keyword retrieval** | IDF-weighted term overlap between the question's content words and each candidate's gloss and lemmas, top-scoring gloss returned |

Question set, fixed before the run: five polysemous heads (`bank`, `bat`, `crane`, `star`, `spring`), five common concepts (`dog`, `water`, `physicist`, `tree`, `river`), four entity and relation questions, three nonsense inputs. **The nulls are load-bearing: they are the only arm-separating item that needs no answer key.**

Measures, each with its oracle named: senses surfaced on the polysemous heads, against the corpus's own noun-sense count — arm B structurally returns 1, so the question is whether A tracks the true count; emission on the three nulls, where **any** emission is fabrication and arm B will emit because morphological stripping maps junk onto real lemmas; the fraction of stated relations that resolve to a real store edge; and citation count.

**What would falsify the position.** If arm B matches arm A on sense coverage across the five polysemous heads, the resolution machinery is not earning its complexity. If arm A emits on any of the three nulls, the computed null is not computing. Known confound, reported with the result: the questions are chosen to include polysemy, which is where a subspace read is predicted to win, so report the per-question table, not only the aggregate.

**The end-to-end trace.** One question — `what is a bank`, the only item that exercises the ambiguity path — instrumented through a driver that reads intermediate values without modifying the source. Nine stages, each reporting actual numbers: tokens with surprisal; grounding per token with the sense counts that decided it; the act selected and where the hole fell; which concepts fired and at what salience; members reached, the propagation floor that stopped the walk, members per hop; the frame handed to the aperture and the fraction of energy the basis retained; the resolved count, the ordered mode-energy series, and what the outbound cut kept; incident, absorbed and residual energy with the conservation imbalance printed; and the answer text with citations and edge labels.

**Where a stage has no instrumented value, the trace says so** rather than inferring it from the stage on either side.

**The conservation check is the trace's own oracle.** If the imbalance exceeds the frame's float noise, the trace is invalid regardless of how good the answer looks.


---

# §65's NULL RESULT HAS AN EXPLANATION, AND THE QUESTION HAS A NUMBER

§65 reports that a connected concept chain (`drift`) and the same twelve concepts scrambled
(`jumble`) are indistinguishable to a fitted propagator, `t = 0.13`. Re-examined with the shuffle
surrogate of `entroptics.sequence` (Lesne, MSCS 2014, §4.2), the finding is sharper than "no
structure was found":

**At the symbol level the two arms are information-theoretically identical, by construction.**

```
drift  H_n = [3.5850, 3.4594, 3.3219, 3.1699]
jumble H_n = [3.5850, 3.4594, 3.3219, 3.1699]        identical to every digit
```

Every concept in either arm occurs **exactly once**, so every n-word is unique and
`H_n = log₂(N−n+1)` — a function of *length alone*. The two arms are permutations of one multiset,
which makes them identical to any order-sensitive statistic, and so is every shuffle of either.
**No instrument could have separated them.** The negative result was a property of the comparison,
not of the corpus.

**Coarse-graining to regions makes them separate, in the direction structure predicts.** Relabelling
the same twelve concepts by their taxonomic neighbourhood (alphabet 4) gives drift `H₂ = 2.845`
against jumble `H₂ = 2.914` — the connected chain carrying *less* block entropy, which is what
temporal organisation looks like. This is the first positive signal on the question. It is not yet
significant at twelve turns (`p = 0.29`).

**And the required length is now measured rather than guessed.** Reproducing the drift chain's own
region-transition statistics and extending, 20 seeds per length:

| turns | mean `z₂` | detection rate |
|---|---|---|
| 12 | −1.68 | 10% |
| **25** | **−3.75** | **100%** |
| 100 | −8.82 | 100% |

**Twenty-five turns, coarse-grained to regions.** Not twelve, and not the hundreds §65's certified
band implies — that band bounds a different quantity, the `resolved_modes` count on a 280-wide
frame, and does not govern this test.

⚠ **What this does NOT establish.** It does not show the corpus carries conversational structure —
only that the question is answerable and has not yet been asked at a length that could answer it.
The `H₂` gap between drift and jumble is one pair of sequences and is not significant on its own. The run that would
settle it is: coarse-grain the fired-concept stream to regions, 25+ turns, `surrogate_test` with its
`n = 1` control asserted.

**One quantity from the same work bears on the certified interval of §11 of `paper-1-the-instrument.md`.** `N_eff = N·h/log₂|X|` — the
effective number of *independent* samples in a correlated sequence (Lesne §4.3). The certified band
goes as `√(F/T)` where `T` is a **row count**; correlated rows carry fewer independent samples than
there are rows, so a band computed on the raw count is **optimistic**. Measured on controls: an
i.i.d. sequence gives `N_eff = 3000/3000`, a periodic one `884/3000`. Whether conversation rows are
correlated enough to matter is **not yet measured**, and until it is, §11 of `paper-1-the-instrument.md`'s intervals on a
conversation screen should be read as upper-bounded by an unchecked assumption.

---

# SCOPE — where these results hold, and what is outstanding

**Identity across sources is derived from edges, not coordinates.** The claim of §8 of
`paper-1-the-instrument.md`, that a direction on
the screen is a concept, holds where both lenses enter on a shared basis, which under the current
coordinate means *within one id family* (§70). Across families the criterion of §71–§73 applies
instead, and its 45.5% is measured within one node's own lexicon. The same criterion run from a prose
article to a synset is specified and not yet measured (§76).

**Direct placement, not a fitted propagator.** §65 establishes that sparse ontology coordinates do
not support one and that the turn axis carries no structure to fit; the answer path therefore places
the field and reads it. Certification on the turn axis is unreachable by arithmetic as well — the
band stays $[0, 280]$ from $T = 4$ to $T = 12$ — so per-screen decay (§61) is what survives there,
and it is what the economy's clock reads.

**Agreement is not accuracy.** §60 is a measured case where several observers converged tightly on
the wrong sense because their evidence was correlated, and every observer here inherits one imported
information-content table, a correlated prior by construction. Any inference of the form *"the
observers converged, therefore it is true"* is refuted here, which is why the independence test of
§92 of `the-economy.md` gates autonomy rather than convergence alone.

**Three items outstanding, each with the measurement that closes it.**

| item | state | what closes it |
|---|---|---|
| the imported information-content prior — **42.7%** of synsets at the zero-frequency sentinel, over which the compression objective of §64 evaluates to zero | the single exception to the closure claim of §3 of `architecture.md` | recount information content over the ingested corpus, mint the counts as artifacts with citations and a producing operator, retire the table (§98) |
| $\rho$, the compression metric — the number that would show entropy falling while coverage holds | **not published**; until it is there is no $\rho$ to read | publish per collection with the byte counts on both sides of the ratio (§68) |
| the language baseline | specified, **not run** | §114 — three corpus-internal arms, fixed question set, an oracle per measure, falsifiers stated in advance |
| the discharge and the junction, measured **jointly** | §62's before-and-after carries both changes at once | run each alone against the same probes, and add the null control that a vertex holding no charge crosses nothing |
| the answer reaches `emit`, not `moo` | the corpus holds the right path, correctly typed | the operator's own discharge must reach `moo`, which carries charge from `cow` and none from `say` (§62, §66.1) |

**The 148 refusals are the open piece of the criterion.** The identity criterion refuses 148 pairs
the string baseline accepts, and is right to: they carry genuine evidence differences, one source
holding lemmas the other lacks. But a colimit is supposed to *carry* that union while the criterion
reports the evidence separates them. A colimit whose band admits the union while the residual still
refuses noise is what reconciles them (§76).

---

# GLOSSARY — the terms this paper uses

The full glossary is in `architecture.md`; the instrument's terms are in
`paper-1-the-instrument.md`.

| Term | Meaning |
|---|---|
| **Artifact** | The universal primitive: a content-addressed object — content, context, identity, provenance, history — carrying edges. |
| **Colimit** | The universal object with morphisms from every member — one thing seen two ways — accepted only when the conservation residual is zero. |
| **Compactification** | Fewer objects, more mass each, morphisms carried and composed: the corpus's learning step. |
| **Concept** | A direction on the screen — universal within one id family, per §70. |
| **K_signal** | The count of singular values of the entropy-folded, MAD-whitened screen above the derived floor $\Phi$. Distinct from `resolved_modes`. |
| **Lattice** | Mantle's store: vertices and edges plus an encrypted content-addressed filesystem, with no external database. |
| **Mass** | Stored verified energy — existence in degrees, inertia, and the switch between propagating and transforming. Derived from the provenance quadruple, never looked up from a rung; the cut it is compared against is a coupling constant still owed its null (§21.1). |
| **Propagation floor** | The weight an unrelated concept pair scores in the corpus graph, measured **0.026284** — a computed null over the propagation weights, derived exhaustively as $\exp(-d_{\max}/\xi)$ rather than sampled, and what terminates an associative walk. Distinct from the mass gap, which carries no corpus number. |
| **Resolved modes** | $\#\{\,k : \lambda_k > \lambda_+\,\}$ — eigenvalues of the unit-diagonal correlation above the bulk edge. |
| **Screen** | The ordered shared surface where signals meet and are read from either side — never shuffle it. |
| **Triple** | Content, context, operator — any two determine the third. |
| **$\lambda_+$** | The Marchenko–Pastur bulk edge, $\lambda_+ = (1 + \sqrt{F/T})^{2}$. |

---

*The measure of this system's life is entropy going down while coverage goes up.*
