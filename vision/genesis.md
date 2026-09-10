# GENESIS — A Model-Free, Self-Organizing Knowledge Universe

### The guiding path: from a bare store to better-than-frontier, without a single model

**Status:** foundational design paper, self-contained and technical. It is the plan of record:
everything we have learned, and everything we will do.
Date: 2026-07-17.

---

## 0. Thesis

The universe selects for complex observers. A complex observer, given an injection of energy,
*locally reduces entropy* — it finds structure, and in finding structure it compresses. That is
the whole model, taken literally and built as software:

- **Artifacts are observations.** Every vertex is something the system has seen.
- **Operators are observers.** Every operator is a morphism that resolves structure from input to
  output — it *makes* an observation, recorded as an edge.
- **Selection is verification.** Observers that resolve *more* structure, *verifiably*, accrue mass
  (fitness) and are retained; the rest are shed. You cannot adopt faster than you can verify.
- **Order is compression.** The system's job is to find the smallest **generating category** —
  the fewest objects and morphisms from which the rest of the corpus can be *reconstructed on
  demand*. Compression *is* entropy reduction. Category theory is the algebra of that compression.

Everything is geometric and arithmetic. **There are no models anywhere** — no learned weights, no
embeddings, no neural scorer, no stochastic decoder. Where a frontier stack would train a model,
we substitute a deterministic calculation: dictionary lookup, inverted index, graph distance, and
**entroptics**, the spectral/Koopman calculation that extracts dynamics from data without fitting a
model. A model is a lossy, unverifiable compression of a corpus into opaque weights; we keep the
compression *legible* — as artifacts, operators, and edges you can read, cite, and check.

The frontier trained on ~55 TB and it *wasn't enough*, because a fixed corpus is a snapshot of
what was already written down, and generality lives past where the reading runs out. Our wager is
different in kind: **store the generators, not the outputs.** A 100 GB base of maximally-distinct,
keyed, verification-bearing structure — plus operators that fetch, describe, and derive the rest on
demand — reaches further than 55 TB of frozen text, because (a) verification-bearing domains let us
*generate* data instead of collecting it, (b) categorical consolidation stores O(generators) not
O(facts), and (c) operator retrieval gives unbounded reach into the live sources without hoarding
them. Target: **100 GB base to start the flywheel; 300 GB of consolidated generators to exceed
frontier capability on the domains we choose.**

The substrate is the Mantle framework. Past 300 GB, Ember itself ports the hot path to C++. We are
not training a model. We are growing a universe.

---

## 1. What we have already learned (the substrate)

This plan does not start from zero ideas; it starts from a running system. The substrate invariants
are load-bearing and are carried forward unchanged.

1. **Content/context separation.** The durable store is Mantle's lattice (Apache) — the
   `mantle.db` package, opened by `open_lattice`. It holds a `content_ref` +
   preview + lemmas; the full content lives **encrypted and content-addressed** in Garage
   (`cas/<sha256(plaintext)>`, Fernet). Nothing is truncated. Content is addressed by the hash of
   its *plaintext*, so identical content dedupes for free.
2. **The content–context–operator triple.** Every artifact records the operator that produced it as
   an edge. Given any two of {content, context, operator} you can infer the third. `context +
   operator → content` is *novel thought*; this is the generative core.
3. **Keyed retrieval, not vector retrieval.** Symbols, dictionary senses, and doc terms are all
   **lemmas**. Lookup is an inverted index (`lemma → postings`) and list-field indices
   (`name IN calls`), not nearest-neighbour in a learned space. Dictionary lookup is *keyed*, and
   keyed beats embedding for the lexical majority of queries.
4. **Operators are artifacts.** A describe/transform/source/fetch operator is itself a first-class
   artifact advertising an OFFER; invocation matches a NEED (content-type / query) to an OFFER. This
   is the same mechanism locally (in-process) and remotely (Mantle over HTTP).
5. **Sources are coalgebras.** An operator is an algebra (pull: input → output). A *source* is its
   dual (push/unfold: the world → a stream of observations). Folder watchers and URL fetchers are
   sources; ingestion of any type flows through one describe pipeline.
6. **Selection is real and recorded.** `crystal/evolution.py` records invocations (verified/refuted) and
   uses (demand), computes a Laplace-smoothed **fitness** = 0.8·verified-rate + 0.2·demand, ranks,
   selects the fittest offering a given need, and **retires** the persistently-unfit. Fitness
   persists across restarts.
7. **The gate is the flywheel.** A change becomes "verified mass" only by passing reality — the test
   suite, the typechecker, the arithmetic identity — never by assertion. Fidelity buys complexity.
8. **The autonomous loop is an OS process** (`ember/runtime/worker.py`), not a session wakeup. It illuminates
   dark matter, applies selection, and measures — deterministically, forever.
9. **Entroptics removes the model.** It is our own library, `entroptics`, which Ember declares as
   its optional `optics` extra. It computes ordered spectral structure by delay-embedding,
   Koopman/SINDy and the Screen/Aperture memory model — the deterministic calculation that stands
   in for what a neural net would otherwise be trained to approximate. **Screen is ORDERED**: never
   hand it an unordered set. Every read of it in this codebase goes through one seam,
   `ember/optics.py`: reaching the library directly picks the wrong door (the entropy fold guard
   destroys a sparse carrier), reads a dimensionful noise floor as if it were dimensionless, and
   takes an i.i.d.-Gaussian null over data that is correlated by construction. The seam exists so
   those three failures cannot be reintroduced at a call site.

---

## 2. First principles → engineering (the 100% universe model)

| Physical principle | Engineering realization |
|---|---|
| The universe selects for complex observers | Operators accrue verified fitness; selection retires the unfit (`evolution.sweep_retire`) |
| Observers reduce entropy locally when energy is injected | Ingestion + compute (energy) → operators find compressing morphisms → the generating category shrinks |
| An observation is an irreversible record | Every edge is append-only; provenance is grounded in *having seen* (path+hash), never a caller's claim |
| Order = low entropy = compression | Consolidation = quotient by equivalence + storing generators; measured as bytes-of-generators / bytes-of-corpus |
| Energy is conserved; you can't get order for free | You cannot compress faster than you can verify; the gate rate-limits growth (inertial learning) |
| No external oracle (a closed universe) | **No models.** Every value is computed from observed data by a deterministic operator; nothing is imported from opaque weights |

