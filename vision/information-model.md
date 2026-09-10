# Information Model (Content / Context / Transform)

Status: **Reference** — design spec
Date: 2026-03-15

Agience's information substrate has a unifying vocabulary:

- **Artifacts are addressable Information**
- Information is described by a triangle: **Content**, **Context**, **Operator**
- Workspaces hold draft artifacts; Collections hold committed, versioned artifacts
- **Cards** are UI components that present artifacts to humans

> **Design principle**: Humans look at Cards. Agents look at Artifacts.

The model is conceptual and implementation-driving. Where the code has since settled on a
different name for something described here, this document uses the code's name. What remains
unbuilt is collected at the end.

The store boundary the model was originally written against no longer exists: the lattice IS the
store — one SQLite file plus FS-CAS content, opened in-process, zero external database processes, and
the backend selector was deleted 2026-07-23.

---

## 1) Core claim

**Agience Artifacts ARE Information.**

An "artifact" is the universal unit for:
- storing knowledge,
- presenting knowledge (via cards in the UI),
- invoking computation,
- linking provenance,
- and promoting drafts into durable truth.

This is already true in the product architecture today:
- Workspaces and workspace artifacts are first-class. A workspace is a collection carrying
  `content_type == "application/vnd.agience.workspace+json"`; artifacts live in one table and
  carry a `state` in `{draft, committed, archived}`.
- Collections hold committed versions. Commit is a state flip, not a copy, and a superseded
  version is archived rather than destroyed.
- Operators are artifacts (`application/vnd.agience.operator+json`), and a workflow is an
  operator whose steps name child operators by artifact id.
- MCP tools and resources are artifacts: `application/vnd.agience.tool+json` carries the owning
  server id, tool name and input schema; `application/vnd.agience.resource+json` carries the
  resource URI and MIME type.

The Information Triangle refines that into a crisp model you can build against.

---

## 2) The Information Triangle

Information is described by three orthogonal components:

- **Content**: *What it is* (payload)
- **Context**: *Where it fits* (ontology + metadata + provenance + trust)
- **Transform**: *How it is produced / used / transformed* (operator semantics)

The code's word for the third leg is **Operator**, and the artifact type is
`application/vnd.agience.operator+json`. `application/vnd.agience.transform+json` is its
predecessor and still ships; its type definition records that operator supersedes it. Both names
are used below where they name different things in the code.

### 2.1 Definitions

**Content** (payload)
- Human-facing text (notes, summaries, claims)
- Media references (uploads, streams) and extracted text
- Structured objects (JSON specs, transform definitions)

**Context** (placement + meaning)
- Ontology/classification: type, membership, semantic kind
- Provenance: sources, evidence, tool runs
- Access boundaries: tenant, grants/permissions, intended audience
- Identity: stable identifiers and version lineage

**Transform** (operational semantics)
- A reproducible *operational meaning* for how information is processed.
- An operator's body is **addressed rather than carried inline**: the pattern goes to the CAS and
  the artifact row keeps `content_ref = cas/<sha256>`. Every *use* of an operator is likewise a
  reference — a typed `operator` edge, or a `transform_id` in a workflow step.

Transform can be represented either as:
- an **executable operator** (an external or internal process that maps inputs → outputs given context), or
- a **workflow** (a graph/sequence that composes multiple operators).

In both cases, Transform is about "how we compute":
- gathers Resources,
- invokes Tools,
- transforms Content,
- yields new Information artifacts,
- yields outputs whose contexts carry receipts/provenance links.

### 2.2 "Two of three" and why it's useful

The triangle is a practical completeness model.

- If you have **Content + Context**, you can *predict Transform*:
  - "Given this decision + its sources, what verification or extraction workflow should we run next?"

- If you have **Content + Transform**, you can *predict Context*:
  - "This output came from a Jira extractor workflow; classify it as `action` and attach source pointers."

- If you have **Context + Transform**, you can *predict Content*:
  - "This is a 'meeting intel' transform; produce the expected artifacts (agenda, decisions, actions)."

Important practical nuance:
- Prediction is **suggestion**, not silent mutation.
- Agience should not overwrite authored content as a side-effect of inference.

### 2.3 Recommended completeness levels (operational)

Instead of a binary "valid vs invalid", use levels:

