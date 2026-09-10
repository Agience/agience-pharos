# Token Security and Transport Binding

**The absolute requirements.** These are not proposals: every token this platform issues must satisfy
all of them, and a violation is a security defect rather than a technical-debt item.

The hardening work that is designed but **not built** — IP-origin binding, DPoP, mTLS, the viewer
iframe auth and the deployment package — is in
[`../vision/transport-security-roadmap.md`](../vision/transport-security-roadmap.md).

## Absolute Requirements

These are not proposals. Every token issued by this platform MUST satisfy all of the following. Violations are security defects, not technical debt.

### R1: Every JWT MUST carry an `aud` claim

No JWT may be issued without an explicit audience. The `aud` claim declares who the token was issued **for**. Without it, a valid signature + `iss` match is sufficient to impersonate any principal at any endpoint — the signature only proves the token came from this authority, not that the receiver is the intended party.

| Token type | Required `aud` value |
|---|---|
| Platform user token | `AUTHORITY_ISSUER` |
| MCP client token | the MCP client's `client_id` |
| Delegation token (Core → server) | the target server's `client_id` |
| Server credential token | `"agience"` |

### R2: Every JWT receiver MUST validate `aud`

`verify_token()` MUST accept `expected_audience` and pass it to `jwt.decode`. All call sites that accept user tokens MUST pass `expected_audience=AUTHORITY_ISSUER`. Server-side delegation receivers MUST pass their own `client_id`. Missing or mismatched `aud` is always a hard 401 — not a warning, not a log line.

### R3: Only clients the operator named are treated as first party

A caller must not become the platform by claiming to be it. Everything else receives a scoped
`mcp_client` token, never a full user JWT.

**This is met, by naming rather than by deriving.** `_is_first_party_client()` in
`origin/routers/auth_router.py` answers "is this the platform?" by testing membership in
`PLATFORM_CLIENT_ID` and the `PLATFORM_CLIENT_IDS` allowlist, and nothing else. It reads no
registry, so a client that enrolled itself through `POST /auth/register` is third party — in the
predicate's own words, "a client vouching for itself is not the operator naming it".

`PLATFORM_CLIENT_ID` does still exist, with `"agience-client"` as its default, and an earlier
version of this requirement called for its removal on the grounds that a guessable value would let
any caller claim to be the platform. **That threat does not hold against the code**, because
naming is not the only control: `/authorize` refuses to deliver a code to a redirect base this
authority does not admit, whatever `client_id` is presented.
`tests/test_mcp_client_token_scoping.py::test_authorize_refuses_a_redirect_base_this_authority_does_not_admit`
pins exactly that, using the default `client_id` with an attacker-controlled redirect, and expects
`403`. The default is what every node ships with so that a fresh install has a working sign-in;
`tests/test_authorize_client_enforcement.py` pins that too, noting that "a check that locked this
out would be a node nobody can sign in to".

Admission and trust are separate questions, and the code answers them separately: a registered
client reaches the login page and is still third party when the token is minted.

### R4: A third-party client's token must not carry the user's identity or authority

There is no `find_mcp_client()` in the code; the test runs the other way round. A `client_id` that
`_is_first_party_client()` does not recognise is third party, and the token it receives:

- carries `principal_type: "mcp_client"` and `aud: <client_id>`, so a receiver that validates
  audience cannot be handed it by mistake
- carries no `roles`, no `email`, and no other personally identifying claim
- does not acquire the user's roles when refreshed — the refresh token is minted from the same
  scoped payload and lives thirty days, which is why that matters

`tests/test_mcp_client_token_scoping.py` pins each of these, including the refresh case.

**On scope intersection.** An earlier version of this requirement asked that scopes be the
intersection of the user's grants with a client artifact's declared `allowed_oauth_scopes`. That
name exists nowhere in Origin, and the requirement is superseded rather than unmet: **a scope
string is not an authorization.** `origin/scopes.py` states it directly — "Origin does not enforce
API-key scopes on content types. Access is decided by the grants the resource server resolves — a
scope string on a key grants nothing by itself."

So the requested scopes do pass into the token as requested, and they bound nothing on their own.
What bounds the caller is the CRUDEASIO light-cone in `agience-mantle/src/mantle/db/access.py`,
evaluated per request against the grants that actually reach the artifact. Intersecting the scope
string earlier would narrow a label, not an authority.

**Note:** This rule is not MCP-specific. It applies to any OAuth client that is not the platform
itself.

---

## Problem: Origin Blindness

OAuth2 Bearer tokens authenticate **who** is making a request. They do not authenticate **where** the request originates.

A valid API key used from Claude Code on localhost produces an identical Bearer token to one used from claude.ai routed through Anthropic's cloud infrastructure. For MCP servers exposing privileged tools (shell access, filesystem, database), this is not a theoretical concern --- it is a concrete operational security gap.

The MCP protocol provides no mechanism to distinguish client execution context. The `clientInfo` field in the Initialize handshake is client-supplied and the server verifies nothing about it. OAuth2 token introspection returns identity claims but no transport binding.

**What we can solve today, server-side, with no protocol changes:**

The TCP connection itself carries a transport-layer signal --- the client's source IP --- that is significantly harder to forge than any application-layer field. The Agience MCP server receives this via the ASGI `scope["client"]` tuple on every request. We already have API key entities with `host_id` and `client_id` metadata fields. They are never enforced against the actual transport.

---

## What is still outstanding

Nothing in these four requirements. R1 and R2 hold as originally specified; R3 and R4 hold through
a different mechanism than the one first written down — the operator names first-party clients and
the redirect allowlist bounds delivery, and authority is resolved at the resource server rather
than carried in a scope string.

The designed hardening *above* these requirements — IP-origin binding, DPoP, mTLS — is in
[`../vision/transport-security-roadmap.md`](../vision/transport-security-roadmap.md), and none of
it is built.
