# The device mesh — every device is a host

**Nothing here is built.** No device described below exists, no tier has been manufactured, and the
mesh has not been stood up. This is the intended shape of the network once the transports Agience
runs on reach beyond IP. The transports themselves, and how far each has been brought up, are in
[`../design/architecture.md`](../design/architecture.md) §49.

## The premise

IoT devices are first-class `agi://` citizens, each holding a keypair in a hardware secure element
by security tier, with the public key in DNS. The end-to-end flow: a sensor reading becomes a compact
presence announcement, an edge gateway decodes it, resolves the identity, verifies the signature, and
creates or updates a sensor-reading artifact, which a human or agent then curates.

**Every device runs the platform, holds its owner's data, and can relay for its neighbours**, so the
same blind, key-borne link works across every tier.

## The four tiers

| Tier | What it is | Role |
|---|---|---|
| **IOT / Communicator** | a wearable or embedded device — a pin, a watch, a sensor | carries identity and presence at the edge of the mesh: a roaming credential and a sensing endpoint |
| **Handheld** | a wideband device that replaces the phone | a personal host that communicates, senses, and extends the mesh wherever its owner goes |
| **Local** | a home node — the **Orb** | the physical home of your identity and memory: it stores your data, runs on your own keys, and relays for the neighbourhood |
| **High-altitude** | the stratospheric backbone | long-range solar relays that knit local clusters into a wide-area, owner-signed network |

Because every tier is a host and the link is blind and key-borne, **the mesh is ciphertext-only end
to end.**

## The high-altitude backbone

A fleet of high-altitude aircraft, separated by long baselines, forms a wide-aperture array. The
backbone economics rest on the position that a 20 km high-altitude platform is an easier regulatory
path than a low-altitude drone — above aviation law, below space law, with dedicated ITU spectrum for
high-altitude platform stations and a 64-day single-authorization flight already flown.

**What this does not establish.** The regulatory position is a reading of the current rules, not an
authorization anyone holds. The 64-day flight is another operator's, and demonstrates endurance
rather than anything about this network. No link budget, spectral efficiency or bit-error rate is
claimed here, and none has been measured.
