# ENTROPTICS — A screened spectral aperture with derived nulls

## What a frame resolves, at its own entropy scale, against a data-derived null

**Agience · Ikailo Inc. (Toronto / Ontario, Canada) · John Sessford**

**AGIENCE** and **CREATE YOUR AGENCY** are trademarks of Ikailo Inc., registered in Canada.

---

## Abstract

Given an ordered $(T, F)$ frame, a screened spectral aperture reports **how many modes rise above a
derived noise floor, within what certified interval, at what per-mode decay rate, and with what
signed coupling to another frame.** The grid each read is taken on comes from the entropy of the
frame's own power, so the signal sets its own resolution. **The one decision input from outside is a
false-alarm level**, and it travels with the null it belongs to; every other declared input is named
at one boundary and published with the run that used it (§105).

Nothing in the read is fitted or calibrated to a substrate. The floor is the finite-size
Johnstone/Tracy–Widom edge of an iid ensemble at the declared level; the interval is matrix
concentration plus Weyl; the decay rates are eigenvalues of a reduced operator, exact when the
dynamics are linear. The single tabulated constant is a table of the $\mathrm{TW}_1$ distribution.

**The same read is validated on four independent carriers.**

| domain | headline |
|---|---|
| seeded ground truth | operator recovery to **4.49e-16**; planted-mode accuracy **1.000** at snr 1–2 across four shapes; $K=0$ specificity **0.950** |
| lattice gauge configurations | 231 ensembles, **11,756 configurations, 11.13 GB**. U(1) steps, SU(2)/SU(3) flat; transition certified at $\beta_c = 1.011$, confined ceiling below the Coulomb floor at **95%** |
| dynamical systems | exact identification at **800–5000× fewer parameters** than a trained network, better forecast |
| a trained model's boundary | retrieval cut F1 **0.630 vs 0.291**; per-token attribution **87.33%** against 33.3% chance; a 41-point gap at a 20% budget from projection alone |

The governing law those boundary results establish: **a subspace read beats a summary statistic
exactly when the deciding distribution is multi-modal, and the margin grows with the number of
modes.** Where it is unimodal the two tie.

**Where the read applies is established empirically, not asserted.** §18 sweeps the interior of a
trained network as well as its boundary, and the result is clean: the wins are at the boundary —
choosing what enters and scoring what leaves — because a gradient-owned interior compensates for any
geometric substitution made in it. That sweep is what fixes the scope, and it is reported in full.

**Declared inputs are named at one boundary and published with the run.** §105 carries the complete
roster with the measurement that retires each. Two entries in §12 — the relative rank cutoff
$10^{-10}$ and the $2F$ pair-count trigger — still move the recovered mode count, and the derivation
that replaces them (applying the derived floor $\Phi$ to the $P_{xx}$ spectrum) is named there.

---

## How to read this

| Part | What it gives you |
|---|---|
| **II** | The instrument: five nouns, the entropy scale, the derived floor, the certified interval, the operator, and the single enforced import seam. |
| **III** | What it recovers: seeded ground truth, the mass gap and the lattice-gauge measurement, deterministic dynamics, the three regimes, and the reads at a trained model's boundary. |
| **XII** | The method: the three legitimate external inputs, what a legitimate test looks like, five failure modes, three excuses that always work, and the full provenance of every number. |

Part XII is not an appendix. On a claim of this shape the method **is** the contribution as much as
the numbers are, and §102–§103 are a record of real errors caught in this work, each with the
measurement that exposed it.

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
# PART II — THE INSTRUMENT

**Entroptics** is a screened spectral aperture. Given an ordered $(T, F)$ frame it reports how many modes rise above a derived noise floor, within what interval, at what per-mode decay rate, and with what signed coupling to another frame — each read from the data's own spectrum against a data-derived null. The one input from outside is a false-alarm level $p_{\mathrm{FA}}$, travelling with the null it belongs to; every other declared input is named where it stands (section 12).

**Symbols.** One symbol, one quantity, throughout Part II:

- $T$ — the ordered-axis length — the number of samples, the rows of the frame
- $F$ — the feature count — the columns of the frame
- $F_s$ — the column count of the screen the singular values are taken on (section 10)
- $W$ — the frame itself, a $(T, F)$ array; $S_t$ its row at ordered index $t$
- $\Phi$ — the derived noise floor (section 10)
- $s_k$ — the singular values of the entropy-folded, MAD-whitened screen (section 10)
- $\lambda_k$ — the eigenvalues of the unit-diagonal correlation, descending
- $\lambda_+$ — the Marchenko–Pastur bulk edge of that correlation (section 10)
- $\varepsilon$ — the relative floor, $\varepsilon = \lambda_+ / \lambda_1 = 1 / \text{contrast}$
- $\alpha$ — the attenuation read, $\alpha = \log\left(\lambda_1 / \max(\lambda_2, \lambda_+)\right)$
- $m_J$, $\varsigma_J$ — the Johnstone centering and scale (section 10)
- $p_{\mathrm{FA}}$ — the declared false-alarm level; $q_{\mathrm{FA}}$ its Tracy–Widom-1 upper quantile
- $z_k$ — the eigenvalues of the reduced operator $\tilde{A}$ (section 12)
- $r_k$ — the per-mode decay rate, $r_k = -\log|z_k|$
- $\xi$ — the correlation length, $\xi = 1/r_1 = 1 / (-\log|z_1|)$ (section 12)
- $c$ — the leading constant of the matrix concentration inequality (section 11)

## 8. Five nouns

```
[ aperture > beam ] --lens--> SCREEN <--lens-- [ beam < aperture ]
```

**BEAM** — the carrier. A beam decomposes into modes and **each mode is a beam**, carrying `energy` (by its own law, about its own zero), `flow` (that energy per ordered step), `basis` (the $(D,k)$ directions it spans), `profile` (the amplitude along each), and `etendue` $= \varphi_T \cdot \varphi_F$ at every depth. Splitting a mode off reads `modes[k].frame` and merging sums frames — exact inverses, because `frame` is the sum of its modes' frames by orthonormal construction.

**APERTURE** — what bounds a beam and the read of everything about it. Streaming-first and adaptively forgetting: the window is a minimum.

**LENS** — a side's conversion into the shared basis, carrying that side's own `entry`, `inverse`, `energy` law, `zero`, and `null`. §32 of `architecture.md` states which system object realises a lens and where each is declared.

**SCREEN** — the measuring surface. A Screen instance is a per-(node, subject) accumulator identified by a content address; two observers share a surface exactly when they place lenses on the same instance. 

**PROJECTION** — the screen as one side sees it, on that side's own entropy-matched grid. 
### 8.1 Entropy sets the scale

Every read the instrument reports is taken on the grid the entropy of the frame's own power sets.

Let $P = |W|^{2}$ elementwise over the cells that carry a measurement, and $S_P$ the total power. A nonfinite or masked cell is **absent**, not zero: it contributes no power, and it is not a cell of the frame. The distinction is invisible in the entropy itself, since $0\log 0 = 0$, and decisive everywhere the entropy is compared against the size of the axis it was read on. An all-zero but finite cell is *not* absent — zero is an observation of no power there, and it counts. The ordered and feature power marginals are the probability vectors

$$
p^{T}_{t} = \frac{1}{S_P} \sum_f P_{tf}
\qquad\qquad
p^{F}_{f} = \frac{1}{S_P} \sum_t P_{tf}
$$

