# Transport security — the hardening roadmap

This is the designed layer above the requirements that already bind, which are in
[`../features/transport-bound-auth.md`](../features/transport-bound-auth.md).

Most of it is a design rather than a description, and each section below says which it is. Two
things it describes are built and running: the delegation identity chain, including the `host_id`
claim, which Origin stamps and Mantle refuses a token without; and the trusted-proxy client-IP
resolution, which exists in Mantle but feeds only the rate limiter, not any credential policy.
Nothing else here — no IP-origin binding on a credential, no DPoP, no mTLS — exists in the code.
The gaps are collected in *What is still outstanding* at the end.

The original design was written against an API-key entity that no longer exists. API keys have been
retired: the bearer credential is now a **grant key** — a `Grant` row with
`grantee_type="grant_key"`, minted at `POST /grants/keys`, carrying the `agk_` token prefix. A
presented `agc_` API key is refused with a 401 that names the replacement. The design below is
restated against the credential that exists.

## IP-Origin Binding (Grant Keys)

No credential this platform issues is bound to the transport it arrives over. Neither
`transport_policy` nor `allowed_networks` exists on the `Grant` entity or on
`CreateGrantKeyRequest`, and no code path rejects a credential for the address it came from.
`CreateGrantKeyRequest` does not forbid extra fields, so **a POST built from the request bodies in
*Transport Policy* or *API Surface* below is accepted with the extra field dropped, and the key is
not network-bound.** Nothing in the response signals this.

### Transport Policy

A `transport_policy` field on the grant key would control enforcement:

| Policy | Behavior | Use Case |
|--------|----------|----------|
| `"any"` | No transport check (current behavior) | Public/cloud keys, backward compat |
| `"local"` | Only accepts loopback connections (`127.0.0.0/8`, `::1`) | Claude Code, local dev tools, desktop relay |
| `"network"` | Only accepts connections from specified CIDR ranges | VPN-bound keys, office network, known infra |

A second field, `allowed_networks`, would hold CIDR strings for the `"network"` policy:

```json
{
  "name": "Office Workstation Key",
  "transport_policy": "network",
  "allowed_networks": ["10.0.1.0/24", "192.168.50.0/24"],
  "resource_id": "…"
}
```

### Enforcement Point

The check would sit in Mantle's `resolve_auth` (`services/dependencies.py`), which is the one place
every credential is resolved and which already receives the `Request`. It would run **after** the
grant key authenticates and **before** the `AuthContext` is returned. That would mean:

1. Invalid tokens still get 401 (auth failed)
2. Valid tokens from wrong networks get 403 (auth succeeded, transport context denied)
3. Valid tokens with `transport_policy: "any"` pass through unchanged

```
Request arrives
  -> Extract Bearer token
  -> Authenticate (grant key hash, or JWT verification)  -> 401 if invalid
  -> Resolve AuthContext + the root grant
  -> Check transport_policy vs the resolved client IP     -> 403 if mismatch
  -> Return AuthContext, proceed to dispatch
```

### Client IP Resolution

This part is built, and it is the only part that is.

The real client IP comes from `scope["client"][0]` in the ASGI scope. Behind a reverse proxy or
Docker networking, that value is the proxy/gateway IP, not the client.

Mantle resolves it through `_rate_limit_client` in `main.py`, using the trusted-proxy list in
`AGIENCE_TRUSTED_PROXIES` (the name in the code; not `TRUSTED_PROXIES`):

```
AGIENCE_TRUSTED_PROXIES=172.17.0.0/16,10.0.0.0/8
```

The algorithm, as `_parse_trusted_proxies`, `_is_trusted_proxy` and `_rate_limit_client` implement
it:

1. Parse `AGIENCE_TRUSTED_PROXIES` into `ipaddress.ip_network` objects at import; an entry that
   does not parse is dropped with a warning rather than widening the list
2. On each request, check whether the socket peer falls within a trusted proxy network
3. If yes: read `X-Forwarded-For`, walk entries right-to-left, return the rightmost entry that is
   neither a trusted proxy nor unparseable
4. If no: return the socket peer directly

This is the standard "rightmost non-trusted" XFF algorithm. Unset means trust nobody's word,
which makes every caller behind an unlisted edge share the edge's one address.

Its only consumer is the per-client rate limiter. No credential check reads it.

### IPv4-Mapped IPv6 Handling

