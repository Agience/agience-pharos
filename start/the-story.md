# Agience — the story

> The narrative, in movements: the premise, the three axes, the mechanism, the economy,
> the scale, the meaning. For the vocabulary it is written in, see [`spine.md`](../design/spine.md);
> for one picture of the whole, [`overview.md`](overview.md).

*Create your agency.*

---

## The premise

No observer sees the whole. Everything anyone knows arrives through a lens — the senses, an
instrument, an organization's meetings, a model's training set. The exact name for the gap between
that lens and the world is **entropy**, measured relative to the observer's own description: the
size of the difference between finite and infinite.

Trust is the technology finite observers use to act on what they did not personally verify. It is a
loan drawn against a completeness no one reaches, and it needs collateral: **accuracy** (is the
content right, at the resolution it claims), **provenance** (where did it come from, and what did
that origin have reason to distort), **transparency** (can the claim and its method be inspected by
anyone), and **corrigibility** (how does the source move when evidence arrives). Every institution
built for knowledge — peer review, notaries, chain of custody, double-entry books, the audit — is
machinery for making that loan safe.

Generative intelligence raised the volume of claims and severed them from their evidence. The
scarce thing in the intelligence era is the structure that makes a claim trustworthy: knowledge
that carries its evidence, answers that can be defended, infrastructure that cannot read what it
holds, and value that reaches the people who create it. **Agience is that structure.**

---

## One principle

Information has a natural resolution — a density set by its own entropy. Describe something more
finely than its evidence supports and you manufacture noise; more coarsely and you destroy signal.
At its natural resolution, information can be read, stored, and exchanged faithfully. A system that
respects that resolution stays coherent at every scale, from a radio burst to an organization's
knowledge to a model's claim against its sources. This is the geometry Agience is built on, which
is why its behavior reduces to measurement rather than to tuned constants.

On that principle stand three axes — the frame a finite observer needs. **Information carries
through space, and through time.** Entroptics carries it through space; Mantle carries it through
time; Agience is the one who is looking. Three names, one coordinate frame, and the observer is its
origin. What holds them together is a single guarantee: **measured, never set, never chosen.**

**The lens — Entroptics.** The instrument of accuracy. It treats any ordered signal as a finite
optical aperture and reads, from the signal itself, the resolution at which to trust it, whether
coherent structure is present, and how many distinct modes rise above the noise. It is
parameter-free — the only external input is the reader's own tolerance for a false alarm — and its
governing mathematics carries a `sorry`-free Lean 4 reduction with its assumptions printed. The same untuned instrument reads a radio waterfall, a
market stream, and a model's answer against its evidence. Meaning is measured, not modeled.

**The memory — Mantle, the lattice.** The instrument of provenance. The model is universal:
**everything is an object, and objects carry edges.** An **artifact** is content plus context,
carrying its identity, its version history, and its provenance inside itself. Its bytes are stored
under the hash of those bytes, and each version carries its own identity under a stable root, so
content cannot be swapped without changing the address it is reached by. Versions
supersede rather than overwrite, each stamped in the store's own ordered time, so the record can
reconstruct what was known and when. The audit is not a separate system — the audit is the data
structure. **Grants** authorize: who can reach an object is computed as reachability across the
edges of the graph, from a principal's grants outward, deny before allow. The content itself is
encrypted at rest, and its keys are managed on the platform — associated with identities and shared
by a standard key-sharing scheme. Content blobs are ciphertext and so is the search index: every term is one-way-transformed
before it is stored, and every posting list is encrypted. What the store keeps readable is an
artifact's metadata — title, description, tags — which is governed by grants instead. Grants and keys are
distinct instruments: a grant is authorization, revoked with one edit; a key is the standard
cryptographic means by which authorized parties read. A secret stays a secret throughout — visible
only to its creator and their delegates, at rest, in transit, and in compute, with no fallback and
no path that widens the set except explicit, revocable delegation.

