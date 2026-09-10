# Learn — the Entroptics course

**The Entroptics course, as files.** 49 lessons that start at grade-ten mathematics and end at a working instrument: what a read measures, what it costs to move a signal, and how far the method carries outside the place it was built.

**This course is a work in progress and still needs refinement.** It is published because it is useful to read, not because it is finished: expect rough edges, uneven depth between sections, and lessons that will be revised.

The table below is the complete set. [`index.html`](index.html) is the browsable door and lists the lessons published as artifacts, which is a subset — where the two disagree, this table is the whole course. Every page is a plain file in this directory — no server, no account, no network except the typefaces, which fall back to a system stack if they do not load.

| | |
|---|---|
| lessons | **49** |
| slides | **363** |
| figures re-derived from live code | **3007** |
| exercises | **34** |
| reads in the reference | **49** |

*These lessons teach the instrument. For what is running today versus designed, the canonical page
is [`../vision/roadmap.md`](../vision/roadmap.md).*

## What makes these different from documentation

**Every number on every slide is recomputed from the running code each time a page is built**, by the build that produces them, which fails if a single value has moved. The builders and the audits are held with the research code; `shared/verify_all.py` re-runs every audit.

Three disciplines are enforced mechanically rather than by care:

- no mathematical symbol is drawn before it has been named in words
- quantitative figures are generated from the audited values, never hand-placed
- an audit compares against the **rendered page**, not the builder's variables — which is how a slide showing a literal `%s` placeholder was caught

**Where something could not be measured, the lesson says so** rather than borrowing credibility from the rest. Six mistakes made while building the course are kept on slides instead of corrected away; they are the most useful part.

## The lessons

