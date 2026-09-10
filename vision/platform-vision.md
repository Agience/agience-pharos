# Agience: An Operating System for the Knowledge Economy

Status: **Draft**
Date: 2026-03-05

---

## What This Is

This document describes the structural reality of the system being built, at full scope.

Agience looks like a knowledge management platform. It is an operating system for the knowledge economy — a full-stack, non-custodial infrastructure that makes truth provable, governance accountable, and capture structurally pointless.

> **The chain layer here is research (measured 2026-07-31).** Everything below that speaks of a
> chain, a block, a validator set, a merit token or a DAO — the layer-4 stack entry, the artifact
> stamp and validation-record rows, *The Trust Economy*, *Governance Resilience*, *Community
> Handover* — is written in the present indicative but describes nothing that exists. There is no
> chain, no validator, no block and no DAO in any live repo, and no public token exists or is
> promised. Read those passages as design, not as behaviour you can observe.

---

## The OS Analogy Is Literal

The Information OS Analogy maps Agience to filesystem and operating system concepts. That analogy is a description of the architecture:

| OS concept | Agience equivalent | What it does |
|---|---|---|
| **Files** | Artifacts | Universal unit of structured knowledge — stable identity, metadata, provenance, history |
| **File extensions** | Content types | Determines how an artifact opens, renders, and what actions apply |
| **File handlers / viewers** | Content-type viewers | Pluggable UI components that render and interact with artifacts by type |
| **Working directory** | Workspace | Ephemeral, high-churn editing layer — where entropy is shaped |
| **Published filesystem** | Collection | Versioned, committed, durable memory — the system of record |
| **Save / publish** | Commit | Explicit promotion from workspace to collection — the entropy boundary |
| **File checksums** | Artifact stamps | Content hash — tamper-evident provenance (the on-chain block reference is research) |
| **Signed certificates** | Validation records | Authority-signed, policy-bound proof of validation (chain anchoring is research) |
| **Kernel services** | Persona servers (Aria, Astra, Iris, Lumen, Ophan, Sage, Seraph — seven, measured 2026-07-31; Verso was renamed Lumen, and Atlas and Nexus were never built) | Core platform capabilities — ingestion, retrieval, provenance, reasoning, presentation, security, finance |
| **Peripheral drivers** | Third-party MCP servers | Standard interface, anyone writes them, they plug in |
| **Processes / jobs** | Agents / operators | Take inputs, call tools, produce outputs — generate explicit artifacts with provenance |
| **System calls** | MCP tool calls | Agents call tools through MCP the same way applications call the OS through an API |
| **Disk blocks** | S3-compatible object storage | Large bytes live outside the database — the DB is metadata, permissions, and history |
| **Filesystem indexer** | Mantle's own index | Lexical/BM25 search — a projection, not the source of truth. OpenSearch was retired 2026-05-09 and kNN needs vectors no embeddings provider produces. |
| **Capability-based access** | Scoped grant tokens / API keys | Explicit capabilities, not ambient access |
| **Audit trail** | Built-in provenance | What ran, on what inputs, producing what outputs — carried in each artifact's context, not a separate receipt object |

The existing manifesto describes Agience as an "organizational entropy management system." That's accurate at the single-organization scale. At the network scale, it's bigger.

---

## The Stack

Six layers, bottom to top:

```
┌─────────────────────────────────────────────────────┐
│  6. Agience Platform                                │
│     Cards, workspaces, collections, agents, search  │
│     The user-facing application layer               │
├─────────────────────────────────────────────────────┤
│  5. agi:// Protocol                                 │
│     Typed agent and device discovery via DNS         │
│     Universal addressing across all transports       │
├─────────────────────────────────────────────────────┤
│  4. Authority Chains (Agience Chain + others)       │
│     Artifact stamps, marketplace settlement, governance │
│     Federated — any authority runs their own chain   │
├─────────────────────────────────────────────────────┤
│  3. Cooperative DNS                                 │
│     Non-custodial nameserver cooperative             │
│     Owner-held DNSSEC keys, revocable delegation     │
├─────────────────────────────────────────────────────┤
│  2. Resolver Network                                │
│     Every node = full recursive resolver + DoH/DoT   │
│     Verifies DNSSEC, caches, participates            │
├─────────────────────────────────────────────────────┤
│  1. Multi-Transport Layer                           │
│     IP → LoRa/Helium → BLE Mesh → Offline cache     │
│     Transport-agnostic, graceful degradation         │
└─────────────────────────────────────────────────────┘
```

