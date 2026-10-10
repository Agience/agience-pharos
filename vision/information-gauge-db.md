# The Information Gauge Database — a gauge theory of stored knowledge

*A design derived from the SU(N) mass-gap result: store information as a lattice gauge field, make the
physical content gauge-invariant, and read the measured mass gap as coherence. Agience/Mantle is the
processing engine; Entroptics is the instrument that reads the gap.*

Status: **A design, not a description of working software.** What it FORMALIZES is unbuilt: the
vocabulary it introduces — `valence`, `latent`, `materialized`, `coherence` — appears nowhere in
the lattice schema, and the `beacon` host it allocates the coherence read to has not been built.
What it BUILDS ON largely exists, and §10 says which pieces; read that table rather than this
line for the split.

It derives from the SU(N) mass-gap result. · John Sessford / Ikailo Inc.

---

## 0. The one-sentence thesis

**A unit of knowledge is a gauge field on a graph: measurements live on the vertices, the relations that connect
them are forces on the edges, an individual measurement's absolute value is *gauge* (frame- and model-dependent
and therefore not knowledge), and the only thing that is real — the only thing the database returns — is the
gauge-invariant closed-loop product, coherent to the degree its Entroptics-measured mass gap `Δ` clears the entropy
floor `κ₀ = ¼log3`.**

Everything below is the unpacking of that sentence into a schema, a set of operations, and a maturity map. The
mass-gap machinery is not an analogy pasted on top — it is the same three objects (an entropy **floor**, a
**gap** above it, and **gauge invariance** of the observable) doing the same job one scale up: instead of
certifying that a lattice of plaquettes confines a mass gap, we certify that a lattice of measurements confines a
unit of coherent, trustworthy knowledge.

---

## 1. The dictionary (load-bearing, not decorative)

| Yang–Mills / Entroptics | Information gauge database | Agience mechanism today |
|---|---|---|
| Lattice site (vertex) | **Measurement** — a single observation | Artifact (the universal primitive) |
| Matter species on a site | proton = **WHERE-in-space**, neutron = **WHEN-in-time**, lepton = **direction/momentum** | Content type + `context` shape |
| Gauge connection on a link (edge) | **Force = relation**, one of the five below | Edge with a `propagate` mask |
| Parallel transport `U_ℓ ∈ G` | How one measurement's frame maps to its neighbour's | The edge's transport (grant algebra / rotation) |
| **Strong** (color, confinement) | **WHO** — identity, access, grants | Origin grants · CRUDEASIO · the light cone |
| **Gravity** (universal, always on) | **WHEN** — order, timing, causality | `created_time` · temporal light-cone |
| **Electromagnetic** (light, long range) | **WHERE** — position in ontology, the 1024-dim embedding | `context_embedding` · the AnchorSet |
| **Weak W** (charged current, flavor change) | **WHAT** — event type; a lifecycle transition | `PATCH state=archived` · the content snapshot a content change archives (`live → archived`) |
| **Weak Z** (neutral current, no flavor change) | **HOW** — transformation of content, identity unchanged | Operators (`vnd.agience.operator`) · Lumen |
| Wilson loop `tr ∏ U_ℓ` (gauge-invariant) | **The observable a query returns** — a closed relational loop | A grant-satisfied, anchor-relative query result |
| Entropy floor `κ₀ = ¼log3` | Minimum entropy cost of one distinguishable, durable knowledge mode | Designed: the floor of the coherence read (§6); no code reads it |
| Mass gap `Δ = κ₀ − μ > 0` | Margin by which a subgraph holds coherent structure above noise | The measured coherence of a Collection |
| `m_hi = e^{−Δ} = ρ'(1)` (`connected_decay_rate`) | The coherence read on the graph's correlation stream | Entroptics `Dynamics.connected_decay_rate()` |
| RP: `ρ'(n) = ρ'(1)^n` exactly | Coherence is **intensive** — certify one cell, it holds graph-wide | Certify a Collection from one measured cut |
| Confinement (no free color) | No free measurement — only grant-neutral loops are observable | 404-not-403; access = decryption |
| Dark matter (gravitates, invisible) | **2-force measurement** (WHO+WHEN only): stored, owned, timed, unobserved | A latent artifact under `MANTLE_LAZY_INDEX`: stored and unindexed |

