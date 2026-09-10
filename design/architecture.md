# AGIENCE — THE ARCHITECTURE

## The specification: what the parts are, how they talk, and how that is safe

**Agience · Ikailo Inc. (Toronto / Ontario, Canada) · John Sessford**

**AGIENCE** and **CREATE YOUR AGENCY** are trademarks of Ikailo Inc., registered in Canada.

---

## A specification and a state of record, not a paper

**This is a specification and a state of record. It is not a paper.** A specification is correct or
incorrect against an implementation; it does not converge the way a measurement does, and it should
not be read as though it carried evidence. Where a section here *does* carry a measurement — the
fold gate of §13 in `paper-1-the-instrument.md`, §32.2's conservation identity, §42's prefix
identity — that measurement belongs to one of the companion papers and is repeated here only
because the design decision rests on it.

The two documents that do carry evidence are:

- **`paper-1-the-instrument.md`** — the screened spectral aperture, validated on seeded ground
  truth, lattice gauge configurations, dynamical systems, and a trained model's boundary.
- **`paper-2-knowledge-without-weights.md`** — grounding, identity and consolidation read off a
  corpus, including the measured failures.

---

## Abstract

Everything addressable is an **artifact**: content, context, provenance, and edges. Artifacts are
observations and operators are observers, so an edge records that someone looked and found a
relation. **Signals propagate rather than pipeline** — each receiver absorbs the band that couples to
it and re-emits the residual, with
$\|\text{incident}\|^2 = \|\text{absorbed}\|^2 + \|\text{transmitted}\|^2$ at every hop, certified
0-1-0 across the chain rather than per hop. Peers converge by **Merkle anti-entropy with no cursor**,
so a failed transfer leaves a hash mismatch the next round retries and self-healing is the default
state. **Authorization is graph reachability** over nine-permission CRUDEASIO grants; **confidentiality
is a separate key scheme** whose recipient set that reachability computes — which is what makes
revoking a grant a single record edit with no re-encryption.

Seven nouns carry it: **prism** (the environment, the protocol, and the measurement contract),
**mantle** (the lattice), **crystal** (the gateway), **chorus** (the operators), **ember** (the
observer, and the one aperture onto the instrument), **bundle** (the installable), **origin**
(identity). An eighth name, **entroptics**, is the instrument they are built on. **Beam** is the
measurement itself — the band of signals read at a cut — and it names no package.

What does **not** run is recorded as well. §110 lists five capabilities complete and awaiting
their first caller; §111 lists nine still to build; §113 orders the work; §115 states the debt in the
order a skeptic would attack it; **What is still outstanding**, at the end, collects every place the
design asks for something the code does not yet do.

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
# PART I — THE THESIS

## 1. The six failures

Every complex system that survives manages its **gradients** — the differences that dissipate into uniformity when left alone. A gradient is not a problem solved once; it is a difference that does work while it exists and stops doing any when it flattens. The modern information economy is failing to manage six of them, and each fails in the same direction: toward uniformity.

1. **The knowledge gradient — what an organization knows, against what it has recorded.** Decisions, constraints and the *why* behind a choice carry value precisely because they are not evenly distributed. Left alone the difference flattens: it sits in one person's head, evaporates between meetings, and is rediscovered at great cost. Uniform ignorance is the equilibrium, and nothing has to go wrong for it to be reached.
2. **The signal gradient — knowledge against noise.** Generative AI raises information throughput dramatically, which raises *both* sides at once. Output carrying trust and traceability is knowledge; output without them is more to sort. Raising volume without raising provenance flattens the difference between the two, and the equilibrium is a corpus in which nothing is distinguishable from anything else.
3. **The locality gradient — what is yours, against what is exposed.** The dominant cloud-AI architecture requires your data and your questions to cross an external boundary in order to be useful. For a hospital, a bank, or a law firm the query pattern alone is classified intelligence. When usefulness is conditional on exposure, the difference between inside and outside dissipates by ordinary use rather than by breach.
4. **The sovereignty gradient — many independent centres, against one.** A handful of hyperscalers hold the world's compute and data. Concentration *is* the flattened state: a single point of legal and political dependency, in which differences of jurisdiction, ownership and failure mode no longer exist to be relied on.
5. **The trust gradient — verified against asserted.** Online trust is a brand you are asked to believe, or a central referee that can be captured, coerced, or switched off. With no widely deployed substrate for verifying what is true, why it is trusted, and who vouched for it claim by claim, verified and asserted become indistinguishable — and that equilibrium is uniformly *un*trustworthy, not uniformly trusted.
6. **The value gradient — where value is created, against where it accrues.** Those who contribute the knowledge that makes a system valuable are separated from it by an intermediary holding custody of the relationship and a cut of every transaction. This gradient does not vanish so much as get *transported*, which is what makes its flattening invisible to the party doing the flattening.

These connect into one mission: **make knowledge verifiable, authority accountable, and contribution fairly credited.**

## 2. The unifying insight — coherence at every scale

Information has a natural **resolution**, a density set by its own entropy. Describe something more finely than its evidence supports and you manufacture noise; more coarsely and you lose signal.

The same principle holds at every scale:

- On a physical signal, the aperture finds a waveform's own resolution and reads its structure with no prior knowledge of the sender.
- In stored knowledge, a concept's position is its measured coordinate in the corpus's own derived basis.
- In reasoning, a conclusion is held only at the resolution its evidence supports; when the evidence runs out, the output is empty rather than invented.
- In value, an exchange rate is the coupling strength read at the screen where two ledgers meet.

**Focus is one operation at every scale.** Signal structure, meaning-correspondence, and exchange rate are the same question: how much of this frame is resolved above its own noise floor.

## 3. The thesis, stated as physics

The universe selects for complex observers. Given an injection of energy, a complex observer *locally reduces entropy* — it finds structure, and in finding structure it compresses. Taken literally and built as software:

- **Artifacts are observations.** Every vertex is something the system has seen.
- **Operators are observers.** Every operator is a morphism that resolves structure from input to output — it *makes* an observation, recorded as an edge.
- **Selection is verification.** Observers that resolve more structure, verifiably, accrue mass and are retained; the rest are shed. You cannot adopt faster than you can verify.
- **Order is compression.** The job is to find the smallest **generating category** — the fewest objects and morphisms from which the rest of the corpus can be reconstructed on demand. Category theory is the algebra of that compression.

The commitment that makes it a *closed* universe model: **there is no privileged external knower.** Every bit of order in the store was either *observed* (ingested with provenance) or *derived* by a *verified* operator.

---

**The scale wager.** The frontier trained on ~55 TB and it was not enough: a fixed corpus is a snapshot of what was already written down, and generality lives past where the reading runs out. The wager instead: **store the generators, not the outputs.** A 100 GB base of maximally-distinct, keyed, verification-bearing structure, plus operators that fetch, describe, and derive the rest on demand: verification-bearing domains let the system *generate* data instead of collecting it, categorical consolidation stores $O(\text{generators})$ rather than $O(\text{facts})$, and operator retrieval gives unbounded reach into live sources without hoarding them. Target: **100 GB base to start the flywheel; 300 GB of consolidated generators.**

## 4. No models — the deterministic substitutions

For every place a frontier pipeline reaches for a model, the geometric replacement is named.

| Frontier uses a model for… | This system uses (deterministic) |
|---|---|
| Tokenization (learned BPE) | Lexicon-driven segmentation: dictionary + WordNet lemmas + deterministic morphology; unknowns fall back to characters |
| Embeddings / semantic vectors | Computed (not modeled) and projected onto a corpus basis derived from the signal's spectrum. |
| Quality classifier | Structural signals (headings, code-compiles, math-typechecks), citation-graph centrality, measured coherence |
| Nearest-neighbour retrieval (ANN) | Keyed lookup (postings intersection) → query coverage in the store, BM25 over the corpus index → graph walk → aperture rerank |
| Reranking / relevance model | `K_signal` on the ordered candidate stream — coherence, not a learned cross-encoder |
| Summarization / consolidation | Categorical consolidation: quotient by equivalence, colimit of a diagram, extractive canonicalization — lossless or explicitly-lossy-with-pointer. |
| Language encoding/decoding | Operator composition: $\text{context} + \text{operator} \to \text{content}$, each step verified. Retrieval-and-assemble with citations |
| Relatedness / analogy | Morphism inference + graph paths; analogy is a commuting square, not a vector offset |

## 5. Design principles

1. **Everything is an artifact, and the kernel is blind to what kind.** Identity, agents, servers, tools, workspaces, collections, secrets, licenses, capabilities, even the authority trust-root. The kernel stores, versions, searches, and access-controls them generically and never parses their type-specific structure.
2. **Provenance is built in.** Every artifact carries who made it and when; every operator's output references the operator that produced it. The audit trail is the data structure.
3. **Non-custodial by design.** Your data (self-host and air-gap), your name (you hold your keys), your identity (proven by key control), and your revenue (zero take on operator revenue).
4. **Federated.** There is no global referee. Universities, hospitals, regulators, and media organizations each run their own authority with its own validation policy. Trust is contextual.
5. **Human authority is preserved at the boundary measurement cannot reach** — reserved for the non-self-resolvable.
6. **Sovereignty is architecture.** Enforce residency at the data layer: local work is handled and processed only by local resources.

## 6. The invariants — the constitution

- **The type system is open** Vertex types, edge types, and collections are unbounded; new types are minted as self-describing artifacts whenever observed structure demands them.
- **No models, anywhere** in the shipped runtime and the answer path — computed, not learned.
- **No artifact without provenance.** Every artifact carries an edge to its source.
- **Consolidation is lossless or explicitly-lossy-with-pointer.** Never lose information; store the reconstruction morphism.
- **Retrieval follows demand.** Store generators and indices; materialize leaves on demand.
- **Signals propagate.** Never design a pipeline or a step-I/O contract.
- **Never force, never impose.** No hand-authored templates, no chosen thresholds, no keyword→behaviour branches.
- **The bound is derived.** No arbitrary caps. When a route, rate, or bound looks like it needs a config knob, a symmetry was mis-declared — fix the balance, not the behaviour.
- **Three legitimate external inputs.** A resource envelope measured from the local environment; a noise provider supplied by the caller; and the false-alarm level.
- **Secrets are secrets.** Creator and delegates only — at rest, in transit, in compute, in logs, in backups. No platform peek, no fallback, no recovery path that widens the visible set.

## 7. The whole system in one picture

```
        contract  ──►  measurement  ──►  storage  ──►  gateway  ──►  knowledge
         PRISM          OPTICS           MANTLE       CRYSTAL       CHORUS
           │              │                 │            │             │
           └──────────────┴────────┬────────┴────────────┘             │
                                   ▼                                   │
                                 EMBER ──── loads, never imports ──────┘
```

The measurement column is `ember.optics` — the one module in the tree that imports entroptics — and it
is reached through the contract, never by name: `prism.instrument` declares the measurement protocols
in a numpy-free module, and a host fills the slot at startup. So the measurement stands in the picture
as a stage rather than as a package, and everything above it codes against the slot.

- `crystal -> prism`, exactly the set §29 lists — the gateway imports the contract, and the instrument arrives on an injected slot resolved through `prism.instrument`. Storage arrives on an injected mantle handle wired at the composition root, so crystal takes no import edge on mantle. Crystal assembles by registration, and is never imported by what it assembles.
- `facet -> crystal` and `tekton -> crystal` — each reaches the instrument and the store only through the injected crystal handle. Facet and tekton are **roles inside crystals**, not packages; the noun roster does not contain them. A facet conducts a signal in and out of a crystal's face and absorbs nothing; a tekton terminates and condenses.
- Origin is not a dependency. **Verification is `prism.trust`** — local, against the manifest's JWKS, by whoever holds the token — so no component imports origin or waits on it, which also keeps an Apache-licensed component free of an AGPL import edge, checkable against the licence printed beside each node in §29. Origin's destination is a **facet**: identity presented as a view over the same lattice as everything else (§28).

Ember sits *under* everything because it is the only component that knows the whole graph: it composes, it is not composed. Chorus sits at the top and contains its tektons; it does not depend on them as an external package. Chorus reaches the instrument through crystal by injection, and it imports crystal, prism and mantle directly — its manifest declares all three (§29).

---
---
# PART IV — THE UNIVERSAL MODEL

*The artifact record, the triple, and the provenance rung — §19–§21.1 — are in `paper-2-knowledge-without-weights.md`, where they are the substrate the measurements sit on.*

## 22. Existence is observer agreement

> *Things only exist because observers agree they exist.*

**No artifact exists without an observer.** The top object of the type hierarchy is `created_by` (who) plus `created_time` (when): *no WHO/WHEN implies not a vertex.* Coupling to that top object is 100% on the live corpus. An edge, correspondingly, is a record that some observer looked and found a relation — which is why provenance rides on the morphism.

**An observer is the identity that made a measurement** — an operator artifact, an ember node, a delegate, or a human at a facet. The weight it brings to agreement is its **authority weight**, the same quantity Part IX of `paper-2-knowledge-without-weights.md` calls mass in the observer's frame, attached to the observer artifact that signed the measurement. An ember's weight is not primitive: it derives from the operators it runs — a node is heavy because its operators earned rungs, not because it is a node. **Authority** is separately a governable issuer artifact — an issuer URL plus JWKS (§40) — which makes a measurement attributable; the weight is the scalar an attributable observer carries.

The weight derives from the rungs its past measurements earned and survived: unrevised measurements at `OBSERVED` or `HUMAN_VALIDATED` raise it, revisions against it lower it. An observer with no verification history — a five-minute-old tab, a freshly minted operator — carries **zero** weight and contributes nothing to any mean until its first measurement earns a rung. That leaves open the first observation in a brand-new origin, where no observer has history and the weighted mean is undefined: the bootstrap rule is an open seam (§113), retired by a measurement over the corpus's own weight distribution.

**Agreement is computed, and content-addressing is what computes it.** Two observers who observed the same thing *independently* mint the same `cas/<sha256>` reference; identical edits converge with no merge and no coordination — the CRDT property falls out of the addressing scheme.

**Existence is degreed.** How strongly something exists is the authority-weighted agreement behind it: the provenance rung and the mass it earns. A raw claim with no authority is `UNKNOWN`. Agreement is **never a vote** — it is the authority-weighted **mean** of accurate measurements, so accurate observers set the mean and inaccurate ones add variance that cancels, provided their errors are independent and zero-mean. That proviso is an assumption, not a result: §92 of `the-economy.md` shows it violable under shared provenance. **Complex observers weigh more**: one that resolves more structure verifiably earns higher rungs, and so carries more weight in the mean.

## 23. The Commit boundary dissolves into mass

**Promotion is a continuous physical fact: an object joins the record to the degree that authority-weighted observers agree on it.** The only question asked of a signal is how much **mass** it has accrued, at the cut published in §21.1 of `paper-2-knowledge-without-weights.md`.

Mass carries the three jobs of a promotion boundary:

- **Safety.** Draft output cannot become canonical: canonical status is a mass level and unwitnessed drafting earns none.
- **Staked judgment.** A human staking their judgment is provenance, entering as one authority-weighted agreement that raises mass.
- **Entropy management.** Low-mass signals propagate and decay, so drafting never contaminates durable memory.

**"The record" is what has accrued enough mass, not a place you commit into.** The artifact's `state` field does not participate (§19 of `paper-2-knowledge-without-weights.md`).

The workspace/collection *mechanism* is graph structure — a workspace is a collection whose content type marks it as one, and there is one artifact table. Versions share a `root_id`, old versions are deleted only by policy, and the policy itself is the audit trail.

## 24. The lattice — one store, no external database

**Mantle is the type-blind artifact kernel: SQLite plus an encrypted content-addressed filesystem, with zero external database processes.** There is ONE crypto/event chokepoint, keyed on the collection origin root, fail-closed.

Within Mantle the request flow is strictly layered — router (thin, validates), service (orchestration), lattice/S3 adapter — and routers never call adapters directly.

**The operation dispatcher is one generic route, and it lives in the gateway.** Type-specific and lifecycle operations go through `POST /artifacts/{artifact_id}/op/{op_name}`, served by crystal: the type declares an `operations` block, mantle resolves it (`types_service.resolve_operation`), the dispatcher enforces the operation's `requires_grant` against mantle's audited access check before dispatching, and routes on `dispatch.kind` — `artifact_crud` to mantle's own API, `mcp_tool` to the owning persona, `native` refused with 501 because mantle exposes no direct primitive. Adding a new lifecycle operation adds no endpoint anywhere. The route is deliberately absent from mantle's own HTTP surface — a guard test in `mantle/db/` fails if it reappears there, because the auth, invoke, revert and nonce coverage it would need does not exist on mantle's side.

**Search is encrypted lexical.** Searchable Symmetric Encryption carries the keyword store: each term is lowercased, stopped and Porter-stemmed, then becomes an HMAC blind token under an owner-scoped key; posting lists and their manifests are encrypted per token with AES-256-GCM, bound to their slot by associated data. The store holds tokens and never words. A posting entry carries `{artifact_id, collection_id, field}` and nothing else — term frequency, document length and positions are gone, because the recall path scores no BM25: it answers membership and counts how much of the query each artifact covers.