- **L1 — Content-only**: raw note/upload/transcript chunk.
- **L2 — Content + Context**: classified + attributable.
- **L3 — Content + Context + Transform**: actionable and reproducible (workflow present).
- **L4 — Training/Eval-ready**: L3 + strong provenance + deterministic evaluation contract (or explicit scoring rubric).

This avoids edge cases where Transform-first artifacts exist (templates/workflows) but content is "empty by design".

---

## 3) Artifacts as the universal representation

Agience relies on this invariant:

- **Everything is an artifact** at the data layer. Containers are artifacts too — a workspace, a
  collection and a context are roles an artifact plays, discriminated by `content_type`.
- **Everything is a card** at the UI layer (cards display artifacts to humans).
- Behavior is driven by **content type** and a type registry. `content_type` is a field on the
  artifact and is mirrored into `context.content_type` when the artifact is created. A type
  definition is itself an artifact (`application/vnd.agience.type+json`); servers self-register
  the types they own, and Mantle persists them so a restart needs no round trip back. Each
  definition carries a `context_schema`, an `operations` map (with `requires_grant` and a
  `dispatch` block), and a `ui` block naming the viewer, modes and tile styling the cards use.

The Information Triangle slots into this:

- Content is stored in `artifact.content`, or content-addressed in the CAS with the artifact row
  carrying `content_ref = cas/<sha256 of the plaintext>`.
- Context is stored in `artifact.context`.
- The operator behind an artifact is reached by reference, not by an inline copy. Two references
  exist in code: a typed edge whose label is the relationship name (`get_relationship_target`
  resolves `operator`, `authorizer` and the rest to a `root_id`), and a `transform_id` naming a
  child operator inside a workflow step.

Design rule:
- An operator's body is addressed into the CAS rather than carried inline in the row.
- This ensures Operators are reusable, versioned, and independently committable.

---

## 4) Transform as Operator + Workflow (making it concrete)

"Transform" becomes implementation-friendly when we treat **Operator** as the core primitive, and treat **Workflow** as one common way an operator is defined (decomposed) or invoked.

High-level model:

1) **Operator**: the primary primitive — a callable execution surface that can live *outside* Agience (RPC, local code, MCP tool) or inside it (backend agent, operator runner).
2) **Workflow-Operator**: a workflow-operator whose implementation is decomposed into steps (a graph/sequence of operator invocations).

### 4.1 Operator summary

An operator is any addressable execution surface that can be invoked with explicit bindings.

Conceptually:

$$
  \text{output} = f(\text{input}, \text{context})
$$

Key rule:
- **Connections to an operator are first-class** (bindings/wiring are explicit data).

The schema is concrete. An operator definition carries `id` (namespaced `op.*`), `content_type`,
`offer` (the transformation it advertises), `needs` (capability kinds it requires), the pattern
itself as `content` or a CAS `content_ref`, an `entry` block saying how to bind it
(`mcp`, `wasm`, `js`, `py-local`, `py-bundle`), `created_by`, and a `spec_hash` stamped at
registration — a changed spec is a different operator, with no inherited fitness. Fitness counters
(invocations, verified, refuted) accrue on the artifact and are never part of the registered spec.

Invocation is a declared operation. A type definition's `operations.invoke` block names its
`requires_grant` and a `dispatch` of kind `mcp_tool`, `artifact_crud` or `native`; for an operator
that dispatch reads `$.context.operator.server`, `$.context.operator.tool` and
`$.context.operator.arguments`. The gateway fetches the artifact under the caller's token, verifies
the declared grant against Mantle's audited access check, then runs the tool.

Bindings are explicit data, as the rule requires. Each argument value resolves from one of four
places: `$.body.*` (the invoke request), `$.content` or `$.context.*` (the operator's own
document), `@relationship.<name>` (the `root_id` behind a typed edge — an attached authorizer, a
tool, a secret, another operator), or a literal.

### 4.2 Workflow summary

**Workflow**
- A directed graph of Steps.
- Has inputs (Information references) and outputs (new Information).

In this model, a workflow is an operator whose implementation is **decomposed into steps**.

**Step**
- One of:
  - **Tool call** (MCP tool invocation)
  - **Resource fetch** (MCP resource read/import)
  - **LLM transform** (prompted transform; ideally represented as a tool)
  - **Human action** (approval/edit/labeling)

Recommended unification:
- Treat each Step as an **Operator invocation** plus optional wiring (Connection/Host selection).