with entropies $\mathrm{H}_T = \mathrm{H}(p^{T})$ and $\mathrm{H}_F = \mathrm{H}(p^{F})$. The effective mode counts and matched cell scales are

$$
n_a = \operatorname{round}\left(2^{\mathrm{H}_a}\right)
\qquad
\delta_a = \frac{L_a}{2^{\mathrm{H}_a}}
\qquad
a \in \{T, F\}
$$

where $L_a$ is the **measured extent** of axis $a$: the number of positions along it holding at least one present cell, $L_F = \#\{f : \exists\, t,\ (t,f) \text{ present}\}$ and likewise $L_T$, each floored at $1$. When $S_P = 0$ the marginal is taken maximally spread, $\mathrm{H}_a = \log_2(L_a)$ and $\delta_a = 1$.

The extent is measured rather than declared because $\mathrm{H}_a$ is never used alone — it appears only against $\log_2(L_a)$, in the maximally-spread fallback, in the finite-sample guard, and in $\delta_a$. Counting a position nothing was observed at raises the bar a signal must clear while supplying no signal, so a frame padded with absent positions reads as more spread, and eventually as unresolvable, purely by being padded. A wholly unmeasured frame then has $L_a = 1$ and reports $0$ bits, which is what was observed, rather than the maximal $\log_2 L_a$ bits of spread asserted from nothing.

Two extents follow, and they are not interchangeable. $n_a$ is a resample target and is expressed in the frame's own coordinates; $\delta_a$ is an information ratio, measured positions per resolved cell, and both of its sides are the measured extent. Where no cell is absent the two coincide and every expression here is unchanged.

Bright rows dominate the marginal however brief they are, so a short burst in a long idle window sets the scale.

$\delta_a$ is the reciprocal of the occupied fraction: a signal concentrating its power into $2^{\mathrm{H}_a} \ll L_a$ effective cells is **oversampled by $\delta_a > 1$**, which is exactly the factor by which it may be folded back onto its own support.

**Only the feature axis folds.** The ordered axis keeps native spacing: $\delta_T := 1$, $n_T := T$, always. The ordered reads are all lag statistics — coherence, decay, the optical transfer function, the per-mode rates — and a lag statistic is defined against native spacing; folding that axis would rewrite the spacing it measures.

**The finite-sample guard.** A structureless feature marginal sits below $\log_2(L_F)$ on any finite record, by plug-in bias alone. The scale therefore holds at $1$ whenever $\mathrm{H}_F$ lies inside the uniform-null band

$$
\beta_F = \min\left( \frac{L_F-1}{2 L_T \ln 2} , \; \tfrac{1}{2}\log_2 L_F \right)
$$

The inner term is the Miller–Madow bias for $L_F$ bins at $L_T$ samples — the bins and samples that exist, since a bias correction for bins that were never observed corrects for nothing. The cap keeps the guard active for wide, short data, where the first term alone would swallow the axis; under it the fold engages once power concentrates below $\sqrt{F}$ effective channels.

**Continuity licenses a fold.** A narrow line on a frequency axis is concentrated *and* continuous: it folds to its own width losslessly, because neighbouring cells are neighbouring quantities. A handful of unrelated active channels is equally concentrated and belongs at native resolution: its axis is nominal, adjacency is an accident of labelling, and folding would sum quantities sharing nothing but a column index. **Concentration says how far a fold may go; continuity says whether folding is licensed at all.** Continuity is read by the coherence z-score of section 9, applied across the feature axis.

## 9. What a read reports

| read | exact statistic | matrix |
|---|---|---|
| `K_signal` | $\#\{\, k : s_k > \Phi \,\}$ | entropy-folded, MAD-whitened screen |
| `k_frac` | $K_{\mathrm{signal}} / F_s$ — the resolved fraction, with $F_s$ stated | entropy-folded, MAD-whitened screen |
| `resolved_modes` | $\#\{\, k : \lambda_k > \lambda_+ \,\}$ | unit-diagonal correlation |
| `contrast` | $\lambda_1 / \lambda_+$ | correlation |
| $\varepsilon$ | $\lambda_+ / \lambda_1 = 1 / \text{contrast}$ — the relative floor | correlation |
| `top_share` | $\lambda_1 / \sum_j \lambda_j$ | correlation |
| `strehl` | $\lambda_1^{T} / \sum_j \lambda_j^{T}$ | **ordered-axis** correlation |
| `coherence` | lag-1 z-score against the exact Cliff–Ord/Mantel permutation null | folded screen row-Gram |
| `attenuation` $\alpha$ | $\log\left(\lambda_1 / \max(\lambda_2, \lambda_+)\right)$ | correlation |
| `dominance` | $(\lambda_1 - 1)/(F - 1)$ | correlation |
| $\varphi_T$, $\varphi_F$ | $2^{\mathrm{H}(\bar\lambda)} / L_a$ — entropic fill | axis correlation |
| `etendue` | $\varphi_T \cdot \varphi_F$ | — |
| `space_bandwidth` | $n_F \cdot n_T$ | — |

`k_frac`'s denominator is $F_s$ (section 10), the number of modes the count could have reached. A call site that wants `K_signal` and computes `resolved_modes` reads a different matrix against a different threshold. `K_signal` is a cardinality — report a fractional value as `k_frac`, or as an ensemble mean with the ensemble size stated, never under the bare name. `coherence` is a permutation z-score and may exceed $\pm 1$. **Three things here are named "coherence" and they read different axes.** Always write the negative control.

## 10. The noise floor is derived

Let $F_s$ be the column count of the screen the singular values are taken on: $F$ when the fold does not engage, $n_F = \operatorname{round}(2^{\mathrm{H}_F})$ when it does. Centering, scale, and the $\chi^2$ correction are functions of that shape; at the pre-fold $F$ they floor the wrong matrix whenever the fold engages.

$$
\begin{aligned}
m_J &= \left(\sqrt{T-1} + \sqrt{F_s}\right)^{2}
    && \text{Johnstone centering} \\
\varsigma_J &= \left(\sqrt{T-1} + \sqrt{F_s}\right)\left(\tfrac{1}{\sqrt{T-1}} + \tfrac{1}{\sqrt{F_s}}\right)^{1/3}
    && \text{Johnstone scale} \\
\hat\sigma^{2} &= \frac{\operatorname{median}_t \|S_t\|^{2}}{F_s \, c_F \, (T-1)/T},
    \qquad c_F = \left(1 - \tfrac{2}{9F_s}\right)^{3} \\
\Phi &= \sqrt{\hat\sigma^{2}\left(m_J + q_{\mathrm{FA}} \, \varsigma_J\right)}
    && \text{$q_{\mathrm{FA}}$ from Tracy–Widom-1} \\
K_{\mathrm{signal}} &= \#\{\, k : s_k > \Phi \,\}
\end{aligned}
$$

$s_k$ are the singular values of the entropy-folded, MAD-whitened screen — the frame folded on the feature axis by the scale of section 8.1, then scaled column by column against its own median absolute deviation:

$$
\begin{aligned}
\mathrm{med}_f &= \operatorname{median}_t W_{tf} \\
\mathrm{MAD}_f &= \operatorname{median}_t \left| W_{tf} - \mathrm{med}_f \right| \\
\hat\sigma_f &= 1.4826 \cdot \mathrm{MAD}_f
    && \text{consistency factor for the normal case} \\
\tilde{W}_{tf} &= \frac{W_{tf} - \mathrm{med}_f}{\hat\sigma_f}
    && \text{with $\tilde{W}_{\cdot f} := 0$ where $\mathrm{MAD}_f = 0$}
