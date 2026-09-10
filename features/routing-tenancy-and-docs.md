# Routing, Tenancy & API Docs

Status: **Decided**
Date: 2026-06-11

Formalizes the public routing scheme, the multi-tenancy model, and the
consolidated API reference. Supersedes the path-prefix routing (`my.agience.ai/api`)
described implicitly across the deploy repos.

Related: [[authority-host-server-topology]], [[four-service-roles]]. The OpenAPI generator does not
run anywhere: neither mantle, origin nor this repository has a `docs/` directory, and the only
copies of `build_openapi.py` and `openapi.json` sit in the archive, outside every live repository.

---

## Decisions (locked)

1. **Subdomain-per-service** for the shared platform. No `/api` path prefix.
2. **`my.agience.ai` is the app** (facet). Service names never appear in the app's path space.
3. **The app is config-driven** — each app host declares which service endpoints it talks to. App-host and service-host are independent.
4. **Tenancy is logical by default** (auth token + authority ACL + BYOK). A tenant becomes a *host* only when it is given a **dedicated install**.
5. **Dedicated install = an isolated stack** (own DB, own keys) on a single host under `on.agience.ai`. Isolation is a property of the deployment, not the domain.
6. **Self-host / white-label** = the same stack on the customer's own domain.
7. **Consolidated API reference** is generated from code into a single OpenAPI 3.1 document and served (with try-it) at **`docs.agience.ai/reference`**.
8. **`docs-agience-ai`** is a self-contained site repo (the `www-agience-ai` pattern), sourcing content from the OSS core repo. Which repo that is has not been settled: the one this originally named, `agience-beam`, is archived.

---

## 1. Host grammar

A platform hostname encodes `<service>.<install>`. **Tenant is an auth claim, not a
host** — until a tenant is dedicated, at which point it becomes a nested install
and reuses the same grammar.

| Role | Pattern | Examples | Path space |
|------|---------|----------|------------|
| **App** (facet; branded, deep-linkable) | `[tenant.]my.<install>` | `my.agience.ai`, `subdomain.my.agience.ai` | **owns `/*`** for SPA deep links |
| **Shared services** (multi-tenant backend) | `<service>.<install>` | `origin.agience.ai`, `mantle.agience.ai`, `crystal.agience.ai` | API only — no SPA lives here |
| **Dedicated install** (one isolated stack/tenant) | `<tenant>.on.<install>` | `subdomain.on.agience.ai` | self-contained backend host |

Services: **origin** (identity/OIDC/grants), **mantle** (artifacts, search, grants, the admin
namespace, the `/events` WebSocket, its own `/mcp` with no gateway in front of it, a read-only OCI
`/v2` registry, and read-only smart-HTTP `/git`), **crystal** (the content-type gateway and the
persona host). A secret has no surface of its own on Mantle: its value is artifact content,
encrypted at rest by the envelope and authorized by the light cone, and a type is an artifact too.
These are the three names the code
derives: facet's runtime config resolves `origin.<base>`, `mantle.<base>` and `crystal.<base>` from
its own hostname, and the node scripts derive `CRYSTAL_URI` as `https://crystal.<base>` the same
way. **chorus is not a host** — it supplies the persona modules that crystal's host mounts, and
crystal owns the MCP gateway, the relay socket and the discovery documents. A `chorus.<install>`
name exists in the edge configuration with nothing behind it.

`<install>` is not always a public root domain, and the grammar does not change when it is not. A
workstation node uses `home.agience.ai` as the install: `origin.home.agience.ai` and
`mantle.home.agience.ai` under one `*.home.agience.ai` certificate, in front of services bound to
`127.0.0.1`. The install bundle carries the subdomain label per service kind, so one grammar covers
a laptop and the shared platform alike.

**prism is not a service** and has no host: it is the client/host developer SDK, shipped as `c/`,
`js/` and `py/` legs in one repo and installed as a library. It was also listed here as
"embeddings", which no service provides. Mantle never produces a vector. It accepts one a writer
supplies with the write — validated by `api/vectors.py`, which requires a `space_id` alongside the
numbers because two vectors are only comparable inside one space — and it resolves one already in
its long-term embeddings cache. With neither, the provider returns an empty vector and search is
lexical for that text.

### Persona hosts