A shipped workflow step is an object with a `transform_id` naming the child operator artifact, an
optional `name`, an optional `condition` evaluated against a state artifact's context, and an
optional `input_mapping`. The runner supports `run.type` values `mcp-tool`, `llm`, `workflow`,
`transform-ref`, `webhook`, `palette-run`, `flow-run` and `host-script`, plus a `retry` block.

**Result**
- Always produces **Information artifacts** (0..n output artifacts).

Design rule (spec):
- The **resulting artifact *is* the audit record**.
- The output artifact's `content` is the result payload.
- The output artifact's `context` records inputs + operator identity + bindings + provenance links (including linkage to the calling information).

What the runner does today is narrower than that rule. Progress is written to a separate **state
artifact** whose context is patched with `status`, `current_step`, `step_index` and `error` as the
run proceeds; the dispatcher returns the tool's result and does not itself mint an output artifact.
Tools that do produce a derived artifact write the linkage by hand — the PDF text extractor, for
instance, stamps `context.derived_from` with the source artifact id, the transform name and the
method used. There is no single provenance envelope every operator emits.

### 4.3 What Transform must capture (minimum)

Minimum data needed for reproducibility and audit:
- `inputs`: references to input artifacts/resources
- `steps`: ordered list or DAG nodes with operator/tool/resource identifiers
- `parameters`: explicit arguments
- `actor`: user/agent identity who initiated run
- `environment`: host/server identity where executed
- `outputs`: created/updated artifact references

And for operator-backed steps, record:
- `operator`: operator identity (`kind`, `id`, and `version` when available)
- `connection`: which credential/routing projection was used (if applicable)

For L4 (training/eval-ready), also capture:
- `model_id` and model config when LLM is used
- prompt identifiers (hashes)
- deterministic evaluation rules or scoring rubric

Where each of these lives today: `steps`, `parameters` and operator identity are in the operator's
own `run` / `operator` block; `actor` and `environment` are on the commit record, which carries
`author_id`, `subject_user_id`, `presenter_type`, `presenter_id`, `client_id`, `host_id`,
`server_id`, `agent_id` and `grant_key_id`; model config is on an LLM connection artifact
(`provider`, `model`, `endpoint`, `tier`, `rate_limits`); prompts are artifacts
(`application/vnd.agience.prompt+json`); and a scoring rubric is an evaluation artifact
(§8 of `paper-1-the-instrument.md`). `outputs` is the one entry with no uniform home.

### 4.4 Transform vs Connection

Two layers are distinct:
- **Transform**: the operator (or decomposed workflow-operator) that defines *what computation happens*
- **Connection**: the pipeline/wiring that defines *how invocations are routed/executed* — credential projection, transport, host/server selection

In practice, a workflow step references:
- an **Operator/Tool** (algorithm surface)
- a **Connection** (routing/credential projection)
- a **Host/Server** (execution location)

---

## 5) Context: ontology, provenance, identity, trust

Context is where Agience becomes "grounded" and auditable.

### 5.1 Ontology placement

At minimum, context should enable:
- Typing (content type)
- Semantic kind (decision/constraint/action/claim)
- Optional ontology nodes (tags/categories)

Typing is built: `content_type` on the artifact, resolved through the type registry.

Classification is built, but not as a context field. The store's position is that a tag, a
collection, a group and an attribute are the same thing — an edge to another artifact — so there
is no `tags` key to read: membership *is* the tag set, and the indexer derives searchable terms by
splitting group ids into words. A `tags` key in the context blob would be a second answer to a
question the graph already answers, and the two could disagree with nothing to notice.

Ontology nodes are built as anchors. The AnchorSet is a set of fully-disclosed reference points
stored as `application/vnd.agience.anchor+json` artifacts; they are the routing centroids that
place an artifact in the shared coordinate system. Edges themselves carry an
information-centric relation vocabulary — `grant` (access/authority), `derivation` (an operator
produced this), `temporal` (order), `semantic` (ontological position), `lifecycle` (a state
transition) — of which `grant` and `derivation` are stored on the edge and the rest are
represented in the version chain, the anchor index and the commit records.

The semantic-kind convention below is proposed, not built. No artifact carries a
`context.semantic` block: a workspace-wide grep for `semantic.kind` across every `.py` returned
zero when it was measured 2026-07-31, and returns zero still — no producer, no consumer, no schema.
- `context.semantic.kind`: `decision | constraint | action | claim`
- `context.semantic.sources`: pointers to sources
- `context.semantic.evidence`: optional supporting excerpts

