# 022 — The feedforward T-maze SOLVER is compact (3 neurons), not a deep delay line. 021's "deep chains" were bloated non-solvers.

**Verdict:** confirmed (solver isolated) + refutes 021's reading; mechanism narrowed, not closed · **Date:** 2026-07-21
**Experiment:** EXP_024b (fixed EXP_024)
**Prior art:** `docs/RESEARCH_PRIOR_ART.md` (reservoir/propagation-delay memory).

## The fix

021 could not isolate the solver because agent fitness was never written to the
genotype. Fixed: `population_monitor:persist_agent_fitness` writes each agent's
fitness to its `#agent` record on termination (additive; selection still uses the
transient fitness_acc). Now the highest-fitness agent is the real solver.

## Results

| Condition | solver fitness | neurons | longest path |
|---|---|---|---|
| tmaze delay=2 (memory) | **100** (solved) | **3** | **4** |
| tmaze delay=8 (memory) | 50 (NOT solved) | 26 | 26 |
| xor (no-memory control) | 1.39 (NOT solved) | 1 | 2 |

Raw: `022-raw-tmaze-solver-topology.txt`.

## Finding — the solver is compact

The delay-2 T-maze SOLVER is **3 neurons, path 4** — tiny. This refutes 021's
"evolved feedforward T-maze agents are deep near-linear chains": those 28-deep
chains were the best of a NON-solving run (bloated complexification, fitness ~0),
not the memory solver. With proper solver isolation the picture inverts: the
network that actually holds the cue is small.

So feedforward memory here is **not depth-proportional-to-delay** — not a classic
delay line. A 3-neuron structure holds a 2-cycle cue. The memory must come from
the FIFO input accumulator (signals buffered across cortex cycles) exploited
within a compact structure, not from a long chain of latency stages.

## Why the controls are inconclusive

- **delay=8 did not solve** (fitness 50 = chance). Consistent with EXP_022's
  finding that delay-8 feedforward is unreliable; this run drew the failing half.
  So there is no delay-8 solver to compare depth against.
- **The XOR control was mis-designed.** It used the genotype-default SHALLOW
  tuner, which cannot solve XOR (insight 014), so XOR "best" was a 1-neuron net at
  fitness 1.39, unsolved. A valid no-memory control must use the deep tuner (or a
  no-memory task the shallow tuner can solve). My error.

So the intended memory-vs-bloat comparison (solved-tmaze depth vs solved-xor
depth) did not run. What survives is the single clean fact: the delay-2 solver is
compact.

## What is now known vs open

Known: the feedforward T-maze memory solver is small (3 neurons); the deep-chain
mechanism is out. Open: the EXACT mechanism. A 3-neuron feedforward net with no
neuron state holding a cue for 2 cycles must be using cross-cycle FIFO buffering,
but the precise wiring is unresolved. Resolving it needs INSPECTING the 3-neuron
solver directly — its connections and weights — and TRACING one trial's per-cycle
firing to watch the cue persist. That is the only thing that will close it.

## Next (EXP_024c, cheap)

- Capture the 3-neuron delay-2 solver's full genotype (connections + weights) and
  trace signal flow per cortex cycle.
- Redo the no-memory control with the DEEP tuner so XOR actually solves, then
  compare solved depths.
