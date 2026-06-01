# Donald Hebb & Hebbian Theory

The conceptual root of the project. This note covers who Hebb was, his four core
ideas, the stability problem that pure Hebbian learning has (and its fixes), and
how the theory maps onto agent-memory design.

## The man

**Donald Olding Hebb** (1904–1985), Canadian psychologist, called *"the father of
neuropsychology and neural networks."* His 1949 book *The Organization of Behavior:
A Neuropsychological Theory* proposed that behavior and thought are explained by the
**connectivity of neurons** — bridging biology and psychology at a time when
behaviorism dominated. Everything below traces to that one book.

## The four core ideas

### 1. Hebb's postulate — the learning rule

> *"When an axon of cell A is near enough to excite a cell B and repeatedly or
> persistently takes part in firing it, some growth process or metabolic change
> takes place in one or both cells such that A's efficiency, as one of the cells
> firing B, is increased."*

Popularized as **"neurons that fire together wire together."** Note the postulate is
actually **causal and temporally ordered** — A must help *fire* B (A before B), not
merely fire at the same time. That detail prefigured **spike-timing-dependent
plasticity (STDP)** by ~50 years.

Mathematically: `Δw_ij = η · x_i · x_j` — the weight grows with the product of
co-activity.

### 2. Cell assemblies — the memory unit

Neurons that repeatedly co-fire bind into a **cell assembly**: a distributed group
acting as one functional unit encoding a concept, percept, or memory (an **engram**).
Because the connections are mutual, activating *part* re-ignites the *whole* — this is
**pattern completion / auto-association**: retrieval from a partial cue.

### 3. Phase sequences — the structure of thought

One assembly's activity triggers the next, forming a **phase sequence** — a temporally
ordered chain. Hebb's claim: a stream of thought *is* a phase sequence of cell
assemblies igniting one another. Sequence and association, not just storage.

### 4. Reverberation — short-term holding

Before a connection is structurally consolidated, activity loops around the assembly in
**reverberating circuits** — self-sustaining firing that holds a pattern transiently.
This is Hebb's proposal for **short-term memory**, and the repeated looping drives the
slow structural change into **long-term memory** (an early consolidation theory).

## The stability problem (the part that matters most for engineering)

**Pure Hebbian learning is mathematically unstable.** Stronger synapses fire the
postsynaptic neuron more, which strengthens them further — positive feedback. Weights
diverge exponentially (`w(t) ≈ e^(η·α*·t)`); everything saturates and nothing is
selective. The fixes are all about **bounding and forgetting**:

| Fix | What it adds |
|---|---|
| **Oja's rule** | Normalization term — keeps weights bounded; neuron becomes a PCA extractor |
| **BCM theory** | A *sliding threshold* — same input can strengthen *or weaken* a synapse by recent activity |
| **STDP** | Strengthen if pre-before-post, **weaken** if post-before-pre — potentiation *and* depression |
| **Homeostatic plasticity** | Global scaling so total synaptic drive stays in range |

**Lesson:** a credible "fire together wire together" system is half strengthening,
half active suppression/normalization. Unbounded accumulation is a bug, not a feature.

## What "Hebbian" means

Learning that strengthens the connection between two things that activate together —
stored in **connection strengths**, changed by **correlated activity**.

| Hebbian | Not Hebbian |
|---|---|
| Local — uses only the two connected units' activity | Global — needs an external error signal (e.g. backprop) |
| Unsupervised — no "correct answer" needed | Supervised — learns from labeled targets |
| Correlation-driven — "what fires together" | Gradient-driven — "what reduces the loss" |

## Why this is the right root for an agent-memory project

| Hebbian idea | Agent-memory analogue | Seen in the surveyed tools |
|---|---|---|
| Co-activation → association | Co-occurring facts/entities get linked | mem0 entity-linking; cognee & zep build **edges** |
| Cell assembly / engram | A consolidated memory item / node | every tool's "memory" / "observation" |
| Pattern completion from a cue | Retrieval from a partial query | all of them (this is just *recall*) |
| Phase sequence (ordered chaining) | Temporal/causal links between memories | zep/Graphiti's **temporal edges** |
| Reverberation → consolidation | Short-term context promoted to long-term | mem0 session→user; cognee `Improve`; claude-mem compress-at-end |
| Instability → needs forgetting | Memory must prune, dedup, invalidate | mem0 dedup; zep `invalid_at`; cognee `Forget` |

**Deepest takeaway:** Hebb's own model only works once you add the inhibitory/forgetting
half. Flat-log tools (claude-mem, engram) are great at the "wire together" accumulation
but have no real "un-wire." The graph systems (zep especially, with bi-temporal
invalidation) honor the *full* Hebbian picture, where memory balances strengthening
**and** active forgetting.

### Caveat: metaphor vs. mechanism

These tools are **symbolic retrieval systems**, not Hebbian networks. None maintain a
graded `Δw` connection strength that grows with repetition and decays with disuse — the
defining feature of anything truly Hebbian. The literal Hebbian substrate is the LLM's
own (frozen-at-inference) weights; the memory tools exist *because* those weights can't
update online. So Hebb is a strong **design north-star** (associate, sequence,
consolidate, forget) — the moment graded, co-activation-driven weights enter the design
is the moment a tool becomes Hebbian in fact rather than in name.