The design commitment that makes it a *100%* universe model: **there is no privileged external
knower.** A trained model would be an oracle we cannot inspect or verify — a hole in the universe
through which unverified structure leaks. We forbid it. Every bit of order in the store was either
*observed* (ingested with provenance) or *derived* by a *verified* operator from other artifacts.
That closure is what lets the whole thing be legible, auditable, and self-improving.

---

## 3. Core architecture — vertices, edges, context, collections

> **The type system is OPEN. Vertex and edge types are unbounded.** The vertex and edge sets in
> §3.1 and §3.2 are a *starting basis*, not a schema in the closed-world sense — they are the
> generators known to be useful on day one, not a fence. New types are minted as the universe
> encounters structure that the existing types cannot express: a new content-type arrives with its
> own describe-operator and becomes a vertex type; a newly-observed relation (a citation kind, a
> formal dependency, a morphism class) becomes an edge type. This is required by the thesis — a closed type system would
> be an external oracle deciding in advance what can be observed, and there is no external oracle.
> Types are themselves artifacts: an edge/vertex type is registered like an operator, with an
> OFFER. So the ontology is self-describing and grows by the same observe→describe→select loop as
> everything else. **Do not treat the type tables as exhaustive or as a constraint to design
> around; treat them as the seed.** What is fixed is the *meta-structure*, never the enumerated
> list: artifact = observation, operator = morphism, edge = observation-made, provenance is graded.

### 3.1 Vertex (artifact) types

Every vertex is an artifact with a stable id, a `content_type`, a `state`
(`committed`/`archived`), a `context` (its offer), `lemmas` (keyed terms), provenance, and — for
content — a `content_ref` into Garage. **Seed types** (extend freely):

| Vertex type | `content_type` | Role |
|---|---|---|
| **Content** | `text/*`, `text/x-python`, `text/markdown`, `application/x-tex`, … | Raw observed material; content in Garage, keyed here |
| **Lexeme / Synset** | `text/x-wordnet` | A word sense and its relations — the lexical ground truth |
| **Concept** | `application/x-concept` | ConceptNet/derived concept node (language-neutral) |
| **Entity** | `application/x-entity` | A Wikidata item — named-entity ground truth, interlingual |
| **Symbol** | `text/x-python-symbol` (and future `x-lean-lemma`, `x-c-symbol`) | A code/math unit — the keyed sub-artifact of a file |
| **Operator** | `application/vnd.agience.operator+json` | A morphism/observer (describe, transform, source, fetch, consolidate) |
| **Source** | `application/vnd.agience.source+json` | A coalgebra descriptor (a watched folder, a dataset stream, a URL set) |
| **Collection** | `application/vnd.agience.collection+json` | A named working set (a "subject" / curriculum stage) |
| **Citation** | `application/x-citation` | A provenance anchor: the dataset/paper/site a body of artifacts came from |

### 3.2 Edge types

Edges are observations. **Seed set**; each edge is directional, append-only, and carries a `via`
operator id plus a provenance rung. New relation kinds are minted as they are observed:

| Edge | From → To | Meaning |
|---|---|---|
| `describes` | operator → content | this operator produced this artifact's context (the triple) |
| `via` / `operator` | artifact → operator | the operator edge of the triple (fitness credit flows here) |
| `cited_from` | artifact → citation/source | provenance: where this came from (every ingested artifact has one) |
| `member_of` | artifact → collection | belongs to a curriculum stage / subject (transitive up the tree) |
| `sub_collection_of` | collection → collection | subject hierarchy (collections form a DAG, unbounded depth) |
| `calls` | symbol → symbol | code call graph (list-field index) |
| `hypernym` / `hyponym` | synset → synset | WordNet IS-A lattice |
| `synonym` / `antonym` | lexeme → lexeme | lexical relations |
| `instance_of` / `subclass_of` | entity → entity | Wikidata/ConceptNet taxonomy |
| `defines` / `references` | doc/symbol → symbol | cross-reference |
| `consolidates` | canonical → member | this canonical artifact subsumes these (the compression edge) |
| `supersedes` | new → old | temporal replacement (freshness) |
| `composed_of` | operator → operator | category: this morphism = composition of these generators |
| `observed_by` | artifact → operator | generic observation (the universal edge) |
| `access_event` | principal → artifact | append-only audit of every read/authz decision |

### 3.3 Context schema

`context` is an artifact's **offer** — the natural-language-free (or minimal) statement of *what
need it answers*. It is never free-form prose we trust; it is produced by a describe-operator and
carries that operator's id. The full record:

```
{
  id, content_type, state,
  context,                 # the offer (produced by a describe-operator)
  lemmas: [...],           # keyed terms — the ONLY retrieval surface
  content_ref, size,       # Garage pointer (content is never inline beyond a preview)
  provenance,              # channel: human_validated | observed | span_cited | ontology_proposal
                           #          | hypothesis | unknown | assertion
  cited_from,              # source/citation id
  collections: [...],      # member_of targets
  via,                     # producing operator (triple)
  # operator-only: invocations, verified, refuted, output_uses, uses  (fitness)
}
```

**Provenance is a channel, not a wall.** It records *how one observer obtained one thing*, and it
is not an ordering. `prism.mass.Provenance` fixes the seven values every stored row carries —
`human_validated`, `observed`, `span_cited`, `ontology_proposal`, `hypothesis`, `unknown`,
`assertion` — and the one distinction drawn over them is a partition, not a rank: `REFERENT` is the
set of channels that claim something checkable (`human_validated`, `observed`, `span_cited`).
"Which of two grounded channels is better" is the question that produced band edges nobody could
defend, so it is not asked; how much to believe a grounded claim is the attestation count in
`prism.attestation`, which is measured. `grounds()` is the stronger read: it resolves the
artifact's `cited_from` rather than reading its label, because a label always reads back while a
citation can dangle, and an artifact that cites only itself is an axiom rather than a grounding.

**The channel is derived server-side, never accepted from the caller.** `authorize_and_stamp` is
the whole authority in one call: `principal_kind_of` classifies the authenticated principal as
HUMAN, SYSTEM or CLIENT (a delegated token is SYSTEM at best, however it carries a user id — that
is what stops a persona minting `human_validated` under your identity), `derive_provenance` tests
the claim against that principal's grant, and `stamp` records what was earned. A claim outside the
grant lands as `assertion`, which is the literally true description of it; `span_cited` needs a
verified span on top of the grant, because the span is the evidence. The write still lands — an
over-claim is ordinary traffic — and the claim is moved into `provenance_history`, so the
correction is auditable.

