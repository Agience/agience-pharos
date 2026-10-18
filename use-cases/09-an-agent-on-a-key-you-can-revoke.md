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
(`create_artifact`, `get_children`, `recall`), revocation.

## To build

- **History view** — Dana reads the log's commits and access log in the app, not only the records.
- **Timer** — the agent's routine checks run on a schedule the platform keeps.
- **Data connectors** — the agent reads the infrastructure through keys Dana issues to those
  systems, and the platform checks what the agent reports against what the systems say.
- A key that can update or remove what it wrote (today it creates and reads).

## Checked by

`usecase_09_an_agent_works_on_a_key_you_can_revoke.py`: the owner issues the key from the Key card in
the app; an MCP client holding only the key records a deployment into the log, is refused anywhere
else, and is refused entirely once the owner revokes the key from the card.
