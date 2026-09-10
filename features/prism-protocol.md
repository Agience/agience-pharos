# Agience Prism Protocol

Status: **Draft** — the language-neutral contract every prism leg (language SDK) implements.
Reference implementation: the Python leg at `agience-prism/py` (dist `agience-prism`, import `prism`).

This document is the source of truth. A "leg" in any language (Python, TypeScript,
C++, …) is a **conformant implementation** of what follows. It specifies *wire
behavior* and the *required SDK surface* — not internal API shape, which each language
renders idiomatically. Keywords **MUST / SHOULD / MAY** are used in the RFC 2119 sense.

Three legs ship: `py` (Python), `js` (TypeScript) and `c` (C++17, for embedded / edge). They are
not uniformly complete, and the sections below say per leg where they differ.

---

## 1. What a leg is

A leg is the SDK you embed to connect to the Agience platform **over the wire** —
never by linking platform code. It has **two surfaces**; an SDK MUST provide at least
one and SHOULD provide both:

- **Host surface (provider).** Build a **Host**: compute that *exposes capabilities*
  and is invoked by the platform.
  *(e.g. a Chorus persona, a desktop runtime exposing local tools. No embeddings host ships.)*
- **Client surface (consumer).** Build a client that *uses* capabilities and reads/
  writes artifacts. The consumer is an **MCP host+client**.
  *(e.g. the web app, a browser extension, an agent.)*

The platform (Origin + Mantle) is reached over the wire. **No platform IP lives
in a leg.** A leg is permissive-licensed (Apache-2.0).

The Python and TypeScript legs render these as two separate modules: `prism.host` /
`@agience/prism/host` is the provider (an HTTP app serving operators), and `prism.server` /
`@agience/prism/server` is the MCP-server scaffold plus the consumer client. The C++ leg
provides neither server: it carries host *identity* and a signed capability manifest, plus a
minimal outbound HTTP client.

## 2. Vocabulary