\end{aligned}
$$

$s_1 \ge s_2 \ge \ldots$ are those singular values, descending. Section 9's correlation read thresholds a different matrix: for a unit-diagonal correlation of a $(T, F)$ frame the bulk edge is Marchenko–Pastur, a function of shape alone,

$$
\begin{aligned}
\lambda_+ &= \left(1 + \sqrt{F/T}\right)^{2}
    && \texttt{resolved\_modes} = \#\{\, k : \lambda_k > \lambda_+ \,\}
\end{aligned}
$$

$c_F$ is the Wilson–Hilferty $\chi^2$ median correction. The single tabulated input is the $\mathrm{TW}_1$ upper-quantile table `{0.10: 0.4501, 0.05: 0.9793, 0.025: 1.3675, 0.01: 2.0234}`, keyed by $p_{\mathrm{FA}}$; any other level is obtained by bisecting the Chiani survival function.

An equivalent evidence form: $g_k = (T \lambda_k - m_J)/\varsigma_J$, $p_k = P(\mathrm{TW}_1 > g_k)$, and $K_{\mathrm{signal}} = \#\{\, p_k < p_{\mathrm{FA}} \,\}$ exactly when the floor quantile is inverted from the same survival function.

**The floor is a caller-suppliable, locally-evaluated callback.** Four ship: the finite-size Johnstone/TW edge; a Tukey upper fence; a closed-form Gaussian reference from a signal-free window ($\text{center} + z(p_{\mathrm{FA}}) \cdot \text{scale}$, $O(1)$, no stored samples); and distribution-free permutation surrogates. Three cut points route apart: `projection`, `spectral`, `bulk`. **The library does not calibrate the caller's null** — a physics null, a confined-vacuum reference, a phase-randomised surrogate are written by the caller and passed in.

## 11. The certified interval

Matrix concentration bounds the empirical correlation:

$$
\|\hat{C} - C\|_2 \le c \cdot \|C\| \cdot \left( \sqrt{F/T} + F/T \right)
$$

$c$ is that inequality's leading constant at the declared failure probability — the same $p_{\mathrm{FA}}$ the instrument already carries — so it is fixed once the level is declared rather than tuned, and it stands among the declared inputs of the taxonomy of §105 (Part XII). $\|C\|$ is the population correlation's spectral norm; the band consumes $c \cdot \|C\|$, and the intervals it certifies are evaluated at $c \cdot \|C\| = 2$.

By Weyl each eigenvalue moves at most that band, and $\lambda_+$ is a fixed function of shape — it does not move with the data. Therefore

$$
\begin{aligned}
k_{\mathrm{lo}} &= \#\{\, \lambda_k - \mathrm{band} > \lambda_+ \,\}
    && \text{modes certainly above the floor} \\
k_{\mathrm{hi}} &= \#\{\, \lambda_k + \mathrm{band} > \lambda_+ \,\}
    && \text{modes possibly above the floor}
\end{aligned}
$$

and the true `resolved_modes` lies in $[k_{\mathrm{lo}}, k_{\mathrm{hi}}]$, certain when $k_{\mathrm{lo}} = k_{\mathrm{hi}}$. The interval certifies `resolved_modes`, not `K_signal`, which is a screen statistic against $\Phi$.

**The band goes as $\sqrt{F/T} + F/T$, approaching $1/\sqrt{T}$ only for $T \gg F$: a read certifies by accumulating.** On a live delegate screen at $F = 195$:

```
 1 turn    T =  34    band = 16.26    interval [2, 195]
 3 turns   T =  88    band =  7.41    interval [6, 195]
20 turns   T = 466    band =  2.13    interval [10, 59]
```

The linear term dominates while $F$ and $T$ are comparable: the band falls by $7.6\times$ over a $13.7\times$ increase in $T$, not the $3.7\times$ a $1/\sqrt{T}$ law would give.

The same construction certifies the attenuation:

$$
\begin{aligned}
\alpha_{\mathrm{lo}} &= \log\left( \frac{\lambda_1 - \mathrm{band}}{\max(\lambda_2 + \mathrm{band}, \lambda_+)} \right) \\
\alpha_{\mathrm{hi}} &= \log\left( \frac{\lambda_1 + \mathrm{band}}{\max(\lambda_2 - \mathrm{band}, \lambda_+)} \right)
    && \text{certified} \iff \alpha_{\mathrm{lo}} > 0
\end{aligned}
$$

`SpectralAccumulator` pools de-meaned column covariance over intact planes and merges across observers by summation, so the band tightens with the ensemble rather than with a re-computation.

## 12. The operator

`Dynamics` accumulates three quantities per frame and reduces:

$$
\begin{aligned}
P_{xx} &\mathrel{+}= x_t x_t^{\mathsf{H}}
    \qquad P_{yx} \mathrel{+}= x_{t+1} x_t^{\mathsf{H}}
    \qquad P_{x} \mathrel{+}= x_t \\
\tilde{A} &= \left(V_r^{\mathsf{H}} P_{yx} V_r\right) \cdot \operatorname{diag}(w_r)^{-1}
\end{aligned}
$$

The reduction rank counts $P_{xx}$ eigenvalues above $\lambda_{\max} \cdot 10^{-10}$, truncated further to the signal rank when $n_{\mathrm{pairs}} < 2F$. That cutoff and the $2F$ pair-count trigger are **declared inputs**, not derived — each changes the recovered mode count — and both stand in the taxonomy of §105 (Part XII) and among the open seams. The derivation that replaces them applies the derived floor $\Phi$ to the $P_{xx}$ spectrum, so the rank is read off the frame in hand rather than typed. From $\tilde{A}$:

$$
\begin{aligned}
r_k &= -\log|z_k|
    && \text{decay rate per mode ($\ge 0$ stable, $0$ undamped, $<0$ growth)} \\
\beta_k &= \arg(z_k)
    && \text{frequency per mode} \\
C(\tau) &= \sum_k P_k z_k^{\tau}
    && \text{the whole decay curve, normalised $C(0)=1$, extrapolating past the window}
\end{aligned}
$$

**Phase cannot corrupt a rate:** $r_k$ comes from $|z_k|$, $\beta_k$ is carried separately. $r_k$ is the reduced operator's decay rate and section 9's $\alpha$ is a spectral contrast on the correlation; the two are never substituted for one another.

The connected accumulators subtract $P_x P_x^{\mathsf{H}} / n_{\mathrm{pairs}}$, with $n_{\mathrm{pairs}}$ the transitions actually accumulated — the count the rank rule compares against $2F$ — removing the DC fixed point that would otherwise read as a persistent mode. `rates()` uses the raw spectrum (exact recovery); `reconstruct_decay()` and `forgetting()` use the connected one.

Rollout uses **exact eigenvalue powers** — $x_h = \sum_k \varphi_k z_k^{h} b_k$ — one eigensolve for the whole horizon rather than iterating $A^{h}$ and accumulating error.

**Theorem (exact recovery).** If $x_{t+1} = A x_t$ then $P_{yx} P_{xx}^{+} = A$ on $\operatorname{span}\{x_t\}$, so $r_k$ and $\beta_k$ are exact.

**Theorem (additivity and exact splicing).** At forgetting $\lambda = 1$ the accumulators are additive over any partition, provided the boundary transition is counted once. Two operators therefore **merge bit-for-bit without exchanging a frame**.

