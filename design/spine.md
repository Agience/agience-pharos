# THE SPINE — the locked vocabulary

> This is not the story — it is the vocabulary the story is written in, and the boundary of what
> any surface may claim.
>
> **Every factual claim here carries its measurement and its conditions.** What is still to build
> is tracked in `_scratch/CURRENT/PRODUCT_BRIEFS/GAPS.md`.

---

## 1. The frame

> **Information carries through space, and through time.**
>
> **Entroptics** carries it through space.
> **Mantle** carries it through time.
> **Agience** is the one who is looking.

Three names, three axes, one coordinate frame — and the observer is its origin.

| | what it is | axis | what it answers |
|---|---|---|---|
| **Entroptics** | the signal — the instrument that reads it | **space** | *How much of this is real, and at what resolution?* |
| **Mantle** | the substrate — where it is kept | **time** | *What was known, when, by whom, and on what evidence?* |
| **Agience** | the observer — where someone stands | **the observer** | *Who is looking, what may they reach, and what do they stake?* |

**Why these three and not others.** The two axes that a system can sum over — space and time — each
get an instrument. The third cannot be summed away, because it is where finitude lives: every
observer is somewhere, and no observer is everywhere. So the third axis is not a component of the
system; **it is the frame the other two are measured relative to.** That is why the house name sits
there, and why "Agience" reads as *agency*.

**Self-similar, deliberately.** The same machinery runs at every size — a browser tab and a
knowledge node differ in mass, not in kind. So *an* Agience is an observer at any scale, and the
system as a whole is one too. This is what "we are part of the system" means concretely, and it is
load-bearing rather than decorative.

---

## 2. The third axis — the observer

**The spine is where the observer is defined.** Its *built surface* is
`agience-chorus/src/agience_chorus/astra/web`, whose own README calls it **"an observation plane"**.

**The observer is the seat: a bounded place to stand, from which what is reachable is decided, and
at which judgment is staked.** Three properties, each mechanical rather than aspirational:

**It is bounded, and the bound is the point.** What an observer may reach is computed, not
configured — a walk outward from their grants across the edges of the lattice, deny absorbing. The
system's name for that bounded horizon is the **light cone**, and it is the same object twice: the
limit of what you can see, and the limit of what can be decrypted for you. An observer who cannot
reach a thing cannot obtain the key to it. *Being finite is implemented, not merely acknowledged.*

**Authority is human, and delegation is narrow.** An agent acts on behalf of a person, through
short-lived delegation, never as its own authority.

**Existence comes in degrees.** An artifact is live from the moment it is written. **In the
design, it joins the record continuously:** a thing has **mass** to the degree that observers agree
on it, weighted by what their authority can support, and mass accrues continuously.

**Dark matter, in the design, is a new artifact that has not been observed.** It is a valence-2
vertex: it carries WHO and WHEN (`created_by`, `created_time`) and has no embedding, no event class
and no operator (no WHERE, WHAT or HOW). It gravitates — it inherits grants, orders in time and
counts toward provenance — and it stays dark until first access converts it (§4 of
`information-gauge-db.md`). The design also has it bend routing and add to its region's density.
**Observation is what turns dark matter luminous.** What is built is the storage half: with
`MANTLE_LAZY_INDEX` on (the default indexes on write), a write leaves the artifact latent — stored,
owned, timed and unindexed — and the first authorized read indexes it.

Three consequences worth stating in that order:

- **A claim is not true-or-false in the record; it is heavier or lighter.** Corrigibility is
  mechanical rather than promised — the record moves as evidence arrives, and you can watch it move.
- **Machine output is not blocked; it simply has no mass.** This is stronger than a gate, and the
  distinction matters. A gate is something to be automated around, and a gate everything passes
  through is theatre. Nothing here refuses model output — it just weighs nothing until an observer
  stakes something on it, and there is no way to fake the staking.
- **A gate would have violated the guarantee.** "Draft on this side, record on that side" is a
  **chosen** threshold — somebody picks where the line falls. Mass is *measured*. Removing the gate
  is not a simplification of §3; it is §3 applied to the observer axis.

