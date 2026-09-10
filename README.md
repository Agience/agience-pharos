# Pharos

[![License](https://img.shields.io/badge/license-CC--BY--4.0-blue)](LICENSE)
[![Docs](https://img.shields.io/badge/docs-90%20documents-6E56CF)](#the-trees)
[![State](https://img.shields.io/badge/built%20vs-intended-0F9D58)](#what-is-built-and-what-is-intended)

**The lighthouse — Agience's documentation, and the system's knowledge of itself.**

Agience is a model-free intelligence platform: a self-compressing knowledge universe built on one
store, where every capability is a shareable, signed, content-addressed artifact. Signals cross a
boundary, condense into typed content, invoke instruments, and every exchange of energy is measured.
The same machinery runs in a browser tab and on a corpus node; the difference is mass, not kind.

The one-line ontology, for orientation:

> A **crystal** = facets (signal conduits) + tektons (condensors) + organons (invoked instruments),
> grown on a lattice. A **bundle** = prisms + crystals. An **ember** = a bundle, **energized** — and
> embers are the same machinery at every scale. The system has no absolute frames and no gates, only
> objects, edges, and measured couplings between relative frames.

## The trees

**Start at [`start/`](start/).** The rest is there when you want it.

| tree | what it holds | state |
|---|---|---|
| [`start/`](start/) | **the door** — three ways in: [the whole thing on one page](start/infographic.html), [the narrative](start/the-story.md), and [the complete picture](start/overview.md) | current |
| [`learn/`](learn/) — [index](learn/README.md) | **the Entroptics course, as files.** 50 lessons from grade-ten mathematics to a working instrument. Start at the [index](learn/README.md), which is the complete set; [`learn/index.html`](learn/index.html) is the browsable door and lists a subset. Every page is a plain file, with no server and no account | work in progress |
| [`features/`](features/) — [index](features/README.md) | **what works today** — the prism protocol, routing and tenancy, transport-bound auth. Everything here describes a surface that exists | current |
| [`design/`](design/) — [index](design/README.md) | **how it is built** — the specification of record, the component map, the one-field substrate, how retrieval works, and the testing standard | current |
| [`research/`](research/) — [index](research/README.md) | **the papers and the physics lineage** — [the instrument](research/paper-1-the-instrument.md) and [knowledge without weights](research/paper-2-knowledge-without-weights.md), the economy [position paper](research/the-economy.md), the information universe, the extraction axiom, and [how to claim a measurement](research/between-sample-variation.md) | current |
| [`vision/`](vision/) — [index](vision/README.md) | **what is intended, and not yet built** — the guiding path, the sovereign stack, the corpus, the information model. Read it as direction, not as working software | future |

## If two documents disagree

[`vision/roadmap.md`](vision/roadmap.md) is the canonical statement of **what is running, what is
built but not armed, and what is only designed**. Where its wording and any other document's — this
overview, the course, a design note — describe the same thing differently, the roadmap is correct
and the other is drift to be fixed. It carries a state marker per item and a completion test per
stage, so a disagreement is checkable rather than a matter of tone.

That precedence covers *status* only. For what a measurement says, the paper that reports it is
canonical; for what a component is, `design/components.md` is.

## What is built, and what is intended

The split is the tree a document sits in. **`design/`** and **`features/`** describe what exists:
`features/` documents declare their own status in their first screen — Draft, Decided, Reference —
and the index reports what each one declares. **`vision/`** is what is intended and **not built**,
and says so in its own opening words.

Where a document describes a surface that is partly built, it ends with **What is still outstanding**:
a table naming each gap and the nearest thing the code does have. Eight documents carry one.

## What is here, and what is not

This repository is the public set. It ships the narrative and the reader's overview, the Entroptics
course, the live capability notes, the design canon, the papers and the physics lineage, and the
forward direction.

Three kinds of material are deliberately absent: the in-progress design records, the measured-state and
claims registers that govern what may be said outward, and the pre-GENESIS product history. They are
working trees, they are not published, and nothing here links into them.

Documents whose evidence is code that is not published are held back with that code rather than shipped
with citations a reader cannot follow.

Licensed under CC-BY-4.0 — see [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE). Corrections and
contributions: [`CONTRIBUTING.md`](CONTRIBUTING.md).

## Declaration of generative AI use

Anthropic's Claude Opus (versions 4.8 and 5) was used throughout: to write code, and to generate
and validate content. No other generative AI tool was used. The ideas, the construction and the
claims are the author's, who reviewed and edited every output and is responsible for all of it.

**Where the work ran matters, and the two are separate.** Model inference was hosted, through the
Claude API. Everything the numbers rest on ran locally on the author's own hardware: the
measurements, the numerical experiments, and the Lean development. No result in this corpus was
produced by a hosted model; a hosted model helped write the prose and the code that produced them.
