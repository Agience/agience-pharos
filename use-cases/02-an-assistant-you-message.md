# 02 — An assistant you message from your phone

**For:** anyone who wants to ask about, add to, and be reminded from their own record without
opening an app.

## The story

Marcus runs a small business from his phone. He wants to message "what's on today?", "add milk to
the list", or "what did the supplier say about the delivery?" and get an answer from what he keeps —
and be messaged first when something needs him.

## How the platform does it

- A messaging adapter turns each message into a request on his behalf, on a key he issued to it.
- Questions are answered by recall over his workspaces, with the artifacts the answer came from.
- "Add" writes an artifact into the list's collection.
- A **Timer** and the **Inbox** decide what is worth messaging him about, and when.

## What it uses today

Workspaces and collections, recall, grant keys, Lumen for composing a reply; the Timer and the
Inbox for what is worth raising unasked.

## To build

- **Inbound adapters** — a chat channel (for example, a messaging bot) in both directions.

## Checked by

Not yet.
