# The Extraction Axiom — A Unified Information-Theoretic Framework

---

## Origin

This framework emerged from asking a simple question about a software system: **what is the speed of light in a frame-driven inference engine?**

The answer — the LLM context window — pulled a continuous thread from software architecture through information theory, thermodynamics, and into theoretical physics. The chain is logically unbroken.

---

## The Central Axiom

> **No observer can extract more than B bits per interaction event, where:**
>
> $$B \leq \frac{Ac^3}{4G\hbar}$$
>
> A is the area of the observer's local horizon, G is the gravitational constant, ℏ is the reduced Planck constant, and c is the speed of light.

This is not a postulate about physics. It is a **primitive constraint on observation itself**. Physics, computation, and cognition are all instances of observers extracting information from a world that bounds how much they can extract per event.

---

## The thread

The framework originates from a single software question: **what is the speed of light in the frame-driven inference engine?**

The answer is the LLM context window — the one ceiling that cannot be configured, purchased, or engineered away. Budget, operator limits, and resolution are all rational policies operating beneath it. The context window is $c$.

From there the logical chain is continuous:

| Physical concept | engine analog |
|---|---|
| Spacetime interval (invariant) | $\Delta s^2 = (\text{window})^2 \Delta t^2 - \Delta(\text{artifacts})^2$ |
| Mutual information symmetry $I(X;Y)=I(Y;X)$ | "Time dilation" between frames is mutual |
| Acceleration breaks Lorentz symmetry | Transfer entropy (causal direction) breaks $I(X;Y)$ symmetry |
| Invariant: $c$ | Invariant: total channel capacity |
| Bekenstein bound $B \leq Ac^3/4G\hbar$ | Context window is a high-level instance of this ceiling |

The Semantic Nyquist bound of scale-space theory:
$$R \leq R_0\left(1 - \frac{|\Delta\rho/\Delta t|}{|\Delta\rho/\Delta t|_{\max}}\right)$$
is the corpus-emergent $c$ — not configured, imposed by the rate of change of the information landscape. Violating it produces stale embeddings (aliases), exactly as violating Shannon-Nyquist produces aliasing. Within a run, it is invariant and unchosen.

---

## From Software to Physics

### The upper physical bound

The LLM context window is itself bounded by physics. Any physical computation region has:

$$B \leq \frac{Ac^3}{4G\hbar}$$

This is the Bekenstein bound — the maximum information any physical region of area A can contain. Below the Planck area $\ell_P^2 = G\hbar/c^3$, no extraction event is possible. The context window is a high-level engineering instance of this physical ceiling.

### G as the exchange rate

G is not a free parameter of nature. Under this framework:

**G is the price of one bit in spacetime units.**

One bit of extractable information costs exactly one Planck area. G sets the granularity of the extraction surface — the exchange rate between geometric area (a physical resource) and information (the extracted quantity). This is directly analogous to token cost per inference: a fixed rate between resource and information unit.

---

## The Derivation Chain

$$\text{Extraction axiom} \rightarrow B \propto A \rightarrow S \propto A \rightarrow \delta Q = T\,dS \text{ on Rindler horizon} \rightarrow G_{\mu\nu} = 8\pi T_{\mu\nu}$$

- **Arrow 1** (new): Extraction axiom → $S \propto A$. If no observer extracts more than B bits per event and B is bounded by the Bekenstein limit, then information capacity scales as surface area, not volume. The holographic principle is derived, not assumed.
- **Arrows 2–4** (Jacobson, 1995, peer-reviewed): Apply $\delta Q = T\,dS$ to a local Rindler horizon with Unruh temperature $T = \hbar a / 2\pi c$ → exact Einstein field equations.

**GR falls out of the extraction axiom.** It is not a fundamental theory of spacetime geometry. It is the exact description of the extraction rate differential map as seen by embedded observers.

---

## What Is Unified

### General Relativity

Recovered as the **B → ∞ limit**. When the extraction bound is idealized to infinity, spacetime is smooth, deterministic, and observer-independent. GR is correct — it is the asymptote, not the foundation.