**The seat — identity and the observer.** The instrument of agency. **Origin** is the identity
provider: it issues every identity. The grants and the keys live in the lattice, alongside the
artifacts they govern. An agent acts only on behalf of a person, through short-lived delegation, never
as its own authority. A person renders an artifact as a view; an agent reads the
same artifact as structured form — one object, two faces. What puts a draft into the record is
**agreement**: an object has mass to the degree that observers agree on it, weighted by their
authority, and a person staking judgment adds mass. Existence in degrees, corrigibility made
mechanical.

The three compose into one loop, which is the whole system in a sentence: **stand, measure, keep,
share — and another observer takes it up.** Observation and testimony, the only two ways a bounded
knower ever comes to know, closed into a cycle with trust supplied structurally at every arc.

---

## How it works

Agience runs on one store and one path. A signal crosses a **prism** — the boundary to an
environment, which names what that place can do (read a file, reach the network, render to a
person, drive a sensor) and signs that manifest. Inside that boundary, everything is structure and
observation.

The unit of shareable structure is a **crystal**: **facets** (the conduits a signal enters and
leaves through), **tektons** (the condensors that turn a continuous signal into typed content), and
**organons** (the instruments invoked when condensation completes). A facet is bound by the waveform
of the signal itself; a tekton is where a content type is born, by recognition learned from what the
system has seen before; an organon is a transformation. A crystal is pure structure:
content-addressed, signed, inert until grounded. A **bundle** is prisms and crystals together — the
complete unit that can ship. An **ember** is a bundle **energized**: grounded on a prism, its loop
running, its slice of the lattice filling with what it has observed. An ember is the smallest thing
that observes, and it is the same machinery at every size — a browser tab and a knowledge node
differ in mass, not in kind.

Nothing in this system is a trained weight. A capability is an artifact: created by a person,
signed, published to the store, discovered by what it offers, and invoked where the capability
physically exists. The store is the registry; the hash is the version; the signature and the
provenance decide whether code is allowed to ground at all. Knowledge is learned the way a mind
learns — verified foundations first, duplicates consolidated with their provenance, categories
resolved from the accumulated mass by compression. What the system cannot yet recognize, it holds
as unresolved, until enough of its kind arrives for the category to form.

---

## Does it hold?

The principle is a claim about geometry: that a system which measures rather than tunes stays
coherent wherever it is pointed. That is testable, and it has been tested in four unrelated fields.
Every figure carries the qualifier it was measured under.

**Retrieval, with no trained weight in the answer path.** Where a query shares no words with its
answer, the model-free geometry is worth **2–8× the lexical baseline** — *what-is* nouns **59/60
against 7/60**, modifiers **41/60 against 17/60**. *Qualifier, and it is a strength: where the
query **is** the answer's text, BM25 wins and should — reverse dictionary 83/90 against 90/90. The
system is not uniformly better at retrieval; it is much better at one kind of question and slightly
worse at another.* The baseline is the field's own yardstick rather than a private one: BM25 reproduces
published **BEIR within 0.016** on three datasets, and the geometry reproduces published
**Jiang–Conrath on SimLex-999 at ρ = 0.5935**.

**How much to keep.** At the retrieval cut, relevance spread across four facets: cosine F1 **0.806**,
the cut **1.000**, edge **+0.194**, 95% bootstrap CI **[+0.099, +0.301]**. At the KV cache, **94% of
the oracle's attention mass at 10% kept**, scored in 8 dimensions instead of 64. How many to keep is
a measurement, not a hyperparameter.

**Pharma.** Four head-to-head experiments against the reductions the industry actually uses, each
with a known ground truth: **three wins and one recorded negative** (dissolution f2). Every
Entroptics-side number has no fitted constant and one declared input. The negative is reported
because a method that only publishes its wins has not been tested.

**The economic clock.** The rate is read, never legislated: **14.24, 81.56 and 91.04** on three live
streams, **13.02 and 14.85** on two live conversations. *Qualifier: the economic layer is exact and
tested as pure functions and is **not yet wired**.*

Four fields, one instrument, no per-substrate tuning. That is the whole of the evidence, and its
limits are stated with it.

---

## The economy