| | lesson | subject | slides | figures |
|---|---|---|---:|---:|
| A1 | [The Screen, the Lens, the Projection](a1-screen-lens-projection.html) | organise, scale and project a grid of measurements | 25 | 196 |
| A2 | [The Aperture Reads](a2-aperture-reads.html) | the shape of a signal: fill, etendue, Strehl, focus | 12 | 70 |
| A3 | [The Point-Spread](a3-point-spread.html) | what a signal does along its ordered axis | 10 | 58 |
| A4 | [Is There Anything There?](a4-permutation-null.html) | whether an ordered record carries any order at all | 11 | 93 |
| A5 | [An Ordered Axis With No Numbers](a5-sequences.html) | reading a record made of symbols, with no numbers in it | 11 | 64 |
| A6 | [Two Screens Meeting](a6-two-screens.html) | two records on one screen: coupling, crossing, brightness | 10 | 58 |
| A7 | [What Does Nothing Look Like?](a7-what-nothing-looks-like.html) | the null itself, and what choosing it wrong costs | 9 | 24 |
| A8 | [The Third Axis](a8-third-axis.html) | when a record has three axes and a screen has two | 9 | 45 |
| A9 | [Too Wide to Read at Once](a9-too-wide.html) | when a field is wider than any single read | 5 | 27 |
| A10 | [How Much Is Enough?](a10-how-much.html) | how much record the instrument needs before its answer means anything | 6 | 32 |
| A11 | [What Is This Number Measuring?](a11-what-is-it-measuring.html) | three tests that establish what a number is measuring | 6 | 79 |
| A12 | [How Many Are Working?](a12-how-many-are-working.html) | the count behind the shape: how many columns carry the record | 5 | 75 |
| A13 | [How Much of This Is Scatter?](a13-how-much-is-scatter.html) | a read that reports its own uncertainty, and the record it disqualifies | 4 | 69 |
| A14 | [Where the Line Goes](a14-where-the-line-goes.html) | the per-mode evidence a count is drawn through, and who draws it | 3 | 95 |
| A15 | [A Count and a Weight](a15-a-count-and-a-weight.html) | how many modes stand up, and how much they carry | 4 | 83 |
| A16 | [How Far Is That From Nothing?](a16-how-far-from-nothing.html) | six reads against their own nulls, and why the eye is the wrong instrument | 4 | 53 |
| A17 | [Did the Record Change?](a17-did-it-change.html) | a record that is two things end to end, and the split that finds it | 4 | 26 |
| A18 | [The Control Inside the Record](a18-the-control-inside.html) | two ways to split a record, and why one of them is the control | 4 | 49 |
| A19 | [Where Did It Change?](a19-where-did-it-change.html) | finding the point a record changed, and why the largest ratio is not it | 4 | 53 |
| A20 | [More Than One Change](a20-more-than-one-change.html) | a record that changes and changes back, and the window that finds it | 5 | 64 |
| B1 | [How a Lens Works](b1-how-a-lens-works.html) | what a lens must preserve, and what it may drop | 9 | 40 |
| B2 | [Projection, Comparison, Transfer](b2-projection-comparison-transfer.html) | three reads that sound alike and are not | 11 | 39 |
| B3 | [Propagation and Attenuation](b3-propagation-attenuation.html) | four mechanisms under one ceiling | 9 | 55 |
| B4 | [Two Lenses in Series](b4-in-series.html) | which certificates survive being chained, and which compound | 6 | 33 |
| B5 | [Telling Things Apart](b5-telling-things-apart.html) | the diffraction limit, measured on records: how many things can be resolved | 5 | 109 |
| B6 | [The Constant in Front](b6-the-constant-in-front.html) | the shape factor: the number that turns a measured width into a gap | 8 | 94 |
| B7 | [Only Two Endings](b7-only-two-endings.html) | a decay built of finitely many pieces has two endings, and the third costs infinitely many | 7 | 117 |
| C1 | [Signal](c1-signal.html) | how words become coordinates with no model | 9 | 49 |
| C2 | [A Record That Is a Picture](c2-a-picture.html) | the instrument on a photograph, where a reader can see what the numbers mean | 5 | 59 |
| C3 | [Two Populations in One Record](c3-two-populations.html) | land and sea floor in one grid, and the control that decides what the difference means | 4 | 25 |
| C4 | [Observer](c4-observer.html) | why tense is decay | 9 | 38 |
| C5 | [Generation Without a Model](c5-operators.html) | continuing a sentence with no trained weights anywhere | 7 | 21 |
| D1 | [The Lens Inside a Language Model](d1-jacobian-lens.html) | a language model's transport, read as geometry | 8 | 45 |
| D5 | [A Claim About the World](d5-mass-gap.html) | checking a claim about physical reality, and what checking found | 9 | 88 |
| D5a | [The Entropy Floor](d5a-entropy-floor.html) | the counting that fixes kappa_0, carried out in front of the reader | 10 | 36 |
| D5b | [The Margin](d5b-the-margin.html) | why the vortex tension stays under the floor at every coupling | 8 | 39 |
| D5c | [The Read](d5c-the-read.html) | how a gauge configuration becomes a decay rate and a mode count | 8 | 45 |
| D5d | [From Decay to Spectrum](d5d-decay-to-spectrum.html) | how a measured rate becomes a statement about a Hamiltonian | 9 | 43 |
| D5e | [The Whole Calculation](d5e-the-whole-calculation.html) | the mass gap in eight lines, every one of them typeable | 7 | 84 |
| D5f | [The Window](d5f-the-window.html) | which theories have a gap, decided by two coefficients and whole numbers | 6 | 84 |
| D5g | [The Ceiling](d5g-the-ceiling.html) | the gap as one comparison: the counting floor, exponentiated, against a measured ratio | 7 | 84 |
| D5h | [The Count That Holds](d5h-the-count-that-holds.html) | a count that stays put as you look more finely, and what a limit carries | 7 | 77 |
| D5i | [Both Sides of the Cut](d5i-both-sides-of-the-cut.html) | cut a system in two and both halves report the same entropy, to twelve decimals | 5 | 69 |
| D5j | [One Read, Two Names](d5j-one-read-two-names.html) | each quantity the physics names, measured, and the unit that works it out | 5 | 61 |
| D5k | [Two Verdicts](d5k-two-verdicts.html) | two correct tests on eight records, and the three where they point opposite ways | 5 | 52 |
| D8 | [What We Are Teaching On](d8-the-records.html) | the two files every number comes from, and what they do not say | 6 | 36 |
| E1 | [One Question, All the Way Down](e1-end-to-end.html) | one sentence through every tier, and what each step measured | 6 | 42 |
| E2 | [Where the Instrument Is Blind](e2-blind.html) | perfect relationships it reports as noise, and the rule that says which | 5 | 47 |
| E3 | [Your Own Record](e3-your-own-record.html) | the whole procedure, in order, on a record this course has never read | 6 | 53 |

## Also here

| | |
|---|---|
| [Try it yourself](exercises.html) | exercises on the two records, every answer computed at build time |
| [The words](glossary.html) | the course's terms, the unit that owns each, and how widely it is used |
| [Every read, in one place](reference.html) | all 49 reads, with a live value on both records |
| [Reading a Jacobian Lens](report-jacobian-lens.html) | the full research report behind unit D1, from before the course |
| [Where to go next](next.html) | the handover: each read's identifier in the library, where the code and data live, and how to support the work — written by hand, carrying no measured figures |
| [index.html](index.html) | the entry point, linking everything above |

## Provenance

These pages are also published as artifacts on claude.ai. **This directory is the record** — the artifacts are a convenience.