A `"local"` policy check must unwrap IPv4-mapped addresses via `.ipv4_mapped` before testing
`is_loopback`: when loopback arrives as `::ffff:127.0.0.1`, Python's
`ipaddress.ip_address("::ffff:127.0.0.1").is_loopback` returns `False`. Mantle's existing IP
resolution does not need this — it compares against configured networks rather than testing
loopback — so nothing in the code does it today.

## API Surface (proposed)

The live grant-key surface is `POST /grants/keys`, `GET /grants/keys`, `GET /grants/keys/{key_id}`
and `DELETE /grants/keys/{key_id}`. There is no `/api-keys` route and no PATCH on a key, so the
update below would need a route that does not exist.

### Create Grant Key

```
POST /grants/keys
{
  "name": "Local Dev Key",
  "transport_policy": "local",
  "resource_id": "…"
}
```

### Create with Network Binding

```
POST /grants/keys
{
  "name": "Office Key",
  "transport_policy": "network",
  "allowed_networks": ["10.0.1.0/24"],
  "resource_id": "…"
}
```

### Update Transport Policy

```
PATCH /grants/keys/{key_id}
{
  "transport_policy": "network",
  "allowed_networks": ["10.0.1.0/24", "192.168.1.0/24"]
}
```

### Validation Rules

- `transport_policy` must be one of `"any"`, `"local"`, `"network"`
- `"network"` requires non-empty `allowed_networks` with valid CIDR strings
- `"any"` and `"local"` reject `allowed_networks` if provided
- CIDR strings validated via `ipaddress.ip_network(cidr, strict=False)`

## Data Model Changes (proposed — neither field exists)

### Grant Entity

| Field | Type | Default | Notes |
|-------|------|---------|-------|
| `transport_policy` | `str` | `"any"` | One of `any`, `local`, `network` |
| `allowed_networks` | `Optional[List[str]]` | `None` | CIDR strings; only for `"network"` policy |

Grants live in Mantle's own store and are hydrated through `from_dict()`, so existing rows without
these fields would default to `"any"` / `None`. No migration required.

### Pydantic Schemas

Add both fields to `CreateGrantKeyRequest` and to the grant response shape, plus whatever request
model a key-update route would take. Cross-field validation via `model_validator(mode="after")`.

### Config

```python
_TRANSPORT_TRUSTED_PROXIES = _parse_trusted_proxies(os.getenv("AGIENCE_TRUSTED_PROXIES", ""))
```

The parser and the environment variable already exist; a policy check would read the same list
rather than introducing a second one.

## Implementation Files (proposed — none of these changes landed)

The change touches the `Grant` entity and the grant-key request/response schemas, the grant-key
routes in `routers/grants_router.py`, the credential resolution in `services/dependencies.py`
(the policy check and the 403 path), a shared reader for the already-parsed trusted-proxy list in
`main.py`, and tests for both validation and enforcement.

## Scope

Token security and transport binding for the Agience platform has these layers:

1. **Absolute requirements** (R1–R4 in `../features/transport-bound-auth.md`) — `aud` validation,
   scoped token issuance, and first-party client routing. These apply to all token types, all
   callers, all environments.
2. **IP-origin binding** — grant key transport policy (`any` / `local` / `network`). Server-side
   enforcement, no client changes required.
3. **DPoP** — Cryptographic proof-of-possession for JWTs. Authority-level option; required for
   hardened deployments.
4. **mTLS** — Certificate-bound tokens for internal server-to-Core communication. Deployment
   infrastructure concern.

Layers 2–4 are defence-in-depth: they reduce replay risk after a token is stolen. Layers 1–4
together form the complete security posture. Layers 2–4 are meaningless without layer 1 — `aud`
validation is the prerequisite for all proof-of-possession mechanisms.

**CORS is not a substitute for any of these.** CORS restricts which origins a browser will permit
cross-site requests from. It has no effect on non-browser clients: curl, server processes, and
stolen tokens replayed via any HTTP client. JWT tokens are bearer credentials and must be
validated on their own merits.

### What CORS actually does here

The two services take deliberately different postures, and neither is a transport binding.

Mantle sets `allow_origins=["*"]` with all methods and headers. That is bounded by the fact that
every authenticated call carries a Bearer token rather than a cookie, so a cross-origin request
without credentials reaches nothing; the OAuth redirect flow uses a same-site session cookie on
browser navigations, which CORS does not govern. Mantle also sets `X-Content-Type-Options`,
`X-Frame-Options: DENY`, `Referrer-Policy: no-referrer` and HSTS on every response.

