# Changelog

All notable changes to `agience-pharos` are recorded here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [2026.10.12] - 2026-10-17

### Changed

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
