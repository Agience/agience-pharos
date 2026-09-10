# Components — the canonical definitions

One line each. These govern separation of concerns; code, docstrings and descriptions are
audited against them. A thing that doesn't fit its component's line lives in the wrong place.

| component | definition |
|---|---|
| **beam** | the measurement of the energy at a cut — the aperture, the property |
| **bundle** | a collection of artifacts |
| **chorus** | operators, by domain (the personas: aria=output, astra=input, iris=networking, ophan=economics, sage=knowledge, seraph=security, lumen=inference; the hands=OPEN) |
| **crystal** | condensation, routing — signal to content type |
| **ember** | an observer unit |
| **facet** | an observation plane |
| **lumen** | inference |
| **mantle** | the lattice — data + backup |
| **origin** | identity, authority |
| **pharos** | documentation — self |
| **prism** | environment adapters (js / py / c) |

**These are component definitions — concepts, not repositories**, which is why `beam`, `bundle`,
`facet` and `lumen` still have rows after ceasing to be repositories. A licence attaches to a
repository rather than to a concept, so the component table carries no licence column: a per-repo
list would be a second copy of one that lives where it is measured, and a second copy drifts. The
principle behind the licensing is stated under "License posture".

Reading the table as physics: an **ember** observes; its **facet** is the plane observations
appear on; **beam** measures the energy crossing any cut; **prisms** adapt the environment
(world ↔ frame); **crystal** condenses signal into typed content and routes it; **chorus**
holds the operators that transform, by domain; **lumen** infers; **mantle** is the lattice the
whole universe persists in; **origin** says who; **bundle** carries collections of artifacts;
**pharos** is the system's knowledge of itself.

---

## License posture — the principle, not the list

**The per-repo facts belong where they are measured, not in the design tree.** The design tree says
why the design is what it is; a licence list copied into it is a duplicate that nothing regenerates,
and it drifts away from the repositories it claims to describe.

Each repository's own `LICENSE` file is the authority. License posture states only the
principle that produces it.

**Two tiers, and one question decides which a thing is in: what does it offer for reuse?**

- **Apache-2.0 — the things meant to spread.** The contract, the store, the adapters, the
  instruments, the gates, the on-ramps. Permissive so that anything can be built on them, open or
  closed, with no copyleft reaching an integrator. A dependency that costs a licence obligation is a
  dependency people route around.
- **AGPL-3.0-only — the platform experience.** Identity and authority, the operators, the observer
  unit, the shipped runtimes. This is the product; the AGPL's §13 network clause is what makes the
  dual-track commercial licence real, and the trademark track sits beside it because the AGPL
  licenses copyright only and never granted the marks.

**Two rules refine it, in this order.**

1. **A repository that ships the runtimes is governed by the runtimes' terms.** This is why
   `agience-observe` moved Apache → AGPL on 2026-08-23: it stopped orchestrating runtimes by image
   reference and started carrying them. Referencing an image by digest is not shipping it.
2. **Whether anything imports it tells you the cost, not the answer.** A package others build on
   must be permissive to be depended on. A package nothing imports is a sink, where copyleft costs
   no one a dependency — but *free* is not the same as *right*, and a sink still gets decided by
   what it offers. `build` and `cloud` are both sinks and both went Apache on 2026-08-25, on the
   gates and the hooks they offer for reuse.

**Neither tier boundary is drawn to maximise the copyleft footprint.** A licence earns its place
by protecting revenue that would otherwise be taken, or by removing friction from adoption that is
wanted. Copyleft that is redundant with copyleft already binding the same deployment protects
nothing and costs adoption — that is the measurement `build` and `cloud` were moved on, recorded in
their `NOTICE` files.

- **Docs:** pharos — CC-BY-4.0.

The rule that makes this consistent end-to-end: **core is Apache** so that both the AGPL platform *and* the Apache instruments can build on the shared engine without a copyleft conflict. (An Apache Mantle cannot sit on an AGPL core.)

## Consolidations applied

