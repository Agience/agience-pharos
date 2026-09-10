# ROADMAP — where this actually stands, and what moves it

**Agience · Ikailo Inc. · John Sessford**

> Reader's companion to [`overview.md`](../start/overview.md). The overview says what the
> system *is*; the roadmap says what is **running**, what is **built and not switched on**, and what is
> **designed and not built** — with a completion test for each stage, so progress is something you
> can check rather than something you are told.
>
> Every state is measured, and each carries its qualifier. Where the roadmap and the
> measured record disagree, the record wins.

---

**This is the canonical status page.** Where another document in this corpus describes the state
of something differently — the overview, the course, a design note — this one is correct and the
other is drift. That precedence is about status, not about measurements: for a number, the paper
that reports it is canonical.

---

## 1 · Five states, and why the distinction is the point

Most roadmaps have two states — done and not done — which is how "designed" quietly becomes
"shipped" somewhere between a document and a conversation. This uses five, and nothing is allowed to
skip one.

| | state | means |
|---|---|---|
| ⬢ | **RUNNING** | serving now, measured this month |
| ◧ | **BUILT, NOT ARMED** | exists, tested, and has never been switched on. **The most misread state, and the most common one here** |
| ◇ | **DESIGNED** | specified against the code, not implemented |
| ○ | **RESEARCH** | a genuinely open question |
| | **BLOCKED** | waiting on a decision or an external party, named |

**Built-not-armed is where most of the risk lives.** A tested backup worker that has never
run is not a backup. That distinction is why the five states exist rather than two.

---

## 2 · What is running today

⬢ **A public lattice.** `mantle.agience.ai` serves **6,493,319 records / 30.1 GB**. ⬢ **A public
identity authority.** `origin.agience.ai` answers OIDC discovery and JWKS. ⬢ **A pilot instance** on
a customer's own domain — ◧ *live, and serving a very small store; it is a deployed instance, not yet
a populated one.*

⬢ **The instrument**, published as `entroptics` on PyPI and public at
[github.com/Agience/entroptics](https://github.com/Agience/entroptics). Its governing mathematics
carries a `sorry`-free Lean 4 reduction with the assumptions printed. ◧ *Lean checks the reduction,
not the physical inputs it reduces to.*
⬢ **Retrieval measured against the field's own yardstick** — 2–8× a lexical baseline where a question
shares no words with its answer, with that baseline reproducing published BEIR within 0.016. ◧ *And
where the query is the answer's own text, BM25 wins and should.*

⬢ **Encrypted content at rest**, per principal, bound to the collection it was written for.
The index holds no plaintext terms, and indexing is unconditional — there is no plaintext fallback.

---

## 3 · The five gates

Nothing downstream moves until these do. They are ordered, and each has a test that either passes or
does not.

| # | gate | completion test | state |
|---|---|---|---|
| **G1** | **One promotion path that has actually run** | A gated deploy reaches the public node through the workflow, and the six post-deploy verifications pass — running digest equals requested, the store did not move, size floor held, root answers, unauthenticated write refused, no schema leak | **never completed end to end.** Three independent faults were found and fixed in the tree; none has been proven in a run |
| **G2** | **One green cross-repo CI cycle** | The runner drains its queue and records a green cycle | last recorded cycle is **red** |
| **G3** | **Backup armed** | A restore is performed from the backup, into a scratch node, and the restored store answers a query | ◧ **installed, not armed.** The worker, both installers and the tests exist; the buckets, keys and destinations are operator acts nobody has performed |
| **G4** | **Monitoring armed** | The checks run against the live surface and one deliberate failure pages someone | ◧ **configured, not armed.** And one configured check would fail today: the authority manifest endpoint answers 404 |
| **G5** | **An install a stranger can complete** | Someone who did not write it installs from the published artifact, on their own hardware, unaided | ◧ **the CLI exists and is registered.** The end-to-end path has never been run by anyone who did not build it |

**G1–G2 are engineering. G3–G5 are operator acts that no amount of code closes.** That is the
shape of the near term, and it is why the six stages start where they do.

---

## 4 · Six stages, in dependency order

Each stage names what it unblocks, so the ordering is checkable rather than asserted.

**Stage 1 — Prove the promotion path (G1, G2).**
*Test:* one gated deploy and one green cycle, recorded.
*Unblocks:* everything. Until a change can reach production by a repeatable route, no other stage's
result can be relied upon to stay true.

**Stage 2 — Arm durability (G3, G4).**
*Test:* a restore that answers a query, and one deliberate failure that pages someone.
*Unblocks:* holding customer data with a straight face. Note a specific exposure: the largest
single corpus in the fleet sits outside every declared backup path, because nothing wrote down where
it lives.

**Stage 3 — Fill the pilot.**
*Test:* the customer instance serves their corpus, and they answer a question from it they could not
answer before.
*Unblocks:* the first reference, and the first real load measurement. **The commercial model is
untested until this passes** — the instance is deployed and nearly empty.

**Stage 4 — The install a stranger completes (G5).**
*Test:* Stage 3's test, performed by someone outside the company, from the published artifact.
*Unblocks:* self-host as a real distribution channel rather than a stated property. Open source that
nobody but its author can run is a licence, not a channel.

**Stage 5 — Close the retrieval gap.**
*Test:* every artifact reaches the blind-token index, a node missing the prerequisites refuses the
write rather than indexing in the clear, and the store carries no plaintext search index.
*Unblocks:* *"search over your data without the search layer reading it"* as an unqualified sentence.
**Today that claim is true with a scope attached, and the scope is what this stage removes.**

**Stage 6 — Wire the economy.**
*Test:* two origins exchange, and the rate is *measured* at the meeting rather than configured.
*Unblocks:* the operator ecosystem. ○ *Three of its quantities are deliberately uncomputed, and one —
what counts as verified work — is unsolved by anyone.* This is research, and it is placed last
because it should be.

---

## 5 · What is deliberately not on this roadmap

**Anything with a date.** There are none here, and §6 explains what it would take to add them.

**The physics programme.** It exists, it is published, and it is not part of the commercial sequence.

**A model.** Nothing in the answer path is a trained weight, and no stage in the sequence adds one. This is a
constraint the system is built around rather than a gap it is closing — *we are not training, we are
educating* — and stages 3 and 5 deliver capability that would ordinarily be reached for a model.

---

## 6 · How to put dates on this

Not by estimating the stages. The sequence is real and dates would not be — a roadmap with invented
dates is the same failure as a tuned constant: a number nobody measured, defended because a plan
needs *some* value there. The stages below are ordered by dependency, and each states the test that
closes it; that is what a reader can check.

---

## 7 · Decisions still owed

Named because a roadmap that hides its open decisions is a wish.

| decision | blocks |
|---|---|
| How the existing token is relaunched into the current economics | Stage 6. ○ *Direction settled: today the token is a holding that shows interest; tomorrow it is revamped and transferred into real value in the ecosystem, as the unit that settles verified work. Open: mechanism, timing, jurisdiction. Not open: every existing holder is accounted for in the transfer.* |
| Whether the product surfaces get rebuilt or repaired | Stages 3–5. The web client was migrated once, incompletely, and calls at least one route the store deliberately answers 404 |

---

*The instruments spread; the products monetise. The sequencing rule governs all of it: you do not
launch an economy — you launch products that are ten times better on one axis, share one substrate,
and each earn on their own at small scale.*
