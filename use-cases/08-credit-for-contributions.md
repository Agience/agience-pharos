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
- Grants decide who can read and who can add: a contributor reads every idea and adds their own,
  and changes nothing anyone else made. Each idea carries who added it and when, for every reader;
  the host's access log on the collection records each addition and every refused change.

## What it uses today

Collections, grants (the `contributor` role: add and read), invitation links, artifacts with
provenance pointers (`created_by`, `created_time`, and `builds_on` naming an earlier idea), the access
log (read in the app from a collection's History), recall.

## To build

- **Inbound adapters** — filing from a chat group.
- Grouping overlapping ideas and showing who came first: a reconciliation over free prose, which
  needs an operator on an LLM connection (Lumen's `reconcile` compares stated facts only).

## Checked by

`usecase_08_credit_for_contributions.py`: two contributors join a puzzle's ideas by one invitation
link and a reader by another; each idea shows every reader who added it and when, and the second
names the first it builds on; a contributor can neither rewrite nor remove another's idea, a reader
adds nothing, and the host's access log records each addition and the refused rewrite.
