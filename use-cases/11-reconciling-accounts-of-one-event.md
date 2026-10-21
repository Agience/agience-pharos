# 11 — Reconciling several accounts of one event

**For:** anyone who must establish what happened from several people's memories — an incident
review, a site inspection, a meeting with no recording.

## The story

Four people attended the same incident and each wrote it up. Their accounts differ on times and
details. The reviewer wants one account that says what all agree on, where they differ, and whose
evidence supports which version — without anyone's original being overwritten.

## How the platform does it

- Each account is an artifact in a shared collection, kept as written.
- Evidence — photos, logs, messages — is attached as artifacts the accounts point to.
- A reconciliation operator writes a new artifact: points of agreement, points of conflict, and the
  evidence behind each. A person settles the conflicts.

## What it uses today

Collections, grants, provenance pointers, commits (a collection's History in the app); Lumen's
`reconcile` operator, which compares the facts each account states as `Label: value` lines (an
incident form's shape) and writes the reconciliation beside them, with each difference raised as
an Inbox item; a workflow card built from the "Reconcile accounts" recipe in the app; the Inbox.
An account's `evidence` (artifact ids in its context) is carried to each version it supports.

## To build

- Facts read out of free prose, through an operator on an LLM connection; today a narrative that
  states no `Label: value` lines is kept and not compared.

## Checked by

`usecase_11_several_accounts_of_one_event.py`: a reviewer files three accounts, builds a workflow
from the "Reconcile accounts" recipe in its card — the platform's operator — picking the accounts
with the app's picker, and runs it; the accounts are a link from the workflow; the reconciliation
names the one agreement, the conflict with each version and its accounts, and the two single
statements; no account changes; the conflict waits in the reviewer's Inbox and is settled from its
card. A second check builds a two-step workflow from operators whose second step takes the first
one's output (the reconciliation's id, chosen in the form), runs it, finds the run recorded step by
step in the workflow's Runs collection, and schedules it to record there with failures to the
Inbox, on a key that reaches each operator and link. A third finds the builder offers operators,
not tools, and that an operator calling itself the platform's is listed as shared.
