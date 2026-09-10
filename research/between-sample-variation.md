# For the testing plan: what replicates across samples, and what does not

`bench_resolution.py` asks whether a difference would survive a DIFFERENT set of questions, and
answers it by exchanging outcomes within the SAME fixed set. That is the right question and the
null is well constructed. This note is a bound on it, not a correction: **a within-sample null
cannot see between-sample variation, and on three benchmarks measured twice today the second is
much larger than the first.**

The evidence is three experiments run on disjoint halves of their own item pools, same code, same
configuration, one thing changed -- which items.

## The three repeats

    PLACEMENT (entroptics-jlens exp42, 300 vs 246 Wikipedia articles)
      baseline error                     6.6847   ->   9.6368        +44%
      the effect under test             -0.8229   ->  +0.5523        SIGN FLIP
                                       (2.7 sigma better)  (1.4 sigma worse)

    CONTAINMENT (part-of, 150 vs 150 WordNet items)
      baseline MRR                       0.1402   ->   0.1596        +14%
      effect of the retrieved entry     +0.0873   ->  +0.0435        HALVED
      shuffled control                  -0.0030   ->  -0.0001        both at zero
      another entry's content           -0.0088   ->  +0.0053        both at zero
      share of items helped               73.3%   ->    73.3%        IDENTICAL

    COMPOSITION (40 vs 40 store entries)
      retention at n = 2                  0.318   ->    0.390        +23%
      retention at n = 4                  0.086   ->    0.092         +7%
      retention at n = 16                 0.035   ->    0.028        -20%
      below the 1/n floor at every n       yes    ->     yes         SAME CONCLUSION

## The pattern, which is the useful part

**Absolute effect magnitudes do not replicate.** They halve, they double, and on the placement
benchmark the sign reverses. A single sample's estimate of "how big" is worth very little at
n = 150-300 on benchmarks of this kind.

**Rates, controls and shapes do replicate, and tightly.** The share of items helped was identical to
the digit. Both controls sat at zero in both samples. The retention curve kept its shape and its
relation to the floor. Every claim that survived today is of this form; every claim that died was an
absolute magnitude.

## A correction, from using the rule on four more benches

The version of this note written earlier said: report rates, not means. **That is too strong**, and
one bench showed why. Fusing a sentence embedder into lexical canon placement carries a better MEAN
on both halves -- sealed MRR 0.4977 against lexical's 0.4622, a 7.7% relative gain -- while beating
lexical on only 38.5% then 34.5% of items. It rescues hard queries and slightly degrades easy ones.
A rate-only report would have thrown that away twice; a mean-only report would have missed that most
queries got worse. Both are true and they answer different questions.

The refined rule: **rates transfer tightly, mean magnitudes move but their sign and ordering can be
stable. Claim the rate. Report the mean.** The four rate pairs below still hold (73.3/73.3,
50.9/50.0, 76.4/71.8, 19.5/20.0) -- it is the "not means" half that was wrong.

## What this argues for in the testing plan

1. **Report control-relative quantities, not raw deltas.** "The effect is +0.0873" did not survive.
   "The effect is present and the controls are at zero" did. The control arm is not overhead; it is
   the part that transfers.

2. **Report rates AND means, and claim the rate.** 73.3% of items helped replicated exactly where
   the mean effect halved -- so claim on the rate. But a fusion arm carried a replicated 7.7% mean
   gain while losing on 65% of queries, so a rate alone hides a real improvement. They answer
   different questions: "is this better on average" and "will this help my query".

3. **Split every bench in half from the start and treat one half as unopened.** Not a train/test
   split for tuning -- a replication half, opened once, after the claim is written down. Today it
   cost one extra run per claim and it falsified one result, confirmed two, and narrowed a third.

4. **A within-sample null bounds the wrong thing.** `bench_resolution`'s exchange null says whether
   a difference is bigger than the noise inside one question set. Measured here, between-set
   variation on the same corpus exceeded that: a 2.7-sigma result by the within-sample standard was
   a 1.4-sigma result of the opposite sign on the next 246 articles. Keep the tool and add the
   disjoint half; the two answer different questions and only the second one predicts.

5. **Size the bench to a rate, not to a mean.** §89 of `the-economy.md` put the labelled bench's resolution floor at
   about three questions in thirty-six. That is a within-set figure. If the quantity reported is a
   rate over a disjoint half, the sizing question becomes how many items make a rate stable, which
   is answerable in advance and does not depend on the effect.

## Provenance

Measured 2026-08-22/23 in `entroptics-jlens`; the three experiments are `exp42_model_places_prose.py`
and two scratch scripts, and the full records are in that repository's `FINDINGS.md` under
2026-08-22bp, 2026-08-23a and 2026-08-23b. The placement experiment produced five different answers
across five runs before the disjoint half settled it, and three of those answers had already been
written down here as results. That is the argument for point 3.