| Term | Definition | MCP equivalent |
|---|---|---|
| **Host** | Compute that connects to the platform and exposes capabilities. | **Server**  (not MCP's "host") |
| **Capability** (operator) | A unit of work a host exposes and runs, e.g. `embeddings.embed`. | **Tool** (or resource/prompt) |
| **Artifact** | The data a capability reads/writes. Addressed `agience://<id>`. | **Resource** |
| **Platform** | Origin (identity) + Mantle (encrypted memory); routes invocations, stores artifacts. | the consumer's server target |
| **Scope** | Entitlement grammar: `resource\|tool\|prompt : contentType : action [: anonymous]`. | capability + auth |

> **The "Host" name clash.** In MCP, *Host* = the AI application (client side). In
> Agience, *Host* = the compute that exposes capabilities = MCP's **Server** role. A
> leg MUST document this so implementers don't mis-map the MCP SDK's roles.

## 3. MCP foundation — inherit, don't reimplement

Agience is **MCP-native.** A leg that serves or consumes MCP MUST build on the target
language's official MCP SDK rather than reimplement the protocol. The leg is a thin layer:

```
leg(language) = MCP SDK (language)         # transport, framing, capability negotiation
                 + delegation auth (§5)        # Agience-specific
                 + client surface (§7)         # AgienceClient
                 + host scaffold + scope (§6,§8)
```

Protocol framing, `initialize`/capability negotiation, and streamable-HTTP transport
come from the MCP SDK. The leg owns only the Agience additions below.

The Python leg builds on `mcp[cli]` (pinned `>=1.6.0,<2`, because `create_server` builds
`mcp.server.fastmcp.FastMCP`); the TypeScript leg builds on `@modelcontextprotocol/sdk`. The C++
leg has no MCP dependency and speaks no MCP: it is a signing, content-addressing and outbound-HTTP
library, and the MCP surface is where it stops rather than where it differs.

## 4. Transport & endpoints

- Consumer↔platform transport is **MCP over HTTP (streamable-http)**.
- An MCP server built with a leg is reachable at `POST <host>/mcp` — the TypeScript leg mounts
  that path explicitly, the Python leg inherits it from FastMCP's streamable-HTTP app. The Host
  surface is *not* MCP: it mounts each operator on a plain HTTP route, `/operators/<name>` by
  default, and an implementer may override the path (e.g. an operator's `/embed`).
- Platform endpoints are discovered from **canonical config** (§Appendix):
  - `ORIGIN_URI` — identity/auth authority.
  - `MANTLE_URI` — artifact store, raw CRUD, search and events.
  - `CRYSTAL_URI` — the content-type gateway, where artifact operations dispatch.
  - `CHORUS_URI` — host / persona discovery.
  - `EMBER_URI` — the local leaf, which serves host self-registration.
- `GET /.well-known/mcp` returns the per-deployment discovery document — slug routes and each
  persona's artifact UUID. It is served by Crystal's host app (`crystal/host.py`), which is the
  app Chorus runs; the legs name `CHORUS_URI` as the target to ask. No leg calls it (§7).
- Beyond the alias in the next paragraph, a leg MUST NOT invent alternative variable names
  (`*_API_URI`, `BACKEND_URI`, …).

All three legs honour one legacy alias: `AGIENCE_API_URI` resolves as `MANTLE_URI`, with a
one-time deprecation warning on first read. Values resolve per call rather than at import, so a
runtime environment change takes effect and the deprecation fires only when the legacy name is
actually read.

## 5. Identity & authentication

### 5.1 Authority roots to a person
Delegation uses **RFC 8693 token exchange**: a service acts on a user's behalf with
`sub = <person>` and `act = { sub: <service> }`, carrying `principal_type: "delegation"`. The
Python and TypeScript legs both sign and verify such tokens (`sign_delegation_jwt` /
`verify_delegation_jwt`, `signDelegationJwt` / `verifyDelegationJwt`), and verification confirms
`principal_type` and, when an expected actor is given, `act.sub`. The C++ leg has no delegation:
it signs only service JWTs (`principal_type: "service"`).

The rooting requirement is enforced at the issuer, not in the legs. Origin's
`POST /internal/delegation-token` refuses to exchange a token whose `principal_type` is
`service`, `server` or `delegation`, so a delegation can only be minted for a person. The
host-side verifiers do not carry that rule: they accept a self-signed service token
(`iss = sub = <service>`, no subject) by design, because platform services call hosts that way.

### 5.2 Inbound verification (host side)
A host MUST verify every inbound bearer token before running a capability, trying these
modes in order (as the reference `TokenVerifier` does):

1. **Authority JWT (RS256).** Signature verified against the issuer's public key,
   resolved from the **authority manifest** (inline JWKS) or the issuer's JWKS endpoint,
   selected by `kid`. The manifest maps a service anchor (`origin`, `mantle`, `chorus`, …) →
   public key; an unknown `kid` reloads the manifest once, to tolerate key rotation. `iss` is
   checked against an allowed-issuer list when one is configured, and `aud` when expected
   audiences are configured.
2. **Local HS256.** A shared secret, for co-located/dev hosts.
3. **Static API key.** A pre-shared key, compared in constant time against an allowlist. Keys
   may be inline or read from a hot-reloaded directory, one file per consumer — drop or remove
   a file to grant or revoke without a redeploy.

A host declares which modes it accepts via config (`HOST_AUTHORITY_MANIFEST`,
`HOST_AUTHORITY_JWKS_URL`, `HOST_JWT_HS256_SECRET`, `HOST_API_KEY`, `HOST_API_KEYS_DIR`,
`HOST_EXPECTED_AUDIENCES`, `HOST_ALLOWED_ISSUERS`; the same names in both the Python and
TypeScript legs, and overridable per-host by constructor argument). Verification failure MUST
yield the auth error in §10.

**A host with no mode configured is open** — it authorizes every request, and logs a warning at
startup saying so. Configuring a mode is what closes it. A configured-but-empty key directory
leaves the host open too, because there is nothing to check against.

Auth is applied as a per-route dependency by the operator decorator, not as middleware. A route
mounted straight onto the underlying app is unauthenticated; both legs expose the same check
(`host.auth_dependency` / the equivalent handler) for hand-mounted routes.

The C++ leg has no `TokenVerifier`. It verifies the signature and structure of a compact EdDSA
JWS against a supplied public key (`verify_jwt_signature`), and leaves claim validation to the
caller.

### 5.3 Outbound auth (client side)
A consumer MUST attach a delegated bearer token on every call. The `Server` handle in the Python
and TypeScript legs captures the inbound bearer token per request and forwards it verbatim on
outbound calls, so the platform authorizes as the end user and the consumer never holds standing
user credentials. Where no user context is present it falls back to a configured API key
(`AGIENCE_API_KEY`).

Only the Python leg mints. `prism.trust.server_auth` forwards the caller's raw user token to
Origin as the `subject_token` of `POST {ORIGIN_URI}/internal/delegation-token`; Origin verifies it,
derives the subject itself, and returns a delegation with `sub = user`, `act.sub = this persona`,
`aud = this persona`. When that mint fails the caller falls back to the persona's own service
identity, which is unrooted.

### 5.4 Host identity
A host that signs (rather than only verifies) MUST load a private key for its service
identity and publish the matching public key into the authority manifest. Hosts that
only verify inbound tokens need no signing key. Keys are read from `KEYS_DIR`; the manifest
default is `$KEYS_DIR/authority.manifest.json`, and it is used only when that file exists, so a
signing-only host is not pushed into JWT-enforcing mode with an empty keyset.

The Python and TypeScript legs sign RS256 against the platform's RSA identities and serve the
public key at `/.well-known/jwks.json`. The C++ leg holds an Ed25519 keypair instead
(`HostIdentity`): generate or derive from a 32-byte seed, persist the seed, sign detached
signatures, and emit the public key as an OKP JWK (RFC 8037) with an RFC 7638 thumbprint as `kid`.
Its tokens are EdDSA rather than RS256, and are verifiable by `jose` and PyJWT.

## 6. Host surface (build a provider)

A conformant Host SDK MUST let an implementer:

1. **Construct a host** with a name/identity and inbound-auth config.
2. **Declare capabilities.** Each capability is registered by name, `<domain>.<verb>`
   (e.g. `embeddings.embed`), and mounted as an HTTP route on the host app. The handler's type
   annotations drive request and response validation.
3. **Handle invocation** with this order:
   `verify inbound token (§5.2) → run → return result`. Both serving legs enforce the token check
   by attaching it as a dependency of every operator route.
4. **Register / advertise** to the platform: a host self-registers by `POST {EMBER_URI}/hosts/register`
   with `{"name": …, "operators": [...]}` and its connection token as the bearer. The endpoint is
   served by the Ember leaf (`ember/surface/serve.py`) and gated by `EMBER_INVOKE_TOKEN`; Mantle
   serves no `/hosts` route. Registration is opt-in: a host with no token, or no `EMBER_URI`, serves
   standalone and logs which half is missing. A refusal is logged as a refusal — the host serves on
   with its operators unannounced.

   The platform is passive: it does not scan hosts. Everything a registration records is declared
   rather than measured, and the artifacts say so — each capability carries `source: "declared"`
   and the host row carries `probed: false`. The host row is written at a deterministic id
   (`host.<slug>`), so a re-registration replaces it. Operator rows are written unsigned and not
   admissible, because the legs send a name and not a spec: they are pointers to a remote endpoint,
   recorded so the graph knows the host exists and what it claims.

A host SHOULD serve `GET /health`; both serving legs mount it, returning the host name and the
operators mounted so far.

The C++ leg implements none of this serving surface. What it does implement of §6 is registration:
`MantleClient::register_host` posts the same `{"name", "operators"}` body, and
`register_manifest` posts a signed capability manifest instead (§15). Unlike the other two legs it
raises `ProtocolError` on a non-2xx rather than logging a warning.

## 7. Client surface (build a consumer)

The client surface is `AgienceClient`, built from a `Server` handle. It provides:

1. **Carry the caller's identity** — forward the captured delegated token, or the configured API
   key when there is none (§5.3).
2. **Invoke a capability** — `invoke(artifact_id, name, arguments)` posts to
   `/artifacts/{id}/op/invoke`, dispatched through Crystal.
3. **Artifacts** — `create(content_type, **fields)` (Crystal resolves the type's `create` op and
   routes it), `get_artifact(id)` (raw read, served by Mantle), `resolve(content_type)` (the
   type's declared operations), and `search_query(...)`, which calls the raw query primitive
   `POST /artifacts/recall` with `candidates: true` and returns the caller's authorized
   candidates, unranked and unhydrated.

Routing is by path rather than by configuration: artifact *operations*
(`/artifacts/{id}/op/*`, `/create`, `/resolve/*`) go to `CRYSTAL_URI`; raw CRUD, search and events
stay on `MANTLE_URI`. Crystal forwards the caller's token, and Mantle enforces access on it.

The client does not need to know which host backs a capability. The TypeScript leg exports
`AgienceClient` from the package root; the Python leg reaches it as `server.client()` or
`prism.server.client.AgienceClient` — it is not re-exported from `prism`. The C++ leg has
`MantleClient`, whose only calls are the two registration posts of §6.

## 8. Capability & scope model

- A capability invocation is authorized by a **scope**: `type : contentType : action`, with an
  optional `:anonymous` suffix; the default is identified access. `type` is one of `resource`,
  `tool`, `prompt` (the MCP primitives); `action` is one of `read`, `write`, `search`, `invoke`,
  `delete`, `create`; `contentType` is a content type or a wildcard (`text/markdown`, `text/*`,
  `*`). So a tool scope reads `tool:application/vnd.agience.search+json:invoke`.
- The vocabulary is a contract and lives in the Python leg (`prism.trust.scopes`): parsing a
  scope, matching a content-type wildcard, and recognising a special scope are pure functions of
  the string. Origin re-exports it wholesale (`origin/scopes.py` holds no implementation of its
  own). The TypeScript and C++ legs carry no scope module.
- Enforcement is not in the legs, and a scope string on a key grants nothing by itself. Access is
  decided by the grants the resource server resolves: Mantle's light-cone over the lattice
  (`mantle/db/access.py`), in CRUDEASIO terms — public is the un-keyed top that everyone reads
  with no grant, and grants only add private or owned reach on top of it. Grants are the unit of
  entitlement; "grants are keys."
- Capabilities MAY be **closed / entitlement-gated**: a host may refuse unless the caller holds a
  feature grant, which is how premium and closed hosts work without any protocol change. The
  mechanism is the special scope `licensing:entitlement:<name>`, which has no parse under the
  content-type grammar above. A host reads the entitlements out of its verified operator token's
  scopes and intersects them with the entitlements it knows; the Chorus `ophan` persona does
  exactly this, using `prism.trust.scopes.extract_licensing_entitlements`, and also narrows by the
  token's `resource_filters` so a token can be confined to named workspaces.

No leg's host scaffold declares or checks a scope requirement. `host.operator(...)` takes a name,
a path and HTTP methods, and nothing else; the invocation order it enforces is the one in §6.3. A
host that gates on an entitlement does so in its own tool code, as `ophan` does.

## 9. Content types

- Content types are `application/vnd.agience.<name>+json`. A host declares the types it owns at
  registration; the content-type gateway resolves `content_type → behavior` and routes
  operations to the owning host.
- The gateway is **Crystal** — `agience-crystal`, its own Apache-2.0 repository (dist
  `agience-crystal`, import `crystal`), not a package inside Chorus. It reaches Origin over HTTP
  at `ORIGIN_URI` rather than importing it, and a test holds `crystal → origin` at zero import
  sites.
- Crystal is not limited to the Agience vendor types. It ships builtin type skeletons under
  `src/types/<top-level>/<subtype>/`, each with a `type.json` (identity, inheritance, embedded
  `ui` display metadata) and optional `schema.json` and `behaviors.json`. Present are
  `text/markdown`, `text/plain`, `application/json`, `application/pdf`, the
  `application/vnd.agience.*` set (anchor, authority, collection, grant-invite, issuer, package,
  prompt, resource, search, secret, type, workspace), and a `_wildcard` fallback for each of
  `text`, `image`, `audio`, `video` and `application`.
- A leg MUST pass content types through opaquely; it MUST NOT hardcode a type
  registry (types are data, owned by hosts, not by the SDK). The legs hold no resolution registry.
  They do each name the content types of their *own* artifacts as constants — the crystal, the
  prism environment, the bundle, the frame, the capability manifest — which is a declaration, not
  a registry.

## 10. Error model

A leg MUST surface a typed, stable error set (names idiomatic per language):

| Condition | Meaning |
|---|---|
| `AuthError` | missing/invalid/expired token, or unrooted delegation (§5.1) |
| `EntitlementError` | authenticated but lacks the required scope/grant (§8) |
| `CapabilityNotFound` | no host advertises the requested capability |
| `HostUnavailable` | the backing host is unreachable / starting up |
| `ProtocolError` | malformed request/response or version mismatch |

Transport-level mapping SHOULD follow: auth → 401, entitlement → 403, not-found → 404,
host down → 502/503.

All three legs carry this set with identical names, a shared base type (`PrismError`), a stable
machine slug per member (`auth_error`, `entitlement_error`, …), a structured body
`{"error": <code>, "detail": <message>}`, and the same statuses: 401, 403, 404, 503, 400. The
Python and TypeScript legs also ship a one-call handler installer that maps any `PrismError`
raised by an operator to its status. `EntitlementError` is defined and tested in all three legs;
no leg raises it from an invocation path, for the reason in §8.

The C++ leg adds one member the others do not give a wire code: `BundleIntegrityError` → 400,
raised when a bundle's content does not match its claimed sha256. All three legs make that check
before grounding; in the other two its callers never cross a transport boundary, so they raise a
plain error with no code and no status.

## 11. Versioning & negotiation

- Additive capabilities are backward-compatible; removals require a major bump.

The prism protocol version is not on the wire. No leg defines or advertises a `prism/1` version
string, and none refuses a peer on version grounds. What is negotiated at `initialize` is the MCP
protocol version, which the MCP SDK owns. Each leg carries its own package version instead
(Python `0.1.2`, TypeScript `0.1.0`, C++ `0.1.0`).

## 12. Conformance

An SDK is a conformant leg if it:

1. builds on the language's MCP SDK (§3);
2. implements at least one surface (§6 or §7) completely;
3. enforces inbound verification (§5.2) on the host surface;
4. uses only canonical config names (§4, Appendix);
5. produces the typed errors in §10;
6. passes the shared **conformance vectors** published alongside this spec.

The vectors exist and are the executable form of the parts of this document they cover. They ship
as package data inside the Python leg (`prism/vectors/*.json`), so `pip install agience-prism` is
enough to run a conformance gate — no source tree and no sibling repo. `contract_vectors.json`
carries 16 canonical-JSON cases, 2 crystal digests, 15 structural-encoding cases and the junction
sections (`capability_reach`, `activates_on`, `required_capabilities`, `validate`); four further
files pin frame-wire, ordering, plane-seal and screen-read vectors.

All three legs' CI runs them against those same bytes, and each harness fails rather than skips
when it cannot load them — a gate that cannot find its vectors has verified nothing. Because the
legs are separate repositories (§13), the TypeScript and C++ workflows check out the Python leg as
a sibling to reach the vectors, and assert the file is present before anything builds. The
TypeScript leg asserts every section; the C++ leg asserts the 16 canonical-JSON cases. That C++
harness found a real cross-SDK divergence on its first complete run: RFC 8785 sorts object keys by
UTF-16 code unit and that leg was sorting by UTF-8 byte, which disagree whenever an astral
character meets a high BMP one — a content-address divergence, invisible while nothing compared
the leg to the vectors.

The vectors cover serialization, content addressing and the junction. They do not yet cover the
auth accept/reject cases, the scope allow/deny cases, delegation propagation or the error mapping.

## 13. Reference implementation & naming

The three legs are three separate repositories, checked out as sibling directories under a plain
`agience-prism` folder that is not itself a repository. This is why the cross-leg CI in §12 needs
two checkouts.

- **Python** — leg `agience-prism/py`, repo `agience-prism-py`, dist `agience-prism`, import
  `prism` (`from prism import Host, create_server`). Host flavor = `prism.host`;
  MCP-server flavor = `prism.server`; trust floor = `prism.trust`.
- **TypeScript** — leg `agience-prism/js`, repo `agience-prism-js`, npm `@agience/prism`. Same
  concepts over the TS MCP SDK, with subpath exports `/host`, `/server`, `/trust`.
- **C++** — leg `agience-prism/c`, repo `agience-prism-c`, a CMake library (ISO C++17, libsodium
  its one required runtime dependency). The performance bundle for embedded / edge / hot-path
  environments the other two cannot reach.
- Further languages (Go, Rust, C#, …) are added on demand; each is its own leg, conforming to this
  spec.

## 14. Canonical JSON and content addressing

Every content address in the system is a sha256 over one serialization, and all three legs
implement it: **RFC 8785 (JCS) canonical JSON**. Two hosts agree on a digest only if they agree on
three independent things, and JCS pins all three — strings are raw UTF-8 with no `\uXXXX` escaping
of non-ASCII; numbers render per ECMAScript `Number::toString`; keys sort by UTF-16 code units,
not code points. This is what the shared vectors in §12 are mostly checking, and a leg that gets
any of the three wrong produces a different address for the same object.

In the Python leg this lives in `prism.canonical` and is stdlib-only, so a bare host or an
installer can compute a content address without pulling a dependency tree. There is one copy of
it; every consumer imports it rather than vendoring it.

Built on top of it, and also pinned by the vectors:

- **The crystal contract** (`prism.crystal_model`, `crystal.ts`) — validate, hash
  (`crystal_sha`), and `verify`, which re-hashes a crystal artifact's content and refuses to
  ground it on a mismatch. Client-side integrity, computed locally rather than taken from the
  server. Crystal re-exports this from the Python leg rather than holding a copy.
- **The structural address** (`prism.structural`, `structural.ts`) — a deterministic-CBOR sha256
  (`cbor-det-sha256`) that refuses values it cannot address faithfully rather than silently
  encoding them as something else.
- **Bundle verification** — verify a payload against a claimed `cas/sha256(<hex>)` reference
  before grounding it, and refuse on mismatch.

## 15. The capability-kind vocabulary and the signed manifest

Separate from the operator names of §6 is a typed, versioned vocabulary of capability *kinds* —
what a prism can physically do. The canonical home is `prism.capabilities.CAPABILITY_KINDS` in the
Python leg, mirrored in the TypeScript leg as `CAPABILITY_KINDS` and in the C++ leg as
`capability_kind::*`; Crystal's `operator_schema` re-exports it, and Ember reaches it through
Crystal. The kinds are `fs.read`, `fs.write`, `storage.kv`, `net.get`, `net.request`,
`compute.local`, `compute.wasm`, `compute.gpu`, `store.read`, `store.write`, `ui.render`,
`human.ask`, `sensor.capture` and `actuator.control`.

Two families — `sensor.*` and `actuator.*` — are accepted by prefix, so a host advertises
`sensor.temperature` without waiting on a vocabulary release. `is_known_capability` is a spelling
check and nothing more: it catches `webgpu` as a typo for `compute.gpu` before the name is signed
into a manifest. Whether a capability is present, reachable or permitted is answered elsewhere —
by propagation for the first two, by the grant on the energy for the third.

The C++ leg builds this into a **signed capability manifest**
(`application/vnd.agience.prism-manifest+json`). A manifest is bidirectional: each capability
carries `in` (world → frame, the host can sense this) and `out` (frame → world, the host can
actuate this), with an optional `scope` narrowing it to a path prefix or an origin. It is
serialized canonically (§14), signed EdDSA with the host identity of §5.4, and emitted as
`{"manifest": {...}, "signature": {"alg": "EdDSA", "kid": …, "sig": …}}`;
`verify_signed` recomputes the canonical body and checks the signature against the embedded JWK.
`MantleClient::register_manifest` posts it to `/hosts/register`. The Python and TypeScript legs
send operator names at registration, not a manifest.

## 16. The `prism` command

The Python leg installs a console script, `prism`, with four subcommands:

- `prism init` — generate this prism's keypair and capability manifest.
- `prism list` — bundles and crystals from Mantle, filtered by what this prism can ground.
- `prism install` — verify and ground a bundle, refusing on a content-address mismatch.
- `prism publish` — build, validate and sha-stamp a local crystal or bundle.

`install` and `publish` verify through `prism.crystal_model`, which sits in the dependency-free
base of the package, so the command has no cross-repo import to arrange. The TypeScript leg ships
no CLI; the C++ leg ships `examples/prism_init.cpp`, which is an example rather than an installed
command.

---

## Appendix — canonical configuration

| Variable | Purpose | Notes |
|---|---|---|
| `ORIGIN_URI` | identity/auth authority | never `*_API_URI`; dev default `http://localhost:8080` |
| `MANTLE_URI` | artifact store, raw CRUD, search, events | dev default `http://localhost:8081`; `AGIENCE_API_URI` is honoured as a deprecated alias |
| `CRYSTAL_URI` | content-type gateway — artifact operation dispatch | dev default `http://localhost:8085` |
| `CHORUS_URI` | host / persona discovery | dev default `http://localhost:8082` |
| `EMBER_URI` | the local leaf — serves `POST /hosts/register` | dev default `http://localhost:8091`, but host registration resolves it with an empty default so registration stays opt-in |
| `KEYS_DIR` | host signing/identity keys (signing hosts only) | no default; `$KEYS_DIR/authority.manifest.json` is the manifest path when that file exists |
| inbound-auth mode config | one of authority-JWT / HS256 / static key (§5.2) | the static-key variable a host declares. EMBEDDINGS_URI / EMBEDDINGS_API_KEY are **not** a live example: crystal marks them left-behind shims, and mantle's only embeddings implementation is an unconfigured stub, so no embeddings host ships. |

`EMBER_URI` names the leaf because the leaf owns what host self-registration touches: the store the
registration writes to, and the `EMBER_INVOKE_TOKEN` gate that protects it. Addressing it anywhere
else would mean a second receiver duplicating both.

A leg MUST read these names verbatim and MUST NOT introduce aliases beyond the one deprecated
`AGIENCE_API_URI` above.

## What is still outstanding

| # | Outstanding | What exists today |
|---|---|---|
| 1 | No leg checks a scope or grant before running a capability. The host scaffold has no way to declare a scope requirement on an operator, and `EntitlementError` is raised from no invocation path. | Inbound token verification runs on every operator route. Grants are enforced by the resource server — Mantle's light-cone over the lattice — and a host that wants a feature gate writes the entitlement check in its own tool code, as the Chorus `ophan` persona does with `licensing:entitlement:<name>` scopes. |
| 2 | The host-side verifiers do not reject a token with a bare service principal and no rooting person. | Origin's `/internal/delegation-token` refuses to mint a delegation for a service, server or delegation principal, so rooting is enforced where delegations are issued. The Python and TypeScript legs sign and verify RFC 8693 delegations with `sub`/`act.sub`. |
| 3 | Capabilities are not exposed as MCP tools, and the artifacts a capability serves are not exposed as MCP resources. `agience://` appears in no leg's code. | The Host surface mounts each operator as a plain HTTP route on a FastAPI or Express app. MCP tools are the separate MCP-server surface, where an implementer defines them directly on the MCP SDK's server object. |
| 4 | The prism protocol version is not on the wire: no leg defines `prism/1`, advertises it at `initialize`, or refuses an incompatible peer. | The MCP SDK negotiates the MCP protocol version. Each leg carries its own package version. |
| 5 | No leg calls `/.well-known/mcp`; enumerating reachable hosts and capabilities is not part of any client surface. | Crystal's host app serves the discovery document, and both the Python and TypeScript legs resolve `CHORUS_URI` as the address to ask. |
| 6 | The conformance vectors do not cover the auth accept/reject cases, the scope allow/deny cases, delegation propagation or the error mapping. | They cover canonical JSON, crystal digests, structural encoding and the junction, and all three legs' CI runs them against the same bytes. |
| 7 | The C++ leg builds on no MCP SDK and implements neither the host serving surface nor the client surface, so it does not meet conformance items 1 and 2. | It implements host identity, the signed capability manifest, canonical JSON and content addressing, the typed error set, canonical config, and registration through a minimal outbound HTTP client. |
| 8 | The client surface has no update, list or delete for artifacts, and no way to acquire a token of its own — only the Python leg's trust floor exchanges one. | `create`, `get_artifact`, `resolve`, `invoke` and `search_query`, all carrying the caller's captured delegation token. |
| 9 | Registration sends operator names only, not the content types a host owns, and clears no operator rows from a previous registration. | The host row is written at a deterministic id, so re-registering replaces it. Operator rows are recorded unsigned and not admissible, as pointers to a remote endpoint. |