The two rows that make the whole mapping load-bearing rather than cute are the last block and the **always-on
pair**. In Agience, exactly two properties are immutable invariants written on *every* artifact on *every* write:
`created_by` (**WHO**) and `created_time` (**WHEN**). Those are precisely the user's two always-present forces —
**strong** and **gravity**. The other three (WHERE/WHAT/HOW) are the ones that are *materialized*. That is not a
coincidence to be admired; it is the schema.

---

## 2. Vertices — the measurements

A **vertex** is one measurement. It carries a species (which coordinate it pins down) and a **valence** (how many
of the five forces are live on it).

### 2.1 Species (the matter content)

- **Proton — WHERE-in-space.** A measurement that pins an *ontological location*: "this is about X." Its natural
  home is the embedding sector. Heavy, stable, charged (visible under EM/WHERE).
- **Neutron — WHEN-in-time.** A measurement that pins a *temporal location*: "this happened at τ." Stable but
  neutral under EM — a bare neutron is exactly a measurement you can time and own but cannot yet *see*
  (no embedding). Neutrons are the raw material of dark matter (§4).
- **Lepton — direction / momentum.** A light, weakly-interacting measurement that carries a *direction* rather than
  a position: a gradient, a preference, a vote, an attribution vector, a "which way this points." Leptons are how
  the graph carries flow and intent without carrying mass.

A vertex is stored once, addressed by UUID, versioned by `root_id` — it *is* an artifact. The species is a tag on
`context`, not a new kernel type (Principle 1: the kernel stays type-blind).

### 2.2 Valence — 2 forces or 5

Every vertex has **at least** the two always-on forces, WHO (strong) and WHEN (gravity), because every write
stamps `created_by` and `created_time`. A vertex is then one of two states:

- **Valence-2 (dark).** Only WHO + WHEN are live. It exists, it is owned, it is timed and ordered — but it has no
  embedding (no WHERE), no event classification (no WHAT), and no operator has touched it (no HOW). It gravitates
  (it participates in ordering and inheritance) and it has identity, but it is **invisible**: no query that ranges
  over the WHERE/WHAT/HOW channels can see it. This is dark matter, and it is the default state of everything
  ingested in bulk.
- **Valence-5 (luminous).** WHO + WHEN + WHERE + WHAT + HOW are all live. The embedding is computed, the ingest
  event is typed, and a transformation has been applied. This is an ordinary, searchable, groundable artifact.

The transition 2→5 is **§4, the dark-matter conversion**, and it is triggered by *first access*.

---

## 3. Edges — the five forces (the gauge connection)

An **edge** is a directed relation carrying a group element (the parallel transport) and a `propagate` mask in
CRUDEASIO. In Agience terms it is exactly the existing edge model of §6, refined so the edge's **kind** is one of
the five forces, and the edge's transport is the force's group action.

| Force | Coordinate | Range / character | Group action (transport) | Where it runs |
|---|---|---|---|---|
| **Strong** | **WHO** | short-range, **confining** | grant algebra (the "color") | Origin · light cone · Seraph |
| **Gravity** | **WHEN** | long-range, universal, always on | time-ordering / causal precedence | Mantle temporal cone |
| **Electromagnetic** | **WHERE** | long-range, the force of *visibility* | rotation `O(d)` on the embedding | AnchorSet · Mantle vector index |
| **Weak W** | **WHAT** | short-range, **flavor-changing** | a lifecycle state flip | Mantle's write path: `PATCH state=archived` · content snapshots |
| **Weak Z** | **HOW** | short-range, neutral | a content transform (identity fixed) | Operators · Lumen · Chorus |