**Definition (the forgetting aperture).** With margin $m = \max|z_k|$, the correlation length is $\xi = 1/(-\ln m)$ — the reciprocal of the mass gap, and what the decay kernel takes as its length. A mode has decayed to $\varepsilon$ by lag $\ell(m) = \lceil \ln(1/\varepsilon)/(-\ln m) \rceil = \lceil \xi \cdot \ln(1/\varepsilon) \rceil$: its **retention horizon**, $\xi$ stretched by $\ln(1/\varepsilon) = \ln(\text{contrast})$. Keep the two apart. The aperture's $\varepsilon$ is the router's — the relative floor $\varepsilon = \lambda_+/\lambda_1$ of section 9 — so the horizon carries no tolerance of its own. The window retains $\max(w_{\min}, \ell(m))$ when anything resolves, $w_{\min}$ otherwise, and $w \to \infty$ as $m \to 1$; $w_{\min}$ comes from the node's measured resource envelope, read at run time, never a typed constant. **The horizon is read incrementally off the operator, never configured.**

## 13. One aperture, enforced

Every read reaches entroptics through one seam, the optics module. No other module imports entroptics directly.

The entroptics Screen applies the concentration fold without the continuity gate of section 8.1, which `ember.optics` adds. Through that wrong door 256 unstructured sparse channels fold to $F_{\mathrm{eff}} = 1$ and report $K_{\mathrm{signal}} = 1$ — at the call site indistinguishable from "there is one real mode" — while through the seam the gate licenses such a fold in 2 of 40 trials (section 14). On a planted 3-mode sparse frame the ungated fold reports $K_{\mathrm{signal}} = 0$ where the projection resolves 3. Ontology coordinates are sparse — about 8 nonzeros in $D = 2048$ — so the fold is the wrong door for them.

**The screen a projection lands on is therefore the transport screen.** `absorb_transmit` reads the projection; the folded screen is reserved for the one use that wants a fold — a merge threshold that is literally `Screen(W).K_signal == 1`.

When the seam is absent the call raises rather than degrading: a dynamics read computed some other way is a different measurement.

**The law is single-sourced.** The optics module measures the scale; the law module applies the kernel: `attenuate(distance, length)`, `cool(stock, dt, tau)`, `similarity(distance)`, `cooled_integral(stock, dt, tau)`, `settle_time(floor, tau)`. Every entry is $\exp(-x/\text{scale})$ or its integral/inverse, with the travelled quantity clamped $\ge 0$ and the scale kept $> 0$. A call site reads `law.attenuate(d, length=optics.correlation_length(frame))`, where `correlation_length` returns $\xi$, not the retention horizon $\ell$; no bare $\exp(-x/\text{scale})$ appears downstream. One kernel means one clamp: a decay written per call site drifts, and the backwards-clock clamp goes missing first.

---
---
# PART III — WHAT THE INSTRUMENT RECOVERS

## 14. Validation against seeded ground truth

**Exact operator recovery.** Three scaled-rotation blocks, $r = [0.99, 0.97, 0.95]$, $\theta = [0.4, 1.2, 2.3]$, $T=160$, 40 seeds per SNR:

| SNR (dB) | mean $\lvert r_k\ \text{err}\rvert$ | mean $\lvert \arg z_k\ \text{err}\rvert$ |
|---|---|---|
| $\infty$ | **4.49e-16** | **4.44e-16** |
| 60 | 1.65e-05 | 1.43e-05 |
| 40 | 2.24e-04 | 1.57e-04 |
| 30 | 2.08e-03 | 5.66e-04 |
| 20 | 2.00e-02 | 1.79e-03 |
| 10 | 1.81e-01 | 1.28e-02 |

Columns: the per-mode decay rate $r_k = -\log|z_k|$ and frequency $\arg(z_k)$, read off the reduced operator. Max eigenvalue error 7.1e-16.

**Planted modes are counted correctly.** $K \in \{0,1,3,5\}$, snr $\in \{0.5,1,2,4\}$, four $(T, F)$ shapes $\{(200,200), (600,40), (40,600), (300,120)\}$, 30 seeds: accuracy **1.000** at snr=1 and snr=2 across all four shapes. $K=0$ specificity **0.950**.

**Coherence separates order from permutation.** Ordered smooth ($T=240$, $F=40$): mean $z$ = **26.67** (std 3.05, min 18.99); the rows permuted, **$-0.15$** (std 1.03). Over 2000 iid draws across five shapes: mean 0.014, std 0.998, $P(z>2) = 0.0265$ against the $N(0,1)$ target 0.0228.

**The coupling null is closed-form.** Against 20,000 brute-force re-pairings the analytic variance ratios are **1.005 / 1.009 / 1.004** — within **0.94%**. Over 1600 independent pairs: mean 0.011, std 1.018, fire rate 0.0488 against a nominal $p_{\mathrm{FA}} = 0.05$. Planted sign agreement **1.000** at $\rho = \pm 1$ and $\pm 0.5$.

**Correlation length and diffraction limit are one quantity.** With $a_\delta$ the diffraction limit read off the aperture and $\xi$ the correlation length of the same frame: Spearman **1.000** between $a_\delta$ and $1/\xi$ across $\rho = 2\ldots 128$, $a_\delta \sim 1/\rho$ log-log slope 0.994, $R^2 = 0.9992$, Abbe invariant (their product) $a_\delta\cdot\xi = 0.502$. $a_\delta$, $\xi$ and the Mercer ratio are owed constructions in the symbol block of §7 of `architecture.md`.

**Mercer ratio detects non-stationarity.** Read as $\mathrm{CV}(\rho)$. Stationary AR(1) $\varphi=0.85$: 0.0621; regime switch $\varphi$ $0.50 \to 0.97$: **0.584** — a **$9\times$** separation.

**A fold requires continuity.** Line vs nominal feature axis, both $(T, F) = (96, 64)$: effective channels $2^{\mathrm{H}_F}$ = 9.7 vs 3.1 — concentration cannot separate them. Adjacency $z$ = **8.37 vs 0.03** — it can. The screen folds in 40/40 of the first and 2/40 of the second, the continuity test the fold gates on. Fold-reconstruction residual: line width 8 $\to$ 0.646, width 40 $\to$ 0.252, nominal $\to$ 0.976.

## 15. The mass gap

**The gap is the decay rate of the dominant mode of the Koopman operator** — $-\log|z_1|$ on the connected dynamics, $z_1$ the leading eigenvalue of the reduced operator $\tilde{A}$ of §12; equivalently the spectral gap of the reconstructed $H = -\log \mathcal{T}$ of the transfer operator $\mathcal{T}$. It carries no corpus number: the floor terminating an associative walk in a corpus graph is a different quantity, the propagation floor of §66 of `paper-2-knowledge-without-weights.md`.

**Theorem (reach-freeze).** For a stationary sequence with entropy rate $s(n) = \mathrm{H}(A_n \mid A_{<n})$ and excess predictive information $\sigma(n) = s(n) - s_\infty \ge 0$, $\sigma$ is non-increasing with $\sigma(n) \to 0$, and

$$
\begin{aligned}
\sigma(n) &\sim e^{-mn} && \implies\quad a_{\mathrm{IR}} = 0,\quad \Delta = m > 0 \\
\sigma(n) &\sim n^{-p} && \implies\quad a_{\mathrm{IR}} > 0,\quad \text{gapless}
\end{aligned}
$$

Equivalently: **the per-step contraction rate of predictability**, the entropy rate of a language model.

