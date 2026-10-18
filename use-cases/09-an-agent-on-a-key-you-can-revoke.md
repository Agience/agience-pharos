# 09 — An agent works on a key you issue and can revoke

**For:** anyone who wants software to act for them — deploy, monitor, file, reconcile — without
handing over their accounts.

## The story

Dana runs a small service on infrastructure she owns. A deployment agent does the routine work. She
wants three things: the agent can touch only what she gave it, everything it does is written down
where she can read it, and she can cut it off in one step.

## How the platform does it

- Dana keeps an **operations log**: a collection.
- From a **Key card** she issues a key that reaches that collection, read-only or read-write, and
  nothing else. A key is a grant: its permissions are everything it can do (`POST /grants/keys`).
  The secret is shown once, by the app, never to the card's view.
- The agent is any MCP client. It holds only the key and speaks to the store's MCP endpoint
  (`/mcp`): it records what it did as artifacts in the log.
- What the agent writes belongs to Dana and names the key that wrote it.
- Dana revokes the key from the same card. The agent is refused from that moment; the record stays.

## What it uses today

Collections, grant keys, the Key card (Seraph's view in the app), the store's MCP tools
(`create_artifact`, `get_children`, `recall`), revocation. For the agent's routine checks: a workflow
(Transform cards, run by Lumen) on a Timer card the owner arms, which runs on a key over the
workspace and records each run in the log. An agent may also be an MCP server Dana adds herself;
the platform reaches it through Iris and its egress guard.

## To build

- **History view** — Dana reads the log's commits and access log in the app, not only the records.
- **Data connectors** — the agent reads the infrastructure through keys Dana issues to those
  systems, and the platform checks what the agent reports against what the systems say.
- A key that can remove what it wrote (today it creates, reads and updates).

## Checked by

`usecase_09_an_agent_works_on_a_key_you_can_revoke.py`: the owner issues the key from the Key card in
the app; an MCP client holding only the key records a deployment into the log, is refused anywhere
else, and is refused entirely once the owner revokes the key from the card. A workflow runs every
minute on a Timer the owner arms from its card, recording each run in the log, until the owner
disarms it and the timer's key is revoked. A server card pointing at the node's own address is
refused, and a server card is reached only by those who can read it.
