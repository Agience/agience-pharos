# A neurosymbolic path

**State: direction.** This names a frame for work that already exists elsewhere in this repository.
It is not a claim that Agience is a finished neurosymbolic system, and nothing in the codebase is
organised under that term.

The term appears nowhere in the code because it was never a goal. What follows is an argument that
the properties arrived anyway, as a consequence of one constraint — and the artifacts, not this
document, are the evidence.

---

## The claim, stated narrowly

Neurosymbolic architectures usually bolt a symbolic layer onto a trained model, and the trained
model stays in the answer path. Agience is the other arrangement: a **typed substrate where every
artifact carries its own identity and provenance**, a **reasoning surface that structurally cannot
call a model**, and a **model-free instrument** that measures what is resolvable in the signal
between them. No trained weights appear in the answer path at all.

Whether that is a *good* way to build such a system is open. That it is *a* way, and that it runs,
is checkable today.

## Why it emerged

One rule produced all of it, and it is not a rule about models. **Rigidity and arbitrary constants
are the problem** — a number decided somewhere else, on other data, that you cannot inspect. A
trained model is the largest such constant available: millions of parameters fitted to a corpus you
never saw, carrying decisions you cannot re-derive. Keeping one out of the reasoning path is the
same objection as removing a hand-set threshold, at a different scale.

So everything a conventional system would delegate to a model has to be derived from the corpus
instead — and those derivations are what a symbolic layer is.

The rule came from a physical argument rather than a preference.
[`../research/extraction-axiom.md`](../research/extraction-axiom.md) starts from a software
question — *what is the speed of light in a frame-driven inference engine?* — answers it with the
context window, and follows the thread to a bound on how much any observer can extract per
interaction event. The same bound is what Entroptics reads as a **finite aperture**. The optics is
not a metaphor borrowed for the instrument; the instrument and the platform are two readings of one
constraint, which is why their properties line up without anyone arranging it.

## The rule is enforced in code

A principle stated in a README is a slogan. This one shapes the reasoning surface itself, and the
place to check it is where a shortcut would have been easiest to take.

In [`agience_chorus/lumen/server.py`](https://github.com/Agience/agience-chorus):

- **`invoke_llm` raises.** Its registered description reads, in full: *"Raises. No-models rule,
  universal and including BYOK. Grounded operators are the reasoning surface."* Universal, and
  bring-your-own-key does not exempt a caller.
- **`transcribe_artifact` raises** — *"a hosted speech recognizer is a trained model
  (no-models rule)."* Speech recognition is the convenient exception almost every system grants
  itself. It is refused here.
- **`evaluate_output` is a stub, not a shortcut** — *"awaits a grounded verifier (LLM judge barred;
  overlap ≠ quality)."* LLM-as-judge is barred outright, and the tool stays unimplemented rather
  than being filled with a model call.

Each of these is a place where importing an outside judgement would have been easy and invisible.
Calling a model stays entirely legitimate as a deliberate act through a tekton outside the core,
where a caller owns the decision and the provenance records it — the care goes into the route the
system takes on its own.

## The three pieces

**A symbolic substrate — Mantle.** Every artifact carries its identity, version history and
provenance inside itself, so the audit trail is the data structure rather than a log beside it.
Authorization is computed as reachability in a typed graph, and that reachability decides which
decryption keys are issued at all. Types and relations, with enforcement — not a blob store with
metadata attached.

**A reasoning surface that refuses models — Lumen.** Grounded operators over that graph, with the
model path removed as above.

**A model-free reader — Entroptics.** It reads any 2-D signal as a finite optical aperture whose
resolution is fixed by the signal's own entropy, returning a rank, a noise floor and a
reconstruction with nothing fitted, trained or tuned per dataset. It supplies the measurement the
operators reason over, and it has been validated on four unrelated substrates — radio astronomy,
transformer internals, retrieval, and lattice gauge theory.

## What has been measured

