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
| [016](experiments/EXP_016_pole_without_velocity.md) | pole 1 no velocity | feedforward vs recurrent (memory) | — | 016 (pending) | **blocked: recurrent eval** |
| 017 | pole 1 no velocity | standard recurrent vs LTC/CfC | — | 017 (pending) | queued behind 016 |
| 018 | pole 2 + velocity | topology-search premium (does it finally pay?) | — | 018 (pending) | queued |
| 019 | pole 2 no velocity | the chaos crown; LTC home turf | — | 019 (pending) | queued behind 016/017 |

## The critical path

Everything memory-related converges on ONE prerequisite: **recurrent network
evaluation** (first-cycle seeding of feedback inputs). It is the single unblock
for EXP-016, 017, and 019. Fix it once, three experiments open. This is the
active engineering task; see `plans/experiments/EXP_016_pole_without_velocity.md`
for the experiment that motivates it and `faber-tweann/ROADMAP.md` item 1 for the
technical gap.

## Templates

- `plans/experiments/EXP_TEMPLATE.md` — the spec/feed/insight shape to copy.

## Related

- [`PLAN_FABER_FOUNDATION.md`](PLAN_FABER_FOUNDATION.md) — the foundation work programme
- [`../insights/INDEX.md`](../insights/INDEX.md) — the insight feed (materialised)
- [`../../faber-tweann/ROADMAP.md`](../../faber-tweann/ROADMAP.md) — DXNN2 conformance gaps
