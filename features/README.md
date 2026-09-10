# Features — what works today

Each document declares its own status in its own first screen; the column below reports what it
declares, it does not impose one.

**Everything here describes a surface that exists.** Designs that are specified but not yet built
are in [`../vision/`](../vision/), and the architecture behind these is in
[`../design/`](../design/).

| doc | what it covers | declares |
|---|---|---|
| [`prism-protocol.md`](prism-protocol.md) | The language-neutral contract every prism leg implements — the source of truth the C, JavaScript and Python legs are written against | Draft |
| [`routing-tenancy-and-docs.md`](routing-tenancy-and-docs.md) | The public routing scheme, the multi-tenancy model, and the API docs surface | Decided |
| [`transport-bound-auth.md`](transport-bound-auth.md) | Token security and transport binding — **the requirements that bind**. Every token this platform issues must satisfy them; a violation is a security defect. The designed hardening above them is in [`../vision/`](../vision/transport-security-roadmap.md) | Reference |