### 5.2 Provenance (baseline vs best-effort vs validated)

Use the layered provenance posture:

- **Layer A (baseline, always on)**: origin + linkage, timestamps, actor, run ids
- **Layer B (best-effort evidence)**: sources/evidence pointers and excerpts
- **Layer C (validated under policy)**: explicit validation metadata + receipt

Layer A is in force for reads and for writes, by different mechanisms. Every artifact carries
`created_by`, `created_time`, `modified_by` and `modified_time`. Every authorization decision —
allow *and* deny — appends an access event naming the principal, action, result, timestamp and
context, emitted from inside the authorization layer so that an artifact cannot be touched without
being witnessed; the log is append-only, has no update or delete surface, and is retained
indefinitely unless an age bound is configured. Commits carry the fuller actor and environment
record listed in §4.3.

Layer B is per-tool rather than platform-wide. Derived artifacts carry a `derived_from` block
naming their sources, and a grounded-answer tool produces a citation receipt giving each cited
card's sha256, length and title, and reporting the ones it could not resolve.

The receipt schema for Layer C is not yet specified. The nearest thing in the code is the commit
record's `confirmation` field (default `human_affirmed`) alongside the `grant_key_id` the change
was made under.

### 5.3 Identity and version lineage

Identity is stable from creation, not from commit. Every artifact carries a `root_id` — its own id
on the first version, and the same value on every version after — and `root_id` is what
membership edges point at, so a reference survives revision. The workspace/collection split is a
`content_type` distinction, not two identity schemes: there is one artifact table, and `state`
carries the draft/committed/archived distinction.

Two fields exist so that lineage survives the storage round trip and are worth naming: `origin_root`,
the collection's immutable key root, inherited from parent to child rather than walked; and
`content_ref`, the CAS address of the bytes. A `from_dict` that silently dropped either would
re-key or re-inline content on the next save.

This is consistent with the current store model: one SQLite lattice plus content-addressed
filesystem content, with no external database process.

### 5.4 Context as an artifact

Context is also a declared role an artifact can play:
`application/vnd.agience.context+json`. The point of making it an artifact rather than a scalar
field is composition — a context can nest, be shared between two collections, be revised and cited,
and be the subject of a grant, none of which a scalar handle can do. Two predicates are kept
apart: one asks whether an artifact *declares* the role, the other whether it *already acts* as
one, which the containers do — a workspace and a collection were context nodes before the
vocabulary existed.

Contexts nest by a typed `context` edge, walked by a bounded traversal. The walk **narrows**: it
takes a required authority universe and admits a node only if it is already in it, so a context
edge can never manufacture reach. The writer half matches — an edge written with no propagate mask
transmits nothing. Three things travel a context edge and they are three different operations:
authority attenuates, recall reach attenuates by the same call, and event delivery fans out.

The access gate in front of every artifact read walks origin containment only and has never heard
of a context edge, so a context edge contributes nothing to the authorized set today. Making
context a real unit of sharing needs that gate to walk the context lattice too.

---

## 6) System entities (refined vocabulary)

The entity list maps into a stable mental model with clean boundaries. Most of these are content
types created at platform bootstrap: authority, host, agency, agent, mcp-server, package, type,
anchor and credential.

### 6.1 Authority (authentication root)

**Authority** is the root of trust for identities and policy claims.
Stored as `application/vnd.agience.authority+json`.

Practical responsibilities:
- issues/verifies identities (Person/Agent) or accepts upstream IdP
- mints tokens and claims
- optionally certifies validation records under policy

### 6.2 Person

A **Person** is a human principal.
- authenticates via Authority
- owns workspaces/collections, or participates via shares

A person's profile is an ordinary artifact (`application/vnd.agience.person+json`) living in a
platform-managed people directory; members reach only their own card, through its owner grant.

### 6.3 Agency / Agent

An **Agency** is a grouping of Agents (organizationally or functionally).

An **Agent** is a principal capable of producing or transforming Information.

Recommended decomposition:
- Agent = (Identity) + (Allowed Tools/Resources) + (Knowledge/Memory) + (Allowed Hosts)