The case does not rest on this document.
[`../research/paper-2-knowledge-without-weights.md`](../research/paper-2-knowledge-without-weights.md)
is the formal treatment — grounding, identity and consolidation read off a corpus with no trained
model in the answer path. Four operations done without a model, each replacing something a
conventional pipeline would either train or hand-write:

| conventionally | here |
|---|---|
| A part-of-speech tagger | **Grounding** from a token's own sense counts — no verb list, no interrogative list, no tagger |
| An `is_question` flag | **Act selection** — apply, preimage, or infer — from where the ungrounded hole falls in the ordering |
| A similarity threshold | **Identity** as a conservation residual, tolerance computed from the frame rather than typed |
| A fallback answer | **Refusal** as a consequence: when nothing rises above the floor, nothing comes out |

Propagation ends at a **computed null** — 0.026284, the weight an unrelated pair scores on this
corpus — in place of a hop cap, which would change reach by three orders of magnitude. Identity is
reported at 45.5% against a 68.2% byte-identical-gloss baseline over 600 synsets, disagreeing in
both directions, with full 2×2s and negative controls. The paper reports where the method loses.

[`paper-1-the-instrument.md`](../research/paper-1-the-instrument.md) is the companion: the measuring
instrument the second paper points at a corpus.

## What is missing

Naming this plainly is why the document sits in `vision/` and not `design/`.

- **The term appears nowhere in the code.** No module, interface or test is organised around it,
  and nothing enforces a boundary described as symbolic versus sub-symbolic.
- **No formal correspondence** has been worked out between the grounded operators and the
  categories the neurosymbolic literature uses. The resemblance is argued in prose here, not proved.
- **The inference engine is not finished.** Reading a corpus's live geometry as the thing that
  answers — rather than pre-trained weights — is the goal. The measured results above are the
  substrate for it, not the engine.
- **Nothing here has been reviewed** by anyone working in the field.

## What would settle it

A direction with no test is a slogan, so:

1. A stated correspondence between the grounded operators and a recognised symbolic calculus,
   precise enough to disagree with.
2. A task where a weightless answer path is measured against a trained baseline on the same corpus,
   reported with the losses.
3. An independent reading of `paper-2` by someone who works on neurosymbolic systems.

The third is the one to ask for first, and this document exists partly to make asking possible.

## What it would take

Those three are a research programme, not a sprint, and it is worth being exact about that. The
substrate is built and measured; the engine that reads a corpus's live geometry as the answer path
is not, and it has been the hard part throughout. Stating the stages is how a reader can judge
whether the direction is fundable rather than merely interesting.

**Stage 1 — the correspondence.** Map the grounded operators onto a recognised symbolic calculus,
precisely enough that someone can disagree with a specific line. Theory work against the existing
implementation, producing a paper. It needs no new compute and it is the stage that makes external
review possible, so it comes first.

**Stage 2 — the comparison.** A benchmark where a weightless answer path runs against a trained
baseline on one corpus, with the losses published alongside the wins. This needs an evaluation
harness, a fixed corpus, and enough compute to run the trained side fairly — a comparison that
under-resources the baseline proves nothing. It is the stage that turns an argument into a result.

**Stage 3 — the engine.** Inference from a corpus's live geometry rather than pre-trained weights.
This is the open problem and the reason the work exists; the first two stages are what make it
possible to tell whether it is working. It should not carry a completion date.

**What it needs.** Stages 1 and 2 are roughly a funded year for one researcher, plus compute for the
baseline. Stage 3 is a programme rather than a project and would want at least one collaborator with
standing in the field — not for the implementation, but so the claims are read by someone with
reason to argue.

**The current state is the constraint.** This work is independent and unfunded, and how much of it
gets done is decided by that rather than by the ideas. Anyone who wants to fund it, collaborate on
it, or point it at a corpus they care about should get in touch — see the sponsorship and contact
links on the [Agience organisation](https://github.com/Agience). Independent review of `paper-2`
costs nothing and would be worth more than most of it.