Origin runs an allowlist instead, because it mints tokens and its session cookie is what a
cross-origin read would be after. The list comes from `ORIGIN_ALLOWED_ORIGINS` when set, and
otherwise is derived from `AUTHORITY_ISSUER`, `ORIGIN_URI`, `FACET_URI` and `FACET_URIS`. It is
read from the environment at import and logged at boot, so an omitted facet reads as a listed
allowlist that does not name it. A facet configured only in the database settings store must be
named in `ORIGIN_ALLOWED_ORIGINS` as well.

---

## Viewer Iframe Auth (MCP Apps)

Viewer iframes are sandboxed HTML pages served from `ui://` MCP resources. They have no direct
access to the user's session, no stored tokens, and must not receive raw JWTs. This is enforced,
not merely intended: `McpAppHost` renders the view with `srcDoc` under `sandbox="allow-scripts"`
and no `allow-same-origin`, so the frame has an opaque origin and cannot read the host app's
`localStorage`, where the session token is kept.

A Content-Security-Policy `<meta>` tag is prepended to the view's HTML before it becomes the
`srcDoc`, built by `buildCspMetaTag` from the `_meta.ui.csp` resource field. The default is
`default-src 'none'` with `connect-src 'none'` and `frame-src 'none'`, widened only by the
domains a server declares. So a viewer that holds no credentials also has, by default, no network
egress to carry one out — the two controls close the same hole from opposite sides. A server that
declares `connectDomains` opens that path for its own views, and what it names is the whole of
what the view can reach.

### Token flow

```
Viewer (iframe)
  ── tools/call JSON-RPC postMessage ──>  McpAppHost (React)
                                              ── POST /artifacts/{id}/op/invoke ──>  Crystal
                                                                                            ── delegation JWT ──>  First-party server
```

1. **Iframe → McpAppHost**: JSON-RPC `tools/call` postMessage. No token. The iframe owns no
   credentials.
2. **McpAppHost → Crystal**: the host's `proxyToolCall` posts to
   `POST /artifacts/{artifact_id}/op/invoke` with the user's session JWT as a Bearer header,
   managed by the React app and never exposed to the iframe. The tool name travels in the body as
   `name`; the artifact's type definition declares which server and tool the operation dispatches
   to, so the frontend never names a server. Mantle separately mounts one JSON-RPC endpoint at
   `/mcp` for MCP clients — `tools/call` is a method in the body, not a path segment, and there is
   no `/mcp/servers` route — but the viewer path does not go through it.
3. **Core → first-party server**: Delegation JWT (RFC 8693), minted by Origin at
   `POST /internal/delegation-token` from a user token Origin has itself verified, defaulting to a
   300-second lifetime. Claims:
   - `sub`: user ID (the human on whose behalf the call is made)
   - `aud`: target server's `client_id`
   - `act.sub`: server's `client_id` (actor)
   - `principal_type`: `"delegation"`
   - `host_id`: the platform host that authorized the proxy call

> **R1 applies at step 3.** The delegation JWT must carry `aud=server_client_id`. The first-party
> server must validate `aud` matches its own `client_id` (R2). This is not optional even for
> first-party servers, and it is met: `ServerAuth.verify_origin_delegation` in `prism.trust`
> verifies the signature against Origin's JWKS and then requires `aud` and `act.sub` to equal the
> persona's own `client_id`, returning `None` on any failure.

### What this means for viewer developers

- Do not attempt to obtain, store, or forward any JWT in viewer HTML.
- Call tools via `tools/call` JSON-RPC — the host proxies them transparently.
- Auth for calling external services (from the server side) follows the Authorizer/OAuth Connection
  model in Part 4 of the server development guide — not anything the viewer controls.

### host_id claim

Origin includes a `host_id` claim in every delegation JWT it mints, identifying the platform host
that authorized the call. It is not advisory: Mantle requires all four entities of the identity
chain — User (`sub`), Server (`act.sub`), Authority (`iss`) and Host (`host_id`) — and a delegation
token missing `host_id` is refused with a 401.

The value is the host artifact's own id, derived as
`uuid5(instance_namespace, "agience/agience-host-current-instance")` from the shared
`KEYS_DIR/instance.uuid` that every service mounts. It is therefore a UUID, not a hostname, and it
matches the host artifact without a database lookup. When the instance namespace cannot be
resolved, the derivation returns an empty string, which Mantle rejects along with any other
missing `host_id`.

The delegation JWT shape:

```json
{
  "iss": "https://origin.example",
  "sub": "user-uuid",
  "aud": "agience-server-aria",
  "act": { "sub": "agience-server-aria" },
  "host_id": "3f2b1c4d-5e6f-5a7b-8c9d-0e1f2a3b4c5d",
  "principal_type": "delegation"
}
```

