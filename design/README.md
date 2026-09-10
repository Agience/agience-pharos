# Design — how it is built

**This tree describes the system that exists.** What is intended but not yet built is in
[`../vision/`](../vision/); what you can use today is in [`../features/`](../features/); the measured
results are in [`../research/`](../research/).

Where a document here and the code disagree, the code is what runs — these describe the design it is
built against, not a record of its current state.

| doc | what it is |
|---|---|
| **[`architecture.md`](architecture.md)** | **The specification of record** — what the parts are, how they talk, and how that is safe. The longest document here and the one to reach for when a design question needs an answer rather than an orientation |
| [`components.md`](components.md) | One line per component, and what each one is for. The fastest way to orient before reading anything else |
| [`substrate-one-field.md`](substrate-one-field.md) | The why beneath the store: content, index, mesh and security as **one field** the aperture relaxes to balance, rather than four systems with a gate on top |
| [`retrieval.md`](retrieval.md) | How retrieval is built, and the rule it turns on — **nothing is chosen; every number is a function of the frame**. Sixteen sections of measured law over the search path |
| [`spine.md`](spine.md) | **The vocabulary the corpus is written in** — three axes, one guarantee, the public names, the word list, and the register each audience is addressed in |
| [`test-architecture.md`](test-architecture.md) | The testing standard: named invariants proven over a seeded world against an independent oracle. *"It ran"* is not a test |