**Theorem (entropy floor).** On the 4D hypercubic lattice the count $\#\Sigma(A)$ of closed connected centre-vortex surfaces of area $A$ through a fixed plaquette satisfies $\#\Sigma(A) \ge 3^{(A-2)/4 - 1}$, so

$$
\kappa_0 = \tfrac{1}{4} \ln 3 \approx 0.2746530,
\qquad \text{independent of the coupling $\beta$}
$$

and the gap clears the entropy margin $\Delta \ge \kappa_0 - \alpha$, where

$$
\alpha = \log\!\left(\frac{\lambda_1}{\max(\lambda_2, \lambda_+)}\right)
$$

is the instrument's `attenuation` read, $\lambda_+ = \left(1 + \sqrt{F/T}\right)^{2}$ being the Marchenko–Pastur bulk edge of §10. With the sub-dominant eigenvalue inside the bulk, $\max(\lambda_2, \lambda_+) = \lambda_+$ and $\alpha = \log(\text{contrast})$, so the confinement inequality $\alpha < \kappa_0$ is the eigenvalue statement $\text{contrast} = \lambda_1/\lambda_+ < 3^{1/4} = 1.31607$.

**Six equivalent faces of forgetting:** Cesàro decay of $|C|^{2}$ · $C(\tau)\to 0$ · $\max|z_k| < 1$ · exponential $|C(\tau)| \le K r^{\tau}$ · summable $|C|$ · positive aperture gauge $a = 2^{-h} > 0$.

### 15.1 Measured

**The confinement order parameter.** $k_{\mathrm{frac}} = K_{\mathrm{signal}}/F$, the resolved-mode fraction of a configuration's screen, is reported as the ensemble mean $\pm$ standard error. U(1) shows a sharp step; SU(2) and SU(3) are flat at every coupling.

| $\beta$ (U(1), $8^3\times16$) | $k_{\mathrm{frac}}$ | $\beta$ (SU(2), $8^3\times16$) | $k_{\mathrm{frac}}$ |
|---|---|---|---|
| 0.90 | $0.0492 \pm 0.0021$ | 1.00 | $0.0407 \pm 0.0020$ |
| **1.00** | **$0.0959 \pm 0.0039$** | 2.00 | $0.0609 \pm 0.0031$ |
| **1.05** | **$0.1532 \pm 0.0045$** | 2.30 | $0.0756 \pm 0.0026$ |
| 1.30 | $0.2673 \pm 0.0062$ | 2.60 | $0.0804 \pm 0.0041$ |
| 1.60 | $0.4043 \pm 0.0049$ | 2.80 | $0.0881 \pm 0.0036$ |

The confined ceiling $k_{\mathrm{frac}}$ = **$0.096 \pm 0.004$** sits below the Coulomb floor $k_{\mathrm{frac}}$ = **$0.153 \pm 0.004$** at **95% confidence**. Transition certified at $\beta_c = 1.011$.

**Disorder response separates by $18\times$.** Peak $-dH/d\beta \approx$ **0.87** for the U(1) foil at $\beta\approx0.97$ against **$\approx 0.05$** for SU(2).

**The transfer margin clears the ceiling.** SU(2) $\beta=2.30$, $m_{\mathrm{hi}} = \rho'(1) = e^{-\Delta}$ against $3^{-1/4} = 0.76$:

| $L$ | $\Delta$ | $m_{\mathrm{hi}}$ |
|---|---|---|
| 12 | 1.187 | 0.305 |
| 16 | 1.160 | 0.314 |
| 20 | 1.115 | 0.328 |
| 24 | 0.739 | 0.477 |
| 28 | 0.997 | 0.369 |

Plateau **0.305–0.477** across $L=12$–28, mean of the five tabulated points **$\approx 0.36$**. Across the full grid $\beta \in \{2.0, 2.3, 2.5\}$ crossed with $L \in \{8,12,16\}$, **7 of 9 points sit below the ceiling**.

**The interior read.** Whitened $\langle d^{2}\rangle$ on SU(2) $L=16$ across the crossover: range **[0.0138, 0.1577]**, peak at $\beta=2.50$. One-sided empirical-Bernstein (Maurer–Pontil) upper bounds at 99.9%, union-bounded over 9 lags, max **0.634** at $\beta=2.30$ — against a proof bound of 1 and an aperture ceiling of 3.52. Joint confidence over the 13-point grid: **98.7%**.

**Operator inversion.** Feed $|z_1| = e^{-\Delta}$, read $\Delta$ back: residual **1.7e-15** at $\Delta=0.1$, worst **6.0e-11** at $\Delta=1.1$.

**Exact-rational certificates.** $\beta^\star \in (0.749, 0.750)$ by geometric tail bounds on both Bessel series and on $\ln 3$. Single-plaquette gap $\Delta \ge 1.633$ at $\lambda=1$ by Sturm-sequence bisection with a Schur/Feshbach truncation tail. Free-field weak-coupling plateau $\alpha_\infty(L) \approx 2.1/L^{2}$, $\alpha_\infty(8) = 0.0326$ against $\kappa_0 = 0.275$, independent of the frame length $T$, flat from $\beta=3$.

**Dataset.** 231 ensembles, **11,756 configurations, 11.13 GB**: SU(2) $L=8$–32, SU(3) $L=6$–12, U(1) $L=8$–32.

## 16. Deterministic dynamics

The `deterministic` column is the **retired sparse-regression baseline** — SINDy and DMD over a fixed function library, retired as a method because a fixed library decides what a law may be before the data is consulted. `params` counts the whole coefficient vector; `nnz` counts its nonzeros.

| system | retired deterministic baseline | params | nnz | trained MLP | margin |
|---|---|---|---|---|---|
| springs (linear) | **DMD, error 0.000** | 36 | dense operator — no sparsity pattern | 1.02 / 30k params | exact, $800\times$ smaller, MLP fails |
| Van der Pol (limit cycle) | **SINDy, error 0.000** — the exact ODE | 4 | 4 | 0.18 / 21k params | $5000\times$ smaller |
| Lorenz (chaos) | **SINDy, error 0.000** | 27 | 6 | 0.02 / 23k params | $850\times$ smaller, better forecast |
| Lorenz-96 $D=6$–12 | SINDy, 40–$260\times$ fewer params | — | — | fails at 1 trajectory | scales |

**Operator identification** on the excited subspace: $\|(A_{\mathrm{fit}} - A^{*})Q\|/\|A^{*}Q\| \approx$ **0.008**. Refusal calibration: answer-rate on signal **1.00**, refusal-rate on noise **$\approx 0.9$**.

**Training is load $\to$ extract $\to$ confirm**, for the spectral identifier of §12:

- **Load** persistently-exciting data.
- **Extract** the operator, not a term list: accumulate $P_{xx}$, $P_{yx}$, $P_{x}$, truncate to the signal rank the noise floor sets, form the reduced operator $\tilde{A}$, and read its per-mode rates $r_k = -\log|z_k|$ and frequencies $\arg(z_k)$. The retained rank comes from the data's own spectrum; no form of law is fixed in advance.
- **Confirm** with a **completeness certificate**: the **resolved rank is exactly equal** — integer to integer — across successive data loads, so no further data resolves a new mode, and the identified operator forecasts held-out trajectories from unseen initial conditions. There is no plateau tolerance to choose, because the quantity that plateaus is a count.