---

## Backward Compatibility (IP-Origin Binding, proposed)

- Default `transport_policy: "any"` would mean zero change for all existing grant keys
- All existing grant key creation requests continue to work (new fields are optional)
- All existing grant key responses gain two new fields with safe defaults
- In hardened deployment, the default shifts to `"local"` — existing keys must be explicitly
  migrated to `"any"` if cloud access is required

## Docker / Deployment Considerations

On the default Docker bridge network, `scope["client"]` is the bridge gateway IP (e.g.,
`172.17.0.1`), not the client IP. To make transport binding work:

1. Set `AGIENCE_TRUSTED_PROXIES=172.17.0.0/16` (or your Docker bridge subnet)
2. Ensure the reverse proxy (Caddy, Nginx, ALB) sets `X-Forwarded-For`
3. The resolver walks XFF to find the real client IP

Without `AGIENCE_TRUSTED_PROXIES`, all containerized requests appear to come from the Docker
gateway. That would make a `"local"` policy useless and a `"network"` policy unreliable, and it
already has a live consequence: the rate limiter counts every caller behind an unlisted edge into
one shared window, so it over-counts and starts refusing real traffic. The failure direction is
deliberate — an unlisted edge refuses rather than admitting a flood — but it is a visible outage.

## DPoP — JWT Proof-of-Possession (RFC 9449)

DPoP is designed, not built. Nothing named `DPOP_MODE`, no proof verification, no `cnf`/`jkt`
claim and no `jti` store exists in origin, mantle, crystal or prism. The single mention of DPoP in
the code is a line in `prism.trust.server_auth` recording that identity binding there is at the
claim level and that transport-level binding sits outside that adapter.

### What it closes

`aud` validation proves a token was **issued for** a specific audience. It does not prove the
presenter **is** that audience. A delegation token addressed to `agience-server-iris` that is
stolen transit-side passes `aud` validation at Iris and acts with full Iris privileges.

DPoP closes this: the token is cryptographically bound to an ephemeral key pair owned by the
intended presenter. Presenting the token without the matching private key gets a 401.

### Mechanism

**At token issuance** (`POST /auth/token`):
1. Client generates an ephemeral RSA or EC key pair (per-session or per-request)
2. Client sends a `DPoP` header — a short-lived signed JWT (`typ: dpop+jwt`) containing the public JWK, HTTP method, request URI, `jti`, and `iat`
3. Core verifies the `DPoP` proof is fresh and well-formed
4. Core computes the JWK thumbprint (`SHA-256` of canonical JWK) and embeds it in the issued token as `cnf.jkt`

**At each use** (any protected endpoint):
1. Client sends `Authorization: DPoP <token>` plus a fresh `DPoP` proof header bound to the current HTTP method + URI
2. Receiver verifies: (a) `aud` in token matches this endpoint, (b) `cnf.jkt` in token matches the public key that signed the `DPoP` header, (c) `DPoP` proof is fresh (`iat` within tolerance, `jti` not replayed), (d) `htu` + `htm` in proof match the current request

A stolen token without the private key is useless. A replayed `DPoP` proof fails on `jti` replay detection.

### Authority-level configuration

DPoP enforcement would be controlled at the authority level, not per-key or per-client. This allows
gradual rollout and clear policy tiers.

```
# .env
DPOP_MODE=off          # default — bearer-only, backward compatible
DPOP_MODE=optional     # accept both bearer and dpop; prefer dpop
DPOP_MODE=required     # reject bearer tokens without DPoP proof (hardened)
```

**Hardened deployment** would set `DPOP_MODE=required`. All OAuth clients must present DPoP proofs.
The platform frontend, VS Code extension, and any registered MCP clients must use DPoP-capable
OAuth libraries.

**Standard deployment** would use `DPOP_MODE=optional` or `DPOP_MODE=off`. Bearer tokens continue
to work. DPoP is opt-in for clients that support it.

### Replay detection

DPoP `jti` values must be tracked server-side within the proof's `iat` + tolerance window. In
production, this requires a shared store (Redis, or a store with a TTL index). In standard
deployment this is an in-process LRU with a configurable TTL (default: 5 minutes). In hardened
deployment, distributed replay detection is required.

### Scope: where DPoP applies

| Token type | DPoP applies | Notes |
|---|---|---|
| Platform user token | Yes | Browser OAuth clients that support DPoP |
| MCP client token | Yes | Primary use case for external integrations |
| Server credential token | No — use mTLS | Internal M2M; certificate binding is stronger |
| Delegation token (Core → server) | No — use mTLS | Same |