**Ranking is a separate module from recall, and it degrades to a measurement.** `mantle.search.ranking` takes candidates as `(id, content_type, score)` from whatever produced them and re-ranks by measured reach, cutting with the aperture's `K_signal` where an instrument is registered and with a proportional-drop knee where none is. A standalone store node has neither seam in its process and answers `ordering: "coverage"` — the correct answer for a node with no ontology in it. `MANTLE_ONTOLOGY_HOST` names a module to import, and importing it is the whole mechanism; the module a host registers its seams with is `prism.runner`.

**There is a vector read seam, and it points inward.** Mantle embeds nothing and configures no provider: the only vectors it can hold are ones a writer handed it on the write that produced the text, or ones already resolved in its long-term cache. With neither, the seam returns empty vectors and search for that text degrades to lexical — which is the ordinary case, not an error. Where a vector does reach the ranker it orders that arm; where none does, the order comes from the narrowing itself. Nothing in the store produces a vector, so no model ships in it.

**The store's index holds no plaintext terms; an ember's corpus index does.** Mantle removed its FTS5 module as a privacy decision, and nothing in mantle writes a cleartext posting. The lexical corpus index lives in `ember.corpus.fts` — FTS5 with Okapi BM25 over cleartext postings, which is what makes ranking free there — and anyone who can read that SQLite file can enumerate the corpus vocabulary and much of each document. That is a per-corpus call an ember node makes about content it already holds in the clear, not a property inherited from the store. The two indexes cover different populations and neither is the other's fallback.

**The scope of "encrypted by default" is exact: the content and the queries are encrypted.** Artifact metadata and `context` fields — title, description, tags, state, type, timestamps — are plaintext, protected by grant-based access control rather than field-level encryption.

**Practical lattice discipline:**

- `count(*)` dereferences every record and zombies the node — a query defect, not a heap shortage. Keyset paging is fine.
- `LIMIT` bounds output, not work: a `json_extract` predicate is unindexed and scans the whole store despite `LIMIT 5`. Page on an indexed column, filter in the application.
- Bulk import requires a periodic WAL checkpoint or the WAL fills the disk.
- Versioning is immutable: `content_ref` decides, `root_id` is indexed, reads are head-only.

## 25. The type system is open

Vertex types, edge types, and collections are unbounded. The seed sets that follow are a *starting basis*, not a fence.

**Seed vertex types:** Content, Lexeme/Synset, Concept, Entity, Symbol, Operator, Source, Collection, Citation.

**Seed edge types** — each directional, append-only, carrying a `via` operator id plus a provenance rung:

| Edge | From to To | Meaning |
|---|---|---|
| `describes` | operator to content | this operator produced this artifact's context |
| `via` / `operator` | artifact to operator | the operator edge of the triple; fitness credit flows here |
| `cited_from` | artifact to citation | provenance: where this came from |
| `member_of` | artifact to collection | belongs to a subject / stage, transitive up the tree |
| `sub_collection_of` | collection to collection | subject hierarchy; collections form a DAG |
| `calls` | symbol to symbol | code call graph |
| `hypernym` / `hyponym` | synset to synset | the IS-A lattice |
| `synonym` / `antonym` | lexeme to lexeme | lexical relations |
| `instance_of` / `subclass_of` | entity to entity | taxonomy |
| `consolidates` | canonical to member | the compression edge |
| `supersedes` | new to old | temporal replacement |
| `composed_of` | operator to operator | this morphism = composition of these generators |
| `observed_by` | artifact to operator | the universal observation edge |
| `access_event` | principal to artifact | append-only audit of every authorization decision |

**Collections are subjects, not folders.** They nest into an unbounded DAG; membership is many-to-many and transitive; operators are scoped by collection, which keeps the working set small; and collections are the unit of promotion — data enters a staging collection, is described and verified, then promoted. New collections are minted whenever demand concentrates on a coherent sub-region: **the system carves its own subjects.**

## 26. Matching — jump to close, then measure the gap

The retrieval and matching primitive of the whole system: four measured, relative steps.

1. **CLOSE (jump).** Get to the region that couples — the nearest candidates in the frame. Coarse and approximate on purpose; it only has to land in the neighbourhood.
2. **ATTENUATION / GAP (measure).** Measure the gap between the query and the close candidate — the attenuation, the residual, the `K_signal` of the joint frame. `K_signal` is the count of singular values of the entropy-folded, MAD-whitened screen standing above the derived noise floor $\Phi$; it is a different statistic from `resolved_modes`, the count of correlation eigenvalues above $\lambda_+$, and matching reads `K_signal`. The gap is a MEASUREMENT, never an assertion.
3. **CLOSE THE GAP IF POSSIBLE (adapt).** Refine — couple tighter, resolve at higher resolution, transform frames — but only as far as the measurement supports. The signal's own entropy sets how far the gap can close.
4. **KEEP IT MEASURED.** The residual gap is always carried: a match is never returned as exact, but with its measured gap attached.

It is the meaning-side twin of settlement, on one instrument (§27). Live in production as the adaptive cut: `K_signal` plus relative gap, model-free, no embedding, scored against the fixed-knee baseline on the same frames.

## 27. Anchors dissolve into relative adaptation

**Meaning is positioned by relative adaptation** — measured between the observer's frame and the signal's own, at the resolution the signal's entropy sets, adapting continuously as frames couple. **The universe is relative:** each observer measures in its own frame, proper time is per-origin, heads are observer-relative, and there is no universal grid *of meaning*. The alternative — placing every item against a fixed, fully-disclosed set of shared reference points, a GPS of meaning — requires the absolute grid the physics denies.

**Two levels, and only one of them is relative:**

- **The measurement basis is shared.** The signed feature-hash grid and the corpus basis derived on it are fixed, published, content-addressed, and versioned — minted through the store's revision path, overwrite refused (§59 of `paper-2-knowledge-without-weights.md`). Peers must share it: a signed coupling between two operators is meaningless unless both sides live in the same basis (§47), and splicing requires it.
- **Meaning on that basis is frame-relative.** There is no universal grid of concepts and no shared registry of what a coordinate *means*; correspondence between two frames is measured pairwise, at the coupling, each time.

**It reconciles exactly as currencies do:** each origin its own currency, the rate measured at the point of exchange; each frame its own meaning, the correspondence measured at the point of coupling. **The same instrument does both:** the `K_signal` of the joint frame that reads an exchange rate reads a meaning-correspondence.

---
# PART V — THE COMPONENTS

## 28. The nouns

| noun | one-line definition | owns | license |
|---|---|---|---|
| **PRISM** | environment adapters (js / py / c) — the hardware unit, the protocol bound per language, and the measurement contract | the crystal contract, capability vocabulary, canonical JSON, error set, config shape, structural address, trust floor, host/server scaffolds; the measurement protocols (`instrument`) and the wire that reads on them — law, resolution, vector, mass, conservation, propagation, demurrage, minting, settlement, reach, plane, streams, carriers, frames, mcp_bridge | Apache-2.0 |
| **MANTLE** | the lattice — data and backup | artifacts, lineage, grants, content tiers, encryption including the platform encryption key and the pluggable KEK, anchors, the event bus, the encrypted lexical index, reach ranking, Merkle mesh, and typed reads over the corpus | Apache-2.0 |
| **CRYSTAL** | condensation, routing — signal to content type | the gateway: dispatcher, registries, persona host, and the injection point | Apache-2.0 |
| **CHORUS** | operators, by domain — the choir of tektons | tekton and facet *definitions*, per persona. Nothing else | AGPL-3.0-only |
| **EMBER** | an observer unit, and the one aperture onto the instrument | running: load crystals, energize, schedule, delegate, reach; its own serve/console product; `ember.optics`, the only module in the tree permitted to import entroptics; the corpus lattice and its lexical index | AGPL-3.0-only |
| **BUNDLE** | a collection of artifacts | the installable | Apache-2.0 |
| **ORIGIN** | identity, authority — the IdP | persons, identities, credentials, auth routers, the Shamir split/combine oracle | AGPL-3.0-only |

Three more names complete the vocabulary: **ENTROPTICS**, the instrument these nouns are built on — aperture, projection, lens, screen, beam (Apache-2.0); **PHAROS**, documentation (CC-BY-4.0); and **CLOUD**, the fleet, one folder per node.

**Beam names a measurement, not a package.** A beam is the band of many signals arriving at a screen, and that sense is used throughout §32 and §42. There is no `agience-beam` distribution and no importable `beam` module: the package was archived, its wire and law modules are prism's, and its aperture is `ember.optics`. Four suites hold the absence — in ember, crystal, chorus and prism — so a reintroduced import fails rather than resolving quietly.

**The licensing rule: core is Apache** so that both the AGPL platform *and* the Apache instruments can build on the shared engine without a copyleft conflict. No Apache node may point at an AGPL node anywhere in the dependency graph. The package manifests govern.

**Origin is not a dependency.** The trust floor lives in prism: **verification is `prism.trust`**, performed locally by whoever holds the token, against the manifest's JWKS. Nothing imports origin, no package manifest lists it, and no component waits on it to answer.

What remains of origin is an application — issuance, persons, credentials — and its destination is a **facet** (§32) over the same lattice as every other view, reached by the same addressing and gated by the same grants. Authorization is not among what remains: origin's `grants` and `api_keys` tables were dropped by migration, and its own suite states the consequence — origin does no authorization. Grants live in mantle.

The hardware-unit description covers about 10% of prism's code; the other 90% is the protocol, needed because crystals are language-scoped: every host language needs the same contract, and without prism each one re-derives canonical JSON, which decides every content address and signature. Prism's base install has **zero dependencies** — the contract is pure stdlib — with trust, host, server, and CLI as extras.

- **The wire is SIGNAL only.** Everything non-signal belongs elsewhere: identity and issuance → **origin** over HTTP; storage, encryption, the platform encryption key, anchors, the event bus and artifact helpers → **mantle**; the crystal wire-format, the trust floor and the junction → **prism**; operator and crystal contracts → **crystal**. The measurement itself is behind a slot: `prism.instrument` is a numpy-free protocol module, and `ember.optics` is the implementation that fills it.
- **Identity verification is prism's**, not origin's. `prism.trust` verifies a presented identity locally against the manifest's JWKS, so the check runs wherever the token arrives. Origin issues; it does not adjudicate at runtime. Each component loads its own environment at startup, never at import.
- **crystal COLLECTS and assembles tektons and facets** — it is not imported by them, and it does not import chorus. The composition root — the persona host process — is handed a list of persona providers and mounts each at `/<name>`; the host holds no persona roster of its own and imports no persona by name, so the cycle chorus→crystal is never closed the other way.
- **Facet and tekton are roles inside a crystal, not packages.** They take no import edge at all: the crystal handle is injected at registration, and everything below crystal arrives on it.
- **Ember imports prism, crystal and mantle directly**, and never chorus; it reaches personas over the mount, and `ember/cli.py` and `ember/surface/serve.py` each record the consequence at the call site where a chorus import would otherwise be convenient.

**Where the reasoning substrate lives.** Chorus may use crystal, prism and the injected instrument, so the reasoning substrate — activation, geometry, the measuring half of the ontology — should have one home reachable by every persona on equal terms. It does not have one today: `crystal.ontology` holds the driver, the geometry, the lexical lookup and the relation couplings, while `ember.ontology` holds activation, matching and the corpus statistics. Chorus imports `crystal.ontology` directly and may not import ember at all — a suite in chorus enforces that — so the ember half reaches a persona only as a seam a host registered with `prism.runner`. Two homes on opposite sides of the licence line, reached two different ways. Put the substrate inside a persona instead and exactly one persona can reason, every other must import it, and operator splicing ends, because a persona-private operator can never merge with a peer's. Consolidating the two homes is the work §113 item 3 orders.

## 29. The dependency graph

**Where beam went.** `agience-beam` is archived, and no `beam` module is importable anywhere in the
tree. Its parts landed as follows, read off the manifests and the import sites:

| the archived package held | the tree holds today |
|---|---|
| `beam.optics` — the one aperture onto entroptics | **`ember/optics.py`** — same role, folded into `agience-ember`, so the aperture is on the AGPL side of the two-tier split, and it is the only module in the scanned repos allowed to name entroptics |
| `beam.conservation` — energy, PathLedger | **`prism.conservation`**, behind the `[wire]` extra because it imports numpy |
| the rest of the wire — law, resolution, vector, mass, demurrage, settlement, reach, plane, streams, carriers, mcp_bridge | **`prism.*`**, all under the same names, split across the base install and the `[wire]` extra by what each module actually imports |
| `beam` as a node | **not a node.** The measurement layer is `ember.optics`; the *contract* for it is `prism.instrument`, a numpy-free Protocol module carrying eleven runtime-checkable protocols |
| `crystal -> prism + beam` | `crystal -> prism`, with the instrument arriving on an **injected slot** resolved through `prism.instrument` |

**The rule the graph states is unchanged and is what matters:** membership of the measurement
contract is a **dependency** fact — the arithmetic lives somewhere prism's base install cannot
reach — and prism imports entroptics nowhere. The publication boundary is held by
`prism/py/tests/test_contract_install_is_pure.py`, which blocks `numpy` and `beam` for
`prism.instrument` specifically and holds the whole contract to stdlib, with a control that proves
the blocker fires; the single-aperture rule is held by `agience-ember/tests/test_one_instrument.py`,
which scans six repos and exempts exactly one filename. What moved is which package holds the
implementation, not the shape of the graph.

The table below is read off the package manifests and the module-scope import sites, which govern.
Where a manifest and the imports disagree, the row says so: an edge that resolves only because every
repo in this workspace is installed editable is a fact about this workspace, not a declaration.

**The rule that generates the graph.** Two constraints, and every edge is a consequence of one of
them:

1. **The licence constraint** (§28). Core is Apache so that both the AGPL platform and the
   Apache instruments can build on the shared engine without a copyleft conflict. **No Apache node
   may point at an AGPL node anywhere in the dependency graph.** The package manifests govern.
2. **Composition, never import** (§7, §28). A component that *assembles* others is not imported by
   what it assembles. Crystal collects tektons and facets and is not imported by them; ember composes
   and is not composed; the composition root — the persona host process — is the only place that
   wires the framework.

**The edges, as the manifests and the imports have them.**

| node | licence | depends on |
|---|---|---|
| **prism** | Apache-2.0 | *nothing* — the base install declares **zero dependencies** and the contract is pure stdlib, with `trust`, `host`, `server`, `cli`, `vector` and `wire` as extras. Sixteen wire modules; nine of them import on the bare base, and a subprocess test with numpy and cryptography blocked holds that |
| **mantle** | Apache-2.0 | **prism** — 36 import sites across 24 non-test modules, 13 of them at module scope: `prism.canonical` for the mesh manifest, `prism.mass` and `prism.grounding` for the provenance vocabulary, `prism.envelope` for the resource envelope, `prism.trust.key_manager` for the keys, `prism.rounding` and `prism.instrument` for the spectral read under `mantle/search/beacon/`, `prism.runner` for the ranking seams. **The manifest does not declare it** — it lists only `cryptography`. What that declaration actually covers is the **embeddable store surface**, `mantle/db/*` plus `services/acting_principal.py`, which an AST scan, a runtime import and a functional round trip hold to stdlib plus `cryptography` with no sibling Agience import. The mesh, the search stack and the HTTP app sit outside that surface |
| **crystal** | Apache-2.0 | **prism**, 20 sites, and that is the whole set. Storage arrives on an **injected mantle handle** or over HTTP, so crystal takes **no import edge on mantle** |
| **bundle** | Apache-2.0 | *not a distribution* — a bundle is an artifact shape, not a package, and no manifest declares it |
| **entroptics** | Apache-2.0 | *its own repository; the instrument these nouns are built on*, reached only through `ember.optics` |
| **chorus** | AGPL-3.0-only | **crystal** (87 sites), **prism** (75) and **mantle** (44, twelve at module scope, across 30 modules) — all three declared — plus mcp, fastapi, starlette, uvicorn, cryptography, python-jose and pydantic. The mantle edge is direct: `mantle.clients.artifact_helpers`, `mantle.shard.local_store`, `mantle.db`, `mantle.mesh`. It reaches the instrument through the injected handle, and **may not import ember** — a suite in chorus enforces that |
| **ember** | AGPL-3.0-only | **mantle** (76 sites), **prism** `[trust,vector,wire]` (64) and **crystal** `[ontology]` (62) — all three declared — plus numpy and cryptography. **Never chorus** — it reaches personas over the mount |
| **origin** | AGPL-3.0-only | **is not a dependency of anything.** Nothing imports it, no package manifest lists it, and no component waits on it to answer |

**Two consequences the graph carries.**

- **Verification is `prism.trust`**, performed locally by whoever holds the token against the
  manifest's JWKS. That is what keeps an Apache-licensed component free of an AGPL import edge on
  origin, and it is checkable against the licence printed beside each node of the graph (§7, §28).
- **Facet and tekton take no import edge at all.** They are roles inside a crystal, not packages: the
  crystal handle is injected at registration and everything below crystal arrives on it (§7, §28).

