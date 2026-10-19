# 08 — Credit for contributions in an open collaboration

**For:** large open groups — a long-running public puzzle, an open research effort — that stall
because contributors will not share ideas without credit.

## The story

Hundreds of people work on a public problem in a chat group. The best ideas are held back: whoever
posts one first gets no credit if someone else finishes it. The group wants a record where each idea
is stamped with who contributed it and when, so sharing is safe.

## How the platform does it

- Ideas worth keeping are filed from the chat into a shared collection, each with its contributor
  and time.
- A later contribution that builds on an earlier one points at it, so the chain of credit is
  explicit.
- Grants decide who can read and who can add; the commit history is the timeline.

## What it uses today

Collections, grants, artifacts with provenance pointers, commit history (read in the app from a
collection's History), recall.

## To build

- **Inbound adapters** — filing from a chat group.
- **Reconciliation operator** — grouping overlapping ideas and showing who came first.

## Checked by

Not yet.