**The data edge is visible in the recovered complexity** — resolved rank for the spectral identifier, term count for the retired baseline. On the baseline: Lorenz-96 $D=24$ from one trajectory recovers **7,660 spurious terms against ~96 true**, forecast diverging; at 8 trajectories it collapses to **962 terms, forecast error 0.004**. Feed data proportional to dynamical complexity.

**Screen-based KV eviction**, synthetic KV with heavy hitters planted early, 12% budget:

| policy | signal recall |
|---|---|
| **screen (resolved energy)** | **0.999** |
| recency (StreamingLLM) | 0.000 |
| random | 0.18 |

Heavy-hitter hit rate **1.000**; mean `K_signal` recovers the exact planted rank; `state()`/`from_state()` round-trips byte-identical.

## 17. The three regimes, and the router

Reasoning over a structured domain is the forward operation of an operator identified from data. The verdict:

$$
0 < K < F
$$

$K = 0$ is *nothing resolved above the noise floor* — a refusal. $K = F$ is full rank: every mode significant, no compression, no governing law. Between them, a proper subspace rises above the null and the instrument's own propagator produces the forecast. **Mode selection admits no ratio gate and no plateau tolerance: the significance test already decided which modes are real.**

The delay depth is the signal's own integral correlation length, read off its autocorrelation. An unmeasurable lag is a refusal, not a depth of 1. One exception: the language path pins $d = 1$, chosen by comparing outcomes across a sweep rather than measured — an open seam carried with the constants sweep, the only place the derived depth is not used.

The forecast horizon is $\ell(m) = \lceil \ln(1/\varepsilon)/(-\ln m) \rceil$ with $m = |z_1|$ of the dominant mode and **$\varepsilon$ the frame's noise floor relative to the amplitude it bounds** — $\varepsilon = \lambda_+/\lambda_1$, i.e. $1/\text{contrast}$, so

$$
\ell = \left\lceil \frac{\ln(\text{contrast})}{-\ln m} \right\rceil
$$

When the spectrum cannot support a step — $|z_1| \ge 1$ is growth, $|z_1| = 0$ is nothing, $\text{contrast} \le 1$ is nothing resolved to decay from — the answer is `None`, never a step count. Measured: $r = 0.99 \to \ell = 51$, $r = 0.90 \to \ell = 7$, monotone in the decay rate, forecast exact to 1.44e-15.

| regime | condition | consequence |
|---|---|---|
| **structured** | a sparse closed form exists in some basis, and data $\gtrsim$ dynamical complexity | the deterministic identifier is exact, tiny, and data-efficient |
| **the data edge** | data $<$ complexity | the recovered complexity explodes — resolved rank, or term count on the retired baseline; feeding data $\propto$ complexity collapses it |
| **the fundamental edge** | no sparse closed form in any tractable basis; recovered complexity unbounded | richer observables are required |

Determinism dominates compact domains — arithmetic, physics, formal math; open natural language is the archetype of the fundamental edge, where keyed retrieval and citation carry the weight instead.

## 18. The same reads at a trained model's boundary

| read | result |
|---|---|
| retrieval cut, BeIR scifact | F1 **0.630 vs 0.291** fixed top-5 — $2.2\times$ |
| multi-modal drift | **+0.17 AUROC** at 5 topics |
| foreign-content anomaly | AUROC **0.994 vs 0.971**; real attacks **0.90 vs 0.72** |
| unified pipeline, HotpotQA | baseline answer F1 at **49% of the context tokens** |
| per-token multi-source attribution | **87.33%** argmax accuracy against 33.3% chance — $2.62\times$ |

**The governing law:** a subspace read beats a summary statistic exactly when the deciding distribution is multi-modal, and the margin grows with the number of modes.

| facets | cosine F1 | subspace F1 | margin |
|---|---|---|---|
| 1 | 1.000 | 1.000 | 0.000 |
| 2 | 0.999 | 1.000 | +0.001 |
| 3 | 0.839 | 0.981 | **+0.143** |
| 4 | 0.755 | 0.938 | **+0.182** |

**The wins are at the boundary — choosing what enters and scoring what leaves.** A trained network's interior is gradient-owned and compensates for any geometric substitution made there: attention temperature converges to $\sqrt{d}$; quantization bit-allocation is far worse than uniform; early-exit exits at layer 1; KV-cache compression inside a trained network loses to random, notwithstanding the 0.999 recall the same read achieves on §16's synthetic KV; fine-tune token weighting is indistinguishable from uniform; embedding migration is won by a trivial linear map 84% to 18%.

Constraining a trained decoder's generation geometrically is an interior move — judge-scored support rate, same prompts, same sampling:

| mechanism | baseline | steered | $\Delta$ |
|---|---|---|---|
| residual-stream V-projection, blend 1.0 | 0.267 | **0.133** | **$-0.133$** |
| logit bonus $\alpha=10$ | 0.267 | 0.267 | 0.000 |
| logit bonus $\alpha=30$ | 0.267 | 0.233 | $-0.033$ |
| logit bonus $\alpha=100$ | 0.267 | 0.200 | $-0.067$ |
| $\alpha=30$, question-conditional | 0.300 | 0.233 | $-0.067$ |
| adaptive top-32, $\alpha=30$ | 0.300 | 0.300 | 0.000 |
| adaptive $\alpha=100$, $n=60$ | 0.417 | 0.400 | $-0.017$ |

Every variant ties or loses, at up to **$8.6\times$ latency**.

**Denoising by projection is exact.** Projecting both keys and query into the kept resolved modes and scoring inside that subspace is denoised $q\cdot k$: the unresolved modes are exactly the ones carrying no signal, so dropping them removes noise rather than information. The projection identity is exact to **7e-15**.

| method | 2% | 5% | 10% | 20% | 30% | 50% |
|---|---|---|---|---|---|---|
| context-fixed (query-independent) | 16.6 | 22.6 | 30.8 | 43.3 | 53.7 | 71.2 |
| **query-dependent** | **43.3** | **58.8** | **71.9** | **84.4** | **90.6** | **96.4** |
| oracle ceiling | 79.4 | 86.2 | 90.9 | 95.0 | 97.0 | 98.9 |

A **41-point gap at a 20% budget**, from the projection alone. Measured cost: **~90–115 ms per 1024 heads**, fixed per 1024 heads and not growing with context across 16–64k: **~1.3 µs/token at 64k**, ~6–7 µs/token at 16k. Budget from the per-1024-head total, not the per-token figure. **~14 modes per head**, concentrated in layers 16–19 and 32.

---
---

# PART XII — METHOD

## 100. Three legitimate external inputs

The three are stated as an invariant in §6 of `architecture.md` and enumerated with their classifications in §105.

The corpus is unbounded; the aperture is not, and **the aperture has exactly one envelope** — no per-function bound, no batch size, no top-N, no truncation. Every "how many / how far / how much" question resolves to the same measured reading of the machine.

**Read the cgroup, because the obvious readings lie:** `free`, `top`, `ps` and `nproc` all report the *host* inside a container. The cgroup is the truth where one exists, and where the platform cannot report a limit the answer is **unmeasured** — never a default.

**Associative reach is bounded by the measured envelope** — cgroup plus time — never by a constant. **Nodes are tight specialists**: a working set inside the envelope, reaching the mesh for the rest. Never force a generalist.

## 101. What a legitimate test looks like

**The unit of confidence is the invariant, not the example**: "it ran and it worked" checks one path with one input. Seven invariants are asserted across the comms substrate alone: isolation, delivery, idempotence, order, propagation (absorb plus transmit), carrier-agnosticism — the same invariants must hold on a listable Plane and on a broadcast Carrier spooled into one — and conservation.

