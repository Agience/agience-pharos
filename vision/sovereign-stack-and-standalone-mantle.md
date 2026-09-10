# Sovereign Stack — Standalone Mantle + Origin as Agience's own IdP

**Most of this is built.** The sovereign shape below landed in `agience-mantle` and
`agience-origin`; two items did not, and they are listed under *What is still outstanding* at the
end. Read the body as the design and the reasoning, not as a queue of work.

Related: [`components.md`](../design/components.md), [`prism-protocol.md`](../features/prism-protocol.md).

---

## 1. Thesis

Agience targets **true sovereignty**: zero dependence on infrastructure it does not
control. Under that thesis an OSS dependency is still a dependency (Keycloak = Red Hat,
Arango = BSL, Postgres = an external chain). So the identity layer is **owned, not
imported** — we keep **Origin** rather than bundling a third-party IdP. The long-term
corollary is that *every* component eventually becomes owned/controlled (Arango and
Postgres included) — but that is north-star, not this work (see §7).

The counter-principle "don't roll your own auth" is not discarded — it becomes a
**condition**: because we own auth, Origin MUST be a *standards-compliant OIDC
provider* (implementing a spec, not inventing a scheme), so it stays defensible and
swappable.

## 2. Principle (the best-architecture shape)

Split by concern; apply the standard rule for each:

| Concern | Rule | Result |
|---|---|---|
| **Authentication** | federate — verify OIDC, don't hand-build | Mantle verifies any OIDC issuer directly |
| **Authorization (grants)** | co-locate with the data it governs | grants live **in Mantle**, next to artifacts |
| **Key custody** | separate from the store (envelope encryption), pluggable | KMS interface in Mantle: local default, external adapter later |
| **Delegation (STS)** | a feature of the IdP, not the DB | RFC 8693 token-exchange on Origin |

Sovereignty then dictates that the *implementation* filling each pluggable slot is one
Agience owns (Origin), while the *interfaces* stay open so a deployment can swap in
Entra / Vault / etc.

## 3. Target architecture

```
IDENTITY   Origin = Agience's OWN OIDC provider (+ KMS/key-oracle + STS).
           Standards-compliant → verified like any OIDC issuer, swappable for Entra.

MANTLE     Self-contained encrypted DB. Owns GRANTS + API keys. Verifies OIDC
           directly (multi-tenant by issuer namespace). Local key backend.
           Runs STANDALONE with no Origin.

KMS        Pluggable key-custody interface inside Mantle. Local default;
           external (Vault/cloud) adapter is a later add, not a new repo.

STS        RFC 8693 delegation minting — an Origin endpoint. Needed only when a
           service mesh (chorus/crystal/personas) acts on a user's behalf.
```

Two modes fall out of the same code:
- **Standalone Mantle** — external IdP (or Origin) for auth; grants + keys local. No platform.
- **Platform Mantle** — points grant/key/delegation backends at Origin for the full stack.

## 4. Current state

Authentication *and* authorization are both standalone; authorization is no longer routed to
Origin. Most of §5–§6 below is therefore delivered rather than pending.

| Capability | Today | File anchor |
|---|---|---|
| OIDC/JWT verification | standalone | `mantle/services/auth_service.py` → `get_oidc_verifier().verify()`; `services/oidc.py` (one module, not a package), `services/issuers.py`; `AGIENCE_TRUSTED_ISSUERS` |
| Multi-tenant identity | per-issuer namespace | issuer artifacts + manifest JWKS |
| Grant model | Mantle entity | `mantle/entities/grant.py` |
| Grant store/checks | **local** (was routed to Origin) | `mantle/services/grant_store.py` `LocalBackend` → `check_grant`, `lookup_grants_by_key`, `upsert_user_grant` — "Grants + API keys from Mantle's own lattice. No Origin dependency." |
| API-key (`agc_`) verify | local | `mantle/services/auth_service.py` `verify_api_key(db, api_key)`; `grant_store.py` `LocalBackend.verify_api_key` |
| Delegation minting | Origin | Origin still mints, at its auth router's `/internal/delegation-token`. Mantle has no client method for it. |
| Secret vault | local | `mantle/services/secrets_service.py`; Mantle owns secret-artifact material — **module deleted 2026-08-10**, see note below |
| Local key custody | backend exists | per `components.md` ("Mantle keeps a local self-custody backend") |

**Path note added 2026-08-23 (additive; the table above is unedited).** The secret-vault row's module
was **deleted from mantle on 2026-08-10** and has no successor file, so it is not repointed. The
CAPABILITY did not regress — it dissolved into the general mechanism: a secret is now an ordinary
artifact whose value is content, encrypted at rest by the envelope and authorized by the light cone, so
mantle mounts no `/secrets` surface and needs no vault module (`agience-mantle/src/mantle/main.py`).
The row's still holds; only its evidence path is gone.
The entities/api_key.py reference in §"Next steps" item 2 (unbackticked here so this note does not add a
copy of the reference it is describing) is a **forward reference** — that item is
proposing to move API-key entities into Mantle, and the file does not exist yet by design. It is left
exactly as written.

Note: Mantle's Origin client is down to one call, `get_operator_id`, and it degrades gracefully
(catch `HTTPError` → `None`). A standalone Origin-off Mantle names its operator via
`AGIENCE_OPERATOR_ID` instead; grant-gated operations no longer deny without Origin.

