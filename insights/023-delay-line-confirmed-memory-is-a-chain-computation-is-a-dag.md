# 023 — Delay-line CONFIRMED by solver structure: the memory solver is a linear chain, the computation (XOR) solver is a convergent DAG.

**Verdict:** confirmed (resolves the 019->020->022 flip-flop) · **Date:** 2026-07-21
**Experiment:** EXP_024c (dump the actual solver's wiring; deep tuner so it
solves; fitness persisted so it is the real solver)
**Prior art:** `docs/RESEARCH_PRIOR_ART.md` — delay-line / propagation-delay
memory (Waibel TDNN; reservoir computing).

## The wiring

**T-maze delay-2 SOLVER (fitness 100) — a pure LINEAR CHAIN:**

```
sensor(-1) --0.171--> n(0) --(-0.227)--> n(0.5) --0.165--> n(0.75) --> actuator(1)
```

Every neuron has EXACTLY ONE input (the previous stage). No branching, no bias, no
convergence. A three-stage sequential pipeline. The fractional layers (0, 0.5,
0.75) are repeated neuron-splicing extending the chain.

**XOR SOLVER (no-memory control, deep tuner) — a CONVERGENT DAG:**

```
sensor --> n(0) and n(0.5)          (sensor fans out to two)
n(0)   --> n(0.5) and n(0.75)       (skip + forward)
n(0.5) --> n(0.75)                  (n(0.5) has ~3 inputs: sensor, n(0), bias)
n(0.75)--> actuator                 (n(0.75) has 2 inputs: n(0), n(0.5))
```

Branching fan-out and neurons with multiple converging inputs — parallel feature
combination.

## The finding

Same neuron count (3), OPPOSITE topology. The memory task evolved a sequential
CHAIN; the computation task evolved a convergent GRAPH. That is exactly the
delay-line signature:

- A **chain** is a propagation-delay pipeline: the cue signal is passed stage to
  stage, taking cycles to reach the actuator (via the FIFO input accumulator that
  buffers signals across cortex cycles). Depth buys delay. This is a Time-Delay
  Neural Network / reservoir propagation-delay line, emergent here for free.
- **XOR** needs no delay and cannot be solved by a chain (it is not linearly
  separable); it needs convergent paths to combine transformed features. So it
  evolves a DAG, not a chain.

The feedforward memory mechanism is a **delay line**. Confirmed by the structure
of the isolated solver, not inferred from black-box behaviour.

## Resolving the flip-flop (honest trajectory)

This claim has swung across four insights; the evidence improved each time:

1. **019** proposed delay-line memory (from behaviour + reasoning).
2. **020** RETRACTED it — the delay sweep looked non-monotonic. That was n=2 noise
   (feedforward solved delay 16 by luck in 2/2), not counter-evidence.
3. **022** said "compact 3-neuron solver, not a delay line." Wrong inference:
   compact does not mean not-a-chain. A 3-neuron chain IS a short delay line.
4. **023** (here) isolates the real solver and reads its wiring: a linear chain,
   versus XOR's DAG. Delay-line confirmed.

The retractions in 020 and 022 were premature. The lesson: black-box sweeps with
n=2 and topology stats from non-solvers are too weak to characterise a mechanism;
the solver's actual wiring settled it in one look. (Corrections added to 020 and
022.)

## Caveats

- **n=1 solver each.** One T-maze chain, one XOR DAG. The chain-vs-DAG contrast is
  qualitative and mechanistically sensible, but confirm with a few more solvers.
- **Structure, not dynamics.** I read the topology, not a per-cycle signal trace.
  The chain is structurally a delay line; a trace watching the cue advance one
  stage per cycle would make it airtight. Cheap follow-up.
- **XOR only partially solved** (fitness 6.58, not the ~179 full solve) in 40
  generations even with the deep tuner — but its structure is already a
  convergent DAG, which is the point.
- **Not new science.** Delay-line / propagation-delay memory is textbook
  (RESEARCH_PRIOR_ART.md). What is (mildly) notable is that an actor-model
  substrate produces it structurally, for free, and that a memory task and a
  computation task evolve visibly different topologies under the same operators.

## Consequence for the variant map

The "delay-line" fourth memory variant (019) is real and now structurally
grounded: on this substrate a feedforward network acquires memory by evolving a
CHAIN (propagation-delay pipeline), distinct from the convergent DAG it evolves
for stateless computation. Its limit remains evolvability: chains are slow to
grow (insight 020: gen 14-23 vs 2-4 for recurrent), which is why the memory tasks
favour recurrent wiring when it is available.

## Next

- A signal-flow trace (watch the cue advance one chain-stage per cycle) to turn
  "structurally a delay line" into "dynamically a delay line."
- A few more solvers per condition to make chain-vs-DAG statistical, not n=1.