Crystal's host router reads one label in front of the install domain and rewrites it to that
persona's path: `lumen.<install>` reaches the same handler as `<install>/lumen`. A deeper label is
not read as a persona, an unmounted name is left alone rather than rewritten, and the apex and
`www` go to the persona named by `CRYSTAL_APEX_PERSONA` (default `aria`). Only a name in the
mounted roster is rewritten, so a service label passes through untouched, and this shape sits
beside `<service>.<install>` under the same wildcard certificate.

### Why subdomain-per-service

- Frees the app host's path space entirely for facet **deep links** (`my.agience.ai/artifact/<id>`).
- Each service owns its host root, because each publishes documents that are properties of the
  origin rather than of a path. Origin serves `/.well-known/openid-configuration`,
  `/.well-known/oauth-authorization-server` (the same document under the name an MCP client probes
  first), `/.well-known/jwks.json`, `/.well-known/agience` and `/.well-known/security.txt`. Mantle
  serves `/.well-known/oauth-protected-resource` (RFC 9728) and `/.well-known/agience-source`.
  Crystal serves `/.well-known/oauth-protected-resource` and `/.well-known/mcp`.
- Independent scaling per service.

Origin builds its discovery document from `AUTHORITY_ISSUER` (falling back to `ORIGIN_URI`), never
from the request. A request-derived value reads `http://` behind a TLS-terminating proxy, which
both downgrades a browser following `authorization_endpoint` and breaks the exact-match comparison
a relying party makes against `iss`. With neither variable set Origin returns 500 rather than
publish an issuer it derived from whoever asked.

### TLS

- `*.agience.ai` covers all shared services.
- `*.my.agience.ai` covers app vanity subdomains.
- One `*.on.agience.ai` wildcard covers **every** dedicated install — no per-tenant cert work.
- All via the EasyDNS DNS-01 path (see [[project_easydns_wildcard_tls]] in working notes).

---

## 2. Config-driven app (the indirection)

facet resolves a small config per app-host declaring its service endpoints. It reads
`window.__AGIENCE_CONFIG__`, set by a `config.js` served beside the app:

```jsonc
{ "originUri":  "https://origin.agience.ai",
  "mantleUri":  "https://mantle.agience.ai",
  "crystalUri": "https://crystal.agience.ai",
  "idpUri":     "https://origin.agience.ai",
  "clientId":   "agience-client" }
```

A deployment that states its URIs is never second-guessed. When the file states nothing, it derives
the sibling names from the app's own hostname — `<facet>.<base>` gives `origin.<base>`,
`mantle.<base>`, `crystal.<base>` — and a bare hostname or an IP literal falls through to loopback
ports for local development instead. A derived URI is a default, not an assertion that the service
is there. `idpUri` is the other half of the indirection: set it and this deployment is a service
provider that renders no login page and deep-links to the authority; leave it empty and this
deployment *is* the platform's login surface.

This makes **"shared tenant" vs "dedicated install" a one-line config flip**, not an
architecture fork:

- `my.agience.ai`, `subdomain.my.agience.ai` → shared `*.agience.ai` services → logical tenant.
- `subdomain.my.agience.ai` → `https://subdomain.on.agience.ai` → dedicated install, same UI.

Because app-hosts and service-hosts are separate hosts, deep-linking is satisfied
automatically and the dedicated backend may path-route internally without any
SPA collision.

---

## 3. Tenancy model

| Scenario | App host | Service hosts | Isolation |
|----------|----------|---------------|-----------|
| **Shared tenant** | `my.agience.ai` or vanity `subdomain.my.agience.ai` | shared `*.agience.ai` | **logical**: Origin token authority claim → Mantle ACL + BYOK |
| **Dedicated tenant** | `subdomain.my.agience.ai` (app config → dedicated backend) | `subdomain.on.agience.ai` (own stack) | **physical**: own DB, MinIO bucket, Origin signing keys, MANTLE master key |
| **Self-host / white-label** | `my.xyz.com` | `mantle.xyz.com`, `origin.xyz.com` … | a separate install — own everything |

**Isolation is a deployment property, not a domain property.** A dedicated stack
under `agience.ai` with its own DB + keys is as isolated as one on a foreign
domain. The only DNS-level care is cookie scope — and the API uses **bearer
tokens** (cookies only for the OAuth handshake), so scope cookies to the host and
there is no cross-tenant leakage.

### Dedicated install — minimal recipe ("doesn't need to be complex")