No single layer is controlled by any single entity. Every layer is designed so that participants can leave, fork, or join alternatives keeping their identity, their data, and their history.

---

## Semantic Provenance: The Structural Difference

Every blockchain before Agience proves *that* something happened. Agience proves *what* is true, *why* it's trusted, and *who* vouched for it — down to the individual knowledge unit.

Artifact stamps are not just hashes. They are linked to:
- **Validation records** — which authority validated this, under what policy, with what cryptographic signature
- **Authority reputation** — the market value of trust in that authority's validation quality
- **Assurance levels** — AL0 (unvalidated), AL1, AL2, AL3 (authority-certified). This ladder is defined here and nowhere else: the Validation & Certification spec it once cited is not in the corpus, so the ladder is unverified against any other source and this is its only surviving statement.
- **Transformation records** — how this artifact was derived from source material
- **Source spans** — which specific parts of source material informed each claim
- **Version lineage** — the complete history of how this knowledge evolved

This means every piece of knowledge in the system carries its own evidence chain. Truth has provenance. Claims have evidence. And the authorities who vouch for them stake their reputation and their currency on being right.

The Semantic Trust Layer design makes corruption structurally expensive: an authority that stamps false claims loses market trust, their currency devalues, and the community forks away from them. Lies have a measurable cost.

---

## The Trust Economy

The platform is not just a knowledge system. It is a marketplace where trust is priced.

### Authorities Mint Their Own Trust

Any validation authority can run their own chain (Federated Authority Chains), issue their own currency, and stake their reputation on their validation quality:

- A university runs a chain and stamps research papers — their currency reflects the market's trust in their academic rigor
- A hospital runs a chain and stamps clinical protocols — their currency reflects trust in their medical validation
- A government runs a chain and stamps regulatory records — their currency reflects trust in their institutional integrity
- A news organization runs a chain and stamps investigative reports — their currency reflects trust in their editorial standards

Each authority's currency value is determined by market discipline, not central enforcement. The chain software would be AGPL-3.0-only — open-source with copyleft, and proprietary/white-label deployments require a commercial license. **Measured 2026-07-31: AGPL-3.0-only is NOT the licence of the rest of the platform.** The package manifests govern, and they split: Apache-2.0 for beam, crystal, mantle, prism, entroptics and bundle; AGPL-3.0-only for ember, origin and chorus. Agience has no claim on the economic activity that runs on an authority's chain.

This is the Mint model: Agience gets paid for the tooling. The currency, the economy, and sovereignty belong to the issuing authority.

### Marketplace Settlement

The Open Operator Marketplace design enables three classes of tradeable resources:
- **Host compute** — run workloads on community-operated execution nodes
- **Server tools** — call MCP server capabilities published by operators
- **Knowledge access** — read from committed, validated collections

Marketplace transactions can be settled on-chain where that is useful. Cross-chain bridges enable settlement between authority currencies. Agience does not need to intermediate every payment; direct billing and optional network-level settlement can coexist.

---

## Non-Custodial All the Way Down

This is the architectural property that makes everything else work. Every layer is designed so that no single entity — including Agience Inc. — can hold participants hostage:

| Layer | Non-custodial property |
|---|---|
| **DNS cooperative** | Owner holds DNSSEC keys; can revoke delegation and join a different cooperative |
| **Identity** | DNS-based (`agi://` resolution via DNS TXT + DNSSEC + JWS) — identity survives any single chain or cooperative going down |
| **Authority chains** | Independent — sovereignty of each authority is structural, not granted |
| **Artifact stamps** | Content hashes are portable — can be re-stamped on a new chain |
| **Content** | Lives in Mantle's SQLite lattice + content-addressed filesystem, with zero external database processes — not on any chain |
| **Software** | Open source; commercial license for proprietary/white-label use. Per-repo (measured 2026-07-31): Apache-2.0 for beam, crystal, mantle, prism, entroptics, bundle; AGPL-3.0-only for ember, origin, chorus |
| **Governance** | Merit-weighted DAO with constitutional limits — designed for community handover |
| **Transport** | Multi-transport (IP, LoRa, BLE, offline) — no single network dependency |

The consequence: **capture is structurally pointless.** If the Agience DAO is captured — by fraud, by slow accumulation, by state coercion — any subset of operators can fork the chain, take their nodes, their zones, their authority chains, and leave. The captured chain becomes an empty shell governing nothing, because there is nothing it can hold hostage.

A governance system you can leave is a governance system that has to earn your participation every day.

---

## Governance Resilience

The constitutional layer would provide hard limits that governance cannot override without forking the software:

- Merit is non-transferable — there is no `merit.transfer` transaction type
- Merit decays — validators prune stale merit on every block
- No single entity can hold >N% of governance weight
- Protocol upgrades require >75% merit-weighted approval
- Constitutional changes require >90% + 6-month delay
- Software updates require validator adoption, not governance vote — governance proposes, validators choose

None of these limits is in force: there is no chain, no validator set and no merit ledger to enforce them against.

Transparency is meant to be the immune system: every proposal, vote, merit mint, and validator weight change would be on-chain, public, real-time, and searchable. The chain explorer is the free press of the cooperative.

---

## Community Handover

Agience Inc. is a temporary steward. The system is designed to outlive its creator:

1. **Phase 1-3**: Agience Inc. operates as founding steward — builds the software, launches the chain, establishes the cooperative
2. **Phase 4**: Governance handover — once merit distribution is broad enough across independent operators, the community governs itself
3. **Post-handover**: Agience Inc. becomes one participant among many, with no protocol-level privilege

What survives the handover:
- The AGPL-3.0-only license (permanent open-source copyleft)
- The AGIENCE trademark (brand protection persists)
- The genesis block attribution (immutable, every full node holds a copy)
- The protocol specification (IANA/IETF registration is permanent)

What transfers:
- Operational control of the Agience chain
- Governance proposal authority
- Software release decisions (validators still choose whether to adopt)

The chain, the cooperative, the marketplace, and the protocol continue to function whether or not Agience Inc. exists. That is the design requirement, not a side effect.

---

## What This Makes Possible

A system where:
- **Truth has provenance** — every claim is traceable to its sources, its validation authority, and its evidence chain
- **Lies have a cost** — authorities that stamp false claims lose market trust and currency value
- **Governance is accountable** — every decision is on-chain, public, and auditable
- **Capture is survivable** — fork rights + non-custodial design means no single entity can hold the network hostage
- **Capture is pointless** — if everyone can leave, what did you capture?
- **Trust is market-priced** — authority currencies reflect validation quality, determined by participants, not decree
- **The infrastructure survives its creator** — designed for community handover from day one

---

## Related Documents

**None of the documents below is in the corpus (measured 2026-07-31).** There is no .dev/, no docs/
and no vision/future/ directory in pharos. They are unlinked here so the titles survive as a record
of what this vision referred to, without implying any of them can be opened.

- Agience Manifesto — Organizational entropy management at single-organization scale
- Information OS Analogy — Filesystem and OS mapping (canonical messaging)
- Solution Taxonomy — described the persona servers (its "eight" count is wrong; there are seven)
- IP Protection & Attribution — AGPL licensing, trademark, attribution
- Network Architecture — The six-layer stack
- Agience Chain — Chain protocol, federated authorities, constitutional layer, fork rights
- Licensing & Billing — AGPL copyleft + commercial model
- Chain & Governance — PoUW consensus, merit DAO
- Semantic Trust Layer — Unit-level truth, artifact stamps
- Open Operator Marketplace — Marketplace design
- Ecosystem Economics — Economic model
