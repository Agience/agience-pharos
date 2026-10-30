# 16 — Your data is reachable by you and those you grant, no one else

**For:** teams whose material cannot leave their control — operational plans, budgets, internal
assessments — and who have been told a hosted tool is the only way to search it.

## The story

An analyst keeps assessment notes in a workspace. They need to find them again by what they say,
share them with one colleague for a review, and take that access back afterwards. Nobody else on the
same platform — and not the platform's operator — should be able to read them.

## How the platform does it

- Every artifact is reached through a grant. Without one, every surface answers as if it did not
  exist: by id, in a container's listing, in search, through operations, over MCP.
- Content and every field of it are sealed at rest under a per-principal key, and search runs over a
  blind-token index the store cannot read. The store reads only structure: identifiers, types,
  timestamps, and the grant edges it walks to decide who is issued a key (`design/architecture.md` §24).
- Sharing is a grant to a named person, at the permissions given. Revoking it removes the access at
  once, search included.

## What it uses today

Grants, per-principal sealing of the content and its title, the blind-token search index,
recall.

## To build

- An install on the team's own hardware that a stranger can complete (roadmap gate G5), for teams
  that cannot use any shared node.

## Checked by

`usecase_16_your_data_is_yours_alone.py`: three accounts. The owner's search finds the note; the
other two reach it by no surface; a grant gives one colleague read and search, and revoking it takes
both away. On the node, neither the note's content nor its title is in any file the store wrote, and the
owner still reads the title back.