Important boundary:
- An Agent's "knowledge" should be represented as artifacts/collections so it's inspectable and shareable.

That boundary holds in the agent written against this model: it reads its corpus from the
store and writes results back as artifacts, destroys nothing, archives superseded duplicates
rather than deleting them, and stamps every artifact it writes with the operator that made it plus
a `derived_from` link to the sources.

### 6.4 Host (execution environment)

A **Host** is where computation runs.
Examples:
- backend server runtime
- desktop host companion
- secure execution node

Host controls:
- allowed tool execution
- secrets availability
- network egress policy

These are expressed as **capability kinds** — a small versioned vocabulary a host signs into a
manifest, naming what its hardware can physically do *and* enforce as a sandbox edge. The lexicon
covers filesystem (`fs.read`, `fs.write`), network (`net.get` is a distinct kind from
`net.request` on purpose, so read-only egress can be granted without write-capable egress),
compute (`compute.local`, `compute.wasm`, `compute.gpu`), the store (`store.read`,
`store.write`), presentation (`ui.render`, `human.ask`) and the physical world (`sensor.*`,
`actuator.*`, accepted by prefix since physical devices are unbounded). An operator's `needs` are
matched against a host's manifest, and an unknown capability kind fails registration loudly rather
than qualifying silently. Holding the name grants nothing on its own: authorization is still the
grant on the resource.

### 6.5 Server (MCP server)

A **Server** is an MCP server: a code package that provides tools/resources.
Stored as `application/vnd.agience.mcp-server+json`.

Server identity matters for auditing which code produced which result: the commit record carries
`server_id` alongside the rest of the actor and environment fields.

A server is *not* a principal. The store registers no servers, issues no server credentials and
keeps no server JWK plane; a token presenting `principal_type: server` is refused by name. So
credential scoping is not done by server identity — it is done by the grant on the credential
artifact and the typed edge that projects it (§6.9, §6.10).

Routing a dispatch target to a running server is the gateway's own `slug → endpoint` map, which
personas populate when they register themselves and push their types.

### 6.6 Tool

A **Tool** is a callable algorithm surface.

Tools:
- accept input arguments
- produce output values
- may create/update artifacts as a side-effect. Such outputs must carry provenance + invocation links in their context.

Tools arrive via MCP. A tool artifact carries the owning `server_id`, the `tool_name` as the
server declares it, and the `input_schema`, all three immutable; user-authored `description` and
`notes` are mutable, so re-import does not overwrite what a person wrote. Invoking one routes to
`context.server_id` : `context.tool_name`, so a third-party server can own the artifact.

### 6.7 Resource

A **Resource** is a readable thing: values, references, sources.

Two resource sources are live:
- MCP resource catalogs
- collections and workspaces (Agience itself is a resource provider)

The first is realized as a type. `application/vnd.agience.resource+json` is an MCP resource
materialized as an artifact, carrying the resource `uri` and `mime_type` as immutable context and
`description` / `notes` as mutable. The second has no provider interface: collections and
workspaces are reachable as containers, and there is no resource-provider registry to enumerate
either source against.

A third source — an external hosted search index — was deliberately withdrawn. The tools that
reached one are tombstones that raise, under the rule that retrieval runs on the platform's own
index and no model-backed connector holds the corpus.

When imported, resources become reference artifacts or content artifacts.

### 6.8 Knowledge

To avoid "two parallel universes", treat **Knowledge** as a specialization of Information.

Recommended: a "Knowledge artifact" (or a collection of artifacts) whose content/context describes:
- tools
- resources
- connections
- policies

In other words:
- Knowledge = Information about how to compute + what to reference.

This is the model's recommendation and not a shipped type. What exists instead is each of those
four as its own artifact type — tool, resource, authorizer/LLM connection, and grant — so
"knowledge about how to compute" is already inspectable and grantable without a wrapper type.

### 6.9 Connection

A **Connection** is the pipeline/wiring layer:
- routes tool calls to servers/hosts
- projects credentials just-in-time
- can enforce policy constraints (scopes, rate limits, audit)

Two halves of this exist under their own names. Credential projection is an **authorizer** artifact
(`application/vnd.agience.authorizer+json`): provider config as its content, references to the
credential artifacts holding the client secret and refresh token, and a typed `authorizer` edge
from the operator that uses it — resolved at invoke time by `@relationship.authorizer`, so the
secret is never copied into the operator. Model access is an **LLM connection** artifact
(`application/vnd.agience.llm-connection+json`), carrying `provider`, `model`, `endpoint`,
`credentials_ref`, `rate_limits`, `tier` and `capabilities`. Routing is the gateway's topology map
(§6.5). There is no single Connection type spanning all three.

