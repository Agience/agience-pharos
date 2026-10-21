# Changelog

All notable changes to `agience-pharos` are recorded here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [2026.10.12] - 2026-10-17

### Added
- `use-cases/`: fifteen use cases, each with what it uses today, what has to be built for it, and how it is checked; and the components several of them need (Timer, Inbox, inbound adapters, data connectors, context encryption and others). Use cases 09 (an agent on a key you can revoke) and 16 (your data is yours alone) are checked by harnesses on staging.

### Changed
- `vision/roadmap.md` §2 and `research/paper-2` §98: `mantle.agience.ai` serves the hosted store (870 records, 6.3 MB, no ontology); the 6.49M-record lattice is archived in object storage and not served. `start/overview.md`: Lumen runs workflows and timers and answers with cited evidence.
- `vision/information-model.md` and `use-cases/`: every workflow run is a Run card in the workflow's Runs collection, and a step's inputs can name an earlier step's output (`$.previous`, `$.steps.<name>`).
- `use-cases/`: use case 11 is checked by a harness (Lumen's `reconcile`, built from a recipe in the workflow builder); the README says how a person makes their own use case in the app; the reconciliation operator is built for stated facts.
- `design/architecture.md` and `use-cases/`: the title is part of an artifact's content, sealed at rest; the other context fields remain plaintext.
- `use-cases/13`: a connection's key leaves the store only sealed to Lumen's own key, for that card.
- `use-cases/13`: an LLM connection card can be invoked (it calls the model it names on the caller's grant to its key); a composed answer from `synthesize` through it is what remains.
- `use-cases/` and `vision/information-model.md`: the Inbox is built (an Inbox item moves new → triaged → resolved; approving runs its action as the approver; a timer raises a failed run into its owner's Inbox); use cases 01, 02, 04 and 05 list the Timer and the Inbox under what they use today.
- `use-cases/`: the History view is built (a card's commits and its access log, in the card's window); use cases 07–11 list it under what they use today.
- `use-cases/13`: a composed answer is to come through an LLM connection card (the type exists; it declares no operation yet), and reach ranking needs the ontology on the node and a public-set measurement first; predicting embeddings is not the route (`research/paper-2` §65).
- `use-cases/13`: what `synthesize` returns today (cited extracts from what the asker can read, or a refusal) and what is to build; `vision/transport-security-roadmap.md`: a persona calling another sends the caller's own credential.
- `vision/information-model.md`: the workflow runner's step types are the three that exist (`mcp-tool`, `transform-ref`, `workflow`), and a Timer card runs a workflow on a grant key; `vision/transport-security-roadmap.md`: a grant key travels the persona path without an exchange.
- `vision/transport-security-roadmap.md`: a viewer's view and tool calls go through `POST /personas/{server}/...` on Crystal to the persona that served it, which exchanges the forwarded user token for a delegation.

- `vision/roadmap.md` records G1 and G2 as met and G3 as armed, with the 2026-10-14 restore of the newest backups to scratch; Stage 2 states that the largest corpus is on a local node outside the platform's backups.
- `design/architecture.md` describes Trash as the deletion policy a person's Delete runs: archived into their Trash, restorable for 30 days, then deleted as them.
- `features/prism-protocol.md` places the builtin types inside Crystal's package and describes the root type, `*/*` (Artifact), that every type inherits from.
- `features/prism-protocol.md` describes a type's `offer` (the paths that give its artifacts their title, description and tags) and how Crystal publishes type definitions to Mantle's indexer.
- `use-cases/README.md`, use case 11 and `vision/information-model.md` §4.2: a workflow's steps are operators invoked through their grants, the platform's own granted to every account one by one; an artifact a step works on is a typed link from the workflow; a timer's key reaches each operator and link, narrowed at each use to what its owner holds.
- `vision/information-model.md` §4.2: a platform operator's tool is reached only by those granted the operator, directly or through any operator card; running a person's own MCP server needs `invoke` on its card; removing a step removes its links.

## [2026.10.0] - 2026-10-09

### Changed

- The design and vision documents (`design/architecture.md`, `design/spine.md`, `design/components.md`, `vision/information-model.md`, `vision/information-gauge-db.md`, `vision/platform-vision.md`, `vision/genesis.md`, `start/the-story.md`) describe the current artifact model: `state` is `live` or `archived`, an artifact is live when written, and there is no commit gate. Promotion by mass and dark matter are described as design; no code computes mass.
- The documents describe reads of archived artifacts, lineage head resolution (newest non-archived version under a root), restoring a retired lineage, and the observer rung recorded on create.
- `vision/information-gauge-db.md` describes the mass-gap read on a collection's membership as reported and fed into mass, not as a write gate.
- Edge counts in `design/architecture.md` are re-measured (`crystal -> prism` is 14).
- `research/extraction-axiom.md` adds a section on non-locality: an aperture limited to the bits it receives about remote data yields the Tsirelson bound 2√2 (Information Causality).
- `research/paper-2-knowledge-without-weights.md` lists `state` as `live | archived`.
- The README's declaration of generative AI use adds Claude Opus 5.5.
