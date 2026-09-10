# Vision — what is intended, and not yet built

**Nothing in this tree is a description of working software.** It is direction: designs that are
planned, specified but unimplemented, or built and not yet general. Read it as intent.

For what exists, see [`../design/`](../design/) and [`../features/`](../features/). For what has
been measured, [`../research/`](../research/).

| doc | what it is | state |
|---|---|---|
| **[`genesis.md`](genesis.md)** | **The guiding path** — from a bare store to better-than-frontier without a single model. Its own header calls it *the plan of record: everything we have learned, and everything we will do* | plan |
| [`sovereign-stack-and-standalone-mantle.md`](sovereign-stack-and-standalone-mantle.md) | Standalone Mantle with Origin as Agience's own identity provider — a hand-off spec. **Most of it shipped** — grants moved into Mantle's own store, API keys became grants, and key custody is pluggable — under different names than the spec chose; the document says which two items did not | mostly built |
| [`transport-security-roadmap.md`](transport-security-roadmap.md) | The designed hardening above the token requirements — IP-origin binding, DPoP, mTLS, viewer iframe auth, the deployment package. The hardening itself is unbuilt; the viewer iframe path is partly shipped and the document says which parts | mostly unbuilt |
| [`roadmap.md`](roadmap.md) | **Where it actually stands** — five states rather than two, five gates, six dependency-ordered stages each with a completion test, and no invented dates | sequence |
| [`frontier-training.md`](frontier-training.md) | The corpus — a genealogy of the datasets that trained the frontier models, and what a model-free system needs instead | direction |
| [`information-model.md`](information-model.md) | Content, context, transform — the design spec for how information is typed and moved | reference |
| [`device-mesh.md`](device-mesh.md) | **Every device is a host** — the four tiers, from a wearable to a stratospheric relay, and the blind key-borne link between them | Not built |
| [`platform-vision.md`](platform-vision.md) | The earliest statement of platform direction, written 2026-03-05 | history |

## Read `platform-vision.md` as history

It predates the GENESIS design and uses the vocabulary of that period, so it does not match the
terms used elsewhere here. The current statement of direction is
[`../start/the-story.md`](../start/the-story.md), which supersedes it wherever the two disagree.
