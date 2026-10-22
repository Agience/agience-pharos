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

Collections, grants, invitations (a link the clinic sends, claimed once), per-principal encryption,
the access log, read in the app from a card's History, where each reader is named by the name the
clinic gave their invitation. A single card can be shared on its own: the nurse holds the care plan
and nothing else in the record.

## To build

- **Context encryption** beyond the title (sealed with the content) — the other `context` fields encrypted at rest.
- An import from standard health-record formats.
- An install on the clinic's own hardware (roadmap gate G5).

## Checked by

`usecase_07_a_clinical_practice_record.py`: a clinic keeps two patients' records and a Billing
collection whose cards are linked into each record. A physician, a nurse, billing and a patient each
join by an invitation link: the physician to both records, the nurse to one care plan, billing to
Billing, the patient to their own record. Each reads their part and nothing else, by id, by listing
and by search; the patient cannot change their record; every read and every refused attempt is on
the card's access log, which only the clinic reads, and which names each reader by the name the
clinic gave their invitation.