- **trust floor → core.** The trust modules (service_identity, authority_trust, key_manager) were `sys.modules` shims to `agience_kit.trust`; they are now the real implementations *in* core, so core is self-contained with no `agience-kit` dependency.
- **kit + host → prism.** One SDK. `agience-host` was already superseded by `agience_kit.host`; both fold into **prism**. Retire `agience-kit` and `agience-host`.
- **gateway → `crystal`.** The content-type gateway, formerly carrying the recycled `agience-prism-py` name, is **`crystal`**: its own top-level repository `agience-crystal`, licensed Apache-2.0, image `agience-crystal`, service identity `crystal`, port 8085. It is not a package inside chorus. The name **prism** is now exclusively the developer SDK (`agience-prism-py/js/c`, import `prism`).
- **grants + keys → Mantle (local-default, pluggable)** — per the approved **Sovereign-Stack** direction ([`../vision/sovereign-stack-and-standalone-mantle.md`](../vision/sovereign-stack-and-standalone-mantle.md)). Authorization is co-located with the data it governs: Mantle owns grants + API-keys + a `KeyCustodian` behind interfaces with a **`local` default** (self-custody, standalone) and an **`origin` backend** for the full platform. Origin stays the standards OIDC provider + STS (RFC 8693 delegation) and hosts the platform-backed grant/key backend. Mantle runs the light-cone and enforces access regardless of backend; **standalone Mantle runs with Origin off.** *This supersedes the earlier "key oracle → origin / reserved threshold-quorum invention" framing. Implementation: the `GrantStore` / `KeyCustodian` local backends are the Phase-1 build. Grants currently still route to Origin via `origin_client`, so this is the doc-of-record posture ahead of that code.*

## Not in the platform set

- **Instruments:** entroptics — the accuracy instrument, Apache-2.0. Separate from the platform components.
- **Packaging:** `agience-observe` — the install bundle (compose, seed-platform, install/dev/backup/restore scripts). **AGPL-3.0-only.**

## Retired

`agience-kit`, `agience-host` (→ prism). The old *gateway* code (→ crystal).

## Domains & routing (subdomain-per-service)

**Subdomains, not paths** — consistent with the locked routing decision. Each service is then independently hostable, movable, scalable, and federatable; per-service TLS and config-driven discovery; clean `agi://` mapping for the sovereign end-state.

| Domain | Serves | Component |
|---|---|---|
| **my**.agience.ai | hosted app (managed SaaS) | facet + full stack |
| **origin**.agience.ai | auth (genesis / root of trust; the key oracle) | origin |
| **mantle**.agience.ai | database | mantle |
| ~~**prism**.agience.ai~~ | Not domained — prism is a zero-dependency SDK contract and ships no embedding service; see "Not domained" | prism |
| **chorus**.agience.ai | ensemble (workers + gateway) | chorus |
| **pharos**.agience.ai | docs | pharos |
| **www**.agience.ai | market interface | www |
| **home**.agience.ai | local / self-host install | seed/home installer |

**The local/home install is the exception:** one domain (`home.agience.ai` → 127.0.0.1) with a localhost Caddy proxy routing paths (`/auth`→origin, `/api`→mantle, …) — a single box shouldn't need per-service DNS/TLS. The seed "plain" installer already does this on `localhost:8080`; `home.` is the TLS'd version. The distributed/hosted deployment uses the per-service subdomains; services find each other by config-driven endpoints such as `ORIGIN_URI=https://origin.agience.ai` and `MANTLE_URI=https://mantle.agience.ai`. Edge routing (Caddy in `agience-infra`) maps each subdomain → its host.

**Not domained:** `prism` (an SDK package, not a hosted service); `facet` (the UI layer of the app domains `my.`/`home.`). Instruments (`entroptics`) get their own domains only if and when hosted.

## Docs & site as artifacts (not static)

- **pharos** = a docs **rendering host** that draws documentation from Mantle **artifacts** (versioned, provenance-carrying, searchable), not static pages. Docs are authored/committed as artifacts; pharos renders them.
- **Private / scoped docs (pharos)** — because docs are artifacts, private sharing is the **native grant architecture**, no special mechanism: public docs are public-granted artifacts; a private/scoped section is a doc collection granted to specific people. Pharos renders per the viewer's Origin session + grants — anonymous sees public only; authenticated-with-a-grant sees their private collections too (the same light-cone / "grants are keys" model as search). *Design note; implement when pharos is built.*
- **www** (market interface) is likewise **artifact-sourced**: blog, offerings, and the operator marketplace render from Mantle artifacts via invoke.
- **Serve statically:** keep artifacts as the source of truth but **cache / SSG at the edge** for public pages — live per-request artifact renders hurt SEO and first-paint. Artifact-driven content, statically served.