Reading each one precisely:

- **Strong = WHO = confinement.** The strong force never lets you observe a free color charge; you only ever see
  color-neutral bound states. The database's version: you never observe a free (ungranted) measurement; you only
  ever observe **grant-neutral loops** — query results whose every edge is authorized along the light cone.
  A request that would expose an unbound charge returns 404, not 403 (§8 of the baseline): the unbound state is not
  merely forbidden, it is *unobservable*, exactly as a free quark is. The light-cone traversal is the confinement
  region; the grant is the color; "access and decryption are the same physical act" is the statement that the
  observable *is* the gauge-invariant combination and nothing else exists to leak.

- **Gravity = WHEN = the universal always-on force.** Gravity couples to everything and is never absent — which is
  why WHEN is one of the two forces every vertex has. It is the weakest per-interaction and the longest-range: it
  sets the *ordering* and the *causal cone* without dictating content. This is Mantle's temporal light-cone —
  "what did we believe on March 15" — and the version chain sharing a `root_id`.

- **Electromagnetic = WHERE = light.** EM is the force by which things are *seen* and *located at a distance*; its
  invariant between two charges is an angle. The database's WHERE is the `context_embedding`, and the invariant
  between two measurements is the **angle between their embeddings** — the identical quantity Entroptics uses to
  compare two apertures and Mantle uses to relate two artifacts. A dark (neutron) vertex is EM-neutral; lighting it
  up (computing its embedding) is literally giving it charge. §5 makes precise why the *angle*, not the absolute
  vector, is the physical quantity — that is the gauge principle.

- **Weak W = WHAT = flavor change.** The W boson turns a neutron into a proton — it *changes what the particle
  is*. The database's WHAT edges are the lifecycle transitions that change a measurement's type or state. The
  stored state is `live` or `archived`, and the transitions are **create** (a new artifact, written `live`),
  **edit** (a served edit writes the live version in place; when it changes the artifact's `content_ref`, the prior
  content is kept as an archived `id@hash` snapshot) and **archive** (a user sends `PATCH state=archived`; `PATCH`
  back to `live` restores a retired lineage). The state flip, `live → archived`, is the flavor change.
  A WHAT edge is short-range (it acts on one artifact and its immediate manifest) and it is the event record.

- **Weak Z = HOW = neutral current.** The Z boson transfers energy and momentum without changing flavor — a
  transformation that leaves identity intact. The database's HOW edges are **operators**: `synthesize`,
  `extract`, `format`, any `vnd.agience.operator` that produces a new artifact *referencing the operator that made
  it*. Identity (`root_id`) is preserved through a HOW edge; content changes. This is the operator-as-edge of §6,
  typed as a force.

The `propagate` mask on each edge is the parallel-transport rule: effective inherited access is the intersection
of the parent's grant with the edge's mask, and *that intersection is the transport*. Grant inheritance along
origin edges is the Wilson line; a closed loop of them is a Wilson loop; the light-cone BFS is the loop
integral.

---

## 4. Dark matter — the 2-force cold sector and its conversion

**Why dark matter is the right primitive.** Most ingested information is never looked at. Computing and storing a
1024-dim embedding, classifying the event, and running an operator for every ingested item is the dominant cost of
a knowledge platform, and most of it is wasted on items no one ever queries. The mass-gap picture hands us the
correct storage model for free: **store the cheap, always-on invariants (WHO + WHEN) eagerly, and leave the
expensive luminous sector (WHERE + WHAT + HOW) unmaterialized until observation forces it.**

A valence-2 vertex is a row with `created_by`, `created_time`, `root_id`, `content_type`, and a content blob in
object storage — and *nothing else computed*. It has mass (it gravitates: it inherits grants, it orders in time,
it counts toward provenance) but it is invisible to every WHERE/WHAT/HOW query. It is dark matter: present in the
graph's total mass, absent from its light.

