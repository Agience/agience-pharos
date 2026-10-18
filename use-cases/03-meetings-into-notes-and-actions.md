# 03 — Meetings into notes, decisions and actions

**For:** teams whose decisions are made in meetings and lost afterwards.

## The story

Lisa leads a product team. She wants each meeting to end with notes, the decisions taken, and who
owes what by when — filed where the team works, linked to what was discussed, without anyone taking
notes.

## How the platform does it

- The meeting is a stream (Astra's stream sessions); its transcript is written into the session.
- Decisions and action items are pulled from the transcript as artifacts, each pointing at the span
  it came from, so a person can check it against what was said.
- Actions land in the team's workspace with an owner and a date; a **Timer** reminds the owner.

## What it uses today

Astra's stream sessions and stream keys, workspaces, artifacts with provenance pointers.

## To build

- **Transcription** — speech to text into the session.
- An extractor for decisions and actions that cites its spans.
- **Timer** — reminders for due actions.

## Checked by

Not yet.
