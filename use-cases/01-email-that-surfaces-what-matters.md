# 01 — Email that surfaces what matters, with drafts for approval

**For:** people who receive far more mail than they can read, most of it routine.

## The story

Sarah, a product manager, gets over a hundred messages a day. She wants to be told only about the
few that need her, have newsletters gathered into one digest, and find replies already drafted for
the routine ones — sent only when she approves.

## How the platform does it

- Each message arrives as an artifact in her workspace, with sender, thread and body.
- A describer pulls out what a message asks for and when; recall relates it to what she already
  holds, so "is this new?" is answered from her own record.
- What needs her becomes an **Inbox** item. Routine replies are drafted into the Inbox as approvals.
- A **Timer** builds the newsletter digest each morning.
- Approving a draft sends it through Iris on her own mail authorizer.

## What it uses today

Workspaces, describers, recall, Iris's `send_email` through an Authorizer artifact; the Timer
for the morning digest; the Inbox for what needs her.

## To build

- **Inbound adapters** — receiving mail as artifacts.
- A draft reply as an Inbox item whose approval sends it through Iris (an item's action is an
  artifact's operation; sending is a persona tool, so it needs a Transform artifact that sends).

## Checked by

Not yet.