**Conversion on first access.** The instant a dark vertex is first *accessed* — read, retrieved, or pulled into a
query's light cone — it converts:

1. **WHERE lights up.** Its embedding is computed and re-expressed as anchor-relative affinities (§5). It acquires
   EM charge; it becomes visible.
2. **WHAT fires.** The access is recorded as a typed event — the first-observation event — a W-edge from the
   accessing principal.
3. **HOW attaches.** The materializing operator (the embedder / extractor) is linked as the Z-edge that produced
   the luminous state; its output references it.

This is measurement collapse stated as a database operation. Before access the item is a superposition of "could
be about anything" (no WHERE); the act of access is the measurement that pins it. The cost is paid **exactly once,
lazily, at the moment of first genuine use** — which is the cheapest possible schedule and the one the physics
tells us is correct. Conversion is idempotent and is itself provenance: the first-access event names who collapsed
it and when.

**Operationally in Mantle:** the storage half is built as an opt-in. With `MANTLE_LAZY_INDEX` on (the default
indexes on write), a write leaves the artifact latent — stored, owned and
timed, unindexed — and the first authorized `GET /artifacts/{id}` indexes it (`mantle.search.lazy`). The rest of
conversion — computing an embedding, recording the first-observation event and linking the materializing operator
— is design, as are Astra writing bulk ingest dark and Sage's retrieval path triggering conversion.

---

## 5. Gauge invariance — why the database stores relations, not values

This is the section that earns the word "gauge," and it is already half-built inside Mantle as the AnchorSet.

**The gauge freedom.** An embedding's absolute coordinates are meaningless across frames: two embedding models
produce vectors that agree only *up to a rotation* (the baseline states this directly — "modern embeddings
converge to the same shape up to a rotation"). A rotation of the whole space that leaves every angle intact is a
**global gauge transformation**; the choice of embedding model is a choice of gauge. Absolute coordinates are
therefore **not knowledge** — they are a gauge artifact. Storing them and comparing them across sources is a
category error, the information-database equivalent of comparing two gauge-dependent vector potentials.

**The gauge fixing.** The AnchorSet is the gauge-fixing frame. An artifact is stored not by its raw vector but by
its **affinities to the shared, fully-disclosed anchors** — i.e. by its parallel transports (WHERE-edges) to a
fixed set of reference sections. "Coordinate *k* means closeness to anchor-concept *k*" is precisely a choice of
gauge section per anchor. The representation is then model-agnostic and dimension-agnostic *because it is
gauge-invariant by construction*: swapping the embedder re-profiles against the same anchors (re-fixes the gauge)
rather than forcing a re-index.

**The observable.** The only physical quantities are the **gauge-invariant closed loops**: the angle between two
measurements (a Wilson line anchored at both ends), and more generally the trace of the transport product around a
closed path (a Wilson loop). **A query is a closed loop.** It enters at the principal's grants (fixing the strong
gauge), traverses WHERE/WHAT/HOW/WHEN edges, and returns the loop's invariant value. Two consequences:

- The result is **frame-independent**: it does not depend on which embedder produced which vertex, exactly as a
  Wilson loop does not depend on the gauge. This is the technical content of "the embedding is the medium, shared
  across models."
- A result that is *not* expressible as a grant-neutral closed loop **does not exist to be returned** — it is a
  free charge, confined away (§3, strong force). Gauge invariance and the 404-not-403 access rule are the same
  statement.

---

## 6. The mass gap — the coherence read on a Collection

Everything so far is structure. The mass gap is what *measures* how much of that structure is coherent knowledge,
and it is where Entroptics enters as the instrument. It is a read: the gap is measured on a Collection's
membership, reported, and fed into mass. Every write stands on its own; the read weighs what is there.