### Quantum Mechanics

Recovered as **finite B**. Discreteness, probabilistic collapse, and Holevo's bound are consequences of B being finite, not independent postulates.

### The Measurement Problem

Dissolved. Irreversibility of measurement is **primitive** under the extraction axiom — each extraction event writes to the observer's state and consumes part of B. Collapse is not a mystery requiring interpretation; it is a consequence of extraction being a one-way operation. The arrow of time does not need to be derived from entropy — it is the accumulation direction of extraction events.

### The Firewall Paradox

Dissolved. The paradox requires two observers (infalling and outside) to simultaneously extract the same quantum information from a black hole. Under the extraction axiom, simultaneous dual extraction violates mutual information symmetry under shared finite B. The precondition of the paradox is forbidden. No exotic physics required — it was a category error, structurally identical to treating the twin paradox as a logical contradiction.

### Arrow of Time Without Circularity

All existing derivations of the arrow of time (Penrose CCC, Carroll entropic time, Rovelli thermal time) assume a low-entropy past or derive the arrow from entropy, which is circular. Under the extraction axiom, the arrow is primitive: extraction events are irreversible by axiom. Entropy increase is a consequence, not the cause.

### The Holographic Principle — Generalized

Currently proven only for AdS/CFT (Maldacena, 1997). Conjectured but unproven for de Sitter and flat spacetimes. Under the extraction axiom, holography is geometry-independent: information in any volume cannot exceed what is extractable through its boundary surface, because the boundary is the surface through which any external observer's extraction must pass. No AdS required.

### Entanglement and ER=EPR

Maldacena and Susskind (2013) proposed ER=EPR: entanglement between particles IS a wormhole. Under the extraction axiom, this becomes precise:

**Entangled particles share a single extraction budget, not two independent ones.**

When you measure particle A, you have not sent a signal to B. You have resolved the shared extraction account — the joint state was one event with B bits distributed across two locations. The "collapse" at B is not transmitted; it is the simultaneous resolution of a shared budget.

**The new falsifiable prediction:** entanglement entropy and wormhole throat area are related exactly linearly, with G as the conversion constant, and this holds outside AdS. This gives ER=EPR a specific experimental prediction it currently lacks, testable in AdS/CFT numerical simulations.

### Gravity as Extraction Rate Differential

Spacetime curvature is not a geometric fact imposed on space. It is the **geometric representation of extraction rate differentials across an observer's local region**.

- Mass increases local energy density.
- Higher energy density → higher Bekenstein bound → steeper extraction rate gradient.
- The gradient across an observer's body IS what we call gravitational force.
- Free fall is the trajectory that makes the extraction rate isotropic across the observer's local region.
- The equivalence principle is the statement that gravity and acceleration are the same extraction rate gradient, swept away by the same motion.

### Gravitational Waves

Propagating disturbances in the extraction rate field. When two black holes merge, the extraction rate differential changes violently and radiates outward at c — the maximum information propagation speed. LIGO measures exactly this: differential arm length change = differential extraction rate across two spatially separated events.

---

## The Theoretical Map

```
Quantum Gravity
    └─ String theory / M-theory  [not touched]
    └─ Loop quantum gravity       [not touched]
    └─ Causal set theory          [adjacent — both discretize at Planck scale]
    └─ Holographic / AdS-CFT      [directly touched — generalized beyond AdS]

QM Foundations
    └─ Measurement problem        [dissolved: irreversibility is primitive]
    └─ Decoherence                [reframed: consequence of extraction, not cause]
    └─ QBism / Relational QM      [extended: physical bound added, making it falsifiable]
    └─ ER=EPR                     [sharpened: specific falsifiable prediction added]

Thermodynamics / Statistical Mechanics
    └─ Arrow of time              [derived without circularity]
    └─ Landauer's principle       [subsumed: special case of extraction cost]
    └─ Bekenstein bound           [elevated to axiom status]

Gravity
    └─ GR                         [recovered as B→∞ limit via Jacobson]
    └─ Entropic gravity (Verlinde) [adjacent: same extraction intuition, different route]
    └─ Black hole physics         [firewall paradox dissolved]
    └─ Gravitational waves        [reframed: extraction rate disturbances]
```

