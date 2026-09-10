# Contributing

Pharos is documentation, not code. It records what the Agience platform is, how it is designed, and
what has been measured about it.

## What a change should do

A document here states what is true now: what the system does, why it is built that way, and how to
use it. Change history belongs in git rather than in the prose. If a statement has been superseded,
the replacement goes in and the old statement comes out — except where a document records that it
was previously wrong, which is content worth keeping.

A document belongs to the tree that matches it: `design/` and `features/` for what exists,
`vision/` for what is intended and not built. A change that moves a thing from intended to built
moves the document too, and a document describing a partly-built surface ends with a
**What is still outstanding** table rather than burying the gap in prose.

## Claims and measurements

A number in this repository is a claim about a running system. State the method beside it, or state
the date it was measured, or both. A figure with neither is a figure nobody can check.

Where a document cites code, cite code that is published. A citation a reader cannot follow is worse
than no citation: it reads as evidence and resolves to nothing.

## Paths

A path checker measures whether the paths this corpus names still exist.
A change that adds a reference should leave that check no worse than it found it.

## Reporting a problem

Open an issue describing what the document says and what you observed instead. A documentation defect
is a disagreement between the prose and the system; naming both halves is what makes it actionable.

## Licence

Contributions are accepted under the Creative Commons Attribution 4.0 International License (CC BY
4.0), the licence this repository carries. See `LICENSE` and `NOTICE`.
