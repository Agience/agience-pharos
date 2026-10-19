# Changelog

All notable changes to `agience-pharos` are recorded here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [2026.10.12] - 2026-10-17

### Added
- `use-cases/`: fifteen use cases, each with what it uses today, what has to be built for it, and how it is checked; and the components several of them need (Timer, Inbox, inbound adapters, data connectors, context encryption and others). Use cases 09 (an agent on a key you can revoke) and 16 (your data is yours alone) are checked by harnesses on staging.

### Changed
- `use-cases/13`: what `synthesize` returns today (cited extracts from what the asker can read, or a refusal) and what is to build; `vision/transport-security-roadmap.md`: a persona calling another sends the caller's own credential.
- `vision/information-model.md`: the workflow runner's step types are the three that exist (`mcp-tool`, `transform-ref`, `workflow`), and a Timer card runs a workflow on a grant key; `vision/transport-security-roadmap.md`: a grant key travels the persona path without an exchange.
- `vision/transport-security-roadmap.md`: a viewer's view and tool calls go through `POST /personas/{server}/...` on Crystal to the persona that served it, which exchanges the forwarded user token for a delegation.

- `vision/roadmap.md` records G1 and G2 as met and G3 as armed, with the 2026-10-14 restore of the newest backups to scratch; Stage 2 states that the largest corpus is on a local node outside the platform's backups.
- `design/architecture.md` describes Trash as the deletion policy a person's Delete runs: archived into their Trash, restorable for 30 days, then deleted as them.
- `features/prism-protocol.md` places the builtin types inside Crystal's package and describes the root type, `*/*` (Artifact), that every type inherits from.
- `features/prism-protocol.md` describes a type's `offer` (the paths that give its artifacts their title, description and tags) and how Crystal publishes type definitions to Mantle's indexer.

## [2026.10.0] - 2026-10-09

### Changed

- The design and vision documents (`design/architecture.md`, `design/spine.md`, `design/components.md`, `vision/information-model.md`, `vision/information-gauge-db.md`, `vision/platform-vision.md`, `vision/genesis.md`, `start/the-story.md`) describe the current artifact model: `state` is `live` or `archived`, an artifact is live when written, and there is no commit gate. Promotion by mass and dark matter are described as design; no code computes mass.
- The documents describe reads of archived artifacts, lineage head resolution (newest non-archived version under a root), restoring a retired lineage, and the observer rung recorded on create.
- `vision/information-gauge-db.md` describes the mass-gap read on a collection's membership as reported and fed into mass, not as a write gate.
- Edge counts in `design/architecture.md` are re-measured (`crystal -> prism` is 14).
- `research/extraction-axiom.md` adds a section on non-locality: an aperture limited to the bits it receives about remote data yields the Tsirelson bound 2√2 (Information Causality).
- `research/paper-2-knowledge-without-weights.md` lists `state` as `live | archived`.
- The README's declaration of generative AI use adds Claude Opus 5.5.
