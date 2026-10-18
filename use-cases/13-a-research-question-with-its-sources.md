# 13 — A research question answered with its sources

**For:** researchers and analysts who need an answer they can defend, with every claim traced to
where it came from.

## The story

An analyst asks a question across a large body of reports. They want the passages that bear on it,
the answer those passages support, and a link from every sentence of the answer to its source — and
to see where the sources disagree.

## How the platform does it

- The reports are artifacts; recall finds the passages that bear on the question, including those
  that share no words with it.
- Sage builds the answer from those passages, citing each one; a sentence with no source is not
  written.
- Disagreements between sources are reported as such, with both sides.

## What it uses today

Recall (model-free retrieval over the blind-token index), Sage's research and lexicon tools,
collections.

## To build

- The cited-answer composer as a Sage operator.
- A view that shows each sentence beside its source.

## Checked by

Not yet.