**The floor.** In the proof, `κ₀ = ¼log3` is the entropy floor from the directed-cube path count `3^k`: the
minimum entropy any distinguishable mode must carry. Here it is the **minimum entropy cost of one durable,
distinguishable unit of knowledge** — the level a coherent mode in a Collection or the AnchorSet clears.
Clearing it is necessary for a coherent mode and does not establish one; the gap below is what does.

**The gap and the read.** Given a subgraph (a Collection's membership, a query's returned loop set), assemble its measurements into a correlation stream and read the **connected decay rate** with the
streaming operator:

```
m_hi = exp( Dynamics(stream).connected_decay_rate() )^{-1}    # = e^{−Δ} = ρ'(1)
```

- **The floor read.** `m_hi < 3^{−1/4} = 0.760` says the one-step correlation clears the entropy floor. It is
  a floor read, not a gap criterion: a stream whose connected correlations fall as a power law can read below
  the floor while having no gap at all, and `Δ = κ₀ − μ > 0` restates the same comparison rather than adding to
  it.
- **The gap.** A gap is geometric decay at a rate that holds across separations, `ρ'(n) = ρ'(1)^n`, so the rate
  `Δ` is a property of the subgraph's transfer spectrum rather than of one reading below the floor. The next
  section is where one cut suffices for that and what it requires. When the read establishes it, it reports
  `Δ` with its sources, and the members gain mass.