Dogfooding: the docs and the market live in the same artifact system they describe.

---

## Composition
- **tekton** (new term): a chorus member - the craftsman; the operators of one domain.
- **chorus**: the choir of tektons: aria, astra, iris, lumen, ophan, sage, seraph.
- **crystal**: condensation + routing, and it **has facets** - the views are crystal's faces.
- **bundle**: contains a crystal, a tekton, and a mantle-shard; fits on any prism (capability-gated).
- **ember**: an observer with bundles and a prism (the capacitor).
- **lumen**: the wisdom/inference tekton in chorus, not a repository or a service of its own.
- **facet**: not a peer component - a crystal's face. No agience-facet repository exists under the workspace root; the render runtime was never split out.

## The crystal defined
crystal = facets (signal conduits) + tektons (condensors) + organons (invoked by condensation),
grown on a lattice (the shard is inside the crystal). bundle = a bunch of crystals. ember =
energized crystals. chorus = the tekton standard library. organon = the invoked instrument
(op.* already abbreviates it). Structure ships, state grows.

## Identity and the universal model
- **origin** = **the IdP**, the identity provider: it issues identities, holds grants, and keys are managed and associated there.
- **universal model**: everything is an **object** with **edges**; an artifact is a content-addressed object (the lattice vertex/edge tables).
- **grants ≠ keys**: grants authorize (reachability over edges); keys = a standard on-platform key-sharing scheme. Distinct instruments.

## The dependency graph

The code dependency graph now matches the component definitions. **Each concern has exactly one home
(no duplication); the foundation is a trio of pure leaves, and a beam is a signal rather than a package.**

```
origin  -> nothing            identity + config + logging      (pure leaf)   [config → origin]
mantle  -> origin             storage: the lattice + content encryption + anchors (shards) + events
prism   -> nothing            environment adapter + the crystal wire-format it grounds   (pure leaf)
facet   -> prism + mantle     terminates a signal (a crystal's face; not a conduit)
tekton  -> prism + mantle     terminates/condenses a signal; leans on prism capabilities
chorus  -> the tektons        the tekton standard library
crystal -> chorus + prism     the IMPARTIAL building block (facets+tektons+organons); developers build
                              crystals of ALL types/sizes — the delivery. Reaches Origin over HTTP,
                              importing nothing from it
ember   -> crystal + prism + mantle + entroptics   a crystal WITH ENERGY (the capacitor; runs the
                              signal on a store, self-identifies). `ember/optics.py` is the one
                              aperture onto entroptics, and the only module that may import it
```

Key rulings realised in code:
- **beam = signal only.** A "beam" is a signal handed to a tekton/facet. The measurement that reads
  it is `ember.optics`, the one aperture onto entroptics, and the numpy-free contract for it is
  `prism.instrument`. There is no `beam` package: everything non-signal lives elsewhere —
  identity, config and logging in **origin**; storage, encryption, anchors, `event_bus` and artifact helpers in **mantle**; the
  crystal wire-format + junction (`activates_on`) → **prism** (a bare host must verify a crystal before
  grounding it); operator/crystal contracts + clients → **crystal**.
- **origin owns identity** and platform config. Identity includes the platform encryption key: origin
  encrypts its own secrets too, and mantle reads it via `mantle => origin`. Each service loads its
  own `.env` at startup (`config.load_env`), never at import.
- **mantle owns storage** and self-identifies to peers over the wire. Services attach by installing a
  prism and attaching a mantle + origin; the hardest case is self-hosting all three.
- **crystal collects/assembles tektons+facets** and is not imported by them: the host injects
  registration, and each tekton declares `ORGANONS = {attr: op}` which the composition adapter wires
  in at assembly. The tekton parts import nothing from crystal; only the composition root
  (`chorus/server.py → crystal.host`) wires the framework.

Verified green across the workspace: origin 136 · beam 173 · crystal ~147 · ember 784 ·
mantle-anchors 21 · chorus 121.