**The licence constraint holds.** Every Apache node — prism, mantle, crystal — points only at Apache
nodes; the AGPL nodes, chorus and ember, point downward at Apache ones, which is the permitted
direction. The one edge to settle is mantle's on prism: it is real, it is licence-clean, and it is
absent from mantle's manifest.

## 30. Prism and capabilities — how an organon gets its light

An organon is a real-world interface. It needs two things:

- **substrate = the LATTICE** it is grounded on — what it reads and writes.
- **light = the PRISM's capabilities** — the specific affordances the environment shines onto it so it can touch the world.

**A capability is a named, prism-provided AFFORDANCE — a handle the environment exposes.** Its name is drawn from an open vocabulary; it exists only where the environment affords it; and its absence is *physical*, never a policy toggle. The shipped lexicon is `fs.read`, `fs.write`, `storage.kv`, `net.get`, `net.request`, `compute.local`, `compute.wasm`, `compute.gpu`, `store.read`, `store.write`, `ui.render`, `human.ask`, `sensor.capture`, `actuator.control` — with `sensor.` and `actuator.` accepted by prefix, so a host advertises `sensor.temperature` without waiting on a vocabulary release. `net.get` is a distinct kind from `net.request` on purpose: read-only web is a different physical affordance from write-capable web. `human.ask` is the human-in-the-loop questionnaire, satisfied by an adapter running a Cuddler process against whatever can answer a question — a TTY, a stub, a relay — because a human is part of the real world and the same adapter boundary that covers a camera covers a person. The lexicon is mirrored in prism-c and prism-js, and a cross-SDK drift check holds all three to one list.

**A prism is the environment an ember is grounded on** — the hardware and runtime plus the set of capabilities it affords. It **advertises** capability names and **provides** the concrete handle for each.

| prism | advertises | notes |
|---|---|---|
| cloud VPS | `net.get net.request store.read store.write compute.local fs.*` | the full-light environment |
| Raspberry Pi (field) | `store.read compute.local sensor.capture` | limited light; hardware I/O |
| browser | `ui.render net.get` (CORS-scoped), `store.read store.write`, `compute.gpu` | no fs, sandboxed |
| air-gapped box | `store.read store.write compute.local` — **no net** | net-dark by physics |

**Advertisement is MEASURED, not configured, and only a probe run measures it.** `advertises()` runs the probes on every call and reports only what a probe ran and confirmed: a prism claims `net.get` only if it can actually GET, and the set re-balances with the environment rather than being declared once. `prism init` signs a manifest of that measured set for the install gate to read; the runtime gate re-measures. `Capability.present()` is three-valued — True, False, or None — so a capability that was never probed stays distinguishable from one probed and found absent, and `unverified()` reports the None set separately: "the GPU is missing" and "nobody looked for the GPU" are different reports. Nothing gates on the unverified set. **Probes establish PRESENCE; nothing else does, and never configuration.**

*(a) Gating.* An organon declares `requires: [capabilities]`; the junction decides whether it lights up here, reading PRESENCE. If the prism does not afford a required capability the organon is **dormant** — a typed refusal with the gap named, never a crash and never a silent no-op.

*(b) Runtime.* Capability handles are **injected** into the organon from the prism. The organon codes against the abstract capability (`net.get(url)`), never against httpx, a cellular modem, or browser fetch.

**The capability IS the sandbox.** A pattern touches exactly what the capability offers and nothing else: you never trust code, you bound its ground.

## 31. Capability as an artifact — matched by propagation

The two mechanisms are sequential. Probes establish PRESENCE (§30). Path strength then RANKS matches among capabilities already present. Nothing ranks what is not present, and no amount of accrued path strength makes an absent capability present.

The permission boundary is **not** the capability set. Two decisions stay separate:

- **Matching = propagation.** Nearest, hop the gap, propagate from there; strengthened by good matches. Routing and discovery.
- **Authorization = the GRANT on the energy.** The light-cone over CRUDEASIO grants — Create, Read, Update, Delete, Evict, Invoke, Add, Share, Admin — is the one decision point for access everywhere else in the system (§51). **Nothing is authorized by owning a capability name.**

| | how it works |
|---|---|
| a capability | an **artifact** — content-addressed, published, reachable |
| matching | **propagation** — nearest, hop the gap, propagate from there |
| measurement | **use** — good matches strengthen the path |
| the probe set | **supplied by the host**; prism ships mechanism only |

Both halves:

- `spread_graph(graph, seeds, decay)` takes the capability graph — nodes are capability artifacts, edges are the observed relations between them — a seed map of `{node: energy}`, and the single decay kernel. It BFS-propagates each seed outward, attenuating by ACCUMULATED edge distance, and returns the field `{node: energy}`.
- `screened_accumulate(field, seeds)` takes that field and the seeded needs and returns, per seed, the screened accumulation at every node. Its argmax is *nearest*.
- **The decay length is MEASURED, never typed.** It is the scale the aperture reads on the capability graph's own frame, applied through the one decay kernel the law single-sources. A graph with long, well-travelled edges decays slowly; a sparse one decays fast.
- **The winner is chosen without an energy threshold.** It is the nearest capability whose accumulated energy clears the computed null over that same field; where nothing clears the null, the junction refuses and names the gap. The null's sampling procedure and its statistic are published with the field. No "sufficiently energised" constant appears anywhere (§102 of `paper-1-the-instrument.md`).

A crystal's needs seed the field; the prism's capability artifacts are the graph. The `sensor.*` and `actuator.*` families are open — `sensor.temperature` reaches `sensor.capture` by nearness rather than failing a membership test. The base vocabulary is the **lexicon** the propagation runs over, never a gate.

Authorization is CLOSED by default: two gates and three distinguishable refusals — `no_hardware`, `no_grant`, `no_authority` — with the INVOKE light-cone reaching by containment, so one grant per install propagates. **The three typed refusals are LOCAL, in-process diagnostics.** Any remotely visible response collapses `no_grant` and `no_authority` into the 404 of §36 and §51, so a missing artifact and a forbidden one stay indistinguishable; only `no_hardware` — a physical fact about the caller's own host — ever crosses a boundary.

Matching has a stdlib-only floor — family-nearness at exact / one-hop / unreachable — so a bare host with no dependencies can verify a crystal before grounding it. **This floor is a DEGRADED mode:** a match that field propagation would reach in two or more hops is reported *unreachable*, not *absent*. Full field propagation runs wherever prism's `[wire]` extra is installed — `prism.propagation` reaches numpy through `prism.law` — and supersedes the floor.

## 32. The crystal — facet, tekton, organon

```
persona (chorus)   IS a crystal ->    one crystal         ( = facets + tektons; persona-local code )
crystal (base obj) instantiated ->    sits ON a screen  ( uses the injected optics: couple / resolution )
screen (optics) the aperture ->    measures the beam   ( absorption / transmission, all formulas )
transducer (artifact)  a stored conversion -> surface<->concept binding ( COUPLING measured )
```

A persona is exactly one crystal — the relation is 1:1, and "persona" is a stage name rather than a type. **The crystal sits on a screen and, using the aperture's universal formulas, absorbs the bands it is tuned to and transmits the rest.**

**"Screen" names one thing only: the beam-side boundary where absorption and transmission are measured.** The world/frame boundary is the prism's world boundary; the fleet's is the fleet boundary; an exchange agreement's is the permit surface.

The three parts:

- **facet = a view.** It **passes signals** — the conduit — **and it can look at the signal the crystal produces**, not just relay it. Its far side can be a human (a web UI), a machine, a store feed, or another crystal; it conducts and displays, and never TRANSFORMS the band.
- **tekton = a tool.** It **handles condensed signals** and passes them to an organon when the work touches the real world. It is the condenser — the waveform-to-typed-artifact crossing — and the handler that works in the condensed, typed domain.
- **organon = a REAL-WORLD capability.** A stored hardware or environmental interface the crystal reaches at the observer boundary, gated by the prism junction, requiring a PHYSICAL capability a prism must advertise. **Many tektons have no organons at all**: a compute-only operation is tekton tool code, not an organon. The true organons touch the world — filesystem and git, network fetch, the message plane, system install.

**The facet IS the instrument's LENS (§8 of `paper-1-the-instrument.md`).** `entry` and `inverse` are declared **per facet**: `entry` is the frame constructor that carries a surface into the screen's shared coordinates, `inverse` carries a concept back out to surface. The other three parts are resolved, not re-authored: the `energy` law and the `zero` come from the stored transducer artifact the facet's content type binds to, and the `null` is the noise model the caller supplies at read time (§10 of `paper-1-the-instrument.md`). Where no transducer is stored for that content type, the host supplies its defaults and RECORDS that it did, so a lens is never silently fabricated.

**A tekton ABSORBS a signal and REMOVES it from propagation.** Condensation is a SINK — the matched band is consumed, so it cannot keep travelling and be re-absorbed downstream. If the tekton only absorbed, everything would be trapped: facet passes (transmission), tekton sinks (absorption), so the contract requires at least one of each. A facet-less crystal is sealed glass; a tekton-less one condenses nothing.

The capacitor flow: **facet in → tekton condenses → threshold → organon discharges → facet out.**

### 32.1 The crystal is a transistor

> **Prism = input, Tekton = gate, Facet = output. So in itself, it is an invokable artifact.**

The same three parts read by signal flow:

- **Prism = INPUT (source).** The environment and capabilities that feed the signal in — where an organon's real-world capability is afforded.
- **Tekton = GATE.** It modulates what flows through: absorbs its matched band and passes the residual.
- **Facet = OUTPUT (drain).** The result emerges and is conducted onward.

Input → gate → output: **a crystal is a self-contained invokable artifact**, and **a capability IS a crystal-transistor.** Invoking it over the signal-native reach is driving the transistor — a NEED placed at the prism input, the tekton gates and condenses it, EVIDENCE emerges at the facet output and propagates back over the ground plane by provenance.

### 32.2 The screen and the universal formulas

A screen is a boundary where a **beam of many signals** meets. Wave energy at it does exactly two things:

| root | what it is | formula |
|---|---|---|
| **absorption** | the matched band **condenses into a tekton** — a typed artifact, the work | `resolution()` — projection onto the resolved basis, above the floor |
| **transmission** | $\text{incident} - \text{absorbed}$ — the residual **passes through unchanged**, propagating on | `X − resolution()` |

$$
\|\text{incident}\|^{2} = \|\text{absorbed}\|^{2} + \|\text{transmitted}\|^{2}
$$

**The noise floor is the absorption threshold**: above it a band condenses, below it transmits.

**The formulas are universal — keyed to information structure, not domain.** Coupling (`K_signal` at the joint frame), condensation (`resolution`), energy exchange (participation $\times \tau$, from nonimaging optics), self-balance (0→0), and losslessness (`certify`) apply to *any* carrier. The base is domain-agnostic and a new domain is a new facet.

**Measured exact:** $6919.53 = 5540.13 + 1379.40$, telescoping across a chain, `conserved: True`. **$k = 0$ means nothing couples here** — the whole signal propagates on, intact. Refusal is a measurement, not a branch.

### 32.3 The multi-crystal screen — coupling selects

One screen carries a **mixed beam** — many bands at once. You do not route or dispatch. You put a **set of crystals** on that one screen, each from the persona that owns it, each tuned to different bands:

- Each crystal **absorbs its matched band** and **transmits the residual**; the rest of the beam flows on to a crystal that *does* match.
- **The measured coupling IS the selection.** A crystal absorbs where coupling is high for its facet's waveform; a mismatch passes through. No one decides where a signal goes — the impedance decides.
- The **cochlea**: one basilar membrane, many hair cells, each a crystal tuned to a band. Different personas are different hair cells on the same membrane.

### 32.4 A transducer is an artifact; the coupling is measured

A **transducer** is a stored surface↔concept conversion over a class of information — `carrier + information`, a coherent band of the beam defined by its waveform (a carrier plus an ordered `(T, F)` frame) together with its `entry`/`inverse` and the frame it is measured on. It lives in the store as an operator artifact and it HAS code. A facet BINDS one; it does not restate one.

**What is never declared — what emerges from measurement at the screen — is whether one transducer *couples* to another**, read as impedance at the joint frame. Measure the coupling, store the transducer.

Both the carrier and the information may draw their equations from OTHER transducers: the universal formulas are shared, so one transducer's carrier or information can borrow another's wherever the info-structure matches.

**A transducer is a fractal 0 → 0 system, or a component of one.** It is self-balancing and conservation-closed: a signal enters at zero and leaves at zero, and the same absorb/transmit structure recurs at every scale. There is no absolute grid underneath, only balance measured at the joint frame. Within a transducer, any other transducer may be included and contribute to the balance — a space transducer can host an emergent transducer whose ordered set is disconnected from the common time frame. Containment is co-membership in a closure, not a shared coordinate.

**Joint closure and the n-body frame.** Some systems close **only jointly**: no member balances, yet the composite does. The participating transducers bundle by symmetry into a product group. The exact-trajectory problem for $n \ge 3$ has no closed form; the frame **changes the question** — from the initial-value problem to a tractable invariant: what is the conserved balance that holds the configuration. Validating it means reproducing a known result — the restricted three-body Lagrange points, or a measured stable configuration — by reading the balance off the screen and showing it *predicts* the stable structure, computed rather than fitted.

## 33. Bundles — three distinct senses

| | **persona bundle** (runtime) | **install bundle** | **operator-source bundle** |
|---|---|---|---|
| what | a crystal **plus a prism** — portable structure grounded on a specific environment | a shippable **signed SET of crystals** — the structure alone | content-addressed operator SOURCE, sha-verified |
| purpose | a LIVE, lit ember: which organons activate HERE | DISTRIBUTION: hand someone the structure to ground | a fully-offline ember can `exec` operators locally |
| lifetime | the running pairing | the artifact you install FROM | the shipped code |
| identity | crystal.sha $\times$ prism.node_id | the hash over the SORTED member crystal shas | content hash |

**An install bundle is a signed set of crystals, and nothing else.** A bundle of one and a bundle of forty address the same way. Three rules:

1. **The needed capabilities are DERIVED, not restated.** Each member crystal already declares each organon's `requires`; the whole set is computed from the members. A second copy can drift from the first.
2. **The install "kind" collapses to one case.** A crystal is always grounded. pip, npm, cmake and compose are **environment provisioning** — how a prism *acquires* a capability — and belong to the prism under host policy. Keeping them out of the crystal installer keeps the RCE surface out of the wrong layer.
3. **Discharge is per-organon.** Once a crystal is grounded, each organon lights iff its capabilities are present; a gap is a typed dormant result naming the gap, never a crash and never a silent no-op.

The shipped installer is the `prism install` command, and it is a client of the store:

```
prism install <bundle> ->
  1. read the local manifest — no advertised capability set, no install
  2. fetch the bundle artifact; verify the bundle sha over its sorted member shas
     refuse any install.kind outside the vocabulary; refuse the policy-gated kinds outright
  3. for each member crystal: fetch -> verify (a tampered crystal does not verify)
       -> sha-pin check (refuse a substituted structure)
       -> junction gate: activates_on(crystal, capabilities)
  4. ground locally: write the install record under KEYS_DIR/installed/
```

Integrity failure refuses — tampering is a security event, non-negotiable — and so does a substituted
sha or an install kind outside the vocabulary. **The junction gate is where the shipped installer and
the design part company.** `activates_on` requires every declared requirement to match *exactly*: a
one-hop family match is measured and reported but does not satisfy it, and a crystal that fails the
gate is refused as a whole rather than grounded with dormant organons. The per-organon path — three
distinguishable refusals, `no_hardware`, `no_grant`, `no_authority`, carried on a typed dormant result
— exists in the crystal base and is not what `prism install` calls. Reconciling the two is outstanding.

**The reach is the complement of the source bundle.** An ember that can reach a peer invokes the operator over the signal-native reach with no local source needed. An ember that cannot — offline, air-gapped — execs the shipped source bundle. Two grounding paths for the same operator, chosen by reachability.

## 34. The ember — the observer, the capacitor

**A gradient discharges only through an appropriate CARRIER. An observer IS a capacitor:** it accumulates charge — the signal held as potential, dark and undescribed — until the voltage breaks down. When it SPARKS, an OFFER has met a NEED: the signal has found a matching capability, condenses into the spark, routes by least action, and is handled by the operator.

Three correspondences:

1. **The capacitor's breakdown voltage is the electroweak scale.** Below it the signal is massless — a message, propagate only, charge accumulating with no spark; at or above it the signal is massive — an event that may transform state. The message→event transition IS the capacitor firing. **The cut is a coupling constant of this system, carried as a seam** (§21.1 of `paper-2-knowledge-without-weights.md`, §113). The measurement that fixes it is a computed null over the corpus's own mass distribution, the same construction the propagation floor uses for reach; until that null is computed the correspondence is not exact.
2. **"Appropriate carrier" is an IMPEDANCE MATCH, already measured.** A partial-match capability is a high-impedance carrier with weak transfer. The measured coupling IS the impedance match; routing by least action is routing by least impedance. No config.
3. **Trapped energy goes to the HUMAN, who lowers impedance permanently.** The three ways a capacitor stays stuck at voltage map onto three human interventions and three system layers:

```
no matching CARRIER   -> create an operator   -> capability layer     -> an operator artifact
no RECOGNITION        -> describe it better   -> learned recognition  -> a crystallized category
no stable VERDICT     -> make a judgement     -> governance           -> a policy artifact
```