**The stored artifact state is `live|archived`, and it is lifecycle.** Every write path stores
`live`. `archived` marks a superseded version, or an artifact a user archived, and head resolution and
default recall read past it. The state is an implementation detail of the store. An unobserved
artifact is dark matter, as defined above. Mass is the design for how real a
thing is. Creating an artifact or a container records the observer rung the write earned, derived
server-side from the authenticated principal; no path computes mass from those rungs, and no read
path consults it.

**One line, for reuse:** *A seat is a bounded place to stand. Agience decides what you can reach,
records what you stake, and never pretends you can see the whole.*

---

## 3. The guarantee

> **Measured — never set, never chosen.**
>
> The resolution comes from the signal. The threshold comes from the noise floor.
> The exchange rate comes from the coupling. Nobody picked any of them.

**Both words earn their place, because they name two different failures.**

***Chosen*** is the tuned constant: the hand-picked threshold, the top-*k*, the hop cap, the MAD
multiple, the similarity cutoff, the sweep against a corpus that then changes. Every one of them is
a number somebody guessed, defended by the fact that it worked once.

***Set*** is the legislated quantity: the decreed rate, the configured coupling, the pegged
exchange, the default clock. Every one of them is a number somebody had the authority to declare,
defended by the fact that a system needs *some* value there.

The system refuses both. Where a number is needed, it is read off the frame in front of it; where
no measurement exists, the operation **declines to run rather than substituting a default** — the
demurrage function returns the stock unchanged when no clock has been measured, and does not
back-fill the gap when one later resolves. *"Nobody has measured this yet"* and *"the value is 40"*
are different statements, and the system is built so they never collapse into each other.

**Three older phrasings are retired into this one.** *"Nothing is chosen — every number is a
function of the frame"* · *"rates MEASURED, never configured"* · *"couplings are measured, never
set."* One sentence, everywhere, from here on.

### 3.1 The declared exception, stated rather than hidden

There is exactly one input the instrument does not supply: **the reader's own tolerance for a false
alarm.** It cannot be derived from data, because it encodes what a mistake costs *you* — and that is
not a property of the signal.

So the guarantee has a precise shape, and the shape is the argument:

> **Everything is measured except where you decide — and the system says exactly where that is.**

This is the observer axis doing its job. Space and time are measured; the observer is where judgment
legitimately enters. A system that hid this would be claiming an objectivity nothing finite has. A
system that buried it in a tuned threshold would be making the decision *for* you, silently, and
calling it math. Naming it is the third option, and it is the whole difference between an
instrument and an oracle.

**And the exception is not a concession — it is the point.** The sharpest statement of it in the
whole corpus predates the exception being named, and is lifted from
[`the-story.md`](../start/the-story.md):

> **Human value is spent exactly where measurement cannot reach, and therefore is never diluted.**

With the mechanism that follows it, which is what makes it more than a slogan: each time a person
resolves what the system could not, **that resolution becomes part of the standing structure**, and
the same question resolves on its own next time. *The system asks for less over time, on every
subject it touches.*

---

## 4. The names

### 4.1 Public — may be used freely on any surface

**Agience** · **Mantle** · **Entroptics**. Three names. That is the whole public vocabulary of the
system, and the discipline is to resist adding a fourth.

### 4.2 The commercial boundary — the cut and the aperture

**The boundary is real, implemented and licence-enforced in code.** `agience-mantle/src/mantle/search/beacon/cut.py` states it in its
own licence header, and `tests/test_beacon_cut.py` holds the two halves bit-equal function by
function.

| | what it does | licence |
|---|---|---|
| **The cut** | *Where does the set stop?* Subspace membership and the parameter-free relative-gap break — one read, one decision, no tuning. Ships **with the store** and makes a standalone store genuinely useful on its own | **Apache-2.0** |
| **The aperture** | *The whole stream.* Projection — absorb the band that couples here, transmit the residual, place surfaces on a screen and read their coupling. And prediction — lag, delay embedding, the propagator | **AGPL-3.0 / commercial** |

