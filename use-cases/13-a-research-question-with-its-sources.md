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
  artifact it came from. It writes no sentence of its own: with no evidence it returns a refusal
  that says which steps found nothing.
- Where sources disagree, the differing extracts stand side by side.

## What it uses today

Sage's `retrieve`, reached by Lumen through Crystal on the asker's own token; Lumen's `synthesize`;
recall over the blind-token index; collections.

## To build

- A composed answer, through an **LLM connection** card: the cited extracts handed to the model the
  person connected, which writes sentences that each cite the extracts they rest on. The card type
  exists (provider, model, endpoint, a pointer to the stored key); it declares no operation yet,
  so nothing can invoke it.
- Ranking and cutting the evidence by measured reach toward the question (Mantle's `rank` and
  `cut_for`), which finds notes sharing no words with it. It needs the ontology loaded on the node
  (production holds none) and a measurement on a public labelled set first: a 12-note check found
  it no better than keyword order once word weights were on (3 of 7), with an erratic cut.
  Predicting the next embedding of a conversation was measured null in
  `research/paper-2-knowledge-without-weights.md` §65 and is not the route.
- A view that shows each sentence beside its source.

## Checked by

`usecase_13_a_question_answered_from_your_notes.py`: a person's question is answered from their own
note, cited by its id and carrying what it says; another person asking the same question gets none
of it, and a refusal instead.