1. Install a `subdomain` node with **its own key material and its own databases** (own root
   of trust). An install is a directory, not an image: `agience-observe`'s installer builds a
   private virtualenv under `$AGIENCE_HOME` and puts the source checkout, the keys and the
   stores inside it, so a second install is a second `--home` and nothing else. Every service
   binds `127.0.0.1` — origin 8080, mantle 8082, crystal 8085, ember 8091 — and a domain is
   served by putting a proxy in front, never by binding wider.
2. One Caddy site `subdomain.on.agience.ai` under `*.on.agience.ai` → that node
   (single host, path-routed internally — `/auth`→origin, `/`→mantle, etc.; no SPA
   lives there, so paths are fine).
3. Point the `subdomain.my.agience.ai` app config at `https://subdomain.on.agience.ai`.

No install runs under `on.agience.ai` today. The dedicated node that does run — the subdomain
lattice — is reached under the customer's own domain, which is the self-host row of the table
above rather than this recipe.

Decision: dedicated installs use a **single path-routed host**, not per-service
subdomains — one `*.on.agience.ai` cert covers all tenants. The app's config
indirection keeps this off-grammar choice invisible to consumers.

---

## 4. Org / authority tenant boundary (to formalize)

The substrate exists (authority roots, CRUDEASIO grants, light-cone ACL, BYOK).
The one gap behind "shared Mantle is safe" is making the **org/authority boundary**
explicit. The semantics below are decided and not yet built. The substrate they rest on is in the
code — CRUDEASIO grants carrying `can_admin`/`can_update`, light-cone traversal in
`mantle/db/access.py`, per-owner envelope keys — but nothing scopes any of it to an authority root:

- Every artifact resolves (via origin edges) to exactly **one authority root** = its **org/tenant**.
- Origin-issued tokens carry the principal's **org (authority) membership**; a vanity
  app subdomain (`subdomain.my.agience.ai`) may set the **default org context** at login.
- Default **deny across authorities**: a principal in org A cannot read/act on an
  artifact rooted in org B without an **explicit cross-org grant**.
- Light-cone ACL traversal stops at the authority root — propagation never crosses
  an org boundary.
- Platform operators remain able to administer via the platform authority (existing
  `can_admin`/`can_update` on the authority collection).

This is a policy layer over existing machinery, not a rebuild. Tracked as the
follow-on implementation task.

---

## 5. Consolidated API reference

The static, code-defined REST surface (Origin + Mantle) is
consolidated into **one OpenAPI 3.1 document**. Dynamic surfaces — the persona
MCP tools crystal's host mounts — are excluded and documented via MCP discovery, at
`/.well-known/mcp`.

- **Generator**: `build_openapi.py` imports each FastAPI app in an
  isolated subprocess (no DB / no running service; `app.openapi()` only introspects
  routes), dedupes/namespaces colliding schemas, drops housekeeping endpoints
  (`/`, `/status`, `/version`, `/healthz`), and tags by service via `x-tagGroups`. It is archived
  and nothing runs it.
- **Output**: `openapi.json`, to be committed, with a `--check` mode that fails CI on drift. No
  such file is committed and no such gate runs.
- **Per-service servers, templated by install root** so one file serves SaaS,
  self-host, and dedicated installs:

  ```yaml
  servers:
    - url: https://mantle.{install}
      variables: { install: { default: "agience.ai" } }
  ```

  (`install` assumes the subdomain-per-service layout; single-host dedicated
  installs are reached via app config, not this reference.)
- **Per-service `/docs`** (FastAPI Swagger UI) remain live on each service host —
  `mantle.agience.ai/docs`, `origin.agience.ai/docs` — for direct try-it. Mantle declares
  `docs_url="/docs"` and `openapi_url="/openapi.json"` with ReDoc off; Origin takes the FastAPI
  defaults, so it also answers `/redoc`. The app host is deliberately stricter than either: the
  `my.agience.ai` block returns 404 for `/openapi.json`, `/docs` and `/redoc`, and for their
  `/api/`-prefixed variants, because an application host has no business republishing a service's
  contract.
- **Consolidated try-it** is the `docs.agience.ai/reference` page (Swagger UI or
  Scalar over `openapi.json`). Mantle's CORS is `allow_origins=["*"]`, which is safe because every
  authenticated call carries a bearer token. Origin's is not: it is an allowlist, from
  `ORIGIN_ALLOWED_ORIGINS` when set and otherwise derived from `AUTHORITY_ISSUER`, `ORIGIN_URI`,
  `FACET_URI` and `FACET_URIS`. A try-it page on a new host has to be named in that list before it
  can call Origin from a browser.