**Why this is a real boundary and not a crippled build.** The cut answers one complete question and
answers it fully; nothing about it is withheld. The aperture is a different question — continuous
reading, coupling between systems, and forward propagation. A store that takes only the cut is not
running a demo; it is running a finished instrument with a narrower scope.

**How to say it:** *the cut is the read; the aperture is the stream.*

*"The beam & aperture"* is **correct and stays** — `Beam` is a live Entroptics type, one of the
five in its apparatus: the signal itself, and a bundle of beams. It is not a component and not a
package, so it does not belong in a licence list, where it would read as one.

**A licence correction the July story got wrong.** It listed the Apache instruments as
*"Entroptics, Mantle, Beam, Crystal, Prism."* Measured: `entroptics` is Apache-2.0, but
**`entroptics-context` is AGPL-3.0-only and `entroptics-rf` is proprietary** — they are products, not
instruments, which is the two-tier model working exactly as designed.

### 4.2a The product nouns

**the canonical nouns are `Artifact` and `Workspace`.**

**Artifact.** The basic object. Content plus context, carrying its identity, its version history and
its provenance inside itself. **Not "Note"** — `product/copy.ts`'s glossary called it that with
Artifact as an alias, which was a half-finished migration. *Note* concedes the category the
positioning exists to escape, and it fights the claim: a note is something you wrote down; an
artifact carries its own evidence. One word, everywhere.

**Workspace — and it is an artifact too.** *"A Workspace is also an artifact, but it represents the
contents of a screen, and is an editable frame. May contain the vertex of the light cone."*

That definition does three things at once and all three are worth keeping:

- **It is an artifact**, so it is versioned, addressed, granted and provenanced by the same machinery
  as everything else. No second kind of thing.
- **It is a frame** — the contents of a screen, editable. Not a folder and not a container: *a view
  that can be worked in.* Which is why "the workspace is what you are looking through" is literal
  rather than a metaphor.
- **It may contain the vertex of the light cone.** The observer's here-and-now sits in a workspace.
  That is the seat, made concrete — and it is what connects the product noun to §2's third axis
  rather than leaving the axis as philosophy.

**Mass is how a thing joins the record, in the design.** An artifact is live when written, and
**mass carries how far it has joined** — see §2 for what that means and why it makes the story more
consistent rather than less. The stored state `live|archived` is lifecycle and an implementation
detail. An artifact's changes are its versions; there are no commit records.

**`Collection` is KEPT**. Not renamed to `Group`.
`Group` is the industry's word for a set of **principals**, and
Agience's whole authorization model lives in principal-space. *"Share this Group with that group"* is
the sentence that decides it.

### 4.3 The persona roster is routing, not a public surface

The persona names are internal routing. The host mounts each one at `/<name>` and holds no roster of
its own, so the names describe where a request goes inside the system rather than what a reader is
offered. A public surface leads with the capability, not with the name of the process that serves it.

## 5. The values

Nine values make up the ethos on `agience.ai`, as the company wrote them: *the values we hold as a company, as a community, and as builders of infrastructure that other people's work depends on.* They describe Ikailo's relationship with society. They are not product properties, and no surface restates them as mechanisms.

| value | as written |
|---|---|
| **Ethics** | *Integrity in every layer.* We uphold transparency, fairness, and accountability in everything we build. The interests of the people who use the platform come first. |
| **Dignity** | *Humans beyond their output.* Human worth extends well beyond what a person produces or automates. We build tools that serve people, and we take seriously what that means as AI takes on more of what humans once did. |
| **Truth** | *The truth economy.* We believe economics can drive truth. When verified, provenance-linked information commands more value, the incentive to be accurate becomes financial. Agience is built so that truth is the highest-value commodity. |
| **Non-Bias** | *Objective by design.* We work proactively to prevent discriminatory outcomes in the design of the platform, the training of models, and the governance of the community. Bias is an engineering problem as much as an ethical one. |
| **Accessibility** | *Built for everyone.* We build tools that are accessible regardless of physical ability, cognitive style, or technical background. Everyone who needs this should be able to use it. |
| **Inclusivity** | *All voices, by design.* Technology built with diverse input is more accurate, more useful, and more trustworthy. We design for the full range of human experience from the start. |
| **Equality** | *A levelling field.* We build technology that amplifies fairness across race, gender, socioeconomic status, and geography. Access to trusted AI infrastructure should be broadly available. |
| **Sustainability** | *Long-term responsibility.* We take seriously the environmental impact of the infrastructure we build and the compute we consume. Sustainability is a condition of building responsibly. |
| **Sovereignty** | *Your knowledge, your infrastructure.* You own your data, your collections, and your deployment. Self-hosting is a first-class option. No lock-in, no forced dependency on our infrastructure. The platform is open source because trust requires inspectability. |