### Implementation files (proposed — none of these landed)

The change adds a `DPOP_MODE` setting, proof verification and `cnf.jkt` binding at issuance,
acceptance of the `DPoP` header on the token endpoint, enforcement on protected endpoints, a replay
store for `jti`, and tests for proof generation, validation, replay detection and binding.

---

## mTLS — Certificate-Bound Tokens (Internal Servers)

For first-party servers (`servers/`), mTLS is the preferred binding mechanism. The private key
never leaves the server process; the certificate thumbprint would be embedded in the token as
`cnf.x5t#S256`.

None of the mTLS binding is built: there is no `x5t` claim, no `X-Client-Cert` header, and no
verification path checks a certificate claim. It is a deployment and infrastructure design:
- Private CA managed by Vault or Caddy PKI
- Certificates issued at container start via Vault agent or init container
- Caddy terminates TLS and forwards an `X-Client-Cert` header to the backend
- Token verification checks `cnf.x5t#S256` against the forwarded certificate thumbprint

mTLS for internal servers is a post-MVP hardening item. The `aud` validation requirement (R2) is the prerequisite — certificate binding is meaningless if the receiver doesn't check what audience the token was issued for.

---

## Hardened Deployment Package

A "hardened" authority configuration combines all layers. Only the first two rows describe
behaviour that exists.

| Layer | Hardened setting | Built |
|---|---|---|
| `aud` validation | Always enforced (R1–R2, non-negotiable) | Yes — `verify_jwt` refuses rather than silently skipping the check, and first-party servers pass their own `client_id` |
| Delegation identity chain | `sub`, `act.sub`, `iss` and `host_id` all required | Yes — Mantle 401s a delegation missing any of them |
| Scoped token issuance | Always enforced (R4, non-negotiable) | Partly — see R4 in the requirements document |
| First-party client routing | No guessable default; an operator names each first-party client | No — routing reads `PLATFORM_CLIENT_ID`, which defaults to `"agience-client"`, plus `PLATFORM_CLIENT_IDS` |
| IP-origin binding | Grant keys default to `"local"` or `"network"`; `"any"` requires explicit opt-in | No |
| DPoP | `DPOP_MODE=required` | No |
| mTLS | All internal server-to-Core paths use certificate binding | No |
| `jti` replay detection | Distributed store (Redis) required | No |

Standard deployment enforces R1–R4 (absolute). IP binding for grant keys, DPoP and mTLS are all
still to come.

---

## What is still outstanding

| # | Outstanding | What exists today |
|---|---|---|
| 1 | No credential is bound to its transport. `transport_policy` and `allowed_networks` exist on no entity or schema, and no code path refuses a credential for the address it arrived from. | Grant keys (`agk_`, `POST /grants/keys`) authenticate on the token hash alone. A request body carrying either field is accepted with the field dropped, and the key is not network-bound. |
| 2 | Nothing enforces a client-IP policy. `MCPAuthMiddleware`, `_resolve_client_ip`, `_check_transport_policy` and `_send_403` exist nowhere. | Mantle resolves the real client IP through `_rate_limit_client` / `AGIENCE_TRUSTED_PROXIES` using the rightmost-non-trusted XFF walk, but only the rate limiter reads it. `resolve_auth` already receives the `Request`, so the value is reachable at the point a check would go. |
| 3 | There is no route to change a key's policy after minting. | `POST`, `GET` and `DELETE` on `/grants/keys`; no PATCH, and no `/api-keys` route at all. |
| 4 | DPoP is entirely absent: no `DPOP_MODE`, no proof verification, no `cnf.jkt` binding, no `jti` replay store. | Claim-level binding only: `aud` identifies the intended audience and is verified. `prism.trust.server_auth` records that transport-level binding sits outside it. |
| 5 | mTLS certificate binding is entirely absent: no `x5t` claim, no `X-Client-Cert` header, no certificate check on any verification path. | The same claim-level `aud` binding, plus per-service RSA keys loaded from `KEYS_DIR` and verified against the authority manifest's inline JWKS. |
| 6 | First-party client routing is named in configuration, not derived, and `PLATFORM_CLIENT_ID` still carries the guessable default `"agience-client"`. There is no `find_mcp_client()`. | `_is_first_party_client` matches `PLATFORM_CLIENT_ID` or an entry in `PLATFORM_CLIENT_IDS`; every other id receives the scoped `mcp_client` token, and `/authorize` separately refuses a `client_id` that is neither configured nor registered. |