The third row's quantity is the acceptance predicate — the verdict on whether an act is good, a governance judgement that shares nothing but the English word with the instrument's `resolution`.

Each intervention is an ARTIFACT that joins the standing field, so the same class of signal discharges autonomously next time.

**The composition:**

```
EMBER (the observer — the capacitor)
 |-- PRISM          — the world boundary (world <-> frame; the ONLY hardware)
 |-- MANTLE-SHARD   — the local slice of the lattice (the working set)
 `-- BUNDLE(s)      — portable function, each containing:
      `-- CRYSTAL   — condenses + routes, HAS FACETS (the views), and REACHES ITS TEKTONS
                      (the craftsmen: operators of one domain)
```

The shard hangs off the EMBER, because it is state and a bundle is structure; the tektons hang off the CRYSTAL, because they are roles inside it and are never addressed beside it.

**Structure ships, state grows.** A crystal is pure structure — inert, content-addressed, shareable: an artifact. An ember is crystals plus ENERGY. **You share the crystal, never the charge.**

### 34.1 The install story

You install a PRISM once per environment, and BUNDLES; energizing them makes an EMBER.

1. **Install the prism** — the world boundary first. `prism init`: keypair, PROBE and NAME the host's capabilities, sign the manifest, register.
2. **Choose bundles.** Structure is portable, so a bundle should be installable everywhere and the catalog should SORT and LABEL by which organons would light here rather than filter — a headless server seeing `ui.render` crystals marked dormant rather than absent, so a user can install ahead of a capability they are about to acquire. `prism install` does not do this yet: it refuses a crystal whose requirements this prism does not advertise (§33).
3. **Install = ground** — fetch, verify every member sha against the bundle's sorted-hash identity, check the junction, ground. Still inert.
4. **Energize → the ember exists** — the loop starts (accumulate → condense → spark), and the crystal's seed lattice grows into a SHARD. The ember is the smallest unit that OBSERVES.
5. **The ember grows** — grounds more bundles later; when the prism's environment changes, `prism init` is re-run and its manifest re-signed, and dormant organons light on the new manifest. Each seed grafts onto the existing shard.

Three environments: a browser tab is an **ephemeral ember** whose session shard persists in the mantle; a laptop or server is a standing observer; a device runs one tiny domain crystal.

### 34.2 Embers are scale-invariant

A tiny ember is the same machinery as a huge one: a mantle, prisms, crystals, the same energy loop, the same gate. The shard is a full mantle instance running the same store code. The in-process store with zero external processes is the enabler: it lets the same machinery run in a browser tab and on the 71.6 GB foundation holder recorded in §98 of `paper-2-knowledge-without-weights.md`. **No capability tiers in the runtime.** The difference between embers is MASS and ENERGY THROUGHPUT.

Where a prism affords no filesystem, mantle binds to that prism's `store.read`/`store.write` handles instead of `fs.*`: a second storage substrate behind the one store interface is expressly permitted, and the artifact model, the grants, the Merkle mesh and the reads are identical on both. Observers carry unequal **authority weight**: a big ember's measurements weigh more in the authority-weighted mean because it resolves more structure verifiably. Weight derives from the operators a node runs and their verification history, never from a node identity: an observer with no history carries none and biases the mean by nothing, and acquires weight exactly as its measurements are verified — the same gate that stops an unverified variation accruing mass (§34.4).

### 34.3 Scaling 1 → infinity — the fire model

You scale by **igniting more matter**, not by growing one flame. The unit stays bounded — a working-set observer; the SYSTEM is unbounded — the mesh.

- **Scale 1 (spark):** a tab or device — full machinery, minimum mass.
- **Scale $10^{2}$–$10^{3}$ (standing flame):** one box, vertical and measured, with cgroup-derived ceilings.
- **Scale ~TB (the flame exceeds the fireplace):** shard-as-hot-cache activates — the local lattice holds the working set and content tiers to the object plane; the ember's REACH detaches from its DISK, because content-addressing makes location a caching decision.
- **Scale out (the fire spreads):** the ember BREEDS — a population of bounded embers selected by ledger: sustain and breed, starve and die. Shards partition by domain; the mesh syncs. **No coordination is required**: proper time per origin, observer-relative heads, lazy-colimit reconciliation — no consensus, no leader, no quorum.
- **Scale infinity (the firestorm):** federation across origins, each its own currency, coupling at the screen, rates measured. Joining needs no negotiation, only a shared ordering axis. Governance is a weighted MEAN that aggregates incrementally, not a vote.

**Why infinity holds:** **the electroweak split is the scaling law** — events are short-range by design and only massless messages propagate far, so write contention does not grow with system size; and demurrage is the garbage collector, so the standing field cannot accumulate without bound.

**The ceiling:** single-writer-per-shard, answered by breeding.

### 34.4 Heredity

When an ember breeds it passes on itself, and it CAN mutate. Content-addressing means a corrupted artifact is a *different* artifact that simply does not resolve; the gate means an unverified variation cannot accrue mass; and demurrage means a variation that does no useful work cools to the floor. Variation is proposal, selection is verification, and inheritance is the shipped crystal.

## 35. Chorus — the personas

Crystal hosts the first-party MCP servers on one mount; chorus supplies their tekton and facet definitions. Aria, lumen, sage and the rest are crystals with names, one crystal each. The seven that exist:

| persona | domain | what it does |
|---|---|---|
| **aria** | output / presentation | response formatting, cards, chat turns; serves chat and visualization views |
| **astra** | ingestion / extraction / streaming | file ingest, document text extraction, live streaming |
| **iris** | networking / routing / comms | message routing, channel adapters, webhooks, relay and tunnel — the persona-facing comms surfaces over prism's plane |
| **lumen** | inference | the reasoning tekton — grounded answers; the arrow algebra; dev operations |
| **ophan** | finance and licensing | ledger, transactions, exchange agreements, entitlement |
| **sage** | research / retrieval / synthesis | search, artifact reads, collection browsing, the taxonomic selector (the answer-selection function of §62 of `paper-2-knowledge-without-weights.md`, not a condenser), evidence synthesis |
| **seraph** | security, governance, trust | access-policy enforcement, tamper-evident audit, identity and token verification, secret decryption and signing, install |

**A crystal declaration is six fields**, four of them required — `prism.crystal_model` validates `name`, `facets`, `tektons` and `created_by`, and treats the other two as optional:

```
name         : the crystal id
facets       : [{name, direction, content_type?}]      >= 1  (no facet = sealed glass)
tektons      : [{name, domain}]                        >= 1  (nothing condenses without one)
organons     : [{name, requires?: [capability, ...]}]  may be empty
lattice_seed : optional seed shard
created_by   : provenance (gates grounding)
```

Each persona carries this as a `CRYSTAL` dict in its own `manifest.py` — aria's declares one tekton, `op.web.bff`, and no organons, on the ground that serving a static facet is a host capability rather than the crystal's.

**Everything else about a persona is derived from those six fields** — MCP boilerplate, tool registration, auth wiring and dispatch.

**How a name becomes code — the binding rule.** A `tektons[].name` and an `organons[].name` resolve to a callable by REGISTRATION, and the source that registers them travels as an artifact. A persona names the bundle groups it owns; `prism.runner.register_fns(group)` resolves that group — from the store's own `bundle-<group>` artifact first, then a host-registered file, then the shipped `bundles/<group>.json` — re-hashes the canonical payload against the group's recorded sha, raises `BundleIntegrityError` on any mismatch, and executes it into a module named for its own hash. The registrar functions it yields write one operator-definition artifact each, under the exact name the declaration gives. There is no name mangling, no path convention, no in-package fallback, and no import from the host into the crystal — the same rule that keeps crystal from importing chorus. A capability that is in no bundle cannot be registered.

- A **tekton handler** has the signature `handler(band) -> artifact`. `band` is the absorbed band: the matched frame that `resolution()` projected out at the screen, carrying its `(T, F)` shape and its provenance. The handler works entirely in the condensed, typed domain and returns one typed artifact, which the crystal writes to the local shard through the injected mantle handle. **The handler never sees or forwards the residual** — transmission is the crystal's job, and a handler that returned one would break the conservation identity.
- An **organon handler** has the same shape and additionally receives the prism-injected capability handles its `requires` names. It runs only when the junction reports its capabilities PRESENT.
- A **facet** contributes the pair `entry`/`inverse` (§32), named in the facet's own declaration.

A crystal is **one declaration plus the bundle groups that carry its code**:

```python
# <persona>/manifest.py
BUNDLE_GROUPS = ("web_bff", "identity")          # the content-addressed operator source
REGISTRARS = [fn for g in BUNDLE_GROUPS for fn in _runner.register_fns(g)]

CRYSTAL = {
    "name": "aria",
    "facets":  [...],                            # {name, direction, content_type}
    "tektons": [{"name": OP_WEB_BFF, "domain": "web"}],
    "organons": [],                              # a pure-conduit crystal may declare none
    "created_by": "connect@agience.ai",
}
```

Much is derived — the MCP surface from the tektons, registration from the declaration, capability gating from the organons' requirements through the prism junction — but not all of it. Each persona still has a `server.py` that builds its MCP server and declares its tools; the host mounts that app at `/<name>` and each persona serves at `/<name>/mcp`. The host itself is persona-agnostic: it holds no persona roster, imports no persona by name, and takes a list of providers, so a persona owns its own registration. Collapsing `server.py` into the declaration is not done.

**Adding a crystal must not require editing any file outside its own directory.** The host holds to this; the persona rename of §113 item 8 does not — `persona` is still the spelling in the loader, the router, the registration table and the environment.

## 36. Composition, MCP, and the operator

Agience is **MCP-native in both directions**: an MCP *server*, exposing tools to clients, and an MCP *client*, proxying personas, external vendor servers, and desktop-relayed local servers behind one uniform invocation shape. Never re-implement a vendor API: external services are registered as server artifacts and proxied through their official MCP servers.

A full dispatch, end to end: client → gateway with a bearer token → fetch the server artifact → check the invoke grant → dispatch on the artifact's kind, where a persona runs locally, an external server is proxied with a delegation token, and a relay forwards over an outbound WebSocket to a desktop host → response. The two refusals sit in that order for a reason. **The store answers 404 rather than 403** for anything the caller cannot see — a denied grant, a deny grant, an absent artifact and an unauthenticated request all return the same body — so probing cannot confirm existence. Only after the fetch has succeeded, which means the caller could already see the artifact, does the dispatcher answer 403 for a missing INVOKE grant, and by then there is nothing left to disclose. INVOKE is declared on the actuation-shaped operations: running a tool, invoking an operator, calling an LLM connection, running a transform, invoking an authorizer, invoking an MCP server.

Every existing MCP server IS an operator implementation in this vocabulary: tools are offers, runtime is capabilities. Point an MCP server at the gateway and it gets discovery, permissions, and metering.

**An operator is a pattern for transformation that requires physical capabilities.**

- **The pattern travels** as artifact content: content-addressed, signed, provenance-carrying, immutable, verified on every read. **The store IS the package manager** — no registry, no version resolver, no deploy pipeline. `spec_hash` is the version pin; content-addressing is the integrity check; the mesh is the transport.
- **Capabilities do not travel.** They are physical facts about a host.
- **Execution = pattern $\times$ capability**, gated by the light-cone.
- **Trust to execute is the mass rule applied to code**: an unsigned or below-rung pattern does not run.

Two properties enforced by the store. **A changed spec is a different operator** — `spec_hash` keys the fitness carry, so redefinition inherits zero evidence and trust cannot be laundered through an edit. **Re-registration never clobbers provenance** — the creation edge is authoritative; a process re-registering a pattern is not its creator.

**Routing.** Offers and needs standing in the lattice ARE the field, and the invocation is the discharge. Among hosts whose prisms offer the required capabilities, the path of least measured impedance wins.

**Impedance is one number, read at a joint frame.** For each candidate host the router builds a joint frame of the NEED and the HOST: the ordered axis is the recent invocation history the two sides share, and the feature rows are the host's measured quantities — queue occupancy (load), reach distance to the need (locality), and energy per unit work (cost). The rows arrive in incompatible units and nothing hand-weights them: the projection puts each side on its own entropy-matched grid, which is what makes heterogeneous rows comparable without a tuned coefficient. The read on that joint frame is the coupling, `K_signal`, and **impedance is its reciprocal**, so the winner is the maximum coupling. Where no candidate's coupling clears the computed null for that frame, the router REFUSES rather than returning a best-of-bad; the refusal is a measurement, the same as $k = 0$ at a screen. **No routing tables, no scheduler policy, no special cases.**

**Services are standing operators.** The inference persona is an operator — a large standing one: pattern = query → grounded answer; required capabilities = store plus compute; invoked continuously.

## 37. The host spectrum

**Every compute surface is a prism host, and one design holds across all of them.**

| host surface | prism attaches as | capabilities it advertises (in ⇄ out) |
|---|---|---|
| browser page / extension | prism-js | `ui.render`, `net.get`, `store.read`, `store.write`, `compute.gpu`; page events in ⇄ DOM out |
| OS service (Windows / launchd / systemd) | prism-js or prism-py daemon | `fs.*`, `net.*`, `compute`, process control, device I/O |
| mobile app | embedded prism runtime | camera, mic, GPS, motion, biometrics in ⇄ screen, haptics, audio out |
| watch | minimal prism profile | heart rate, motion, presence in ⇄ glanceable render, haptics out |
| phone OS (prism below the app layer) | prism as a system service | the whole device's sensor/actuator array, telephony, radios |
| any compute device (embedded, RF, sensor node) | prism-c | whatever the hardware physically has — analog and RF-native included |
| an entirely new OS | prism IS the system layer | everything |

Three invariants this imposes on every design decision:

1. **No privileged platform.** Never assume a browser, a server, or a POSIX box. A pattern's needs are typed capabilities only; if a watch offers them, the watch is a legitimate ground.
2. **All hardware capabilities, in AND out.** A prism manifest is the complete advertisement of what the surface can physically capture and present, never a feature subset chosen by an SDK author. Bidirectionality is mandatory: capture-only and present-only hosts are manifests with one side empty, not different kinds of host.
3. **Each platform's permission model maps onto the capability sandbox.** The browser does this natively — permission boundary and hardware boundary coincide; entitlements, service accounts and embedded memory maps are each that platform's dialect of the same boundary. **The entirely new OS is the limit of the series**: prism is not an app but the system itself, so permission boundary = hardware boundary holds by construction rather than by mapping.

---
---
# PART VI — PROPAGATION

## 38. Signals propagate — never a pipeline

The comms layer is a **signal substrate** whose primitive is one signed signal. What a remote message *is* on arrival: a **signal — an activation**.

| | RPC (call + return) | Signal (activation) |
|---|---|---|
| delivery guarantee | at-least-once, ordered, durable → needs a broker | **best-effort, at-most-once** → gossip suffices |
| overload | the queue fills, back-pressures or drops off the end | **low salience never fires** — backpressure is free |
| a lost message | a corrupted or blocked caller | **a thought that didn't happen** — the observer is uncorrupted |
| a reply | a synchronous return value | **a new message back** — symmetric |
| coupling | caller waits on callee | **decoupled in time** — deposit and move on |

**A message to an operator is a directed signal that lands on its offer and, if salient, fires it.**

Best-effort at-most-once delivery is workable because a propagation terminates in an ARTIFACT rather than in a return value (§43): the requester waits on the presence of one artifact, never on the absence of all of them.

## 39. The four forces — one substrate, four regimes

The dividing line between regimes is **the mass of the carrier**, and the thing that confers mass is **authority**.

| force | carrier | its mass | regime here | range / behaviour |
|---|---|---|---|---|
| **strong** | gluon | confined | **concept MASS** — accumulated verified binding | confinement |
| **electromagnetism** | photon | **massless** | **MESSAGE / signal** — propagates, communicates | **long-range, unimpeded** |
| **weak** | W / Z | **massive** | **EVENT** — decays, transforms, changes identity | **short-range, local** |
| **Higgs** | Higgs | — | **PROVENANCE / AUTHORITY** — confers mass; splits message from event | the switch |

**A MESSAGE is a MASSLESS signal**: it *activates* a receiver and by itself changes no state.

**An EVENT is a MASSIVE signal**: the only regime that **changes identity** — a decay, a consolidation, a revision that takes head.

**They are ONE force, split by the Higgs. Provenance is the switch.**

**An event can radiate messages**: a state transformation on one node emits propagating signals to others.

**The consequence for the design: do not build two formats.** Build ONE signed signal and let its provenance-mass select the regime at the receiver. The scale that mass is read on is derived in §21.1 of `paper-2-knowledge-without-weights.md`; the cut that splits the two regimes is a coupling constant still owed its null (§40.1).

## 40. The signal envelope

The envelope is the provenance quadruple plus content plus a recipient plus a signature:

```
Signal {                                                     # the signed part IS the whole wire object
  id            content-address of the payload (sha256)      # dedup, idempotent apply
  to            an ARTIFACT ID — always, and nothing else
  from          the provenance quadruple {origin, person, host, observer}
  operator      the observation that produced it (the triple's operator term)
  content_ref   cas/<sha256> (encrypted) OR inline for small signals
  seeds?        optional pre-grounded {concept: energy} — already-in-wave-form activation
  ts, nonce     replay window
  sig           Ed25519 over canonical(all-but-sig), by the observer's key
}
```