---

## 6. `docs-agience-ai` repo

Self-contained site repo following the `www-agience-ai` pattern:

```
docs-agience-ai/
  <docusaurus site>
  Dockerfile                 # build + serve the static site
  docker-compose.yml         # join the agience network
  docs.agience.ai.caddy      # docs.agience.ai → this service
```

The repo does not exist, and neither does the content it would source. The markdown and
`openapi.json` are to live in the **OSS core repo** and must not be forked out; the repo that was
named for the job, `agience-beam`, is archived, and no repo has been named in its place. The site
would source them by git submodule or a build-time sync, and the `/reference` page would render
the committed `openapi.json`.

---

## 7. Migration notes / open items

- **Caddy**: the `origin.agience.ai` and `mantle.agience.ai` site blocks exist. The `/mcp` path
  route is gone from `my.agience.ai`; `/auth/*` and `/api/*` are not. The block still sends
  `/auth/*`, `/setup/*`, `/.well-known/*` and `/version` to Origin, and `handle_path /api/*` to
  Mantle with the prefix stripped, so `/api/artifacts` arrives as `/artifacts`. The catch-all
  serves the facet dist, wrapped in `handle` so a bare `try_files` cannot rewrite an API call to
  `index.html` before a proxy matches it.
- **OAuth**: Origin's issuer is `AUTHORITY_ISSUER`, and every URL in the discovery document is
  built from it, so moving Origin to a new host is a change to that variable and to the redirect
  URIs registered at the upstream IdPs. Phase via redirects from the old paths during transition.
- **facet**: the endpoint-config resolution exists. What remains is the deployed `config.js` for
  `my.agience.ai`, which keeps `mantleUri` on `https://my.agience.ai/api` so the browser makes no
  cross-origin request and no CORS is involved. Repointing it at the public names works only after
  giving two services a CORS policy they do not need today.
- **www**: `agience.ai/api/{chat,blog,contact}` is a separate marketing concern — those paths go to
  the site's own BFF, not to any platform service. Out of scope here, but note it as the remaining
  "api" usage to revisit.
- **Org boundary** (§4) is the implementation follow-on.
- **docs-agience-ai** to be created; then promote the worktree site + wire `/reference`.

---

## What is still outstanding

| # | Outstanding | What exists today |
|---|---|---|
| 1 | No install runs under `on.agience.ai`, and no `*.on.agience.ai` certificate or Caddy site exists. The name appears nowhere but this document. | The one dedicated node in service is reached under the customer's own domain, which is the self-host row of §3. |
| 2 | `my.agience.ai` still routes `/auth/*` and `/api/*` through the app host's path space, so decision 1 is not in force on the app the platform actually serves. | The shared services also answer on their own subdomains, and facet's config indirection can be repointed at them without a code change. |
| 3 | The consolidated OpenAPI 3.1 document is not generated. The generator and its output are archived outside the live repositories, no `openapi.json` is committed, and no CI gate checks for drift. | Each service publishes its own schema and Swagger UI at `/openapi.json` and `/docs`. |
| 4 | `docs.agience.ai/reference` does not exist, and neither does the `docs-agience-ai` repo. | The per-service `/docs` pages are the only try-it surface. |
| 5 | The docs site has no content source: `agience-beam`, the repo decision 8 named, is archived, and no repo has been named in its place. | The public prose lives in this repository. |
| 6 | The org/authority boundary of §4 is not implemented: no artifact resolves to an authority root as its tenant, nothing denies across authorities by default, and light-cone traversal does not stop at an authority root. | Authority roots, CRUDEASIO grants with `can_admin`/`can_update`, light-cone traversal in `mantle/db/access.py`, and per-owner envelope keys. |
| 7 | Nothing serves a `chorus.<install>` host; the name is in the edge configuration with no upstream behind it. | Crystal's host mounts the persona MCP endpoints, the relay socket and `/.well-known/mcp`, and routes `<persona>.<install>` to them. |
| 8 | No service produces embedding vectors, so a text arriving with neither a writer-supplied vector nor a cache entry is ranked lexically only. | `WriterSuppliedEmbeddings` for a vector handed in with the write, and the long-term embeddings cache for a text already at rest. |
