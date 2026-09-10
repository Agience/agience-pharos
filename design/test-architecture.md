# Test architecture — beyond "we ran it and it worked"

***The testing standard.** The strategy and the pattern,
realized and proven on the communication plane: `agience-chorus/src/agience_chorus/iris/tests/test_comm_plane.py`
implements the `World(seed)` harness + independent oracle + named-invariant properties + adversarial
roster + multi-node partition→heal. It generalizes to every subsystem (§8).*

*The requirement it answers: a test architecture for the comms plane and the multi-user paths that
amounts to more than "we ran the test and it worked".*

---

## 0. The thesis

"It ran and it worked" checks ONE path with ONE input. That proves almost nothing about a distributed,
multi-user, encrypted system, where the failures live in the *combinations* — the third user, the
out-of-order delivery, the wrong key, the partition. A real test architecture asserts **INVARIANTS** —
properties that must hold for ALL inputs — and proves them over a *generated space* of scenarios, plus a
roster of *adversarial* cases that must fail safely. The unit of confidence is the invariant, not the
example.

## 1. Five levels (each answers a different question)

| level | question | form | example (comms) |
|---|---|---|---|
| **L0 Unit** | does this function compute right? | example asserts | `leaf_id` stable; `seal`/`unseal` round-trips |
| **L1 Property/Invariant** | does a PROPERTY hold for ALL inputs? | generated over a seeded space | isolation, idempotence, order, propagation |
| **L2 Scenario/Integration** | does a real multi-actor story work end-to-end? | a HARNESS builds a world & runs it | 3 users, 2 nested groups, a partition heal |
| **L3 Adversarial** | does the wrong thing FAIL SAFELY? | explicit attacks, assert refusal | wrong key, tampered leaf, replay, partition |
| **L4 System** | does the running fleet behave? | live nodes (deferred) | N embers over real carriers/NAS/S3 |

L0–L3 are deterministic and fast (no network, no time, no randomness that isn't seeded) so they run every
change (the `node-repair`-is-the-gate discipline). L4 is the slow, real-fleet confirmation.

## 2. The INVARIANTS — what "correct" MEANS (stated so tests can assert them)

For the communication plane these are the contract. A test that does not assert one of these is not
testing comms; it is watching it run.

1. **ISOLATION.** A principal NOT in a group's lightcone never obtains a signal sent to that group —
   under any polling order, any number of re-polls. (Enforced by key derivation, not by a filter that
   could be bypassed.)
2. **DELIVERY.** Every principal whose lightcone reaches a target receives every signal sent to it —
   eventually, even across a partition (store-and-forward), and regardless of the order leaves arrive.
3. **IDEMPOTENCE.** Duplicate leaves / re-delivery / re-poll never yield a duplicate received message
   (content-addressed leaf id dedups).
4. **ORDER.** Received messages are ordered deterministically by HLC, independent of arrival order.
5. **PROPAGATION (absorb + propagate).** Addressing a collection reaches its members AND the members of
   any collection it contains (containment propagates the lightcone down); an agent target is terminal.
6. **CARRIER-AGNOSTIC.** Invariants 1–5 hold identically across carriers (in-memory, NAS, S3) — the
   plane never depends on which substrate delivered the leaf.
7. **CONSERVATION / no-leak.** A receiver obtains exactly the set `{signals it was in-lightcone for}` —
   never a signal it wasn't sent, never a decryptable form of a sealed one it can't open.

Each invariant maps to L1 property tests (hold over the generated space) AND L3 adversarial tests (the
negation fails safely).

## 3. The HARNESS — a `World` you build and run

The multi-user test is not many hand-written cases; it is one **harness** parameterized over a *world*:

```
World(seed) builds:
  - P principals (embers/users)
  - G groups with a random CONTAINMENT topology (nested collections) + a key each
  - a random membership relation (principal → groups)
  - a carrier (parameterized: InMemory | NAS | S3-sim) shared by all
Then a script of send(from, to, signal) events (random, seeded).
The ORACLE (computed independently from the world, NOT from the plane) says, for each principal, which
signals it SHOULD receive: exactly the signals to a target its lightcone reaches. The test asserts the
plane's `receive()` equals the oracle — for every principal, over many seeds.
```

The oracle is the load-bearing idea: a *second, independent* computation of the expected result (from
the membership/containment model directly), so the test cannot be fooled by the plane and the test
sharing a bug. Plane-says == oracle-says, for all principals, all seeds.

## 4. Property-based, without a framework

`hypothesis` is not installed, so properties are proven with a **seeded generator**: run the harness over
`range(N)` seeds (the comms suite uses `SEEDS = range(150)`), each producing a distinct random world +
event script, asserting every
invariant. A failure prints the seed → the exact world is reproducible (deterministic from the seed).
This is property-based testing's core (generate → assert invariant → shrink-by-seed) without the dep. If
`hypothesis` lands later, the generators drop straight into `@given` strategies.

## 5. Multi-user AND multi-node

- **Multi-user** = many principals in one world (the harness already is this). The oracle covers who-sees-
  what across all of them.
- **Multi-node** = the SAME logical feed reached through DIFFERENT carriers that reconcile (anti-entropy).
  Model it with two carriers + a `sync(a, b)` (Merkle-diff stand-in): send on node A's carrier, sync,
  assert a principal polling node B's carrier receives identically (DELIVERY + CARRIER-AGNOSTIC across
  nodes). Partition = withhold `sync`; heal = `sync` later; assert eventual delivery (no message lost,
  none duplicated).

## 6. Adversarial roster (L3) — each must fail SAFELY, with the failure named

- **wrong key / non-member** → `receive` yields nothing (ISOLATION); never a decrypt.
- **tampered leaf** (sealed body edited) → does not open / integrity check fails; never silently accepted.
- **replay** (same leaf delivered twice, or an old leaf re-injected) → idempotent; HLC keeps order.
- **spoofed sender** (`frm` forged) → detected at the signature boundary (when signing lands); until
  then, asserted as a known gap, not a silent pass.
- **partition** → messages queue on the carrier and deliver on heal; none lost.
- **out-of-order arrival** → order by HLC is unchanged (ORDER).
- **malformed leaf** → skipped/quarantined, never halts the receiver (total, idempotent apply — the
  `GENESIS-PLAN-OF-RECORD` rock).

## 7. What a green run MEANS (the standard)

A subsystem is "tested" when: every INVARIANT (§2) holds across the generated world-space (§3–4), every
ADVERSARIAL case (§6) fails safely with its failure named, and the multi-node reconciliation (§5)
delivers exactly-once under partition+heal. "The happy path ran" is L0 evidence only — necessary, never
sufficient. This is the bar for comms, and the template for the mesh, the reach, economics, and consent.

## 8. Applying it elsewhere (the template)

- **The signal-native reach** (lumen→sage): invariants = grounding is cited-or-refused, the reach is
  idempotent, silence stays silence; oracle = the corpus's own answer set.
- **Economics/settlement**: invariants = conservation (nothing minted without a residual), no double-
  spend; oracle = the ledger sum.
- **Consent/access**: invariants = a non-grantee never reads; oracle = the lightcone.
- Every subsystem gets: named invariants → an independent oracle → a seeded world harness → an adversarial
  roster. That is the architecture; comms is the worked example.
