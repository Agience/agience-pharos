# 15 — Proprietary logic delivered sealed into a customer's own install

**For:** a software vendor whose value is its method — prompts, rules, analytic procedures — and
whose customers will only run software inside their own network.

## The story

A vendor sells an analysis method to organisations that cannot send their data out. The customers
want it on their own hardware; the vendor cannot hand over its method in readable form. Both want the
licence enforced without the software calling home.

## How the platform does it

- The method ships as sealed operators: signed, executed by the platform, never readable by the
  customer's operators.
- The customer's data stays in their install, encrypted under their keys.
- The licence is an artifact the install verifies locally (Ophan's licensing); usage is metered
  locally and reported only as the contract says.

## What it uses today

Signed operator bundles, Ophan's licence, entitlement and usage artifacts, per-principal encryption,
a dependency-severable install.

## To build

- Sealed execution: operator logic the host runs but cannot read.
- An install a stranger completes on their own hardware (roadmap gate G5).

## Checked by

Not yet.
