# 04 — Watching the business: cash, pipeline and campaigns

**For:** the owner of a small company who cannot watch every number every day.

## The story

The owner of a fifty-person company wants to know early when cash runway shortens, a large deal
stalls, or a campaign stops paying — and to have the numbers behind a board update gathered without
a week of spreadsheet work.

## How the platform does it

- Connectors read accounting, CRM and campaign figures on keys the owner issues, as dated artifacts.
- A **Timer** reads them on a schedule; a change outside its usual range becomes an **Inbox** item,
  with the figures it came from.
- A board update is assembled from the period's artifacts, each figure linked to its source.

## What it uses today

Collections, grant keys, recall, Lumen for drafting; the Timer to read the figures on a schedule
and the Inbox for a change that needs the owner.

## To build

- **Data connectors** — accounting, CRM, advertising.
- A change-detection operator that reports a shift with its evidence rather than a threshold.

## Checked by

Not yet.