### 6.10 Authorizer / Secret / Key<Type> / Grant

These are credential primitives.

- **Authorizer**: provider-specific OAuth flow logic, as an artifact whose content is the provider
  config and whose credential artifacts are named by id.
- **Secret**: inbound credential material received from a third party (refresh tokens, API
  secrets), stored as a credential artifact (`application/vnd.agience.credential+json`). The value
  IS the artifact's content, so the envelope encrypts it at rest under the origin-root principal
  and the light cone decides who may read it — no second store, no second authorization path. Its
  `context` stays plaintext and carries only non-secret metadata (provider, kind, label).
- **Key**: outbound credential given to a client — a JWT access token, or a raw grant-key token
  prefixed `agk_`. The older `agc_` API keys are retired: a token presenting that prefix is
  refused by name, so a stale key reads as a decommissioned credential rather than a malformed
  one.
- **Grant**: the permission bundle. A grant links a principal to a resource with CRUDEASIO
  permissions — Create, Read, Update, Delete, Evict (remove from a container), Add (intake into a
  container), Share, Invoke, Admin. A grant key is an ordinary grant whose grantee is
  `sha256(raw_token)`, so presenting the token makes the holder act as that grant and its bits are
  the entire extent of what they may do; there is no second permission vocabulary on top. A
  bundle is the same object with members — grants whose grantee is the bundle — so composition
  reuses the ordinary grantee lookup. Members carry independent bits, and the bundle is a
  ceiling: effective permission is member ∩ bundle, so clearing a bit narrows every member at
  once and revoking the bundle revokes the set without touching any member. Minting a key and
  adding a member both require admin on the resource, so a bundle can never widen anything.

---

## 7) Artifact taxonomy

The model sharpens the artifact taxonomy into four groups:

### 7.1 Container artifacts
- Represent a browsable universe; opening triggers a live query.
- Examples: Resources, Tools, Prompts, Collections.
- A type definition marks these with `is_container`, and carries the browse modes (tree, grid,
  list) and the default and maximum depth the card should expand to.

### 7.2 Reference artifacts
- Stable pointers to external truth.
- Minimal snapshot + stable external IDs.
- Refresh preserves authored annotations.
- Realized by the resource and tool types, which mark the external identifiers immutable
  (`uri`, `mime_type`, `server_id`, `tool_name`) and the authored fields mutable (`notes`,
  `description`) — which is the mechanism by which a refresh preserves what a person wrote.

### 7.3 Knowledge artifacts
- Authored or derived knowledge units.
- Must carry provenance pointers (Layer A baseline at minimum).
- No knowledge type is registered; an authored unit is an ordinary typed artifact, and the
  baseline provenance it carries is the one described in §5.2.

### 7.4 Execution artifacts
- Workflow definitions (Operator artifacts) and **execution provenance embedded into output artifacts**.
- Make "chat-like" interactions a first-class, repeatable tool pattern by always producing new information with inspectable context.
- Operator and transform artifacts are built. Embedded execution provenance is the part that is
  per-tool rather than uniform (§4.2). A related mechanism does ship: a **describer** — an
  enrichment tool declared on a content type and fired after a change, running under a bounded
  delegation rooted in the platform operator rather than impersonating the change's actor, and
  deduplicated so the enrichment write does not re-trigger itself.

---

## 8) Training / evaluation readiness (what "good for training" means)

The triangle defines training/eval readiness. An artifact set is training/eval-ready when:
- Content is stable and semantically typed (or at least typed by content-type)
- Context includes provenance and identity lineage
- Transform captures the transformation process and parameters
- Evaluation is reproducible:
  - deterministic checks, or
  - explicit rubric + human validation receipts

The rubric half is built as an artifact type. An evaluation
(`application/vnd.agience.evaluation+json`) is a scored assessment of another artifact, carrying
`subject_artifact_id`, `criteria` as an array of `{name, score, max_score, notes}`, an overall
score and maximum, the `evaluator` (a human name or an agent id), the `model` used when the
assessment was automated, and a timestamp. It is a stored, addressable, grantable record rather
than a transient score.