## 5. What changes

1. **Grants → Mantle (local, pluggable).** Reintroduce/implement an Arango-backed grant
   store in Mantle behind a `GrantStore` interface with two backends: `local` (default)
   and `origin` (current behavior). Enforcement happens in Mantle regardless of backend.
   The `origin_client` grant methods become the `origin` backend, not the only path.
2. **API keys → Mantle.** Verify Mantle-owned `api_key` entities locally (`entities/api_key.py`);
   keep Origin verification as the optional `origin` backend.
3. **KMS interface.** Formalize key custody as a `KeyCustodian` interface in Mantle:
   `local` default; leave an `external` adapter (Vault/cloud) as a stub for later. No new repo.
4. **Origin → standards-compliant OIDC.** Add/confirm `/.well-known/openid-configuration`,
   JWKS, and standard `authorization_code` + `token` endpoints so Origin is a drop-in OIDC
   issuer. Mantle already verifies it via the generic OIDC path — no Mantle change needed.
5. **STS standardization.** Expose the existing `/internal/delegation-token` as a proper
   RFC 8693 `token-exchange` grant on Origin's token endpoint. Multi-service only.

**How it actually landed.** The three-way config selector was never needed, because the
platform went further than the design asked: rather than defaulting to local, it became
local-only.

- **Grants** live in Mantle's own store — the SQLite lattice reached through `db.backend` — and
  Mantle enforces authorization itself, in `services/dependencies`, with no call to Origin.
  Origin is identity-only. `services/grant_store.py` carries this. There is no `origin` backend
  to select between, so `GRANT_BACKEND` does not exist.
- **API keys** are grants. A bearer credential is stored as `grantee_type="grant_key"` and
  handled by `services/grant_key_service.py`, so it needs no backend of its own and
  `APIKEY_BACKEND` does not exist.
- **Key custody** is pluggable, under a different name. `search/mantle/key_provider.py` defines a
  `KeyProvider` abstraction over the key-encryption key, selected by `MANTLE_KEK_PROVIDER`
  (default `local`). It supports both an exportable KEK — local file, Vault KV, Secrets Manager —
  and a non-exportable one where the KEK never leaves the service and only the 32-byte data key
  crosses the wire. That is the `KeyCustodian` idea, built and named differently.

## 6. Work plan (sequenced)

Phases 1 to 3 are delivered. The table records how the work was sequenced, not what is left.

| Phase | Work | Effort | Priority |
|---|---|---|---|
| **1** | `GrantStore` interface + local backend; enforce in Mantle; default `local` | M | first |
| **2** | `APIKEY_BACKEND` local; Mantle-owned key verify | S | with §1 |
| **3** | `KeyCustodian` interface (local default; external stub) | S | with §1 |
| **4** | Origin → full OIDC compliance (discovery/JWKS/flows) | M | second |
| **5** | STS = RFC 8693 token-exchange grant on Origin | S | when mesh needed |

## 7. Non-goals / out of scope (north-star only)

- **Replacing ArangoDB (BSL) or Postgres** with owned datastores. Real direction, *far*
  bigger than a thin IdP — do NOT bundle it with this work.
- **Building a bespoke KMS.** Adapt to Vault/cloud when external custody is needed; don't
  ship a key daemon.
- **Rolling bespoke auth.** Origin MUST be standard OIDC; this is not license to invent.
- The "reserved-invention" framing is explicitly set aside — this is the merit +
  sovereignty answer.

## 8. Acceptance criteria

- **Standalone proof:** Mantle boots and serves with **Origin OFF** — OIDC auth works,
  grants enforced from the local backend, artifact CRUD + encrypted search work.
- **Parity proof:** retired. It asked the same Mantle to behave identically with
  `GRANT_BACKEND=origin`, and no such mode exists — the origin grant backend was removed when the
  platform became sovereign-only. There is nothing to compare against, and nothing to fix.
- **Federation proof:** a node configured to trust **Entra** (no Origin) authenticates
  and authorizes end-to-end.
- **OIDC proof:** Origin passes an OIDC discovery + JWKS + code-flow smoke test and is
  verifiable by Mantle's generic verifier with no special-casing.

---

## 9. What is still outstanding

Two items from this design are not in the code. Both sit on Origin, and neither blocks a
standalone Mantle.

| # | Outstanding | What exists today |
|---|---|---|
| 5 | **Token exchange is not advertised as a standard grant.** A third-party client that discovers Origin's capabilities cannot find delegation, because `urn:ietf:params:oauth:grant-type:token-exchange` is not among the grant types the discovery document lists — `tests/test_jwks.py` asserts that absence deliberately | Delegation itself is built and is RFC 8693-shaped: `POST /internal/delegation-token` exchanges a verified user token for a short-lived delegation JWT (`services/auth_service.py`). It works, at a non-standard path, so only Agience's own clients reach it |

Item 4 of the plan is done. Origin serves `/.well-known/openid-configuration` and
`/.well-known/oauth-authorization-server` from `routers/auth_router.py`, built from
`AUTHORITY_ISSUER` rather than from the request, with JWKS at `/.well-known/jwks.json`. It is a
drop-in OIDC issuer for a client that discovers its endpoints.

The one outstanding item is not required for the standalone case: a node that trusts an external
OIDC issuer, or one that uses Origin directly, works today.
