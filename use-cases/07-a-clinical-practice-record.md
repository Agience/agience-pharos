# 07 — A clinical practice's records, each reader seeing their part

**For:** a small clinic whose records are read by physicians, clinicians, administrators and the
patients themselves, each of whom should see only their part.

## The story

A clinic keeps visit notes, results and billing. A physician needs a patient's whole history; a
nurse needs the current plan; billing needs codes and dates but not notes; a patient needs their own
record and nobody else's. Every read should be on record.

## How the platform does it

- Each patient's record is a collection; visits, results and plans are artifacts in it.
- Each role is a grant at the permissions that role needs; a patient holds a read grant on their own
  record.
- Content is encrypted at rest; search runs over the blind-token index.
- Every read and write is in the access log, which the clinic can review.

## What it uses today

Collections, grants, invitations, per-principal encryption, the access log.

## To build

- **Context encryption** — titles and other `context` fields encrypted at rest.
- **History view** — the clinic reads who opened what, in the app.
- An import from standard health-record formats.
- An install on the clinic's own hardware (roadmap gate G5).

## Checked by

Not yet.