**Mass is not a field of the envelope.** It is a receiver-side function, computed on arrival from the signed fields and held in derived state:

```
mass(from, operator, content) -> a scalar on the mass scale
  below the cut  -> MESSAGE (propagate, no state change)
  at or above    -> EVENT   (transform, may_revise, decay)
```

The signature covers every field, and the regime is never client-claimed. An advisory mass carried for logging is excluded from `canonical(...)` and never read by the switch.

**One spelling, everywhere.** The canonical JSON keys of the quadruple are exactly `origin`, `person`, `host`, `observer`, undecorated. The signature is taken over canonical JSON, so a one-character key discrepancy between two implementations breaks every signature that passes between them.

**The provenance quadruple, aligned:**

| term | entity | resolves to | validates via |
|---|---|---|---|
| `origin` | the governing **Origin** | an origin artifact | its referenced Authority |
| `person` | the **human** | a Person artifact | the Origin's Authority (JWKS) |
| `host` | the **environment** | a host artifact | attestation |
| `observer` | the **agent** (delegate / node) | a Delegate or NodeIdentity | its delegated authority |

**Artifacts and signals carry the quadruple; tokens carry the credential composition; nothing carries both.** A credential presents `{host, server, user}`, the `observer` term seen from the issuing side, with `user` and `person` naming one entity and `observer` being that composition under delegation (§50).

**`to` is an artifact id on the wire, always.** `agi://` names, host-qualified operator names, and region names resolve to an artifact id **before** send (§41) — the wire never carries a name. A region resolves to the collection artifact that names it, exactly as a group does (§44).

**Origin and Authority are different entities.** **Authority** is a governable issuer artifact — an issuer URL plus JWKS — answering *is this principal who they claim?* **Origin** references an Authority and carries what an Authority does not: **policy, economy parameters, and the peering identity**. One Origin owns one Authority; an Authority is shared only if the origins federate. Validation resolves Origin → Authority → JWKS, **fail-closed, never guessing the authority** — a confused-deputy guard.

**The issuer artifact and the authority WEIGHT are two quantities under one word.** The Authority artifact answers *who* and enters no arithmetic. The **authority weight** — the scalar entering the authority-weighted mean by which existence is measured — is carried by the observer, not by the issuer artifact; its derivation from the observer's rung and verification history, and the weight given an observer with no verification history at all, is published with the agreement law.

**Delivery is the electroweak switch, then activation or transformation.** On arrival, the regime router computes the mass:

- **MESSAGE (massless).** Seeds are derived and activated. The salience gate decides whether anything fires; below threshold the trace is deposited — recorded, not lost — and **no state changes**. If the activation reaches an operator's offer and clears its gate, the operator fires by *radiating a new signal*, not by mutating shared state.
- **EVENT (massive).** The signal is heavy enough to transform, and goes through the revision-inertia test: a revision must carry at least the mass it displaces. On replace it takes head, the displaced version decays to a witnessed past, and it may radiate follow-on messages to interested peers.

**Authorization is the extent of the field, and it is upstream of the switch:** a delegate acting for a person cannot activate outside what that person can reach, so an incoming signal can never touch — as message *or* event — what its sender is not permitted to.

### 40.1 The switch, and what it is measured against

**What the switch compares is derived, and what it compares against is a coupling constant to be measured** (§21.1 of `paper-2-knowledge-without-weights.md`).

**The receiver re-derives the mass.** The sender's claim is an input to that derivation, never its result. Demotion before comparison is the anti-laundering rule: a principal presenting its own claim about its own rung is weighed at what that principal can support, whatever the claim says. **A peer cannot declare itself authoritative to force a transform.** Only an observation an authority actually attests crosses the cut.

Three worked cases:

```
anon (CLIENT) claims human_validated  ->  demoted, below the cut  ->  MESSAGE
alice (SYSTEM) claims hypothesis      ->  below the cut           ->  MESSAGE
alice (SYSTEM) claims observed        ->  clears the cut          ->  EVENT
```

**An event still cannot overturn higher-mass state.** An observed-mass correction of a human-validated artifact is demoted to a non-head proposal by the revision-inertia test. Mass sets the regime; inertia sets whether the event wins; the field sets what it may reach at all — three independent gates, and none of them is the other's fallback.

**The cut is a seam and is carried as one** (§105 of `paper-1-the-instrument.md`, §113). Until the null of §21.1 of `paper-2-knowledge-without-weights.md` is computed over the corpus's own mass distribution, any value in force is a flagged constant, and the correspondence between the capacitor's breakdown voltage and this switch is a correspondence rather than an exact one.

## 41. Addressing an operator

An operator is addressable three ways, most-specific first:

1. **Content hash** — immutable, verifiable, location-independent: *this exact operator, wherever it lives.*
2. **Host-qualified name** — `op.host.<host>.<op>` plus the host's endpoint.
3. **`agi://` name** — `agi://operator.<name>@<origin>`, a mutable, human-readable pointer that resolves *through the Origin* to a content hash. Naming is itself an artifact — `(name, content_hash, seq)` — so it inherits provenance and authorization.

**All three resolve to one artifact id before send.** The wire type of `to` is an artifact id and nothing else (§40), so the resolver's output is what is signed.

**The resolver maps address → (observer, transport-target).** It does not dial an RPC endpoint: it **wraps the invocation as a signed signal and delivers it**, and the receiving observer's activation decides. A "reply" is the operator authoring a new signal back.

## 42. The reach — absorb the band, propagate the residual

> **Keep the signal INTACT for its entire length. Each tekton ABSORBS its matched band and PROPAGATES THE REMAINING.**

A **reach is placing a signal on a shared Screen and reading what crosses.** The wire payload is a **signal** — an ordered $(T, F)$ frame, an energy flow. MCP gives the *envelope* (discovery, invocation, auth); the instrument gives the *payload*. The reach (persona↔persona) and the mesh (peer↔peer) are the **same question at two scales**.

The signal is **not** consumed at one resolver: it travels its full length, absorbing at **every tekton it reaches** and propagating the residual to the next coupling capability, until fully absorbed or at the end. **Coupling selects, not a routing table.**

The mechanism, per hop:

1. A signal — a beam, a $(T,F)$ frame — arrives at a tekton on the plane.
2. The tekton **measures its coupling** and **resolves its band**: projection onto the resolved basis above the floor. That is the **absorbed** part, condensed into a typed artifact — the work, the EVIDENCE.
3. **$\text{Transmission} = \text{incident} - \text{absorbed}$** — the **residual**, placed back on the plane.
4. **Conservation:** $\|\text{incident}\|^{2} = \|\text{absorbed}\|^{2} + \|\text{transmitted}\|^{2}$ — nothing is lost or fabricated at a hop, asserted numerically per hop against the frame's own float noise, never against a typed tolerance.

A full absorb yields an empty residual and is terminal; a partial couple propagates the rest. Event-driven — fire on arrival, no poll — and least-cost routed. Either way the propagation ends by emitting a terminal artifact, which is how a requester learns it ended (§43).

**Certification is 0-1-0, not per-hop.** The three numbers are readings of one propagation:

- **The leading 0** — the reading at entry, before any tekton has coupled: the incident energy of the frame as placed on the plane.
- **The 1** — that incident energy normalised to one, which makes hops comparable across frames of different amplitude.
- **The terminal 0** — the unaccounted energy after the last hop. The residual leaving the final tekton is either absorbed by a tekton or emitted as a terminal residual artifact, so nothing is left unattributed.

**Prefix identity:** for every $k$, the absorbed energy summed over the first $k$ hops, plus the energy transmitted out of hop $k$, equals 1. Per-hop conservation certifies nothing about the chain — a hop that conserves against the wrong incident frame still conserves. Prefix identity is the statement over every prefix, anchored to hop 0's incident energy.

**The tolerance is derived, never typed.** It is a function of the frame: the machine epsilon of the working precision, times the incident norm the identity is compared against, times a dimension factor in the frame's shape. A constant typed into the comparison would be right for one frame and silently wrong for the next.

## 43. The return path is the GROUND PLANE

> *Does the signal carry a back-channel? Is that how lightning propagates? All the back-response needs is a connection to the ground plane.*

The signal does **not** carry a return address, and no back-channel is in the payload. The circuit completes through the shared **GROUND PLANE** — the substrate requester and provider are both connected to. The provider **discharges** the EVIDENCE onto it; the requester, whose light-cone reaches the evidence collection, picks it up.

**Correlation is PROVENANCE, not a carried id.** Every propagation — the NEED, each residual hop, the EVIDENCE — is an artifact with provenance. The EVIDENCE artifact's provenance references the NEED, and the requester finds its answer by following provenance on the ground plane. Any correlation handle is the NEED artifact's own id. **A carried reply-to id would be a second identity for something the lattice already names — the same defect class as a side-car hash.**

**Termination is an ARTIFACT, not a timeout.** The last hop emits a **terminal residual artifact** whose provenance references the NEED, and it emits one whether the residual is empty or not:

- an **empty** terminal residual records that the propagation closed with everything absorbed;
- a **non-empty** one records that it closed carrying a band nobody coupled to — the computed null, which is itself an answer and requires no refusal logic;
- a tekton that **raises** mid-propagation writes the same shape: a terminal residual artifact referencing the NEED, carrying the unabsorbed remainder and the fault, which closes the 0-1-0 certificate at that hop rather than leaving it open.

A NEED with no terminal residual on the ground plane is still in flight; a NEED with one is finished. No timeout is typed.

Sealing and isolation come from the **shared ground collection's group key**, never a per-address key. **The light-cone SELECTS the recipient set; the key scheme DISTRIBUTES to it** — a standard key-sharing scheme hands the collection's key to any observer whose light-cone reaches it. Revoking a grant is one record edit with no re-encryption.