The guarantee layer above it — "validated under policy", where an authority certifies a validation
record — has no code and no receipt format.

---

## 9) Mapping to current Agience implementation (reality check)

This model intentionally fits the current architecture.

Already true today:
- The lattice — one SQLite file plus FS-CAS content, no external database process — holds workspace
  drafts vs collection versions. ArangoDB is gone; the backend selector was deleted 2026-07-23, so
  anyone designing operator persistence must build for the lattice.
- Artifacts store `content` and `context`; where a content tier is mounted, the body moves to the
  CAS and the row keeps the address. With no tier reachable the body stays inline, so an
  air-gapped node still works.
- Content types drive UI behavior, through a registry of type-definition artifacts that servers
  self-register.
- Operators are artifacts, with a versioned schema, a spec hash, a capability contract, and an
  invoke operation whose argument bindings resolve from the request, the document and typed edges.
- Bindings are first-class data: a typed edge label is the relationship name, and
  `@relationship.<name>` resolves it at invoke time.
- Baseline provenance for access is unconditional — every allow and every deny is witnessed.
- Context is itself an artifact type, with a nesting edge and a bounded, attenuating walk.

Gaps / follow-ups the model implies:
- One provenance envelope emitted by every operator run, rather than a per-tool `derived_from`
  convention.
- A `context.semantic` classification vocabulary, or an explicit decision that edges carry it
  instead.
- A receipt format for validation under policy.
- The access gate walking the context lattice, so a context edge is a real unit of sharing.
- A resource-provider registry, if the store's own collections are to be offered as a source the
  way an MCP catalog is.

The vocabulary is a target; implementation can be phased.

---

## 10) Suggested next: implementation planning axes

When we plan this, the highest-leverage decisions are:

1) **Represent Transform**
- Decided and shipped: an operator's body is addressed into the CAS, and every use of an operator
  is a reference — a typed edge or a `transform_id`.

2) **Unify audit/provenance**
- One invocation/provenance envelope embedded in output artifacts' `context` (MCP calls, backend agents, and operators)

3) **Connection layer**
- How credential projection and routing are represented and enforced. Two of the three pieces
  exist (§6.9); what is open is whether they become one type or stay separate.

4) **Type packages**
- How content types are distributed as "packages" (manifests, viewers, operators). A package
  content type exists at bootstrap; how a package is built and distributed is the open part.

Once those are chosen, we can produce a phased delivery plan with minimal churn.

---

## What is still outstanding

| # | Outstanding | What exists today |
|---|---|---|
| 1 | No `context.semantic` classification vocabulary — `kind` / `sources` / `evidence` have no producer, consumer or schema. | Typing by `content_type`; classification by membership edge, since a tag, collection, group and attribute are all the same edge; ontology placement by anchor artifacts. |
| 2 | No single provenance envelope emitted by every operator run; the design rule that the output artifact *is* the audit record is not enforced anywhere. | Per-tool `context.derived_from` blocks written by hand; a workflow state artifact patched with status, current step and error; the commit record's actor and environment fields; the append-only access-event log. |
| 3 | No receipt schema for Layer C, and no "validated under policy" certification path. | The commit record's `confirmation` field and `grant_key_id`; evaluation artifacts carrying criteria, scores and an evaluator; a citation receipt tool giving per-source sha256, length and title. |
| 4 | The access gate does not walk the context lattice, so a context edge confers no reach and context is not yet a unit of sharing. | The context artifact type, the nesting edge, and a bounded walk that narrows and cannot widen an authority set. |
| 5 | No resource-provider registry, so "Agience itself is a resource provider" has no interface to enumerate. | `application/vnd.agience.resource+json` for MCP resources materialized as artifacts; collections and workspaces reachable as containers. |
| 6 | No Knowledge artifact type. | Tools, resources, authorizers, LLM connections and grants each as their own artifact type. |
| 7 | No single Connection type covering routing, credential projection and policy together. | Authorizer artifacts with a typed `authorizer` edge for OAuth credential projection; LLM connection artifacts carrying endpoint, tier and rate limits; the gateway's `slug → endpoint` topology map for routing. |
| 8 | `outputs` — created and updated artifact references — has no uniform home in a transform record. | Inputs, steps, parameters and operator identity in the operator's own `run` / `operator` block; actor and environment on the commit record. |