Expected results are computed *independently of the mechanism under test*, so a test cannot share a bug with the thing it tests. Against a known-correct answer recall is absolute rather than relative: the lexicon *is* the answer key, the AST *is* the definition site, collection membership *is* topical truth.

Properties are proven over seeded generators — 150 seeds in the comms suite — and a failure prints the seed, so the exact world is reproducible.

**A check that cannot fail proves nothing.** State the failure mode first; a test that compares a literal to itself cannot catch drift.

**Editable installs hide dependency edges.** A passing suite never proves an edge is ABSENT — block the import and prove the blocker fires.

**Duplicate module basenames substitute silently, so make the collection count part of the assertion.** One suite reported 312 tests where 308 ran, dropping 4 with no error.

**Concurrent lanes make a shared gate lie; the tell is *duration*.** Re-measure before attributing, and never read a shared status file as your own result.

**Pin the BLAS thread count to reproduce**: the eigensolver is not thread-safe on this hardware, and it hangs as well as faulting, so **a single green run proves nothing.**

**Bare invocation must work in every repository with no path manipulation**: packaging metadata in each repository, exactly one test configuration per repository — two conflicting configurations disagreed by 87 tests — and fully qualified imports, since a bare import resolves to whatever unrelated site-packages package answers to the name.

## 102. Five failure modes

**A gate that permits is still forcing when the quantity it gates on is a property of the corpus or the observer rather than of the frame in hand.** Replacing a typed threshold with a threshold measured *on the corpus* is the same defect with better provenance; replacing it with a null computed from the frame's own surrogate distribution is the sanctioned instrument. Even that form must be checked against the geometry it shuffles: a permutation-null floor, correctly derived, sat *above* real couplings — these coordinates are non-negative, so a shuffled vector reaches ~0.78 cosine — and refused true corpus edges.

**A formula with an invented input is an invented number with extra steps.** A published horizon formula supplied with machine epsilon asks *when does this underflow a float* and answers **74 steps**. **A representability limit is not a resolution limit.**

**The hardest variant to catch is the right quantity in the wrong units.** A horizon guard fed the correlation spectrum's edge — dimensionless and bounded below by 1 — into a slot that inverts a *relative* amplitude is **unsatisfiable by construction**: the reasoning operator can never return a deterministic verdict on any input, and publishes a units error as a measurement about the data. The tell "tuning a number to fix an output you dislike" misses this one; **what catches it is asking the aperture what the frame resolved and noticing the answer never reaches the caller.**

**A measured cut on the corpus is still a cut on the corpus.** The resolution read needs no constant, returns everything when nothing separates, and applied to the *frontier* answers a question about the corpus rather than about the observer. Under gap physics alone `water` reaches 74,426 members and `bank` 74,426; with the cut gating the frontier they reach **99** and **4** — three orders of magnitude decided by whether one round's top weights happened to separate from their own tail.

**Adopting a number because an instrument printed it is the same error as choosing one because it looked reasonable.** A hand-derived correlation length of 0.4653 against the aperture's 1.0415 is not resolved by taking the instrument's value: if the matrix handed to the aperture was invented for the occasion, the instrument faithfully reports the autocorrelation of *that*.

## 103. Three excuses that always work

Every constant that survives an audit survives behind one of these:

- **"a declared risk, not tuning."** A false-alarm level typed by a module for an instrument that already has one, and duplicated as a default in two called functions, so changing the named constant changes nothing. The level is a legitimate declared input at exactly one boundary; a second copy anywhere else is a constant with an excuse.
- **"the common setting, so both sides are compared through the same instrument."** True, and irrelevant to where the number came from. It sets the delay depth, which decides how much structure the rank read can see — **it decides the verdict.**
- **"a fallback, reported not substituted."** A docstring reading *"'could not measure' is not 'measured 12'"* immediately above a `return 12`.

The invariant is stated as a set: **the only float literals the module may execute are the identity and the zero of an amplitude.**

**The vocabulary smuggles forcing back in** — *guard, check, filter, validate, allow, reject, skip*. When a rule appears, ask which measured quantity it stood in for.

## 104. What is retired, and why

**Sparse polynomial regression is retired**, despite being the best-measured method in the physics table. Its rows stay published, labelled at the point of presentation as a **retired comparison baseline**, in the physics table and in the regime table alike. The surviving identifier reads the operator off the data's own spectrum: what is extracted is a reduced operator and its per-mode rates rather than terms drawn from a library, and what plateaus in the completeness certificate is the **resolved rank** rather than a recovered term count.

**A regime called "model" invites someone to supply one (§63 of `paper-2-knowledge-without-weights.md`).** Keep the measurement and emit nothing.

**A report that outlives what it describes is worse than no report.** A message reading *"the operator snapped nothing above the coupling floor"* that survives the floor's deletion reports a reading of nothing as a reading of the corpus.

**A side-car is the defect.** A hash, id, or registry maintained beside an artifact IS the defect; scoping a migration to reconcile two identities is the tell, and deleting the side-car is the fix.

**Absence is not an affirmative claim.** A default that reports an artifact as grounded fabricates a grounding nobody measured. Derive it or default to the negative, and always write the refusal control.

**False grounding on fragments.** Refuse when the grounding is a lexical fragment; raise the bar via reach distance, not via a list.

**Never hand-roll a probe.** Read published statistics only; a missing statistic means adding it to health monitoring, rather than inventing a parallel reading.

**A permanent skip is a silent pass.** Retire the tooling; never patch it.

## 105. Provenance of the numbers

Instrument constants are classified by kind — derived, forced, definitional, external input, optional input, locality, implementation, declared input — and **no constant is fitted to data or calibrated to a substrate.** Every threshold, cutoff, floor, limit, weight, depth, and rank outside the declared-input roster is computed from the frame in hand, and a literal standing where a derivation belongs is a defect however well it is justified.

**Declared inputs — the whole roster.** A declared input is a value the instrument cannot derive, named at one boundary and published with the run that used it. §12 and §59 of `paper-2-knowledge-without-weights.md` point here rather than assert membership, and every entry is swept in item 1 of §113 of `architecture.md`.

Three the instrument carries at its own boundary:

- **The false-alarm level.** *Declared input, non-derivable by lemma*: a floor is meaningful only relative to a rate of false positives someone is willing to accept, and no frame contains that willingness. Its definitional constant is the $\mathrm{TW}_1$ upper-quantile table of §10 — a table of the distribution, not a choice about the data. Declared once at the aperture boundary, the level in force is published alongside the reads it governs.
- **The leading constant $c$ of the matrix concentration bound** in $\|\hat{C} - C\|_2 \le c \cdot \|C\| \cdot \left(\sqrt{F/T} + F/T\right)$. It is not chosen: the inequality fixes it at the declared failure probability, and that probability is the false-alarm level the instrument already carries.
- **The message/event cut.** A coupling constant standing where a measurement belongs. The mass it compares is derived (§21.1 of `paper-2-knowledge-without-weights.md`) and needs no table; the cut itself does not yet have its null. What retires it is a computed null over the corpus's own mass distribution, exactly as the propagation floor is computed over the propagation weights for reach.

Four further entries stand in named subsystems, each with the measurement that retires it:

- **The relative rank cutoff $10^{-10}$** (§12): the reduction rank counts $P_{xx}$ eigenvalues above $\lambda_{\max} \cdot 10^{-10}$, and the cutoff changes the recovered mode count. Applying the derived floor $\Phi$ to the $P_{xx}$ spectrum retires it — the rank is then read off the frame in hand.
- **The $2F$ pair-count trigger** (§12), which truncates that rank to the signal rank when $n_{\mathrm{pairs}} < 2F$; the same derived floor retires it.
- **The basis parameters** (§59 of `paper-2-knowledge-without-weights.md`) — the signed-hash seed, the exact form of $w$, the normalisation — published with the basis artifact rather than retired: a peer that hashes differently is not in the same basis and cannot couple, so the declaration is what makes coupling checkable.
- **The language-path delay depth $d = 1$** (§59 of `paper-2-knowledge-without-weights.md`), selected by comparing outcomes across a sweep rather than measured, and an open seam for that reason. The integral correlation length of the language screens retires it; everywhere else the autocorrelation-derived depth of §17 already runs.

---


---

# TWO FURTHER READS — JOINT ENTROPY AND THE ORDERED AXIS

Both are Shannon 1948 mathematics, both live in `entroptics.entropy` / `entroptics.sequence`, and
both reach a caller through the one aperture and the `prism.instrument` contract. Neither changes any
number in Parts II, III or XII.

**The joint read — `joint_entropies(W_x, W_y)`.** Three entropies of two **co-registered** frames and
the three differences, all from one joint table `J = P_xᵀ P_y`, so `I_XY = H_X + H_Y − H_XY` and
`I_XY = H_X − H(X|Y)` hold *exactly* rather than to float noise. `H(X|Y)` is Shannon's
**equivocation** (Shannon 1948, §12) — what remains uncertain about one frame once the other is known. It reads a
failure the conservation certificate cannot: a hop may absorb every joule and still leave *which*
signal arrived ambiguous, because `‖·‖²` counts energy and this counts distinguishability.

Two properties worth stating with the instrument's other invariants:

- **Co-registration is enforced and a mismatch raises.** Frames on private ordered axes superpose
  into noise, so truncating to a common length would publish a reading of the misalignment.
- **`I_XY` is basis-invariant, and it is the only read here that is.** Mutual information is a KL
  divergence, `I(X;Y) = D(p_XY ‖ p_X p_Y)`, and the continuous extension of a KL divergence is
  invariant under a change of coordinates (Lesne, MSCS 2014, §2.6). So two peers on different basis
  generations cannot compare `H_F`, cannot splice operators, and **can** compare `I_XY`.

**The ordered-axis read — `entroptics.sequence`.** Block entropies `H_n`, the entropy-rate ladder,
the Lempel–Ziv rate, and `surrogate_test` — a sequence against its own shuffles, on the theorem that
a shuffle can only raise block entropy (`H_n(σX) ≥ H_n(X)`, equality iff uncorrelated).

⚠ **A correction that was nearly a published error.** A first version
collapsed the ladder to `min(h_n)`, on the monotonicity of Lesne eq. 45. That ordering holds *in the
infinite-data limit*; on a finite sequence the ladder turns over and dives. An i.i.d. source with
true rate 2.0 bits reported **0.43**, and — worse — a strongly correlated source had its verdict
**inverted** (`z = +6.59`) because the plug-in bias is not symmetric between a sequence and its
shuffle. Comparing `H_n` at a **fixed order** is what fixes it: at fixed `n` both sides carry
identical bias and it cancels exactly. **No word-length limit, bias correction or threshold appears
in the module**, and none is needed.

`n = 1` is a free internal control and is asserted, not assumed: a permutation cannot change the
symbol histogram, so `z₁` must be exactly zero.

---

# SCOPE — the conditions a read is valid under

Four conditions, each derived in Parts II and III and gathered so a caller can check them at a glance.
The declared inputs themselves are not repeated: §105 is the complete roster, with the measurement
that retires each entry.

**A fold needs continuity, not just concentration.** Concentration says how far a fold may go; only
continuity says whether folding is licensed at all. The two are independent — a narrow line on a
frequency axis is sparse *and* continuous and folds losslessly, while a handful of unrelated active
channels is equally concentrated and must stay at native resolution. The gate is measured (§14:
adjacency $z$ = 8.37 vs 0.03; the screen folds in 40/40 of the first case and 2/40 of the second),
and the seam of §13 applies it. **Sparse nominal axes therefore read through the projection, not the
folded screen.**

**A cell that was not measured is not a cell carrying zero power.** Nonfinite and masked cells are
excluded from the power sum *and* from the axis extent (§8.1). Counting them would bias the null in
two opposing directions at once — the de-biased variance divides row energy by a denominator in $F$,
so absent columns sink the floor, while the Johnstone centring returns the edge of a wider ensemble
and lifts it. An all-zero but *finite* cell is a real observation of no power and does count.

**Certification is by accumulation, and the rate is arithmetic.** The band goes as
$\sqrt{F/T} + F/T$ and reaches $1/\sqrt{T}$ only once $T \gg F$; while $F$ and $T$ are comparable the
linear term dominates (§11: the band falls $7.6\times$ over a $13.7\times$ increase in $T$, not the
$3.7\times$ a $1/\sqrt{T}$ law would give). An axis on which $T$ grows one row at a time against a
large $F$ does not close its interval quickly, and `SpectralAccumulator` merging across observers
(§47 of `architecture.md`) is the intended remedy: the ensemble certifies what no single node could.

**The library does not calibrate the caller's null.** A physics null, a confined-vacuum reference, a
phase-randomised surrogate — each is written by the caller and passed in (§10). What the instrument
guarantees is that the floor it computes is the floor of the null it was handed, on the frame in
hand.

---

# GLOSSARY — the instrument's terms

The full glossary is in `architecture.md`.

| Term | Meaning |
|---|---|
| **Aperture** | What bounds a beam and reads everything about it, streaming-first and adaptively forgetting. |
| **Beam** | The carrier, an extended object, and the measurement repository — the one seam onto entroptics. |
| **Entroptics** | The instrument: a screened spectral aperture that reads a frame at its own resolution and reports modes, interval, decay, and coupling. |
| **K_signal** | The count of singular values $s_k$ of the entropy-folded, MAD-whitened screen that stand above the derived noise floor $\Phi$ — the resolution read. Distinct from `resolved_modes`. |
| **Lens** | A side's conversion into the shared basis, carrying its own entry, inverse, energy law, zero, and null. |
| **Mass gap** | $-\log \lvert \mu_1 \rvert$: the decay rate of the dominant Koopman mode, the per-step contraction rate of predictability. A spectral quantity read off a fitted operator; it carries no corpus number. |
| **Projection** | The screen as one side sees it, on that side's own entropy-matched grid. A projection does not fold. |
| **Resolved modes** | $\#\{\,k : \lambda_k > \lambda_+\,\}$ — eigenvalues of the unit-diagonal correlation above the bulk edge, and the count the Weyl interval certifies. Distinct from `K_signal`. |
| **Screen** | The ordered shared surface where signals meet and are read from either side — never shuffle it. It is the object that folds, and only where the feature axis is continuous. |
| **$\lambda_+$** | The bulk edge: the Marchenko–Pastur edge for a unit-diagonal correlation matrix of a $(T, F)$ frame, $\lambda_+ = (1 + \sqrt{F/T})^{2}$. $\mathrm{contrast} = \lambda_1 / \lambda_+$. |

---

*Declare the symmetries; measure what converges.*
