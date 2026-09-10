# Forecasting a Fractal Series

## What the zero-sum property does and doesn't give you

Begin with the thing that feels most like information and turns out to be worth the least. Knowing that a series balances to zero over its full, possibly infinite length constrains almost nothing about the near future. Any finite prefix you have observed can be offset by infinitely many different tails, and there is no principle that says the offsetting has to begin soon, or proceed at any particular rate, or happen in any recognizable shape. The constraint is real but it is satisfied so easily that it rules out essentially no candidate futures.

The temptation the property invites is worth naming directly, because it is where most people go wrong. If the series has run negative for a while, it feels as though a positive stretch is owed — that a debt has accumulated and must be repaid. This is the gambler's fallacy wearing continuous-time clothing. In a self-affine process the expected next increment is typically zero no matter what came before. Balance is achieved not through scheduled repayment but through rescaling: the process wanders, and the wandering looks statistically identical when you zoom out, which is a very different mechanism from a restoring force pulling the series back toward its mean. If you build a forecast on the intuition of owed debt, you will systematically bet against whatever just happened, and if the series is persistent rather than mean-reverting you will be wrong more often than chance.

What is genuinely exploitable is the self-similarity itself — specifically, the correlation structure it implies. That structure is called long memory, and it is the entire basis of everything that follows.

## The core idea: forecasting as a weighted vote of the past

In an ordinary short-memory model, a forecast is a function of the last value or two, and everything older is discarded. An AR(1) model, for instance, weights the past exponentially: each step back cuts a value's influence by a constant factor, so after twenty or thirty steps its contribution is numerically indistinguishable from zero. Memory in such a model has a characteristic length, and beyond that length the model has nothing to say.

A fractal process is different in exactly one respect, and it is the respect that matters. The weights decay as a power law rather than exponentially. A value five hundred steps back still contributes to the forecast — faintly, but not negligibly. The forecast is still a weighted sum of the observed history,

> next value ≈ w₁·x(t) + w₂·x(t−1) + w₃·x(t−2) + …

but the weights shrink slowly, and their shape is determined by a single number. Estimate that number and you have specified the entire weighting scheme. That is the whole game.

## Measuring the memory

The number goes by two interchangeable names: the memory parameter *d*, or the Hurst exponent *H*, related by H = d + 0.5. It is estimated from how the variability of the series scales with the size of the window you measure it over.

The procedure is conceptually simple. Chop the series into windows of length *n*. Measure the typical fluctuation within each window. Repeat across many different values of *n*. Then plot fluctuation against window size on logarithmic axes. If the series is genuinely self-similar, the points fall on a straight line, and the slope of that line is H. The various named methods — detrended fluctuation analysis, wavelet-based estimators, spectral fits near zero frequency, the classical rescaled-range statistic — differ in their robustness to trends and short samples, but they are all measuring the same slope. In practice detrended fluctuation analysis is the reasonable default, since the classical R/S statistic is badly biased on short or trending samples.

The slope tells you which regime you are in:

- **H > 0.5 — persistent.** Above-average stretches tend to be followed by more of the same. Forecasts lean in the direction of recent behavior.
- **H < 0.5 — antipersistent.** Moves tend to reverse. Forecasts lean against recent behavior.
- **H ≈ 0.5 — white noise.** There is no linear predictability. The best forecast is zero, and no amount of modeling sophistication changes that.

One diagnostic is worth running before you commit to a single H: check whether the scaling exponent varies with the moment order, using multifractal DFA. If it does, the series is multifractal, and a single-exponent model will fit the body of the distribution while badly misestimating the tails — which is usually where the consequential events live.

## From the exponent to actual forecasts

The standard route from a measured exponent to numeric predictions is fractional differencing. Apply the operator (1−B)^d to the series — in practice a long convolution with coefficients from the binomial expansion. If *d* was estimated correctly, what emerges is approximately short-memory noise: the long-range structure has been absorbed by the differencing, leaving an ordinary residual process. Fit a conventional ARMA model to that residual. Forecast the ARMA part one step at a time, then invert the differencing to map the forecasts back onto the original scale. The combined object is called ARFIMA, and it is the mainstream answer to the question.

For fractional Brownian motion specifically, there is a more direct path: closed-form expressions for the conditional expectation given the observed path, due to Gripenberg and Norros and to Norros, Valkeila, and Virtamo. These discretize reasonably well and avoid the two-stage fitting procedure entirely.

Where the fractal assumption earns its keep is at long horizons. Short-memory forecasts collapse to the unconditional mean within a handful of steps; past that point the model is simply reporting the average. Power-law weights decay slowly enough that a small but genuine edge survives at horizons of hundreds of steps. The edge is small — the conditional mean still shrinks toward zero — but it shrinks polynomially rather than exponentially, and that difference in decay rate is the entire practical payoff of taking the fractal structure seriously.

## Forecast distributions, not forecast numbers

Uncertainty in these models grows roughly as h^H with horizon h, which for a persistent series means the bands widen faster than they would under a random walk. Reporting a single projected value at horizon 200 is therefore close to meaningless: the point estimate may be near zero while the plausible range spans most of the series' historical excursion.

The deliverable is a median path with widening quantile bands around it. Construct them either analytically, from the model's error variance, or by simulating many synthetic futures from the fitted model and reading off empirical quantiles. The simulation route is more work but handles the multifractal case, non-Gaussian innovations, and any nonlinearity you have bolted on, none of which the analytic formulas accommodate.

## Validation, and the failure mode to watch for

Fit on an initial segment, forecast forward, slide the window, refit, repeat. This walk-forward procedure is the only evaluation that mimics the situation you will actually be in. Compare the results against the naive forecast of zero at every horizon.

That baseline comparison is not a formality. Long-memory estimators are notorious for reporting spurious values of H on series that merely contain a slow trend or a level shift — structural features that mimic long-range dependence on a log-log plot without implying any of the predictability the model assumes. The failure mode is seductive because it produces a clean straight line and a confident exponent. If the walk-forward test cannot beat a constant forecast of zero out of sample, the structure you detected lives in the estimator rather than in the data, and the correct conclusion is that the series is not forecastable by these means.

Which brings the argument back to its starting point. "Sums to zero over infinite length" and "is self-similar" are separate claims, and only the second one has to hold for any of this machinery to function. The first is nearly free; the second is a strong empirical assertion that shows up as a straight line on a log-log fluctuation plot or does not show up at all. Draw that plot before building anything on top of it.