The system runs an economy, and the economy is physics read as accounting. Energy is conserved and
always dissipates; that is the whole of it. **Value is verified work** — the only thing that cannot
be counterfeited, because it costs real verification to create. Held value **decays** unless it is
maintained, because the second law is not optional: knowledge that is not re-verified cools, so
there is no passive wealth and no rent. Each **origin** keeps its own ledger in its own proper
time, and that ledger is its currency. When two origins exchange, the rate is **measured** at the
point of exchange — read from the coupling of the joint frame — never set by decree. Conservation
is the audit: what flows out of a branch sums to what flowed in.

None of that settles by itself. Energy that dissipates and value that cools have to be accounted for
somewhere real, which means the economy needs a crypto system underneath it. Agience has an official
token today, predating this design. **Today it is a holding** — a way to hold a position and show
interest in what is being built, carrying no claim on revenue, no governance right and no share.
**Tomorrow, when the economy is implemented, it is revamped and transferred into real value inside
the ecosystem**: the unit that settles verified work, dissipates as energy dissipates, and decays
unless it is maintained. Between the two is a transfer conducted properly, with every existing holder
accounted for. No token is sold to raise capital; that has not changed and does not depend on any of
this.

The instrument that measures energy anywhere is the same lens that measures meaning: every emergent
system — an economy, a supply chain, a body of knowledge — is ordered energy, and an aperture reads
the beam wherever it is placed. Markets, meaning, and work are measured by one instrument.

At human scale this closes into something plain. A large need is a steep gradient. People are
operators; they respond because they hold a capability; energy flows down the gradient because the
act improved lives. **The surplus — the lift across many lives, beyond the cost of the act — is the
free energy that mints**, and the beneficiaries themselves are the verifiers. The economy's fixed
point is circulation through flourishing: the more lives improved for the better, the more the
system creates. Value reaches the people who create it, without a custodian in the middle taking
the relationship and a cut of every exchange.

---

## The scale

Agience is the same machinery from one tab to a civilization. It does not grow by making one node
larger; it grows the way fire spreads — by igniting more matter. A single observer holds a working
set and stays bounded. When the work exceeds one machine, the node **breeds**: a population of
bounded observers, each sustained by its own ledger, partitioned by domain, synchronized across a
mesh. This costs nothing to coordinate, because there is nothing to coordinate — each origin keeps
its own time, each observer holds its own view, and agreement is reconciled only when someone looks.
No global clock, no consensus, no central coordinator. Across origins, whole societies of the
system federate the same way: currencies couple at the point of exchange, and joining requires only
a shared axis to measure against, not a negotiation.

The property that makes this hold without limit is built into how information moves. Transformations
stay local; only information propagates far. So the cost of change is independent of the size of the
system, and unattended structure decays away rather than accumulating as dead weight. The system
scales because nothing anywhere requires permission to proceed.

---

## The governance

The system develops its own policies, and it knows the boundary of what it may decide. Most
judgments are objective — resolvable by measurement, by whether repeated accurate readings converge
on a stable value. Where they converge, the system governs itself. Where they do not — where a
question is genuinely undecidable by measurement, or concerns something no instrument can weigh, like
fairness or care — it reaches for human judgment. Human value is spent exactly where measurement
cannot reach, and therefore is never diluted. Each time a person resolves what the system could not,
that resolution becomes part of the standing structure, and the same question resolves on its own
the next time. The system asks for less over time, on every subject it touches.

---

## What it means

Agience is an operating system for truth and fair exchange: **knowledge that is verifiable,
authority that is accountable, and compensation that is just.** The instruments spread freely,
because a trust substrate has to be inspectable and exitable to be trusted at all; the whole system
is open, self-hostable to fully disconnected, and holds no custody of a person's data, name,
identity, or money.

It is one instrument, one store, one path, one economy — and one idea carried all the way down: that
a finite observer, clear about the limits of its own lens, can build knowledge that others who were
not there can trust. That is the oldest problem of knowing, and Agience is its infrastructure.

*Create your agency.*

---

*AGIENCE and CREATE YOUR AGENCY are trademarks of Ikailo Inc. (Toronto, Ontario, Canada). The
lens (Entroptics), the lattice (Mantle), Beam, Crystal and the Prism SDKs are Apache-2.0; Origin,
Ember and Chorus are AGPL-3.0-only; this documentation is CC-BY. The mathematics of the lens and the store is set
out in their dedicated papers and reference implementations.*
