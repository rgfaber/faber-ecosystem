# Faber Experiment Programme

The master plan for the faber-neuroevolution research programme: what we are
measuring, in what order, and why. This is the index; each experiment has its
own file under `plans/experiments/`.

## Why this document exists (the corpus intent)

We are practising, by hand, the exact lifecycle a future
Experiment-with-Neuroevolution-as-a-Service (EaaS) would automate. Every
experiment we run leaves three linked artifacts:

1. **Plan** — the spec written BEFORE the run: hypothesis, method axes, config,
   kill criterion. (`plans/experiments/EXP_NNN_*.md`)
2. **Feed** — the live record DURING the run: the stream of per-run results we
   watch (`{reason, generation, evaluations, best_fitness, wall_ms}`). Captured
   as raw data next to the insight (`insights/NNN-raw-*.txt`).
3. **Insight** — the signed finding written AFTER: claim, evidence, verdict,
   honest caveats. (`insights/NNN-*.md`)

This Plan → Feed → Insight triple is the proto-EaaS contract. It maps directly
onto the CQRS shape we already build with:

| Hand practice now | EaaS later |
|---|---|
| Experiment plan | ExperimentSpec (command) |
| Run result stream (feed) | Progress feed = domain events (per-generation slips) |
| Signed insight | Integration fact published to the mesh |
| `insights/INDEX.md` | Query read-model of the insight feed |

We are NOT building the services yet. We are accumulating the corpus that tells
us what those services must carry. Numbering: an experiment `EXP_NNN` is named
for the insight number it is expected to produce, so the corpus stays coherent.

See insight 015's "Consequences" and the L1/L2/L3 feed design (session
2026-07-20) for the feed-UX layering.

## Operational variants: the Neurolab-as-a-Service thesis

What this programme is really building is a **map of operational variants** — the
knobs of the neuroevolution design space and how they interact with problem
structure. Each axis below is a variant; each signed insight is a cell in the
"which variant wins where, and at what cost" matrix.

The strategic point (raised 2026-07-20): that map IS the product. A
Neurolab-as-a-Service would let a user bring a problem and ask "which
mechanism/substrate/tuner fits?" — and answer it cheaply because it holds
**priors over the variant space** rather than searching from scratch. Our
by-hand corpus is the seed of those priors.

A finding is not only "variant X beat variant Y on problem P." It is also the
*characterisation* of a variant: its robustness, its cost, its failure modes.
Example (this session): memory-in-the-wiring (recurrent) vs memory-in-the-neuron
(LTC internal_state). On a process-per-neuron substrate the recurrent variant is
fragile (synchronous-cycle desync) while the LTC variant is robust and cheap.
That characterisation is exactly what a NLaaS user needs before choosing a
memory mechanism — and it means **every working, characterised variant we add
enriches the offering**. The recurrent deep-fix is therefore product work, not
just bug work: it promotes a broken variant to a working, menu-able one.

## The measurement axes

An experiment is a point in this space:

- **Problem** — XOR, pole (1 or 2 poles, with or without velocity), later
  T-maze, foraging, forex. Chosen for its LANDSCAPE STRUCTURE, not folklore
  difficulty (insight 015: cost tracks structure, not intuition).
- **Method path** — DXNN (process-per-neuron, topology + weights) vs domain_sdk
  (vectorised, weights only, fixed topology). Note: domain_sdk x topology-search
  is an empty cell by definition.
- **Substrate** — standard neuron vs LTC/CfC (continuous-time recurrent). LTC x
  static-problem is an empty cell: LTC only earns its keep where memory matters.
- **Tuner** — shallow default (`dynamic_random` + `wsize_proportional`) vs deep
  crude (`all` + `const N`). Insight 015: the right tuner depends on problem
  structure; this is a first-class axis, not a constant.

The discipline (insight 013): match capability to problem, or the cell is null.
A problem that does not need a capability cannot measure it.

## Status

| EXP | Problem | Axis tested | Feed | Insight | Status |
|---|---|---|---|---|---|
| — | XOR | DXNN vs domain_sdk (topology cost) | — | [013](../insights/013-topology-evolution-costs-37x-on-a-known-topology-problem.md) | done |
| — | XOR | shallow vs deep tuner | [014 raw](../insights/014-raw-tuner-comparison.txt) | [014](../insights/014-shallow-tuning-fails-xor.md) | done |
| — | pole 1 + velocity | shallow vs deep tuner | [015 raw](../insights/015-raw-pole-tuner-comparison.txt) | [015](../insights/015-tuner-fitness-depends-on-problem-structure.md) | done |
| [016](experiments/EXP_016_pole_without_velocity.md) | pole 1 no velocity | feedforward vs recurrent (memory) | [016 raw](../insights/016-raw-recurrent-vs-feedforward.txt) | [016](../insights/016-recurrence-works-but-single-pole-is-a-weak-memory-test.md) | done — **fix validated, comparison inconclusive** (task too weakly non-Markov) |
| [017](../insights/017-ltc-wired-memory-is-dead-weight-at-this-budget.md) | pole 2 no velocity | LTC/CfC vs memoryless feedforward | [017 raw](../insights/017-raw-ltc-vs-memoryless.txt) | [017](../insights/017-ltc-wired-memory-is-dead-weight-at-this-budget.md) | done — **LTC wired in; memory HURT at 30 gens** (capability-cost, 3rd instance) |
| 020 | recurrent cycle protocol | FIFO-queue accumulator fixes cycle desync | (in commit) | folded into [018](../insights/018-three-way-memory-none-beats-both-wiring-beats-neuron.md) | **done — recurrent variant now works** (471→30 stalls; residual edge case) |
| [019](../insights/018-three-way-memory-none-beats-both-wiring-beats-neuron.md) | pole 2 no velocity | none vs neuron vs wiring memory, 60 gens | [018 raw](../insights/018-raw-three-way-memory.txt) | [018](../insights/018-three-way-memory-none-beats-both-wiring-beats-neuron.md) | done — **none > wiring > neuron; memory still a tax at 60 gens** |
| 021 | pole 2 no velocity OR short T-maze | budget-to-solution: does memory EVER pay? (100s of gens / solvable memory task) | — | 021 (pending) | **next: the only honest test of memory's value** |
| 018 | pole 2 + velocity | topology-search premium (does it finally pay?) | — | 018 (pending) | queued |

## The critical path

Recurrent network evaluation (first-cycle seeding) is DONE (insight 016,
faber-tweann f16228f) and validated. It unblocks the memory experiments.

EXP_016 taught that single-pole-without-velocity is too weakly non-Markov to
discriminate memory (feedforward solved it). The decisive memory test is
**EXP_019: double pole, no velocity** — the genuine non-Markov benchmark. Two
harness prerequisites before running it (both from insight 016, now in the
template): bound the evaluation cost with a modest goal, and kill timed-out
populations. `pb_sim` already supports a goal via the actuator's third
parameter; a bounded-goal morphology variant is the small piece of infra EXP_019
needs.

## Templates

- `plans/experiments/EXP_TEMPLATE.md` — the spec/feed/insight shape to copy.

## Related

- [`PLAN_FABER_FOUNDATION.md`](PLAN_FABER_FOUNDATION.md) — the foundation work programme
- [`../insights/INDEX.md`](../insights/INDEX.md) — the insight feed (materialised)
- [`../../faber-tweann/ROADMAP.md`](../../faber-tweann/ROADMAP.md) — DXNN2 conformance gaps