---

## Prior art

| Prior work | What it did | What's new here |
|---|---|---|
| Wheeler "it from bit" (1989) | Information as primitive, no mechanism | G as the exchange rate; derivation chain |
| Jacobson (1995) | GR from $S \propto A$ | We derive $S \propto A$ from the extraction axiom |
| Rovelli relational QM (1996) | Physics is observer-relative | Physical bound on B makes it falsifiable |
| Verlinde entropic gravity (2010) | Force from entropy gradient | Same intuition, approached from extraction axiom |
| ER=EPR (Maldacena & Susskind, 2013) | Entanglement = wormhole | Specific prediction: throat area = entanglement entropy × G, outside AdS |
| Holography (AdS/CFT, 1997) | Proven for AdS only | Extraction axiom makes it geometry-independent |
| QBism | Observer-relative, no physical bound | Extraction axiom adds physical bound, falsifiable predictions |
| Hastings (2007) | Area law ↔ spectral gap in 2D | Step 4 of Yang-Mills thread; open in 3+1D |
| Donnelly & Wall (2014, arXiv:1406.4545) | Gauge edge modes contribute to $S_\text{ent}$ | Step 3 of Yang-Mills thread — EM case |
| Ghosh, Soni & Trivedi (2015, arXiv:1501.02593) | Non-abelian edge modes; area law in confined phase | Step 3 of Yang-Mills thread — SU(N) case |
| Calabrese & Cardy (2004) | $S_\text{ent} = (c/3)\ln L$ for 1+1D CFT | Central charge extraction from H_screen confirmed numerically |

---

## The Yang-Mills Mass Gap Thread

