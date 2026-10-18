# 12 — A shared map from a crowd at a live event

**For:** organisers and spectators of an event spread over distance — a marathon, a regatta, a
cycling race — where no single camera sees everything.

## The story

Thousands of spectators film a race from the roadside. The organiser wants their clips and
observations placed on one shared map of the course as the race happens, so anyone can see what was
seen where and when.

## How the platform does it

- Spectators submit to a write-only share: they can add, not read each other's submissions.
- Each submission is an observation artifact with time and place, pointing at its media.
- A reconciliation operator places observations on the course and groups those of the same moment.
- The map is a view over the observation collection.

## What it uses today

Collections, grants (including write-only shares), stream ingestion.

## To build

- **Transcription** and media placement.
- **Reconciliation operator**.
- The map view.
- Later: 3D reconstruction from many cameras, which needs heavy compute.

## Checked by

Not yet.