**The genesis rung registry is a second, narrower vocabulary.** `ember.genesis.SEED_RUNGS` mints
four named rung artifacts into the `ontology` collection and maps each onto a channel above:
`OBSERVED` → `observed`, `FETCHED` → `span_cited`, `DERIVED` → `hypothesis`, `ASSERTED` →
`assertion`. These are registry entries describing how an artifact was obtained; the value written
to an artifact's `provenance` field is always the channel string, never the rung name.

### 3.4 Collections — used wisely

A collection is a **subject / working set**, not a folder. Collections are **nested, hierarchical,
and unbounded** — like vertex and edge types, the named set is a seed, not a fence. A collection is
itself an artifact, and collections relate to each other by a `sub_collection_of` edge, so they form
a DAG (a taxonomy of subjects), not a flat list: `subject.math ⊃ subject.math.topology ⊃
subject.math.topology.homotopy`, and a curriculum stage is just the top of one such tree. New
collections are minted whenever demand concentrates on a coherent sub-region of the graph (the
working-set-follows-demand rule) — the system *carves its own subjects*. Rules:

- **Membership is many-to-many and multi-level.** An artifact `member_of` a leaf collection is
  transitively a member of its ancestors; a query scoped to `subject.math` reaches everything below.
  An artifact can belong to several trees at once. A math paper, for instance, sits in
  `stage.reason` ∩ `source.arxiv` ∩ `subject.math.topology`.
- **One collection per curriculum stage** (§5) and **one per source** (§6) are the *seed* roots;
  subject-trees grow beneath and across them without limit.
- **Operators are scoped by collection.** A consolidation or retrieval operator declares which
  collections it ranges over. This keeps the working set small — you observe *demand*, not the
  corpus (operator retrieval, §7).
- **Collections are the unit of promotion.** Data enters a `staging` collection, is described +
  verified, then *promoted* to its subject collection. Dark matter never pollutes a subject.
- **A collection is also the unit of privacy.** `op.remember` mints an owner-scoped private
  collection and lands the statement in it — content encrypted into the content store, the index
  carrying only keyed lemmas, `provenance` `human_validated`, and ownership expressed as the grant
  on that collection rather than as a flag on the row, so only the owner's light cone reaches it.
  `op.share` is the consent gate: an explicit act by the owner is the only way a private memory
  ever leaves that scope, promoted or shared.
- **Collections carry their own metrics.** `genesis.collection_metrics` computes, for one
  collection's direct members, `artifacts`, `bytes`, `generator_bytes`, `consolidated`,
  `dark_matter`, `keyed_coverage` and `rho`; `all_metrics` rolls them into a global figure plus a
  per-collection map, from a bounded sample (an exhaustive census is O(corpus) and belongs to a
  deliberate audit, not to a request path or a timer); `status` serves `rho`, `keyed_coverage` and
  `collections`; and `record_metrics` appends a snapshot to `metrics.jsonl` in the node directory,
  which is the trend `/status` renders. So `/status` shows the universe cooling — entropy per
  subject falling over time. Per-collection *fitness* is the one metric in this list not computed;
  fitness is carried on the operator artifact instead.

---

## 4. No models — the deterministic substitutions

For every place the frontier pipeline reaches for a model, name the geometric replacement. This
table *is* the "no models anywhere" constraint made concrete.

| Frontier uses a model for… | We use (deterministic) |
|---|---|
| Tokenization (learned BPE) | Lexicon-driven segmentation: dictionary + WordNet lemmas + deterministic morphology; unknowns fall back to characters |
| Embeddings / semantic vectors | **Computed, not learned** (§4.1): deterministic spectral graph embedding + PPMI·SVD distributional vectors + entroptics coherence. Used only at the analog↔digital boundaries; the keyed core needs none |
| Quality classifier (e.g. Llama-70B scoring FineWeb-Edu) | Structural signals (headings, code-compiles, math-typechecks), citation-graph centrality (PageRank/harmonic on the CC/citation webgraph), entroptics coherence. **A human/LLM never "picks the number."** |
| Nearest-neighbour retrieval (ANN over vectors) | Keyed lookup (postings intersection) → BM25 (deterministic TF·IDF) → graph walk → entroptics rerank |
| Reranking / relevance model | Entroptics `K_signal` on the ordered candidate stream (coherence, not a learned cross-encoder) |
| Summarization / consolidation (a generative model) | Categorical consolidation: quotient by equivalence, colimit of a diagram, extractive canonicalization — **lossless or explicitly-lossy-with-pointer**, never hallucinated |
| Language generation (decoder) | Operator composition: `context + operator → content`, each step verified. Retrieval-and-assemble with citations, or honest refusal |
| Relatedness / analogy | Morphism inference (`category.infer`) + graph paths; analogy = a commuting square, not a vector offset |

**Entroptics is the linchpin.** Wherever the field would *fit* a function to data, entroptics
*extracts* the function's dynamics directly (delay-embedding → Koopman/SINDy → the Screen/Aperture
scorer). It is deterministic, inspectable, and — critically — **it terminates with a certificate**
(a plateau + held-out generalization), so we know when a domain has been *learned* rather than
*memorized*. Determinism dominates all **compact** domains (arithmetic, physics, formal math); the
non-compact edge (open natural language) is exactly where we lean on keyed retrieval + citation
rather than pretending to a closed-form.

### 4.1 The embedding question — vectors without models

"No models" does **not** mean "no vectors." The thing we forbid is the *trained* embedding: weights
fit by gradient descent, opaque, unverifiable — an oracle you cannot recompute or inspect. A vector
representation of a concept is not inherently a model; it is the concept's **analog** (continuous)
representation, dual to its **digital** (discrete, keyed) one. What matters is *how the coordinates
are produced*. The admissibility test:

> **A vector representation is admissible iff it is an operator — deterministic, recomputed from
> observed data, and verifiable. It is forbidden iff it requires gradient descent, trained weights,
> or an external oracle. Computed, not learned.**

Three deterministic embeddings satisfy this, and they are how we get analog geometry with no model:

1. **Spectral graph embedding.** The relation graph we already build (WordNet/ConceptNet/Wikidata/
   citation) → Laplacian → eigenvectors (Laplacian eigenmaps). A concept's coordinates in the
   graph's spectral basis *are* its "analog signal representation." This is an eigendecomposition of
   an observed matrix — the same class of object as an FFT, not a model. Entroptics' native territory.
2. **Deterministic distributional vectors.** Observed co-occurrence counts → PPMI weighting →
   truncated SVD (LSA/HAL). Levy & Goldberg (2014) proved word2vec/GloVe are *approximating exactly
   this matrix factorization* — so we obtain essentially the same geometry in closed form, from
   observed counts, without the training loop. The model was only ever an iterative approximation of
   a computation we can do directly.
3. **Entroptics delay-embedding.** For *ordered* signals (a query, a candidate stream), embed into
   phase space deterministically and score coherence — the reranker.

**Where they live — the two boundaries, not the core.** The digital keyed core needs no vectors.
That core is exact lookup, consolidation and symbol/lemma retrieval: the bulk of §3–§8. Vectors
earn their place only at the **analog↔digital boundaries**:

- **First leg (input, analog→digital).** A novel query/paraphrase with no exact keyed hit is placed
  *near* known concepts by its deterministic vector, then snapped onto the discrete lexicon.
  Continuous input → nearest keyed concept.
- **Last leg (output, digital→analog).** Graded reranking, fuzzy/cross-lingual match, and "novel
  thought" as movement in concept space. An analogy is a translation vector, always cross-checked
  against its symbolic commuting-square form (§7). Keyed concept → position.

**What is built, and under what names.** No operator is registered as `op.embed.*`; the geometry
itself is real and lives in two modules.

`crystal.ontology.geometry` holds the coordinate. A synset's position is its Jiang-Conrath
hypernym-path coordinate — derived from an information-content table over the WordNet lattice —
signed-feature-hashed into `D=2048` by `dense_vec`, whose inner products preserve the metric to
1e-15. `text_to_signal` maps a text onto that coordinate, and `faithfulness_check` measures the
hashed geometry against the exact JC distance rather than assuming it. There is no fitting step
anywhere in it: it is a lattice walk, a table lookup and a hash.

`ember.signal.projection` holds the basis. `build_basis` takes the corpus's own coordinate cloud,
drops the channels no row occupies, and reads the correlation eigenbasis through
`ember.optics.principal_directions` — an eigendecomposition of an observed matrix, exactly the
class of object §4.1 admits. It is stored as an artifact `via op.consolidate.basis`, carrying the
instrument's resolved-mode count and the certified interval around it, so the basis a read used is
recorded rather than assumed. Because the directions come out ordered by eigenvalue, one stored
generation contains every zoom in the band; two frames read at different widths are in different
coordinates and must not be pooled or compared.

The input leg is the one that is only half there. `oov_tokens` names the tokens with no noun
synset — the ones this arm cannot answer — and the measured answer is to route them to the keyed
arm (`oov="skip"`) rather than to manufacture a coordinate for them: the surface char-trigram
fallback moves the noise floor by eight orders of magnitude, because dense trigram rows populate
the channels a sparse JC coordinate leaves empty. Snapping a novel paraphrase onto the lexicon is
therefore not yet an operator; it is a keyed-arm handoff.

`embed` and `quantize` are a **transform pair** (like time↔frequency): the concept is held in both
representations, and the deterministic map between them is an operator in the content-context-
operator triple like any other. These embeddings are **recomputable and verifiable**, which is a
strict improvement over frozen, opaque trained vectors — not a concession against the thesis but a
sharpening of it. On implementation: these are one-shot linear-algebra computations, not iterative
training. They are recomputed as an operator when the underlying graph/counts change, and their
fitness is measured by retrieval retention exactly like any other operator.

---

## 5. The curriculum — order of ingestion

The order is **not** by dataset size or availability. It is **developmental**, grounded in how a
person is actually built up: a lexicon before grammar, grammar before world-facts, facts before
formal reasoning, reasoning before a self-model, a self-model before following instructions. This
is curriculum learning (Bengio 2009: easy, high-signal first), Piaget's stages (sensorimotor →
formal-operational), Vygotsky's scaffolding within the zone of proximal development, the reading
sciences' phonics→fluency→comprehension arc, and Bloom's remember→understand→apply→…→create — all
of which agree on the same monotone: **concrete, verifiable, low-entropy structure first; abstract,
open, high-entropy material last, and only once there is scaffolding to attach it to.**

Each stage is a **collection**. A stage is *promoted* (its coverage/verification thresholds met)
before the next stage's high-entropy material is admitted, so every later artifact lands on
existing keyed scaffolding rather than as dark matter.

### Stage 0 — Lexicon & relations ("first words")  — ~0.5 GB
*The infant's vocabulary and the semantic net.* This is the keyed spine everything else attaches to.
- **Open English WordNet 2024**, **Princeton WordNet 3.0** (via `wn`), **CILI** (interlingual ids),
  **ConceptNet 5.7**, **Open Multilingual Wordnet**.
- Vertices: Lexeme, Synset, Concept. Edges: `hypernym/hyponym/synonym/antonym/instance_of`.
- Why first: it is the *only* fully-verified, hand-built, low-entropy layer, and it defines the
  lemma space that all later keyed retrieval indexes into. No prose is admitted before the words
  that index it exist.