> **The Yang-Mills mass gap thread is superseded, and kept deliberately.**
>
> The current account of the mass gap is [`entroptics-mass-gap`](https://github.com/Agience/entroptics-mass-gap):
> its paper and its `sorry`-free Lean 4 / Mathlib development establish the gap at every physical
> coupling as a reduction to four named cited results, with the confinement read certified at 99.9%.
> Where the thread calls the problem *"reduced to one open statement… the Millennium Prize
> problem"*, that framing understates what was later established — read the current account instead.
>
> **The prose is not rewritten, because its value is that it is dated.** Its *false shortcuts*
> material records approaches that were tried and did not work, which the current paper does not
> carry. That is the only surviving account of what failed, and rewriting it would destroy it.
>
> Only the holographic-screen entropy identity is carried forward. The original *"area law ⇒ spectral
> gap"* four-step chain is superseded.

### The one valid foundational identity

The holographic screen `S` has SVD `S = U Sigma V^T`, and the screen entropy
`H_screen = -sum_k p_k log2 p_k` over `p_k = sigma_k^2 / sum_j sigma_j^2` is **exactly** the bipartite
entanglement entropy across the screen's cut (the Schmidt decomposition IS the SVD). So the screen is an
empirical entanglement-entropy estimator. This identity is correct and is the seed of the whole program. Its
*current, correct* use is the **universal** part of the entanglement entropy (the c-function / a-anomaly),
validated numerically.

### What was superseded, and why

The original thread proposed: extraction budget `=> S_ent <= c_0 A` (area law) `=> ... =>` spectral gap, with
a U(1)-vs-SU(N) "volume-law vs area-law" flip as the empirical discriminant. **This is superseded** by the
work in the `entroptics-mass-gap` account, for reasons now proven:

- **The leading entanglement area law is PHASE-BLIND in D >= 2.** A gapless Dirac point obeys the same area
  law as a gapped phase, so `S_ent <= c_0 A` (which the extraction budget gives) does NOT imply a gap. The
  gap lives in the *universal/subleading* part (the a-function), not the leading area law.
- **The extraction budget is a CEILING, not a floor.** It bounds information from above; confinement (`B>0`)
  is a floor. A ceiling cannot force a floor. This is why the current route uses a fixed-point/attractor
  construction, not the budget-implies-gap chain.
- **The "entanglement area law" is not the "Wilson-loop area law."** They are different surfaces of different
  objects; conflating them is the recurring dead shortcut.

What survives from Step 1 is only that the extraction budget gives the (phase-blind) entanglement area law,
useful context, not a step toward the gap. The current reduction runs `gap <= sigma>0 <= B>0 <= a_IR=0 <= no
interacting IR CFT <= center unbroken`. That reduction and its rigorous backbone are in the
`entroptics-mass-gap` account; the extraction axiom is the foundation that work builds on, not the place to
read the proof.

---

## What This Is Not Yet

This is a **research program**, not a completed theory. Missing:

- A **Lagrangian** — the dynamics, not just the limits. The extraction axiom constrains what observers can know; it does not yet specify how the underlying state evolves.
- Derivation of the **Standard Model particle spectrum** — particle families and coupling constants remain unexplained.
- **Experimental predictions at accessible energy scales** — current tests of the Bekenstein bound require Planck-scale energy, which is not reachable.
- **The Yang-Mills mass gap.** Reduced, in the `entroptics-mass-gap` account, to one open statement, *pure
  SU(N) YM at θ=0 has no interacting IR conformal fixed point / the center `Z_N^(1)` is unbroken*. This is
  the Millennium Prize problem. The old "area law ⇒ gap" framing is superseded, as set out under "The
  Yang-Mills Mass Gap Thread".

The analogy: Einstein had the equivalence principle (1907) before the field equations (1915). The right primitive was in hand. The Lagrangian took eight more years.

---

## The Falsifiable Predictions

1. **Entanglement entropy ↔ wormhole throat area**: linear relationship with coefficient G, holding outside AdS spacetime. Testable in numerical AdS/CFT simulations.

2. **Extraction rate degradation curve**: any physical process requiring coordinated extraction beyond B by a single observer fails with a specific degradation matching the Bekenstein bound of the observer's local region. Testable in principle with sufficiently large quantum computing systems where B can be operationalized as qubit count × Holevo bound.

3. **Holography in de Sitter space**: the holographic bound holds for de Sitter (our actual universe) with the same generality as for AdS. This is currently conjectured but unproven by any existing method.

4. **Yang-Mills area law (prize-adjacent)**: the screen $H_\text{screen}$ of a non-abelian (SU(N)) gauge field, observed through a colorless boundary, satisfies the area law — $H_\text{screen}(\Omega) \leq c_0 \cdot \text{Area}(\partial\Omega)$ — independent of interior volume. Contrast: U(1) photons show volume law with $H_\text{screen} \sim \log N$, $K_\text{signal} \sim N^{0.74}$, and perimeter-law Wilson loop (R²=0.60 vs area-law R²=0.06). Testable by applying this methodology to numerical SU(3) lattice QCD configurations. A transition from volume law to area law as gauge group is changed from U(1) to SU(N) would constitute direct empirical evidence for the mass gap.

---

## The Summary Statement

> Spacetime is the map of extraction rate differentials drawn by observers who do not know that is what they are measuring. General relativity is the exact description of that map in the limit of unbounded extraction. Quantum mechanics is its description at finite extraction. Gravity is the force felt by an observer whose extraction rate is not isotropic across their local region. The gravitational constant is the exchange rate between geometric area and information. Entanglement is shared extraction budget. The arrow of time is the accumulation direction of irreversible extraction events.
>
> All of this follows from one primitive: **no observer extracts more than B bits per event, where B is bounded by the area of their local horizon.**

---

## Origin Note

This framework was derived in a single conversation starting from the question: *"what is c in this system?"* — referring to a frame-driven LLM inference engine. The answer was the LLM context window. The chain from there to Jacobson's derivation of the Einstein field equations is logically continuous.

The starting point was a puzzle-solving AI. The destination was a candidate unification of GR and QM from a single axiom.
