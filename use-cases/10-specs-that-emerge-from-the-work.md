# 10 — Specifications that emerge from the work

**For:** software teams moving fast with coding tools, whose written specification falls behind the
code within a week.

## The story

A team ships daily. Decisions are made in code review and chat, and the specification no longer
describes the system. They want the specification to grow from the work — written while exploring,
filed when agreed — and to be told when code and specification disagree.

## How the platform does it

- Specifications in progress live in a workspace; agreed ones are filed into a collection, and each
  change to one is a new version of the same specification.
- Each specification artifact is typed (requirement, decision, interface) and points at the code or
  review it came from.
- A coherence pass compares what the code declares with what the specification says and files each
  disagreement for a person to settle.

## What it uses today

Workspaces, collections, versions of each artifact and the access log (a card's History in the app), typed
artifacts, Astra's code and document indexes, grant keys for the agents doing the work.

## To build

- Specification types and the coherence pass as an operator.

## Checked by

Not yet.