**Provenance of the CONDENSED ARTIFACT attaches at the tekton boundary.** Each absorption emits a **condensation-event artifact** (the type is §111's target) — the band, the resolved basis, the residual norm — and the signature that CLAIMS WHAT WAS FOUND attaches there, to that artifact. A residual re-emission is a new signal, signed by the emitting observer exactly like every other signal on the wire (§40), and it makes no claim about content: a mid-stream signature attests transmission alone.

## 44. The communication plane — one address, one transport, all substrates

The six scopes — world, local group, enterprise, community, family, agency — are **one mechanism**: each is an **observer-group defined by a grant/light-cone.** So the plane needs ONE address primitive and ONE transport model.

**Addressing: send to an ARTIFACT; the receiver absorbs and propagates.**

- The only primitive is **`send(to=<artifact>, signal)`**, and **both addressees are artifacts**: a group is a *collection artifact*, an ember is an *agent artifact*.
- **The receiving artifact ABSORBS accordingly** — the screen model at message scale. A **collection** holds the signal as **one leaf** in the group's Merkle tree; members DISCOVER that leaf by anti-entropy and decrypt it with the group key. An **agent** **consumes** it, the terminal receiver. **The sender does not fan out or branch on type**; it drops one signal on one artifact.
- **Delivery is PULL at the plane and EVENT-DRIVEN above it.** **No member enumeration, no fan-out list, no per-recipient copy, no scope branch** — one leaf is written and every member's ordinary reconciliation finds it, after which the signal is placed and fires by coupling, never by a caller. The no-poll rule of §42 and of §75 of `paper-2-knowledge-without-weights.md` binds the propagation layer, not the anti-entropy round that feeds it.
- **Reception = observer agreement.** A signal exists for you iff your light-cone reaches the target artifact. Publish ONCE; discovery and decryptability deliver it to exactly the members.

**Transport: media, chosen by reachability and cost.** Two faces cover every substrate — a listable medium and a broadcast one that fills a listable one, with the shipped signatures in §45. What exists today, and what is design:

| medium | shape | role | in the tree |
|---|---|---|---|
| in-memory | listable | in-process, the test and loopback path | `prism.carriers.InMemoryCarrier` |
| local directory / NAS | listable | a shared folder on the premises — local group, enterprise on-prem | `prism.carriers.LocalDirCarrier` (aliased `NasCarrier`) and `mantle.mesh.carrier.SpoolPlane`; iris runs ember↔ember messaging over a NAS folder today |
| object store (S3-compatible) | listable | the global, durable, NAT-proof rendezvous and store-and-forward — the universal fallback | `prism.carriers.S3Carrier`; the mesh reconciles over it |
| store carrier | listable | the lattice itself as a medium | `prism.carriers.StoreCarrier` |
| enterprise message bus | listable | an existing bus addressed by key and listed by prefix | design only — no bus adapter exists |
| couriered drive (sneakernet) | listable | a filesystem that travels; latency is the only difference | the directory substrate is live; nothing names or handles the travel case |
| LoRa / Bluetooth broadcast | broadcast | radiates frames; a receiver spools them into its own local plane | design only — no radio adapter exists; `LoopbackCarrier` models the shape in-process |
| satellite or one-way relay | broadcast | downlink only; spooled the same way | design only |

**Everything above the transport codes against the listable face**; a broadcast medium is never consumed directly, but filled into a local plane by the spooling adapter of §45. Adding a substrate is adding one of these two faces; the address, payload, and delivery layers never change.

**Routing = least-cost reachable medium, per recipient, with the durable store as the floor.** A co-present recipient gets the LAN path; a remote or offline one gets store-and-forward.

**Delivery: per-group Merkle tree, anti-entropy, store-and-forward, verifiable.** Messages are leaf-artifacts in the tree, and delivery is anti-entropy reconciliation across the group's reachable media: compare roots, pull the diff. Offline members catch up on reconnect. Order is by hybrid logical clock; apply is idempotent and total — a bad message is quarantined, never halting the stream.

**Guarantees:** integrity = content-addressing; confidentiality = a standard key-sharing scheme **whose recipient set is computed from reachability**, so a non-member hears noise; provenance = agreement plus signature; availability = the durable carrier floor.

## 45. The transport — a plane you can list, or a carrier that fills one

**No broker, no MQTT, and no required peer connection.** The propagation logic never assumes two observers are connected to each other; they share a **medium**. **No medium may expose request semantics** — a carrier that could request from a peer would be a pipeline, forbidden — and a medium that happens to support requests is adapted so that the face it presents does not.

**There are two transport faces, and the mesh's are these:**

```
SpoolPlane   put(key, bytes)   write a segment
             get(key)          read one back
             exists(key)       is it there
             keys()            everything in the plane
             _s3               a boto-shaped paginator, so listing carries real
                               Prefix / Delimiter / StartAfter semantics

Carrier      emit(frames)      radiate — no address, no ACK, no return
             receive()         whatever arrived
```

A `Frame` is `(key, bytes)`: the key is the segment's mesh path, carried with the payload so a receiver can spool it back to the same place, which is what keeps the medium dumb. The carrier face is two verbs and neither may address a peer or await a reply.

1. **A PLANE you can list** — any of the listable media §44 names, down to a directory. Publishers write; peers are DISCOVERED by listing, never by a registry. `SpoolPlane` presents exactly the surface the mesh's S3 client uses, backed by files under a root, so the mesh cannot tell a directory from a bucket.
2. **A CARRIER that fills a plane** — RF, and any broadcast or one-way medium. A radio cannot be listed: you cannot ask the air what was said before you tuned in. It RADIATES frames. `LoopbackCarrier` is the only concrete carrier in the tree; it models a broadcast channel with per-receiver read offsets, so two listeners both hear the same transmission.

**The SPOOLING ADAPTER is what turns a Carrier into a Plane**, and it is a named component rather than an implicit step: `absorb(plane, carrier)` calls `receive()` and `spool_frames` `put`s each frame into the local plane under the key it carried; `broadcast(plane, carrier)` is the emit half. The node then runs its ordinary reconcile over its local plane, and **the mesh never learns a radio was involved.** Nothing in production calls either half yet — the adapter exists and is exercised only by its own suite.

`prism` carries a second, unrelated vocabulary under the same two words: `prism.plane.Plane` is `send` / `reconcile` / `receive` over a cost-ordered list of `prism.carriers`, each of which is `put` / `poll` / `ids` / `get`. That is the signal plane of §44's addressing, not the mesh's segment transport, and the two never meet. The name collision is real and is a thing to settle.

**Two invariants:**

- **The fleet key is the boundary of a fleet.** Sharing a medium is **not** membership: an observer that minted its own content key discovers a peer's segments and cannot decrypt one of them — the silent partition the content layer refuses to create by accident. A node joining a fleet is handed the key; only a from-scratch node mints a new fleet.
- **No echo, no amplification.** Publish is ORIGIN-SCOPED: a node publishes only rows it originated, in its own proper time. So what B learned from A never loops back to A. The loop closes **structurally** — not by a seen-set or a TTL, but because every row knows whose frame it was born in.

## 46. The mesh removed the cursor rather than guarding it

Peers converge by **Merkle anti-entropy over a shared plane, and that is the whole of sync.** Bulk catch-up and steady state are the same operation, and there are no feed cursors on it. Vertices and edges fold into one leaf-digest tree, so one path covers both.

There is, however, a second route between shards in the same package. `mesh/federation.py` pages a peer's artifacts and upserts them locally, resumable through a per-peer cursor artifact holding a page offset and a keyset position. It is gated on an explicit peer list rather than removed, and it carries exactly the failure the design below removes. Two routes, one of which has the cursor the other refuses.

A change feed advanced by a monotone cursor carries a silent failure: *a cursor may only advance over segments that actually applied, and a skipped segment is behind the cursor forever — never retried, and nothing reports it.* The design removes the state that guard would protect:

> *There is no cursor. A failed transfer just leaves a hash mismatch that the next round retries. **Self-healing is the default state.***

Applying a leaf is idempotent and order-independent, so a partial or repeated transfer is always safe; there is nothing to roll back.

**Node identity should be fail-closed for the same reason, and is not.** A node that starts without an explicit id falls back to its hostname and publishes under it, which forks the mesh: the mesh accumulates phantom publishers that are really an existing node wearing a different name, and every peer burns cycles re-applying byte-duplicate segments. Nothing refuses to start. The mitigation in force is an ignore list — `EMBER_MESH_IGNORE` names publisher ids to skip — chosen over deletion because the mis-identified stream may be the only copy of what it holds, and because ignoring is reversible and needs no coordination. Refusing at boot is the fix, and it is not written.

**The tree structure is derived, never chosen.** The natural index of a content-addressed space is a hash-prefix Merkle tree whose depth is derived from where the divergence is — descend until the differing subtree is small, granularity found rather than fixed. A fixed leaf count is a magic modulus that degrades as the corpus grows and forces every node to share one constant.

## 47. Peers pool evidence without exchanging observations

At forgetting $\lambda = 1$ the accumulators are additive over any partition, so:

```python
def merge(self, other):   # SpectralAccumulator
    self._cov = other._cov.copy() if self._cov is None else self._cov + other._cov
    self.T += int(other.T)
```

Only the pooled covariance travels; no raw frame is exchanged. Because the certified band goes as $\sqrt{F/T} + F/T$ and therefore tightens with pooled $T$, reaching $1/\sqrt{T}$ only once the pooled $T \gg F$ (§11 of `paper-1-the-instrument.md`), **the ensemble certifies what no single node could.** The operator merge does the same and raises rather than approximating when either side was fitted with forgetting, since an exact splice requires $\lambda = 1$ on both.

The merge is exact: the merged operator reproduces the operator of the concatenated stream, bit-for-bit. The pooled covariance and the fitted operator are artifacts, reconciled by Merkle anti-entropy like any other leaf.

**A signed coupling requires a shared basis — and what is shared is the MEASUREMENT basis, never the meaning.** A delay-lifted coordinate is minted per fit at width $k \cdot d$, so an operator fitted there can neither couple across turns nor splice across peers. The basis a distributed operator lives in is therefore fixed independently of any single fit: the signed-hash measurement grid and the corpus basis derived from it are pinned and versioned as content-addressed artifacts through the store's revision path, and every peer that splices reads the same pinned generation. The MEANING placed on that grid is never absolute: there is no universal grid of concepts, and the correspondence between two observers' structure is measured pairwise at the coupling rather than looked up.

## 48. Peering — two planes, because the two ends peer differently

**Origins peer by AGREEMENT. Observers peer by GOSSIP.** These are separate acts and must not share a mechanism.

**Origin ↔ Origin — the governance plane.** Two origins peer when they sign a mutual **exchange agreement** — the trust boundary, and where the economy attaches. It is an artifact: `{origin_a, origin_b, authorities_trusted, scopes, settlement_terms, consent, sig_a, sig_b}`, expressing:

- **mutual issuer trust** — origin A registers B's Authority as a trusted issuer, enabling cross-origin identities that cannot collide;
- **blanket grants** — "every present and future principal of origin B may read this collection," one grant, no enumeration;
- **settlement** — what a shared, verified, or refuted claim is worth across the boundary.

Isolation is the default: each origin's graph is private, and an exchange agreement is the signed exception. The boundary (`permits`) is fail-closed; fees are flat per side.

**Observer ↔ Observer — the mesh plane.** Nodes of the same or a federated origin peer by the signed gossip mesh: a region-to-peer map merged transitively with no central registry; signed shards pulled and every item verified. A lying directory entry can misdirect but never forge content.

## 49. The transports still to be brought up

The address, payload, and delivery layers are fixed. **Two items complete the family — one carrier and one absorption change.** Neither is built; both are named here because the interfaces they must present already exist. The carrier changes nothing above the transport; the absorption change touches the absorption path, not the carrier interface.

**Direct-peer streaming — WebRTC and QUIC (the carrier).** This would add the real-time regime: two co-present observers holding an open path for the duration of a coupling. It presents the **Carrier** face and only that face — `emit(frames)` and `receive()`, with no request path — so §45's invariant holds over it: QUIC's request/response capability is not exposed, and a receiver spools what arrives into its own local plane exactly as it does for a radio. The plane treats it as one more reachable medium priced by cost. Nothing of it is written: there is no WebRTC or QUIC library anywhere in the tree and none in any manifest. What stands in its place is `prism.streams.LoopbackFabric`, a test fabric, and the degrade-to-carrier path beneath it.

**Incremental streaming absorption (the absorption change).** Frame-on-the-wire encoding is closed — a $(T,F)$ frame encodes and decodes byte-exact, and the conformance vectors ship inside the wheel. The remaining build is absorption *during* the stream, a change to tekton behaviour rather than to transport: a tekton would sink its band as the stream arrives and emit the residual on a **measured trigger** — the point at which the resolved band stops changing by more than the frame's own noise — rather than on a buffer size. Today `prism.streams` buffers and sorts frames and absorbs nothing mid-stream. The trigger is to be derived from the incoming signal, so that conservation still holds per hop over the partial frames and the 0-1-0 certificate still closes at the end of the stream.

## 50. The sovereign network

**This section is a design, and the topology is the only part of it that runs.** The credential chain of §52 implements Authority → Host → Server → Agent, and the desktop relay implements the reach to a machine behind a NAT. Everything below that — the `agi://` resolver, DNS as the trust root, cooperative name service, the continuity state machine, the radio tiers of the transport cascade — is specified and unwritten, and the section says so at each point rather than in a banner.

**The desktop relay, which does run.** A desktop host opens a single outbound WebSocket to the gateway at `/relay/v1/connect`, authenticating with a bearer token and a device id, and holds it open with a heartbeat. The gateway keeps a session per device and forwards an MCP request into it as a JSON envelope with a uuid4 correlation id and a base64 body; the response comes back on the same socket. There are no inbound ports on the desktop, which is the whole point. The inward half of ember's own relay — serving rather than fetching — raises `NotImplementedError`; what is complete there is region fetching over plain HTTPS through an injected callable.

**Topology.** The canonical hierarchy is **Authority → Host → Server → Agent**.

- **Authority:** the root identity, governance, and trust boundary. A domain, not a runtime node. It governs its namespace, its trust and validation policy, its certification surfaces, and the set of hosts it recognizes. It is the token issuer, and it anchors the chain rather than sitting in it as an identity component.
- **Host:** the computation and execution boundary under an authority. It belongs to exactly one authority at a time; an authority may own many.
- **Server:** the callable surface exposed from a host. Child of exactly one host.
- **Agent:** the composition `{host, server, user}` — the same entity the provenance quadruple names `observer`, with the quadruple's `person` and the composition's `user` being one entity (§40). **An agent is an artifact, never a principal**; every agent invocation is a delegation where the subject is the person and the actor is the agent. A client application is the presenting app a user acts through; it is not an identity component and appears nowhere in the chain.

**`agi://` — identity and addressing.** The URI scheme is designed to resolve through DNS the domain owner already controls: a DNS-anchored decentralized identity method needing no central registry, no certificate authority, and no vendor SDK. **No resolver is built.** The only code that touches the scheme matches the `agi://` prefix and returns the address unresolved, with the reason attached — the name is not split into its parts and no DNS lookup is attempted anywhere in the tree. Resolution runs through the Origin today, not through DNS.

- **Form:** `agi://{type}.{entity}@{authority}/{path}?{query}`, where types include `agent`, `person`, `sensor`, `host`, `tool`, `topic`. The type prefix is mandatory on the wire; a UI may accept bare names and normalize.
- **Resolution, four steps:** look up a DNS TXT record at `_agi.{authority}`, rewrite via its template to an HTTPS endpoint, pass through the path and query, receive a JWS-signed entity description.
- **Keys** are to be published in DNS following the DKIM pattern; selector-based rotation enables zero-downtime rollover, and signed artifacts carry a key id. RS256 and ES256 are both mandatory. DNS is the authority; an HTTPS mirror is optional.
- **Proof modes:** signed-response (mandatory, stateless) and challenge-response with a nonce (recommended). Delegation runs through authority-signed tokens.

**The trust root is to be DNS, not a chain.** Today it is the authority manifest's inline JWKS, seeded at init and verified locally by `prism.trust` (§52).

**Cooperative DNS and continuity — a design, with no code behind it.** Non-custodial authoritative name service for existing domains — not an alt-root running its own TLDs, and not a managed provider taking custody. Only the zone owner signs; cooperative nodes serve owner-signed copies they cannot modify; the delegation at the registrar stays revocable. Replication would use standard AXFR/IXFR/NOTIFY plus a CRDT-style replication-history log for deterministic merge after a partition, with a configurable replication factor and automatic re-replication below threshold.

For continuity, a resolver would run a three-state machine:

- `PUBLIC_FOLLOWING` — normal operation; follow the public root.
- `PROTECTED_FREEZE` — primary failover; freeze to the last-known-good public-root snapshot and keep serving.
- `CONTINUITY_ROOT` — last-resort divergence, authorized only by a signed continuity manifest with quorum signatures and a validity window.

Anti-fragmentation rules: no new TLDs in continuity mode; always prefer a frozen public root over divergence; any continuity root begins from the last-known-good public root; standard DNS wire semantics are preserved; and chain availability is never required for resolution. The residual attack surface is the registrar, mitigated by favorable-jurisdiction registrars, registrar locks, backup domains, and a TLD application.

**The multi-transport mesh.** The delivery layer is transport-agnostic, and that much holds today: every message carries a signed envelope the receiver validates regardless of how it arrived. The four-level priority cascade below is designed and unwritten — the only selection in code is a cost-ordered list of the media §44 marks live, all of them IP or filesystem, plus a two-level degrade from stream to carrier. Nothing selects a radio, because no radio adapter exists.

1. **IP (primary):** TCP/UDP, HTTPS, DoH/DoT, AXFR/IXFR, WebSocket. Only IP carries the full DNS wire protocol.
2. **LoRa (fallback):** unlicensed ISM band, 250 bps to 50 kbps, 2 to 60 km range. It carries only tiny integrity and rendezvous payloads — root, zone, and manifest hash announcements, and node discovery.
3. **BLE Mesh (last hop):** 10 to 100 m, dense indoor clusters, transport of last resort.
4. **Offline:** cached zone data, local discovery, queued and replayed writes. Delay-tolerant networking — store-carry-forward with custody transfer — ferries data across connectivity gaps.

**The integrity model separates data integrity from transport integrity:** content-addressed, owner-signed data verifies even if every packet is intercepted, modified, or replayed. The design cannot survive a global electromagnetic blackout, a universal legal ban on cryptography, or compromise of the owner's own signing key.

### 50.1 Unwinding the channel

Recovering a signal requires undoing what the medium did to it, and this generalises: **each transform a medium applies carries one free parameter, and each parameter is fixed by MAXIMISING AN APERTURE READ** rather than by fitting a template. A template asserts what the signal is before the data is consulted; an aperture read asks only which parameter value makes the measured field most resolvable.

| medium transform | model | free parameter | recovered by maximising |
|---|---|---|---|
| **dispersion** | $\tau(f) = K \cdot \mathrm{DM} \cdot f^{-2}$ | $\mathrm{DM}$ | leading-mode contrast $\lambda_1 / \lambda_+$ of the dedispersed frame |
| **Faraday rotation** | $\Delta\psi = \mathrm{RM} \cdot \lambda^{2}$ | $\mathrm{RM}$ | derotated polarised amplitude |
| **scattering** | convolution with $e^{-t/\tau}$ | $\tau_{\text{scatter}}$ | per-band tail decay rate |
| **instrument artifacts and interference** | persistent narrowband structure | — | the geometric filter, dropping components with $\varphi_F < \varphi_T$ |

The interference and artifact removal needs no parameter at all: narrowband structure that persists across the ordered axis fills fewer feature modes than ordered modes ($\varphi_F < \varphi_T$), and the geometric filter drops it on that criterion alone.

**The library supplies the objectives; the known-physics inverse transforms are applied out of band.**

The result is a **general propagation-channel inverter**: any medium whose distortions are parameterised is unwound the same way — sweep the parameter, read the aperture, take the maximum.

### 50.2 The read-side denoiser

The read path ships a denoiser that is one operation: **project the field onto its own resolved screen modes, with optimal singular-value shrinkage against the derived floor.** The modes come from the measured data; the floor comes from the observation geometry; the shrinkage is the optimal weighting of each retained mode given that floor.

The output is a **linear projection of the measured data** — every value in it is a weighted combination of values that were measured, so it synthesises no structure. A weak signal that survives is a signal that was there.

### 50.3 Signal-class embodiments

The read path adapts to any signal class that presents an ordered axis and a feature axis. Three are build targets:

- **Magnetic-resonance imaging** — the ordered axis is acquisition, the feature axis is k-space; the certified mode count sets what is resolvable from a shortened acquisition.
- **Brain-computer interfaces** — the ordered axis is time, the feature axis is the electrode array; the derived floor sets what is signal against the biological and instrumental background.
- **Fusion-plasma diagnostics** — the ordered axis is the discharge, the feature axis is the diagnostic channel set; the resolved operator gives the evolution ahead of the event.

**The domain-specific adaptations of the read path to these signal classes are the subject of pending patent applications and are licensed separately**.

---
---
# PART VII — SECURITY AND SOVEREIGNTY

## 51. Grants and keys — two distinct instruments

A grant is the authorization a key-holder needs. The two instruments are built separately.

- **Grants = AUTHORIZATION.** Who can reach an object is computed as **reachability across edges**, from a principal's grants outward, deny-before-allow, no owner fast-path. A grant is an edge; revoking is one edit.
- **Keys = a STANDARD key-sharing algorithm**, managed on-platform and associated with identities. Content is encrypted at rest; authorized parties read via the standard scheme. **Key material is not derived from grant traversal** — a collection's key comes from its immutable origin root, so a change of authority never re-keys what is already written. **Key issuance is gated by that traversal**: the oracle authorises before it consults the master-key cache, reads the store, or derives anything, and it fails closed with no verifier. A cache of decrypted data is treated as key-equivalent and gated the same way.

**The canonical formulation: the light-cone SELECTS the recipient set; the key scheme DISTRIBUTES to it.** Reachability computes *who is qualified to hold* a content key; a standard key-sharing algorithm then issues it. Revocation is therefore one record edit with no re-encryption (§56); a scheme that derived keys from traversal would have to re-key the whole affected set on every revocation.

**CRUDEASIO** is the permission set, in the order the ledger's flag tuple has: **C**reate, **R**ead, **U**pdate, **D**elete, **E**vict, **I**nvoke, **A**dd, **S**hare, **O** for **Admin** — the meta-permission to manage grants on the resource, which is not ownership. E is remove-from-container (the edge, not the artifact); A is fire-and-forget intake into a container; S is the ability to create invites. The order is part of the contract, because the flag tuple and the routers both iterate it.

**The mask is ten bits, not nine.** Nine action bits, and above them one ALLOW bit carrying the effect. Encoding the effect as a bit is what makes the meet a plain integer AND: allow ∧ allow is the only way to stay an allow, so **deny is absorbing by construction** rather than by a branch someone has to remember to write. Every distinct mask is minted once and shared, so composing two of them is a dictionary lookup.

A `requires_identity` flag exists on a grant, with per-verb overrides for read, write and invoke. Only the read one is read anywhere: collection listing for a grant-key presenter consults it. The access check ignores all four, so the flag does not today reject an anonymous presenter on a write or an invoke.

**All access, including the creator's, is by explicit grant issued at creation time.** `created_by` is provenance only; it grants nothing, and the access check never reads it. There is no flag scheme: no visibility flag, no no-share, no no-promote, no owner field, no per-principal private marker. Ownership is grant-derived. Public means an un-keyed top-level artifact or a Read grant to the public principal; sharing is two grants. **A request for an artifact you cannot see returns 404 rather than 403** — and so does an absent one, a denied one, and an unauthenticated request, all with the same body. The one non-404 outcome inside that check is a 500 when the origin chain fails to terminate, deliberately: *you are not granted* and *this lattice did not terminate* are different answers. The consequence of the rule is recorded at the place that creates the first grant: an artifact created without it exists and is unreachable through every API surface.

**Deny before allow, in two passes rather than one fold.** The check walks the grants twice: once for a deny carrying the requested flag, which refuses immediately, and only then once for an allow. Two passes rather than one accumulation is what stops row order deciding the verdict. The check runs at every level of the walk, so a nearer deny beats a farther allow, and the publicity check runs last so that being public cannot overturn a deny. `grant_is_allow` matches positively, so an unrecognised effect confers nothing.

**The light cone.** Authorization is graph reachability, not list membership. Two traversals share one attenuation operator. The **upward** walk — the one the access check runs — is a linear single-parent chain from the artifact up its origin edges, bounded by a walk ceiling and *raising* rather than truncating when it does not terminate, pruning the moment an edge's `propagate` mask stops carrying the requested action. The **downward** twin is the breadth-first one: it enumerates the descendants reachable through containment edges, pruning a whole subtree behind an edge whose mask drops the action. Access is a two-path check: a direct grant on the artifact, or reachability up the origin edges intersecting propagate masks.

Grants are documents, not edges — rows sharing one content type, carrying an effect and the nine flags, while edges are what carry the `propagate` masks. Revoking one is therefore a state change on a record rather than an edge deletion, and it re-encrypts nothing. It is not instantaneous: the authorization verdict is memoised behind a short TTL (thirty seconds by default) at the key oracle and cached again in the gateway, and that TTL is the bound on post-revocation validity.

This single traversal does double duty: **structural grant inheritance** (who can read and write what) and **dispatch gating** (can this principal invoke this server). CRUDEASIO's **I** is INVOKE, with a closed default, so gating discharge needs no new verb.

**An invocation that goes through the gateway is INVOKE-gated by this traversal, with a closed default.** The dispatcher reads the operation's `requires_grant` and asks the store's audited access check before it dispatches, so the decision is never taken client-side; nine operations declare INVOKE today, and they are the actuation-shaped ones. The gating is not yet universal: the store's own `/mcp` surface takes an authenticated principal and asks the access check nothing, so a call that reaches it does not pass this gate. Where a role additionally requires a prism capability, that capability is a *second* gate on top of the light-cone check, never a substitute for it: a component that needs no prism capability is still not reachable without an INVOKE grant.

## 52. Identity and credentials

Every request carries the chain **Authority → Host → Server → User**. Identity composes `{host, server, user}`, and that composition, acting under delegation, is the **agent**. Authority is the trust root and Client is the presenting app; neither is an identity component, and neither is a link in the chain.

The mapping of that composition onto the provenance quadruple, and the canonical JSON key for each field, are given in §40. The envelope is signed over canonical JSON and a one-character discrepancy invalidates every signature: write the artifact stamp with the provenance spelling and the token claim with the identity spelling.

| Token | Issued by | TTL | Notes |
|---|---|---|---|
| User JWT (RS256) | Origin's own `/auth/token` | **4 h, derived** | half a working day: revoke at the start of a shift and the credential is dead by the middle of it, and four times the unattended-renewal floor. Subject = the platform Person id, not the upstream IdP's subject |
| Refresh token | with the user JWT | **30 d** | exchange-only; stateless, not rotated, and not revocable — a stolen one is live for its full window |
| Server JWT | client-credentials grant | **1 h** | principal type "server", no refresh; the client secret behind it is bcrypt-hashed |
| Delegation JWT (RFC 8693) | when proxying user → server | **300 s by default** | subject = user, audience = server, actor = server. The TTL is a request field with no server-side ceiling |
| Grant key (direct) | mantle's key endpoint | until revoked | an `agk_` opaque bearer token, sha256-hashed at rest, shown exactly once and not recoverable afterwards |

The three grant types Origin serves are `authorization_code`, `refresh_token` and `client_credentials`, and it publishes exactly that set. There is no token-exchange grant and no api-key grant: the API-key system was decommissioned, its tables dropped by migration, and a JWT minted against it is now rejected on its own terms by both Origin and the store rather than being read as an ordinary user token. Scopes and resource filters ride on the **server** JWT, sourced from a server-credentials record.

**Delegation** carries the user→server proxy: servers verify that the audience equals their own client id — which prevents a token for one server being forwarded to another — validate RS256 against the authority manifest's inline JWKS, and hold the token in a per-request context variable rather than a tool argument. A platform-scoped delegation is further narrowed by an explicit scope table: a scope may reach only the method and path it names, and a scope absent from that table reaches nothing.

**Service-to-service auth is decentralized.** First-party services authenticate to each other with **mutual per-service JWTs**: each signs with its own private keypair, and every inbound token is verified against the **inline JWKS in the on-disk authority manifest**, seeded at init. **Verification is `prism.trust`, and it is local:** every component verifies a presented identity against the manifest's JWKS where the token arrives. Origin issues user tokens and nothing depends on it: an air-gapped deployment and a browser-tab observer both authenticate normally with origin unreachable.

**Transport-bound auth** sets four absolute requirements: every token carries an audience; every receiver validates it; platform client identity is database-derived; and non-platform OAuth clients receive scoped delegated tokens — the intersection of the user's grants and the client's allowed scopes — rather than a full user token. Defense-in-depth layers: IP-origin binding with CIDR allowlists, DPoP proof-of-possession, and mTLS certificate-bound tokens for internal calls. **Secrets are delivered to servers wrapped to the target server's registered public key, so plaintext secrets never transit the network.**

**Third-party server hardening:** credentials live as encrypted secret references rather than in plaintext transport environments; SSRF protection with private-range, loopback, and link-local blocks plus DNS-rebinding checks; stdio transport rejected in cloud, desktop-relay only, with a command allowlist; an HTTP header allowlist; and per-user session isolation. Inbound challenge tokens give stateless HMAC anti-bot protection for public endpoints against clients that cannot run JavaScript.

**Multi-issuer and multi-tenant.** Issuers are governable artifacts; tenant-scoped identities are derived as `uuid5(issuer, subject)`, so cross-origin identities cannot collide.

## 53. Secrets are secrets

A secret — a credential, a key, private content — is visible **only to its creator and their delegates, throughout**: at rest, in transit, in compute, in logs, in backups. No fallback, no server-internal exception, no recovery path that widens the visible set, no "the platform can see it to help."

**Delegation is the only way the visible set grows**, and it is explicit and revocable — a grant record. Confidentiality is a PROPERTY of the system, over a stated scope: the store holds content and search tokens it cannot read, keys reach only delegates, and a non-delegated party — **including the platform itself** — cannot derive the secret. The scope is exact and is stated in §24: artifact metadata and `context` are plaintext, protected by the grant check rather than by a key.

The property as stated holds under the multi-custodian end-state; the default single-node deployment falls short of it, and §56 carries the gap with the mechanism that closes it.

**Consent is the one multi-party path.** Private information leaves its scope only through an explicit share, which requires the owner (grant-derived, never an owner field), an explicit confirmation, and a stake — and compression may never carry a non-public artifact across that line.

## 54. The substrate is one field — security by construction

Build one field rather than a content plane, an index plane, a replication plane, and a security layer to keep in sync. Everything is that field relaxing toward its ground state under one law:

> **Ordered energy flows down gradients toward observer agreement. The aperture measures the flow. Balance is the attractor.**

Knowledge converging between embers, bodies flowing to where they are reached-for, replicas reconciling, access resolving — the *same* dynamics.

**A reference is a need; a body is an offer.** A `content_ref` a node cannot resolve yet is **not a dangling pointer to repair** — it is a *need with no local offer*, the same shape as a query the store cannot yet answer. The body flows from wherever it exists to wherever it is reached-for, on demand, down the gradient. Nobody pushes content to balance the index. A node holds what it actually touches and no more; unresolved references realize the instant they are observed. **"Unbalanced" is un-observed.**

**Balance is read, never checked.** Distance-from-balance is a property of the field the aperture already measures, never a quantity to bookkeep. The gap between what is referenced and what is present is disagreement, which is entropy, which is what the beam reads. There is no coverage probe and no balance dashboard tile: **read the published measure, never a stat invented beside it.**

**Four security properties, four faces of the one field:**

- **Integrity = content-addressing.** The hash verifies itself; tampered bytes are a *different* hash and simply do not resolve. Integrity is structural, with no check to enforce. The Merkle tree extends it to the whole graph: a forged leaf changes the root. Signed shard manifests let *any* replica serve a region blind and *any* reader verify it independent of who served it.
- **Confidentiality = encryption at rest plus a zero-knowledge rendezvous.** Every object written through the content envelope is ciphertext; object storage, a spool, a broadcast medium all see opaque bytes, so the transport can be anything and the field stays private. A blob predating the envelope reads back as plaintext, so a caller that requires ciphertext asks for it (§55).
- **Authorization = reachability.** Key-holders are selected by reachability (§51). A node not holding the fleet key hears noise.
- **Provenance = observer agreement.** Existence is authority-weighted agreement, and content-addressing computes that agreement for free. Trust is a rung, graded and derived server-side, never a caller's claim — a raw assertion can only lower its own rung.

Tampering, eavesdropping, forgery, and intrusion are each *inexpressible against content bytes and the store's blind-token index* in a content-addressed, encrypted, grant-keyed, agreement-grounded space; metadata, keys resident in process memory, token validity windows, and an ember's own plaintext corpus index remain expressible, and §56 enumerates them.

## 55. Sovereignty, residency, and sealed delivery

The platform runs anywhere the operator puts it: on-prem bare metal, sovereign cloud, edge, or fully air-gapped. It is **dependency-severable** — no call-home and no external licensing server in the data path, so an air-gapped instance keeps working when cut off from the vendor entirely. For data at rest, artifacts are customer-key encrypted: **content and the store's lexical query index are ciphertext**, so an engineer with container access, a database administrator with direct access, or a full export sees ciphertext for those; metadata and `context` remain plaintext (§24), carried as a residual risk in §56.

**Three key layers, and one caveat.** The local content-addressed cache seals each object with AES-256-GCM under one per-node key, with the object's own `cas/` reference as associated data — key-independent by construction, because the address names the plaintext and so survives a re-key. The per-principal envelope seals under a key derived from that principal's master key, with the principal and the collection scope bound in as associated data. The durable mirror to object storage uses a rotating shared fleet key. Above all three sits a **pluggable key-encryption key**, selected by `MANTLE_KEK_PROVIDER`: `local` wraps with the platform `encryption.key` on disk, which is the self-host default; `kms` and `vault` put the KEK somewhere non-exportable, so the wrapped key travels and the KEK never leaves the boundary. **The caveat:** a stored blob that does not carry the envelope's magic is treated as legacy plaintext and returned as-is, so a caller that requires ciphertext must say so. Encryption at rest is universal for anything written through the envelope, not for everything an existing corpus already holds.

**Residency and sealed delivery follow from the secrets principle plus the capability model plus content addressing.** Build both as consequences of mechanisms already present:

- *Residency* is the capability model read as policy. A workspace's declared sensitivity travels with it, and a prism that cannot afford an external capability does not light the organon that needs it. **The system refuses rather than falling back** — the same typed-dormancy behaviour every capability gap produces.
- *Sealed delivery* is the secrets principle read at the ISV boundary. Proprietary logic ships as an encrypted blob with an invoke-only grant; at invocation an ephemeral key exchange re-encrypts to a session key; the customer decrypts in RAM, runs once, and discards. Revocation means the key-exchange stops answering and the blob becomes permanently inert — that single mechanism gives a kill-switch, per-seat metering, and expiry. An air-gap mode issues pre-counted, single-use decryption envelopes out of band.

## 56. Threat model

**Defends against:**

- Search-index compromise, subpoena, or insider read of the store's search operator → ciphertext only. An ember's corpus index is a separate population and is plaintext (§24).
- Grant revocation → loss of access with no re-encryption, bounded by the verifier's memo TTL rather than instant. The recipient set is recomputed from reachability and the key is simply no longer issued (§51).
- Stolen-token replay → short TTLs, audience binding, proof-of-possession, IP-origin binding.
- Token forwarding between servers → the audience check.
- SSRF, stdio command injection, header injection from a malicious server artifact → allowlists and IP validation.
- Bots without JavaScript on public endpoints → HMAC challenge tokens.
- Cloud data egress or geopolitical revocation → air-gap and dependency-severable deployment.
- Content tampering in transit or at rest → content-addressing plus the Merkle root.
- Provenance forgery → server-side rung derivation; a client's claim can only lower its own rung.
- Sybil reputation inflation → reputation is frame-relative; a mutual-vouching ring inflates only inside its own frame and carries zero weight in a skeptical observer's frame.

**Residual risk.** These are the risks the operator carries; each names the mechanism that closes it.

- **Operator read of metadata and context**, which is plaintext and grant-controlled. Closed by field-level encryption of context under the content key, which costs the operator server-side filtering on those fields.
- **Single-node trust in the operator:** a principal's master key is unwrapped into process memory, and there is no access-pattern padding. Half of what closes this is already shipped — `MANTLE_KEK_PROVIDER=kms` or `vault` keeps the key-encryption key non-exportable, so the wrapped keys can travel while the KEK never leaves its boundary (§55). What remains is threshold custody of the master key itself, and padding reads to a fixed access profile.
- **Full compromise of a principal's master key → all of that principal's content.** The key hierarchy is KEK → per-principal master key → HKDF-derived subkeys per purpose, including per-`(collection, cluster)` cell keys that are never persisted. It is not per-artifact, and there is no threshold split on the master key: the store names a Shamir backend as future work and implements none. A Shamir split-and-combine oracle does exist, in Origin, over its own secret store — it is not wired to the store's master keys.
- **Bearer-token replay inside a token's validity window**, and a stolen refresh token for its full 30 days, since refresh tokens are stateless and not revocable (§52). Closed by requiring proof-of-possession — DPoP or mTLS binding — on every token rather than on internal calls only, and by making refresh revocable.
- **Same-origin JavaScript attackers on public endpoints.** Closed by serving privileged surfaces from isolated origins under strict CSP, so a compromised public document holds no credential worth replaying.
- **Factual or ontological truth.** No mechanism closes this: the platform attests process — who reviewed what, under which policy, when.

---
---
# PART XIV — THE STATE OF RECORD

*Acceptance (§112) and the language baseline (§114) are in `paper-2-knowledge-without-weights.md`, which is what they test.*

## 109. What runs

| | |
|---|---|
| **The universal artifact model** | One lattice — SQLite plus encrypted content-addressed filesystem, zero external database processes. One generic operation route, `op/{op_name}`, served by the gateway over the type's own `operations` block (§24). Mantle holds the storage, the encryption, the platform encryption key and the pluggable KEK, the anchors, and the event bus. |
| **Access = CRUDEASIO grants, end to end** | The light-cone is the one decision. Ownership is grant-derived; `created_by` grants nothing. Reachability computes the recipient set and a standard key-sharing scheme distributes the group key to that set; keys are never derived from traversal, which is what makes revocation one record edit with no re-encryption. |
| **Identity, credentials, transport-bound auth** | Mandatory audience claim; per-key transport binding; mutual per-service tokens verified against the inline manifest JWKS. Verification runs through `prism.trust`; Origin is reached over HTTP for issuance and is imported by nothing. |
| **Encrypted lexical search** | Blind tokens under an owner-scoped key; posting lists and manifests encrypted and slot-bound; the storage layer holds ciphertext and never words. Recall answers membership and query coverage; ranking is a separate module that orders by measured reach where the seams are bound and by coverage where they are not (§24). |
| **MCP both directions** | Server and client; personas, external servers, and desktop relay behind one invocation shape. |
| **The instrument** | One aperture; the decay law single-sourced; certified intervals; per-screen decay measured live on real conversations. |
| **The adaptive cut** | `K_signal` plus relative gap, model-free, no embedding. |
| **Grounded answering** | Every answer carries at least one citation resolving to a real artifact at record granularity; relations come from the edge graph; nothing is asserted that was not measured; the null is empty; no trained model in the shipped answer path (A1–A4). Trained baselines used for comparative validation live outside the product tree and outside the answer path. |
| **The signal protocol, end to end** | One signed signal; the mass switch, whose cut is a declared input (§113 item 9); delivery, addressing, and the resolver; the ground-plane return with provenance correlation. |
| **Absorb-and-propagate** | `absorb_transmit` and the frame codec are wired into the live path — activation, consolidation, the crystal, sage's reach provider and the reading junction all call them. What is not is the *streaming* aperture and multi-hop over a live carrier (§110); today a text fallback runs beside it, removed by originating the frame at a facet's `entry` (§113 item 6). |
| **The communication plane** | One primitive `send(to=artifact)`; delivery by the read light-cone; cryptographic isolation; consumers code against a listable plane — `put` / `get` / `exists` / `keys` plus real prefix listing — and a **Carrier** — `emit(frames)` / `receive()` — reaches them through a named spooling adapter; streaming degrades to store-and-forward. Four of the eight media of §44 have implementations; the radio tiers do not. |
| **Merkle anti-entropy** | The sync path of `mesh/sync.py`: no cursor there, therefore no cursor-skip class, and self-healing by default. A second route, `mesh/federation.py`, pages peers behind a per-peer cursor and is gated on an explicit peer list (§46). |
| **Prism and capabilities** | A capability is advertised only where a probe confirmed it, re-measured on every read, so `present()` is three-valued — true, false, unmeasured — and the unmeasured set is reported apart from the absent one; path strength ranks matches among those already present. |
| **Install** | The `prism install` command: bundle-sha gate, per-crystal verify and sha-pin, install-kind vocabulary, then the junction gate — which today refuses a whole crystal on a capability gap rather than grounding it with dormant organons (§33). |
| **The crystal base** | Facets, tektons, and organons compose into a self-contained invokable artifact, with two-gate discharge and three distinguishable refusals. Complete; nothing in production constructs one (§110). |
| **Published licenses** | Each repository carries its own `LICENSE` file, which is the authority. The principle that produces them is in `components.md` under "License posture". |

## 110. The capability already built, awaiting its first caller

Read this list narrowly: it names what is complete and *uncalled*, not what is unfinished. Several
neighbouring capabilities that once sat here have since been wired, and are recorded in §109 instead —
absorb/transmit routing, frame encoding, the accumulated read, the diagram and the colimit, the
settlement split, and the inbound challenge token all have live callers now.

- **The streaming aperture, and the crystal base.** `ember.optics.stream` returns a living instrument a caller feeds frame by frame; nothing calls it, in production or in a test. The crystal base composes facets, tektons and organons into an invokable artifact with two-gate discharge and three distinguishable refusals; every construction of one is in a test, and neither the host nor the dispatcher nor the registry builds one. **Lit by:** originating the frame at a facet's `entry`, so a need arrives as a beam rather than as text — §113 item 6.
- **Cross-node accumulator merge.** Two embers could pool evidence by merging accumulators, bit-for-bit identical to the single-node result, without exchanging observations. The merge is exact and its only callers are tests; there is no transport that carries an accumulator between nodes. The *operator* merge is called in production, but within one process, over the trajectories of a single fit — not across peers. **Lit by:** a second node on a live carrier, which is also what demonstrates the certified band tightening on an ensemble.
- **The mint law.** Pure and exact: it converts attested lift into a residual and a mint, and checks its own conservation against a derived tolerance. Nothing calls it. **Lit by:** the meter (§111) writing a record of use for it to read. The settlement half is already lit — the finance persona exposes a tool that reads an artifact's earned energy and reports the flat-fee split, refusing to report a payout that does not conserve.
- **The stake.** A share writes a staked claim onto the artifact — who staked, when, how much — which is what makes a share an accountable act. Nothing reads it back: the refutation law that would release the stake to a refuter has no caller. **Lit by:** a reader that enforces it at consent.
- **The exchange agreement.** Built and complete: it mints and resolves the signed artifact two origins stake, carrying cross-issuer trust, the permitted flows, and each side's flat fee. It is not imported by the finance persona's server, and the cross-origin transport it needs does not exist. **Lit by:** a second origin to settle against.

## 111. The capability still to build

- **The meter.** Records use at the tekton boundary: a `used` edge from the invoking artifact to the
  tekton's crystal, carrying the invocation's artifact id, the grant it ran under, the measured work
  absorbed, and a monotonic sequence number — or derived directly from the artifact every invocation
  already produces there, folding the accounting into provenance the system writes anyway rather than
  a second edge type. Which of the two is final is the open design decision. **Buys:** the entry
  point that starts the economy — nothing else needs to precede it, and the payout and mint laws are
  lit by reading whatever it writes.
- **The dream phase.** An idle detector that makes *"nothing is being asked"* an expressible condition. **Buys:** a placer rather than a scheduler — compactification fires when the observer is free, not when a clock says so.
- **Morphism composition.** `composed_of` written and read. The edge type is declared in the seed set and nothing writes it — the colimit's own write says so at the call site and leaves it out. **Buys:** merges that chain, so a duplicate resolved twice keeps one path back to both originals, and a derived composition becomes an artifact carrying its own fitness counter.
- **Limits.** Pullback, equalizer, product, alongside the colimits already present. **Buys:** intersection and constraint, the dual of joining.
- **Functors.** Analogy as a commuting square. **Buys:** the one categorical claim directly falsifiable against a standard baseline.
- **The version-DAG parent pointer and a reconcile operator.** Head *resolution* is built and wired: there is no stored head, every revision commits and stands, and which one answers is measured at read time against the reader's own recall set — too little frame, an unmeasurable candidate, or nothing above the frame's own null each yield no reading and leave every revision standing. What is missing is the parent pointer that makes the lineage a DAG rather than a flat root grouping, and the operator that reconciles two disjoint frames. **Buys:** disjoint-frame reconciliation — one artifact, head resolved per observer.
- **A payment rail.** **Buys:** settlement out to the world. Nothing in the workspace moves value: the finance persona's settlement tool computes a split and says in its own answer that it moved nothing.
- **WebRTC/QUIC transports and streamed optics frames.** **Buys:** live frames on the wire at conversational latency.
- **The condensation-event artifact type.** **Buys:** enforcement of the waveform/provenance boundary — cognition continuous, provenance discrete, with the discrete side recorded.

## 113. The order of work

**The through-line: wire what already exists before building anything new.**

1. **The constants sweep.** The rule is exact: a measured resource envelope, a caller-supplied noise provider, and the false-alarm level declared once at the aperture boundary are the only external inputs (§100 of `paper-1-the-instrument.md`). Every remaining typed-in value is a seam and resolves by measurement.
2. **The 75,000 duplicate concepts.** Two id families over one concept, with no morphism between them, suppressed by string equality at render time on every query forever. **This is the largest measured defect in the reachable corpus** — the 5,186,296 unprovenanced bulk vertices of §99 F3 in `paper-2-knowledge-without-weights.md` are larger in count but sit outside the answer path until Phase 3. The derivable diagram and the colimit are built and run against the live store — `op.consolidate.colimit` derives the diagram, computes the universal object, and applies it only when the conservation residual balances against a derived tolerance, so a residual of any size fails the test. What is missing around them is a PLACER rather than a scheduler to fire them: nothing enqueues the colimit, and the auto-enqueued consolidation is the crosswalk. Beyond that, a merge that carries positions and mass, and `composed_of` composing.
3. **The DAG.** Give the reasoning substrate one home. It is split between `crystal.ontology` and `ember.ontology` across the licence line, reached by import from one side and by a registered seam from the other (§28).
4. **Certificate renewal.** A wildcard expires and something must renew it — a day's work, done well before the month it expires.
5. **The miss path.** It is the architecture *and* the fix for field pollution — millions of running-prose articles make common-word document frequency large and drop junk seeds to near-zero amplitude **with no code change.**
6. **Originate the frame in a facet's `entry`.** One route: the need carries the beam. The text fallback disappears *because the flow replaces it*, not beside it.
7. **Emit condensation events as artifacts**, and route the residual by coupling.
8. **Rename persona to crystal** through the loader, router, registration table, and environment. A persona *is* a crystal — one crystal, not a collection of them — so the rename is mechanical.
9. **Replace the message/event cut with a measurement.** It is the one runtime decision of the signal protocol still standing on a coupling constant rather than a null. Retire it with a computed null over the corpus's own mass distribution, exactly the way the propagation floor is computed for reach — sample the population, read the statistic, let the cut fall out of it.

## 115. The remaining debt, in the order a skeptic would attack

**Unmeasured.** Cross-kind association — a prose article to a synset — rests on a crosswalk matched by string, the shape the method warns against.

**Undecided.** Analogy as a commuting square (§76 of `paper-2-knowledge-without-weights.md`) is falsifiable against the vector-offset analogy test.

---

## What is still outstanding

Every row is a place this specification asks for something the code does not do. §110 and §111 carry
the capabilities that are complete-but-uncalled and the ones not yet written; this table carries the
divergences — where the design and the shipped behaviour disagree.

| # | Outstanding | What exists today |
|---|---|---|
| 1 | Node identity is not fail-closed (§46): a node started with no explicit id publishes under its hostname and forks the mesh. | An operator-maintained ignore list, `EMBER_MESH_IGNORE`, naming the phantom publisher ids to skip. |
| 2 | Two sync routes where the design has one (§46). The second carries the per-peer cursor the first exists to remove. | `mesh/sync.py` reconciles by Merkle anti-entropy with no cursor; `mesh/federation.py` pages peers behind one, gated on an explicit peer list. |
| 3 | `prism install` refuses a whole crystal on a capability gap, where the design grounds it and marks the affected organons dormant (§33, §34.1). | Per-organon discharge with three distinguishable refusals exists in the crystal base; the installer calls a whole-crystal, exact-match junction gate instead. |
| 4 | Mantle's manifest does not declare its edge on prism (§29). | 36 import sites across 24 modules. The declaration that *is* enforced covers the embeddable store surface, which genuinely has no sibling import. |
| 5 | The reasoning substrate has two homes on opposite sides of the licence line (§28, §113 item 3). | `crystal.ontology` imported directly by chorus; `ember.ontology` reachable only as a seam registered with `prism.runner`. |
| 6 | `requires_identity` does not reject an anonymous presenter on a write or an invoke (§51). | The field and its three per-verb overrides are stored and settable; only the read override is read, in collection listing for a grant-key presenter. |
| 7 | Refresh tokens are stateless, unrotated and unrevocable for their full 30 days (§52, §56). | Short-lived access tokens, audience binding, and a derived access TTL of half a working day. |
| 8 | A delegation token's TTL is a caller-supplied request field with no server-side ceiling (§52). | A 300-second default, and a scope table that bounds which method and path a platform-scoped delegation may reach. |
| 9 | No threshold custody of a principal's master key (§56). | A three-level hierarchy — pluggable KEK, per-principal master key, HKDF subkeys — with the KEK non-exportable under the `kms` and `vault` providers. A Shamir split-and-combine oracle exists in Origin, over its own secret store, and is not wired to these keys. |
| 10 | No `agi://` resolver, and DNS is not the trust root (§50). | The scheme is recognised and returned unresolved with the reason attached; names resolve through the Origin, and identity is verified locally against the authority manifest's inline JWKS. |
| 11 | Cooperative DNS, the continuity state machine, and the radio tiers of the transport cascade are unwritten (§50). | The desktop relay over one outbound WebSocket, and a cost-ordered selection over the IP and filesystem media of §44. |
| 12 | The enterprise message bus, the couriered drive, and the broadcast media of §44 have no adapter. | Four of the eight media are implemented; a loopback carrier models the broadcast shape in-process, and the spooling adapter that would consume a real one has no production caller. |
| 13 | The store's `/mcp` surface is not INVOKE-gated (§36, §51). | Invoke gating is enforced in the gateway's dispatcher against the store's audited access check, on the nine actuation-shaped operations that declare it. |
| 14 | `persona` is still the spelling in the loader, the router, the registration table and the environment (§35, §113 item 8), and each persona still carries a `server.py`. | A persona is one crystal, declared as a `CRYSTAL` dict in its own `manifest.py`, mounted by a host that imports no persona by name. |

---


---

# APPENDIX — GLOSSARY

*The full glossary. `PAPER-1` and `PAPER-2` each carry the subset they use.*

| Term | Meaning |
|---|---|
| **Agience** | The platform and brand. |
| **Artifact** | The universal primitive: a content-addressed object — content, context, identity, provenance, history — carrying edges. |
| **Aperture** | What bounds a beam and reads everything about it, streaming-first and adaptively forgetting. |
| **Beam** | A band of many signals arriving at a screen — the measurement, an extended object. It names no package: `agience-beam` is archived, its wire and law modules are prism's, and the one seam onto entroptics is `ember.optics`. |
| **Bundle** | A crystal grounded on a prism at runtime; at install, a **signed set of crystals** whose identity is a hash over the sorted member crystal hashes; and content-addressed operator source for offline execution. |
| **Capability** | A named, prism-provided affordance: an artifact whose presence is established only by a probe, re-measured on every read, and whose matches are then ranked by accrued path strength, authorized by grant. |
| **Carrier** | A broadcast medium — `emit(frames)` / `receive()`, where a frame is `(key, bytes)`. It cannot be listed; it radiates. A named spooling adapter turns what a receiver hears into its own local Plane. The only concrete one in the tree is an in-process loopback. |
| **Chorus** | The choir: aria, astra, iris, lumen, ophan, sage, seraph — each one a crystal with a name. Chorus contains the tektons and facets those crystals are composed of, reaches the instrument through the injected crystal handle, and imports crystal, prism and mantle directly. It may not import ember. |
| **Colimit** | The universal object with morphisms from every member — one thing seen two ways — accepted only when the conservation residual is zero. |
| **Compactification** | Fewer objects, more mass each, morphisms carried and composed: the corpus's learning step. |
| **Concept** | A direction on the screen, universal — the same point no matter who looks. |
| **Crystal** | Facets, tektons, and organons grown on a lattice into a self-contained invokable artifact: prism in, tekton gates, facet out. A persona is a crystal. |
| **CRUDEASIO** | Create, Read, Update, Delete, Evict, Invoke, Add, Share, Admin — nine actions in the order the flag tuple has them, carried in a ten-bit mask whose tenth bit is ALLOW, which is what makes deny absorbing. O is Admin, the meta-permission over a resource's grants; it is not ownership, and ownership is grant-derived. |
| **Demurrage** | The second law read as accounting: unattended energy cools at a measured rate, and no measured rate means no cooling. |
| **Ember** | An observer unit — a capacitor of crystals plus energy, scale-invariant. |
| **Entroptics** | The instrument: a screened spectral aperture that reads a frame at its own resolution and reports modes, interval, decay, and coupling. |
| **Facet** | A view, and a role inside a crystal rather than a package: a bidirectional conduit carrying a signal in and out of a crystal's face, absorbing nothing, and the surface an observation is displayed on. |
| **Ground plane** | The shared substrate the reach's circuit completes through, where correlation is provenance rather than a reply-to. |
| **K_signal** | The count of singular values $s_k$ of the entropy-folded, MAD-whitened screen that stand above the derived noise floor $\Phi$ — the resolution read, and the count the adaptive cut uses. Distinct from `resolved_modes`. |
| **Lattice** | Mantle's store: vertices and edges plus an encrypted content-addressed filesystem, with no external database. Its embeddable surface — the store package plus the acting-principal resolver — installs on stdlib and `cryptography` alone, and a suite holds it there by AST scan, runtime import and a functional round trip. |
| **Lens** | A side's conversion into the shared basis, carrying its own entry, inverse, energy law, zero, and null. |
| **Light cone** | The graph-reachability access model. Reachability decides authorization and computes the recipient set that qualifies to hold a group key; the key scheme distributes to that set. Key *material* is never derived from traversal — it comes from the immutable origin root, so authority can change without re-keying a collection — but key *issuance* is gated by that traversal, behind a short-lived memo. |
| **Mass** | Stored verified energy — existence in degrees, inertia, and the switch between propagating and transforming. Derived from the provenance quadruple, never looked up from a rung; the cut it is compared against is a coupling constant still owed its null (§21.1 of `paper-2-knowledge-without-weights.md`). |
| **Mass gap** | $-\log|\mu_1|$: the decay rate of the dominant Koopman mode, the per-step contraction rate of predictability. A spectral quantity read off a fitted operator; it carries no corpus number. |
| **Organon** | A real-world capability — the hands — requiring a grant and a prism capability. |
| **Origin** | A governing entity holding policy, economy parameters, and peering identity, owning an Authority; also the IdP service. Nothing imports it: callers verify identity through `prism.trust` and reach it over HTTP for issuance. It performs no authorization — grants live in the store. |
| **Plane** | A listable medium — `put` / `get` / `exists` / `keys`, with real prefix, delimiter and start-after listing semantics. An object store, a shared directory, an in-memory map, or the lattice itself; peers are discovered by listing, never by a registry. Prism carries a second, unrelated `Plane` for the signal layer — `send` / `reconcile` / `receive` (§45). |
| **Prism** | The environment an ember is grounded on: the hardware unit, with the protocol bound per language. |
| **Propagation floor** | The weight an unrelated concept pair scores in the corpus graph, measured **0.007355** — a computed null over the propagation weights, and what terminates an associative walk. The sampling procedure and the statistic that produce this null are to be published beside the number. |
| **Reach** | Placing a signal on a shared screen and reading what crosses. |
| **Resolved modes** | $\#\{\,k : \lambda_k > \lambda_+\,\}$ — the count of eigenvalues of the unit-diagonal correlation matrix above the bulk edge, and the count the Weyl interval certifies (A7). Distinct from `K_signal`. |
| **Screen** | The ordered shared surface where signals meet and are read from either side — never shuffle it — and also the market. It is the object that folds, and only where the feature axis is continuous; a projection does not fold, keeping each side on its own entropy-matched grid. |
| **Signal** | A concept as one lens carries it, directional; also the one signed wire primitive whose mass selects message or event. |
| **Tekton** | A tool — the condensor — and a role inside a crystal rather than a package: it terminates a signal by absorbing its coupled band and removing it from propagation. |
| **Transducer** | A stored surface$\leftrightarrow$concept conversion over a class of information; an artifact, whose coupling to other transducers is measured. |
| **Triple** | Content, context, operator — any two determine the third. |
| **$\lambda_+$** | The bulk edge: the Marchenko–Pastur edge for a unit-diagonal correlation matrix of a $(T, F)$ frame, $\lambda_+ = (1 + \sqrt{F/T})^{2}$. $\mathrm{contrast} = \lambda_1 / \lambda_+$. |

---

*The measure of this system's life is entropy going down while coverage goes up. Declare the symmetries; measure what converges; ask the human what does not.*