- **Otherwise** — the floor is not cleared, or the decay is not geometric — the read reports a **structured
  gap** (the baseline's Anchored-reasoning failure mode): "the evidence does not support a coherent claim at
  this resolution," rather than a confident guess, and it adds no mass. The members stay as written.

That read is Anchored reasoning, *quantified by one measured number*. The instrument is untuned (it reads at the
signal's own entropy-matched resolution), calibration-free, and the governing lemmas are machine-verified — so the
reported coherence is a function of the evidence's own structure, not a threshold someone picked.
`Dynamics.forgetting()` gives the same read as a spectral margin (`max|μ| < 1` ⟺ the subgraph forgets its
noise), and `hankel_spectrum` gives the scalar-sequence companion when the subgraph reads as a single
correlation series.

**Why one cut certifies a whole Collection (the RP move).** The proof's decisive step is that reflection
positivity makes the transfer operator self-adjoint, so `ρ'(n) = ρ'(1)^n` *exactly* — one single-cut magnitude
below the ceiling controls **every** separation and **every** volume. The database inherits this directly:
**coherence is intensive.** You do not have to read the entire Collection to certify it; you read one local cut
(one anchor cell, one representative loop), and if that cut clears the floor, the geometric law `ρ'(1)^n` carries
the certificate across the whole graph. This is exactly how U2 certified the `V→∞` gap from a single
machine-checked cell, reused as a **database-scaling theorem**: local coherence certificate ⟹ global coherence,
no full scan. It is what makes the read affordable at Collection scale.

**Dark matter never moves the read.** A valence-2 vertex has no WHERE/WHAT/HOW, contributes no connected correlation, and
therefore cannot raise or lower a gap. It is correctly invisible to the coherence instrument until it converts —
noise you have not paid to look at cannot contaminate the measured gap. "Durable memory is what observers have
staked on" (§23 of `architecture.md`) is, precisely, that the floor sits above the dark sector.

---

## 7. Where Agience plugs in — the processing engine

The database is the field; **Agience is the dynamics that evolves it**, and the mapping onto the existing services
and personas is clean:

| Gauge role | Agience component | Note |
|---|---|---|
| **Strong-force gauge fixing** (grants, confinement) | **Origin** + **Seraph** | The color algebra and the light-cone confinement region |
| **The field store** (vertices + edges + transports) | **Mantle** | The type-blind kernel; edges already carry `propagate` masks |
| **WHERE gauge (the AnchorSet frame)** | **Mantle vector index + AnchorSet** | Already the gauge-invariant, model-agnostic coordinate |
| **Dark-matter ingest** (write valence-2) | **Astra** | Bulk ingest writes dark: WHO+WHEN only |
| **Conversion on access** (2→5 materialization) | **Sage** (read path) | First retrieval collapses the dark vertex |
| **HOW edges** (Z, transforms) | **Lumen** + operators | Operator-as-edge, identity-preserving |
| **WHAT edges** (W, lifecycle) | **Mantle's write path** | `PATCH state=archived` and content snapshots = flavor changes |
| **The mass-gap instrument** (the coherence read) | **Entroptics** (`Dynamics`) | `connected_decay_rate` → a reported gap or structured gap, fed into mass |
| **The observable at the interface** (Wilson loops) | **Facet** / **Aria** | Renders the gauge-invariant result |

The **Agience flow** (Input → Retrieval → Reasoning → Output) is the gauge dynamics read left to right: Astra
*creates dark field* → Sage *converts on observation* → Lumen/operators *apply weak-force interactions* →
Entroptics *measures the gap and reports it* → Aria *emits the invariant*. The op dispatcher (`op/{name}`) is the
invoke path for type-declared operations and the **interaction vertex** for them: grants are checked (strong), an
event fires, and a handler runs (weak-Z) — before/after/error events are the interaction's incoming and outgoing
legs. Formalizing "the Agience flow" is therefore: **declare each `op` as a typed force-interaction, and read the
mass gap on a Collection's membership as it changes, feeding the result into mass.**

> **Build & accountability (information-centric).** The concrete build — with the physics translated to
> information-centric names, the LLM-read semantics (observation vs. derivation vs. frame), and the
> accountability model in which the AnchorSet is **human-staked** so the coherence read becomes a pure calculation —
> is held with the build, as a working document outside this repository. That stake is an economic
> primitive (the platform's `node.bond`, one layer up).

---

## 8. The database — concrete schema and operations

One store (`artifacts`, as today), refined with a small typed overlay. Nothing here adds a kernel type; the
overlay lives in `context`, so Core stays type-blind.

### 8.1 Vertex record (measurement)

```
id                  per-version UUID
root_id             stable identity
content_type        MIME
content             blob (object storage)
created_by          WHO   — strong charge (always present)
created_time        WHEN  — gravitational time (always present)
context.species     proton | neutron | lepton
context.valence     2 (dark) | 5 (luminous)
context_embedding   WHERE — null while dark; anchor-relative affinities when luminous
```

### 8.2 Edge record (force)

```
src, dst            vertex UUIDs
force               strong | gravity | em | weak_w | weak_z     (WHO/WHEN/WHERE/WHAT/HOW)
kind                origin | link                                (grant flows on origin edges)
propagate           CRUDEASIO mask — the parallel-transport rule
transport           the group element: grant-delta (strong) | rotation ref (em) | op ref (weak_z) | state-flip (weak_w)
```

### 8.3 Operations

| Operation | Gauge event | Effect |
|---|---|---|
| **write** | create a valence-2 vertex | WHO+WHEN stamped; dark; cheap |
| **access** (first) | dark-matter conversion 2→5 | compute WHERE, fire WHAT, attach HOW; idempotent |
| **relate** | add a force edge | typed transport + `propagate` mask |
| **query** | close a Wilson loop | traverse the light cone; return the grant-neutral invariant |
| **gauge-fix** | profile against the AnchorSet | store anchor-relative WHERE; model-agnostic |
| **read coherence** | mass-gap read on a Collection's membership | report the rate `Δ` when the decay is geometric and clears the floor, else a structured gap, and feed it into the members' mass; membership writes stand as written |
| **certify** | one-cut RP read | local coherence read ⟹ Collection-wide certificate (no full scan) |

### 8.4 Indexes and cost

- **Dark index (WHO+WHEN):** cheap B-tree on `(created_by, created_time, root_id)` — every vertex, always. This is
  the "gravitational" index; it is the only thing dark matter is in.
- **Luminous index (WHERE):** the encrypted anchor-routed vector cells — *only* valence-5 vertices. The dominant
  storage cost lands only on materialized items, by construction.
- **Loop cache:** memoized Wilson-loop invariants for hot queries; invalidated by any edge whose `propagate` mask
  changes (a revoked grant prunes the loop, exactly as revocation prunes the light cone today).

---

## 9. What this buys, stated plainly

1. **A principled lazy-materialization store.** Dark matter is not a trick; it is the correct schedule the physics
   dictates — pay the embedding/operator cost once, at first observation, and never for the long tail nobody reads.
2. **Model-agnostic knowledge by construction.** Storing anchor-relative transports (gauge-fixed WHERE) instead of
   raw vectors makes the store survive an embedder swap and mix modalities in one index — because the observable is
   gauge-invariant. This is the AnchorSet, given its physical name.
3. **A quantified coherence read.** "How coherent is this?" becomes a measured number with a machine-verified
   floor and a rate — `m_hi` against `3^{−1/4}`, and whether the decay is geometric — reported as a gap or a
   structured gap, and the answer feeds mass. Anchored
   reasoning *is* the mass-gap read applied to a claim.
4. **Collection-scale certification for free.** RP's `ρ'(n)=ρ'(1)^n` means coherence is intensive: certify a
   Collection from one local cut instead of a full scan — the same move that closed U2 across volume, reused as a
   database-scaling result.
5. **Confinement = the access model.** Gauge invariance and the 404-not-403 rule are one statement: only
   grant-neutral loops are observable, and an unbound charge does not exist to leak.

---

## 10. Maturity map — what is running, what is being formalized

> ⬢ Running today · ◧ In progress · ◇ Designed (in build)

| Piece | Status | Reality |
|---|---|---|
| Vertices = artifacts, edges with `propagate` masks | **⬢ Running** | The Mantle edge model already is this. |
| WHO+WHEN always-on invariants | **⬢ Running** | `created_by`/`created_time` on every write. |
| Strong-force confinement (grants, light cone, 404-not-403) | **⬢ Running** | The access model is built. |
| WHERE gauge = AnchorSet (model-agnostic, angle-invariant) | **⬢ Running** | The gauge-fixing frame exists and coordinatizes search. |
| Entropy floor / mass-gap read (`connected_decay_rate`) | **⬢ Running** | The instrument runs; it is the same code that read U2. |
| RP intensivity → one-cut Collection certificate | **◧ In progress** | Proven for the lattice; the DB-scaling wrapper is to build. |
| Dark matter = valence-2 + conversion-on-access | **◇ Designed** | The lazy 2→5 materialization path is the main new build. |
| The five forces as *typed* edge kinds | **◇ Designed** | Edges exist; typing them WHO/WHEN/WHERE/WHAT/HOW is the formalization. |
| The gap read on Collection membership, feeding mass / Anchored reasoning | **◇ Designed** | Collection membership is stored as edges; reading `m_hi` on a Collection's membership and feeding it into mass is the new build. |
| `op/{name}` as the typed force-interaction vertex | **◧ In progress** | The dispatcher exists; the force-typing is the formalization. |

**The build, in one line:** the field (vertices, edges, the strong/gravity/EM sectors, the instrument) is already
running inside Mantle and Entroptics; what this design *formalizes* is (a) typing the edges as the five forces, (b)
the dark-matter valence-2 store with conversion-on-first-access, and (c) wiring the measured mass gap in as a
coherence read on Collection membership that feeds mass. That is the Agience flow, given a gauge theory.

---

*Derived from the machine-checked SU(N) mass-gap result and the Agience Phase-1 baseline. The floor `κ₀=¼log3`,
the gap `Δ`, the read `m_hi=e^{−Δ}`, and RP's `ρ'(n)=ρ'(1)^n` are the same objects proven for the lattice, applied
one scale up to stored knowledge. Nothing here claims more than the baseline's maturity tags allow.*