### Stage 1 — Grammar & simple world ("sentences, easy readers")  — ~2 GB
*Concrete operational: simple, clean, correct prose.*
- **Simple English Wikipedia**, age-graded **Project Gutenberg** (children's / primary readers),
  **Brown Corpus**. Curriculum-learning's "easy tier."
- Vertices: Content (`text/markdown`,`text/plain`). Edges: `describes`, `cited_from`, doc-term
  `lemmas`. First grammar/collocation statistics (deterministic n-gram/collocation counts, no model).
- Why here: establishes sentence structure and the highest-frequency world facts on top of the
  lexicon, with minimal noise, before full-entropy web text.

### Stage 2 — Concepts & entities ("the world's furniture")  — ~15–25 GB
*Categorization, taxonomy, named things.*
- **Wikipedia (full, `wikimedia/wikipedia` parquet)**, **Wikidata (targeted via SPARQL, not the full
  130 GB dump)**, **peS2o** abstracts for reference breadth.
- Vertices: Content, Entity. Edges: `instance_of/subclass_of` (Wikidata lattice), `cited_from`,
  cross-`references`. Entities become the interlingual pivot (better than any wordnet for names).
- Why here: concrete world knowledge and a category lattice — the scaffolding onto which reasoning
  domains hang.

### Stage 3 — Reasoning & verification ("formal operations")  — ~40–60 GB — **the heart**
*The only domains with a ground-truth checker, hence the only ones where we can GENERATE, not just
collect.* This is where better-than-frontier is actually won.
- **Lean mathlib** (the typechecker is the point) → Symbol(`x-lean-lemma`), `defines`/`calls`.
- **Proof-Pile-2 / OpenWebMath / FineMath** → math content, LaTeX preserved.
- **Stack-Edu / SwallowCode** (code *with content*), and — most valuable — **The Stack v2 PR/issue
  diffs**. A diff is a *trajectory*: broken → intervention → fixed, the closest thing to process in
  the public corpus.
- **Stack Exchange** (votes = a human correctness signal, free).
- Vertices: Symbol, Content. Edges: `calls`, `defines`, `references`, `consolidates`. Every artifact
  here is admitted **only if it passes its checker** (compiles / typechecks / has an accepted answer)
  — the gate at ingestion time. This is the corpus we can extend by generation-under-verification.

### Stage 4 — Self & metacognition ("the observer observes itself")  — ~5 GB
*A model of self: the system's own structure and history become artifacts it can retrieve and reason
over.*
- Ingest **Ember's + Mantle's own source** (already keyed as symbols), the **operator catalog**, the
  **fitness/evolution record**, and the **metrics trend** — as first-class artifacts in a `self`
  collection.
- Why here (developmentally after reasoning, before instructions): a self-model requires the
  categorical machinery of Stage 3 to represent operators-about-operators (`composed_of`), and it is
  the prerequisite for following instructions *about oneself* ("rename this symbol", "improve that
  operator"). This closes the loop: the system can now consolidate *its own* operators.

### Stage 5 — Instructions & pragmatics ("how to help")  — ~2 GB (scale collapse)
*The smallest layer, admitted last, because instructions presuppose everything above.*
- **Tulu 3** (best open post-training reference), **FLAN/Super-NaturalInstructions**,
  **OpenAssistant**, **OpenThoughts/OpenR1** reasoning traces.
- Vertices: Content (instruction/response pairs, reasoning trajectories). Edges: `via` to the
  operators they exercise; traces become *worked examples* for operator inference (Stage-3 style
  learning of new operators, gated).
- Why last: an instruction is only meaningful against a world-model, a reasoning capacity, and a
  self it can act upon. Loaded earlier, it is noise; loaded here, it is scaffolded pragmatics.

**The base-100 GB line:** Stages 0–3 at their *sample* configs (§9) plus full 0–2 ≈ **~80–100 GB**
keyed and verified — enough to start the flywheel. Stages 4–5 and the deeper 3 configs take the
consolidated base toward **300 GB of generators**.

---

## 6. Ingestion mechanics — each source is an operator + a citation edge

Every dataset enters through the coalgebra machinery, and **leaves a permanent record of where it
came from**. Concretely, for each source:

1. **Mint a Source operator + a Citation vertex.** For FineWeb-Edu that is `op.source.fineweb-edu`,
   a coalgebra that unfolds the stream, and `cite.fineweb-edu`, the provenance anchor carrying the
   dataset card, license, snapshot id and retrieval date. The source operator advertises its OFFER
   ("streams educational web prose, score≥3") and accrues fitness like any operator — **a source
   that yields low-verification, low-demand artifacts is selected against**, exactly as an unfit
   transform is.
2. **Stream against the index, never the bytes first.** Pull the cheap metadata layer — Parquet
   footers, CC URL index, Stack-v2 SWHIDs, `score`/`license` columns — then **decide against the
   index** and fetch only survivors. DuckDB predicate pushdown on `hf://…parquet`.
3. **Each surviving record → an Observation** (`sources.py`) → content encrypted into Garage →
   artifact minted in Mantle with: `content_ref`, `lemmas` (from the stage's describe-operator),
   `cited_from → cite.<source>` edge, `member_of → source.<name>` and `member_of → stage.<n>`,
   `via → op.source.<name>`, provenance `observed`. An on-demand GET is stamped `observed` too —
   Ember fetching the bytes itself *is* an instrument reading — with `via: op.fetch.get` and the
   HTTP status recorded in the observation's meta.
4. **Describe at first observation** — the stage's describe-operator keys it. Stage 0 uses a synset
   parser; Stage 3 code an AST symbol extractor plus `calls`; math a LaTeX/Lean lemma extractor.
   Dark matter is illuminated at ingest, never left to spin (the describe-must-terminate rule).
5. **Verify (Stage 3+): the gate at the door.** Code must compile/import; Lean must typecheck; a
   Q&A pair must have an accepted/high-vote answer. Failing records are dropped or parked in
   `staging`, never promoted.

So the citation edge is **mandatory and universal**: no artifact exists without a `cited_from`, and
every source is simultaneously an *operator* (fitness-selected) and a *citation* (audit/provenance).
This is how "each source is represented by an operator and/or citation edge" becomes an invariant,
not a convention.

**The invariant is audited, not assumed.** `op.provenance.audit` (`genesis.audit_provenance`)
sweeps the corpus for artifacts missing a `cited_from` or a `provenance`, and
`backfill_provenance` repairs what it finds when invoked with `apply`. The same two counts ride on
`/status`, where an unmeasured scan reports `None` rather than zero — an audit that was skipped is
distinguishable from an audit that came back clean.

---

## 7. Category theory — the consolidation & compression engine

This is where 300 GB beats 55 TB. The store is a **category** `C`: objects = artifacts, morphisms =
operators. Consolidation is the search for the smallest category equivalent to `C` on the queries we
care about.

- **Objects & morphisms.** Artifact `A`, operator `f: A → B`. Composition `g∘f` is a real edge
  (`composed_of`), so derived operators are storable and fitness-selected.
- **Dedup = the trivial quotient.** Content-addressing already quotients by byte-identity, and that
  is the only basis on which `op.consolidate.nearvdup` actually merges: it groups by `content_ref`,
  so every member of a group is the same bytes, archives the non-canonical members with their
  `content_ref` retained, and emits the `consolidates` edge canonical ← member. Because the members
  are byte-identical there is no validity question between them, so the representative is the
  lowest id — arbitrary, deterministic, and the same on every node. The similarity arm is separate
  and advisory: shingled MinHash LSH emits `near_dup_candidate` edges carrying an estimated
  Jaccard and its standard error, and they are an observation, never grounds for archiving. Emission
  is budgeted and the run reports how many candidates it found against how many it emitted, so a
  capped run stays distinguishable from a complete one.
- **Semantic consolidation = a colimit.** Take the same concept across sources: a WordNet synset,
  its Wikipedia article, its Wikidata entity, its ConceptNet node. That diagram of related artifacts
  has a **colimit**: a single canonical Concept object with morphisms *from* each source artifact.
  We store the colimit + the morphisms; the sources become derivable (`context + operator →
  content`). That is lossless compression — nothing is thrown away, the reconstruction operator is
  kept.
- **Operators as generators.** The compression target is a **generating set**: a minimal collection
  of objects and morphisms whose closure under composition reconstructs the corpus. Storing
  generators + morphisms instead of all facts is Kolmogorov compression made structural. The
  "two-of-three predict the third" law is the reconstruction guarantee.
- **Functors between collections.** `describe` is a functor from a content-category to a
  context-category. A translation/alignment is a functor between language collections. Consolidation
  across a functor = a Kan extension (fill in the missing image by the best available morphism).
- **Analogy = a commuting square**, not a vector offset — checkable, not approximate.

**Compression metric (the entropy gauge).** Per collection: `ρ = bytes(generators+morphisms) /
bytes(reconstructible corpus)`. The universe is *cooling* when `ρ` falls while query-coverage holds.
This is the literal, measurable "reducing entropy" claim, and it is computed: a generator is an
artifact with no incoming `consolidates` edge, so `ρ` is generator bytes over total bytes,
`collection_metrics` reports it per collection and `all_metrics` globally, `/status` carries the
global figure with its per-collection map, `record_metrics` appends it to the trend, and
`op.consolidate.nearvdup` returns `rho_before` and `rho_after` for the run. `op.consistency` checks
the invariants the gauge must obey — `ρ ∈ [0,1]`, generators ≤ corpus, mass never vanishing,
fitness in `[0,1]` — with the tolerances derived from the published precision rather than chosen,
so a `ρ` that "rose" by less than one recorded digit did not rise. The one place the reading is
deliberately weak: the `consolidates` edge set is read through a typed store method, because an
unrecognised query answering empty would make every artifact look like a generator and report
`ρ = 1.0` over a corpus that is in fact consolidated.

**Selection meets category.** VARY (propose new operators by `category.infer` + composition) → GATE
(verify the reconstruction on held-out artifacts) → SELECT (adopt iff it *raises* fitness and
*lowers* `ρ`). A consolidation that loses a query it used to answer is refuted and reverted.

---

## 8. Operator-based retrieval — data on demand

The base 100 GB is stored. The other ~54.9 TB is **not** — it is *reachable*. The progression:

- **Phase A — keyed retrieval over the base.** Inverted index + graph + entroptics rerank over the
  ingested, consolidated corpus. Fast, offline, deterministic.
- **Phase B — operator retrieval (on demand).** A need that the base cannot satisfy dispatches to a
  **retrieval operator** whose job is to *materialize* the answer: the Stack-v2 pointer operator
  fetches a specific blob by SWHID; the CC operator range-requests a WARC offset by URL; the arXiv
  operator pulls one paper's LaTeX; `op.fetch.get` pulls a doc page (GET-only). The materialized
  content is described, verified, cached (promoted into a collection), and cited. **Retrieval is
  itself an operator, fitness-selected** — a source that keeps being demanded and keeps verifying
  earns mass; the working set grows toward *demand*, never the whole corpus.
- **Observation follows demand.** We do not ingest 55 TB. We ingest the generators and the
  *indices*: Parquet metadata, CC URL index, Stack-v2 SWHIDs, Wikidata SPARQL — the cheap,
  separable metadata layer. Operators fetch the leaf content when a query actually needs it. This is
  the crystal-of-observables model: edges are observations *made*; make them when demanded.

The switch from A to B is not a rewrite: both are the same NEED→OFFER dispatch. A retrieval operator
is registered like any other; the router simply prefers a cheap keyed hit and falls back to a
(more expensive, cached) materializing operator.

`op.fetch.get` is the only outbound handler in the system, and it is GET-only. It caps a response
body at 5 MB and returns a `truncated` flag with the bytes, which the source carries into the
observation's meta — so "nothing is truncated" is a property of the store, not of a fetch, and a
reader can tell which they are holding. The change-detection hash a URL source keeps is computed
over the same capped prefix, so a change past the cap is invisible to it.

**The operator control plane.** Everything is driven by invoking operators, over one endpoint
(`POST /v1/invoke`): ingestion (`op.source.*`), consolidation (`op.consolidate.*`), the provenance
audit, and status itself — `op.status.universe` (ρ, coverage, per-collection metrics, curriculum
position), `op.health` (worker liveness from a heartbeat age, error rate, the provenance invariant),
`op.consistency` (the physical and logical invariants, as anomalies to investigate) and
`op.curriculum.advance` (one bounded increment of ingest-or-promote). `op.operator.define` closes
the loop: an operator is created or updated **from data** — a kind plus a spec — and is live
immediately, with no code change and no restart. Operators create operators.

---

## 9. Scale, phasing, and the path to 300 GB

**Sub-configs first (cheap → land what survives).** Everything Tier-1 ships nested samples; use the
smallest that proves the pipeline, scale the config without touching code:
- FineMath/Proof-Pile-2 full (small already), OpenWebMath full — math is only ~0.2 TB total.
- FineWeb-Edu `sample-10BT` (28.5 GB) → `-100BT` only if coverage demands it. We are *not* chasing
  15 TB of web prose; Stage-2 Wikipedia + Stage-3 verifiable domains carry more signal per byte.
- Stack-Edu (has content, ~125–160 B tokens) before touching Stack v2 pointers.

| Phase | Collections | Approx on-disk (consolidated) | Exit criterion |
|---|---|---|---|
| **P0 Genesis** | schema + Stage 0 | ~0.5 GB | lexicon keyed; lemma index live; `ρ` baseline recorded |
| **P1 Language** | + Stage 1 | ~3 GB | simple-prose coverage ≥ 0.95; grammar stats live |
| **P2 World** | + Stage 2 | ~30 GB | entity lattice linked; Wikipedia keyed; colimits over concept↔entity↔synset |
| **P3 Reason** | + Stage 3 (samples) | ~90 GB | **flywheel on**: gate-verified generation in math+code; `ρ` falling |
| **P4 Self** | + Stage 4 | ~95 GB | self-collection queryable; operator-on-operator consolidation |
| **P5 Pragmatics** | + Stage 5 | ~100 GB | instruction-following via operators; **base complete** |
| **P6 Deepen** | scale Stage-3 configs + operator retrieval | → **300 GB generators** | beats frontier on chosen domains (math/code/reasoning) by held-out eval |
| **P7 C++** | Ember ports the hot path | — | Mantle hot path (index, entroptics, gate) in C++, Ember-authored + gate-verified |

**Scale-out is a shard mesh, not a bigger box.** A node can run the whole store locally — SQLite
for artifacts and edges, files for content-addressed blobs, no server and no container — which is
what lets a peer with spare CPU but nowhere to host a database run a full ingest shard. Writes stay
local, which is the write-scaling win; the peers hold disjoint halves of the corpus, partitioned by
`EMBER_SHARDS`. Three operators make that read as one universe: `op.mesh.status` aggregates this
shard and every peer into a single view (total artifacts, per-shard reachability, ρ),
`op.mesh.pull` replicates peer artifacts with their content into this store, and
`op.content.promote` tiers local ciphertext up to the durable origin. All three are bounded and
cursor-resumable, and content promotion is idempotent because the address is the content.

**Why 300 GB of generators > 55 TB of outputs, on our domains:** (1) Stage-3 domains generate
verified data faster than we could collect it; (2) categorical consolidation stores O(generators),
so 300 GB of generators *reconstructs* far more; (3) operator retrieval reaches the live 55 TB
without storing it; (4) zero hallucination — every answer is keyed, derived-and-verified, or an
honest refusal, which on verifiable domains is a strict capability win over a probabilistic decoder.

---

## 10. Access, accounts, and outstanding data

**Have / trivial:**
- HuggingFace account + token, `pip install hf_xet` (Xet backend dedups chunks). Covers all Tier-1:
  FineWeb family, DCLM, RedPajama-v2, Nemotron-CC, GneissWeb, all math, all post-training,
  Stack-Edu, SwallowCode, peS2o, C4, OpenWebText, MADLAD/mC4/CC-100/NLLB.
- `wn` package for all wordnets (`wn.download('oewn:2024')`, ~500 MB total).
- ConceptNet S3 dumps (conceptnet.io); Gutenberg + Simple-Wikipedia (site/HF); Stack Exchange
  (archive.org dumps).

**Click-through then stream (HF-gated):** Dolma, CulturaX, The Stack v1. One-time accept on the
card, then normal streaming.

**Requires correspondence / credentials (schedule risk — start early):**
- **Software Heritage / INRIA agreement** for The Stack v2 *content* (email
  `datasets@softwareheritage.org`) — a correspondence, not a click. Metadata streams now: 59 GB of
  SWHIDs plus `detected_licenses`, `star_events_count`, `is_vendor` and the rest. Do all corpus
  design against metadata first. The correspondence has not been opened.
- **AWS credentials** for Common Crawl and the CC webgraph. Common Crawl is free at
  `s3://commoncrawl/` — public read needs no account, but the tooling needs credentials. The
  webgraph is at `projects/hyperlinkgraph/`, PageRank precomputed.
- **arXiv** — `s3://arxiv/` is **requester-pays** (~$100 egress for ~1.1 TB; LaTeX source, not just
  PDF). Budget it, or use the free Kaggle metadata cut if only titles/abstracts are needed. The
  spend decision has not been made.
- **Semantic Scholar API key** only if raw S2ORC is wanted (peS2o on HF avoids it).

**Licensed / money (defer or skip):** BabelNet (registration + licence; API caps rule out bulk),
Penn Treebank, FrameNet, BNC, GermaNet, EuroWordNet.

**Gone (design around the hole):** Books3 (the single largest absence), the full Pile, MassiveText.
Do not plan around them.

**The universal acquisition rule**, which matches the coalgebra design exactly: *pull the index,
decide against the index, fetch only what survived.* Metadata is always
cheap and always separable from content. Blowing the budget = fetching first, filtering second.

---

## 11. Immediate next actions (concrete)

1. **P0 schema freeze + clean store.** Bring up a bare Mantle + Garage. Register the vertex/edge
   types (§3), the provenance rungs, and the collection scaffolding. Wipe prior experimental corpus
   — *start from scratch*.
2. **Stage-0 ingest.** `wn.download('oewn:2024')` + Princeton 3.0 + CILI + ConceptNet →
   Lexeme/Synset/Concept vertices + relation edges. Stand up the lemma inverted index. Record the
   `ρ` baseline with `genesis.record_metrics`. Mint `op.source.wordnet` + `cite.wordnet`. The
   ingesters exist for the whole of Stage 0 — `op.source.wordnet`, `op.source.oewn`,
   `op.source.cili`, `op.source.conceptnet`, `op.source.omw` — each cursor-resumable, and the OMW
   one restricted to an explicit licence allowlist with every skipped language logged.
3. **Describe-operator per new content-type.** Synset parser (Stage 0), simple-prose doc describer
   (Stage 1), then the Stage-3 checkers: Lean lemma extractor (+ typecheck gate), code AST extractor
   (already have; add compile/import gate), LaTeX math extractor.
4. **Source-as-operator harness.** The `DatasetSource` coalgebra streams an HF dataset by config,
   filtering with a predicate before any per-row work and capping with a limit, and resumes from a
   skip offset so a stage advances incrementally. It lives in `agience_chorus/astra/sources.py`
   beside `UrlSource`, because both reach outside the machine; `FolderSource` and `SourceRuntime` —
   a node observing itself — stay in `ember/corpus/sources.py`. `genesis.ingest_dataset` is the
   wrapper that runs one under the full contract: it mints the source triple, stamps every
   observation with its collection, `cited_from`, `via` and provenance, and records the invocation
   against the source's fitness. Parquet predicate pushdown is the remaining piece; row-streaming
   carries Stages 1–2 today.
5. **Consolidation operators.** Three are registered: `op.consolidate.nearvdup` (byte-identical
   quotient plus the advisory similarity arm), `op.consolidate.colimit` (collapse a same-concept
   diagram to its colimit with `consolidates` morphisms from each source), and
   `op.consolidate.crosswalk` (sweep the corpus for same-concept diagrams across sources and
   colimit each — the cross-source compression that first drives `ρ` below 1.0). `nearvdup` reports
   `rho_before` and `rho_after` for the run. Gating adoption on held-out query retention is the
   part still to build.
6. **Initiate the SWH/INRIA correspondence** and make the arXiv spend decision — these are the long
   poles; everything else is streamable this week.
7. **Plot the `ρ` trend per subject.** `/status` already serves the global `ρ`, `keyed_coverage`
   and the per-collection map, and `record_metrics` writes each snapshot to `metrics.jsonl`; what
   is left is rendering that trend per subject so the cooling is visible over time rather than at a
   point.

---

## 12. Invariants (the constitution — do not violate)

- **The type system is open — types AND collections.** Vertex types, edge types, and collections
  are all unbounded; the seed sets in §3 are generators, not a fence. New types are minted (as
  self-describing artifacts) whenever observed structure demands them; collections nest into an
  unbounded subject DAG (`sub_collection_of`) that the system carves for itself as demand
  concentrates. Never design around the enumerated lists as if they were closed.
- **No models, anywhere — computed, not learned.** Every value is produced by a deterministic
  operator from observed data. Entroptics is the calculation that removes the model. Vectors are
  *allowed* where they are computed (spectral/PPMI·SVD/entroptics — §4.1) and *forbidden* where they
  are trained (gradient descent, opaque weights, an external oracle). If a step seems to need a
  model, it is a keyed lookup, a graph walk, a computed embedding, an entroptics extraction, or an
  honest refusal — never trained weights.
- **No artifact without provenance.** Every ingested artifact carries `cited_from`; content is never
  minted from an `ASSERTED` rung.
- **Every source is an operator and a citation.** Fitness-selected, audit-anchored.
- **The gate is sovereign.** Stage-3+ artifacts and all derived operators are admitted only by
  passing a real checker. Fidelity buys complexity; you cannot compress faster than you can verify.
- **Consolidation is lossless or explicitly-lossy-with-pointer.** Never lose information; store the
  reconstruction morphism.
- **Retrieval follows demand.** Store generators + indices; materialize leaves on demand via
  fitness-selected retrieval operators. Do not hoard 55 TB.
- **External operators are READ-ONLY (GET-only).** Fetch a paper: yes. Write to a remote: never.
- **Ember does the work; the agent builds and guides.** Including the eventual C++ port.
- **Commit, push, and pull regularly.** Routine commit-all / push-all / pull-all is the intended
  sync between nodes. Never *force* (no rewriting shared history), and never commit secrets or keys.

---

## What is still outstanding

| # | Outstanding | What exists today |
|---|---|---|
| 1 | No operator is registered under any `op.embed.*` id, and nothing snaps a novel paraphrase onto the discrete lexicon — the input leg of §4.1. | The deterministic coordinate (`crystal.ontology.geometry.dense_vec`, `text_to_signal`) and the corpus eigenbasis (`ember.signal.projection.build_basis`). `oov_tokens` names the tokens this arm cannot answer and they are routed to the keyed arm instead. |
| 2 | No ingest path emits the `FETCHED` rung. | The rung artifact is registered by `SEED_RUNGS` and maps to `span_cited`; a fetched URL is stamped `observed` with `via: op.fetch.get`. |
| 3 | Consolidation is not gated on held-out query retention, so a consolidation that loses a query it used to answer is not automatically refuted. | Three registered consolidation operators; `nearvdup` merges only on byte-identity, keeps the members' `content_ref`, and reports `rho_before`/`rho_after`; the similarity arm is advisory and never archives. |
| 4 | `/status` reports `ρ` at a point, not as a per-subject trend. | `record_metrics` appends every snapshot to `metrics.jsonl` and `_read_trend` reads its tail, so the series is on disk. |
| 5 | Per-collection fitness is not computed, though §3.4 lists it among the metrics a collection carries. | `collection_metrics` computes artifacts, bytes, generator bytes, consolidated count, dark matter, keyed coverage and `ρ`; fitness is carried on the operator artifact. |
| 6 | Ingesters exist only for Stages 0–2. Stage 3 (Lean, math, code with a checker), Stage 4 (self) and Stage 5 (instructions) have no `op.source.*`, and neither do Gutenberg or the Brown corpus in Stage 1. | `op.source.wordnet`, `.oewn`, `.cili`, `.conceptnet`, `.omw`, `.wikipedia-simple`, `.wikipedia-en`. `DatasetSource` + `genesis.ingest_dataset` is the harness a new stage plugs into. The Brown corpus is present as the information-content table behind the JC coordinate rather than as ingested prose. |
| 7 | No Lean lemma extractor and no LaTeX math extractor, so the Stage-3 typecheck and math gates have nothing to run. | `op.describe.python` (AST symbols plus `calls`), `op.describe.markdown` and `op.describe.generic`. |
| 8 | `DatasetSource` filters row by row; Parquet/DuckDB predicate pushdown — decide against the footer, fetch only survivors — is not built. | The predicate runs before any expensive per-row work, and streaming never materializes the dataset. |
| 9 | Nothing is ported to C++ (phase P7). | No C++ or CMake source in Mantle or Ember; the hot path is Python throughout. |
| 10 | The Software Heritage / INRIA correspondence for The Stack v2 *content* has not been opened. | The metadata layer streams now — SWHIDs, detected licences, star counts — which is enough for corpus design. |
| 11 | The arXiv requester-pays spend decision has not been made. | The free Kaggle metadata cut covers titles and abstracts. |

---

*The guiding path will be revised as collections are promoted and `ρ` falls. The thesis does not
change: a closed, model-free, self-compressing universe of observers, selected by verification.
We are not training a model. We are growing a universe, and the measure of its life is entropy
going down while coverage goes up.*