---

## 6. The words

**Say:** measured · derived · read off · resolution · aperture · the cut · the stream · artifact ·
grant · provenance · light cone · the record · mass · weight · stake · observe · observer · self-host ·
non-custodial · **educating, not training**.

**Do not say:** ground truth · *the* source of truth · objective · unbiased (say *no tuned
constant*) · AI-powered · military-grade · zero-knowledge (not what the
scheme is) · blockchain — *as a description of what the platform is*; the economy's settlement
layer is a separate thing and is named as such · *"trust us."*

**Retired outright:** the `GENESIS` byline and every creation-narrative framing · *"storage gets ciphertext"* unqualified · *"the traversal that authorizes a query is the
same derivation that produces the decryption keys"* (§2.1) · *"Facet is
designed and its repository is absent"* · `membrane` as a public name — the optical surface is
the **screen**, and a policy or trust boundary is a **boundary** · `beam` as a package or a
repository — the measurement it named is `ember.optics`, and a beam is the signal.

**Recover before use:** *"We are not training. We are educating."* Survives only in an archived
document. It is the sharpest sentence available against a market that knows one word, and it should
be restored to canon and then used everywhere.

**This list is enforced by a reviewer, not by a gate — and that was measured, not assumed.**
2026-08-24: a mechanical check for these phrases was built and run over every surface. It produced
**7 hits, all 7 false.** *"Four experiments, each with a known ground truth"* is the scientific sense;
*"everyone is selling you ground truth — nobody has it"* is the thesis stated by naming what it
rejects. **The distinction this list draws lives in what a sentence is doing, not in which words
it contains**, so an automated check was discarded rather than shipped with a baseline of noise.

**One rule that outranks all of them.** *A surface says what the system does, in the present tense,
and every number carries its conditions.* What is still to build is tracked in
`_scratch/CURRENT/PRODUCT_BRIEFS/GAPS.md`; each line there is a present-tense statement a surface makes that the code does not yet back.

---

## 6.5 The lines

> # Harness the universe.
> # Create your agency.

The pair is the three axes compressed into two lines: the first is
the **instruments** — Entroptics through space, Mantle through time — and the second is the
**observer**. The same sentence from two ends: the world, and your position in it.

**Why `harness` is the right verb and not a near-miss.** You harness a river. You do not own it or
comprehend it. The word claims **capability without claiming mastery**, which is the finite-observer
thesis in one word — where *understand the universe* or *know the universe* would claim exactly the
objectivity this system exists to deny. It also picks up the economics, where harnessing energy is
literal rather than figurative.

**It does not compete with `The universe, in focus.`** That is the instrument's line: Entroptics
brings the universe into focus, and Agience harnesses it. One is the read, the other is the use.

**The one rule that keeps it grounded.** This is cosmic register, and it is quarantined
— **for body copy, not for taglines.** A tagline may reach; a paragraph may not. *Harness the
universe* works set over *"You remember deciding it. You can't find it."* precisely because the
line beneath it is so plain. **The moment the body copy starts reaching too, the tagline stops
being a promise and becomes the whole message** — which is the failure the site's first draft
actually made.

---

## 7. The one line, and the close

> **Objectivity is infinite. Your lens is finite. Agience is the instrument in between.**
>
> **Create your agency.®**

Both survive from the July document unchanged. They were already right; the rest of the spine is what
makes them earned.
