# 13 — A research question answered with its sources

**For:** researchers and analysts who need an answer they can defend, with every claim traced to
where it came from.

## The story

An analyst asks a question across a large body of reports. They want the passages that bear on it,
the answer those passages support, and a link from every sentence of the answer to its source — and
to see where the sources disagree.

## How the platform does it

- The reports are artifacts. Sage's retrieval (`op.retrieve`) finds the ones that bear on the
  question, under the asker's own grants, so nobody is answered from what they cannot read.
- Lumen's `synthesize` returns those passages as the answer, each extract numbered and cited by the
  artifact it came from, with a numeric reading when the question carries a series. It writes no
  sentence of its own: with no evidence it returns a refusal that says which steps found nothing.
- Where sources disagree, the differing extracts stand side by side.

## What it uses today

Sage's `retrieve`, reached by Lumen through Crystal on the asker's own token; Lumen's `synthesize`;
recall over the blind-token index; collections.

## To build

- A composed answer: sentences that each cite the extracts they rest on, still writing nothing the
  evidence does not carry.
- Retrieval that finds passages sharing no words with the question.
- A view that shows each sentence beside its source.

## Checked by

`usecase_13_a_question_answered_from_your_notes.py`: a person's question is answered from their own
note, cited by its id and carrying what it says; another person asking the same question gets none
of it, and a refusal instead.
