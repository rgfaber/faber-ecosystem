# 030 — Generalisation DPNV overshoots: at wide initial velocity (±1.0 rad/s) BOTH memoryless and CfC fail (0/5, plateau ~350/1000) and memory shows NO advantage. The pole domain is bimodal; no clean memory-pays regime was found. That remains the T-maze (019).

**Verdict:** signed negative + inconclusive (the fair test overshot; memory shows no
measurable advantage at any tested point) · **Date:** 2026-07-21
**Programme:** 1 x 2 (bridge). **Uses:** `sep_cma_es`, `network_evaluator`, `pb_sim`
(with a new initial-condition override). Follows 029.

## The question

029 showed DPNV from one benign start is reactively solvable (memoryless 7/10), so
it did not test memory. 029b makes the task genuinely non-Markov via GENERALISATION
(Gruau F2): balance from a grid of initial conditions that vary the unobserved
VELOCITY. A reactive net sees the same instantaneous position for different
velocities and cannot disambiguate; a memory net should integrate to recover it.

## Method

pb_sim gained an additive, backward-compatible `init/1` override so initial
conditions can be set (validated: `init([])` still gives the benign start; velocity
overrides do not appear in the variant-3 sense, so they are genuinely unobserved).
Fitness = average steps across a 9-condition grid (p1_angle {-0.05,0,0.05} x p1_vel
{-V,0,V}). sep-CMA-ES, lambda 100, 200k evals, n=5. Two arms: memoryless feedforward
vs CfC + co-evolved tau. Solve = average 1000 steps. Raw:
`030-raw-generalisation-memory.txt`.

## Results (V = ±1.0 rad/s)

| Arm | solved | fitness /1000 |
|---|---|---|
| memoryless_gen | 0/5 | 146, 351, 350, 350, 159 |
| cfc_gen (memory) | 0/5 | 354, 356, 347, 349, 351 |

An earlier run at V = ±0.2 (halted before completion) had memoryless SOLVING (1000
across all 9 conditions), i.e. that range was too easy — reactively solvable.

## Finding 1 — the range is bimodal, and I overshot it

At modest velocity (±0.2) the grid is reactively solvable (memoryless generalises).
At wide velocity (±1.0) it is unsolvable by BOTH nets (0/5, both plateau ~350/1000 =
balancing the ~3 low-velocity conditions, failing the fast ones). ±1.0 is past the
memory-recoverable envelope: a CfC net needs a few steps to build its velocity
estimate, but at ±1.0 the pole diverges before it can. So the single widened point
does not discriminate memory; it just makes the task too hard for everything.

## Finding 2 — memory shows NO advantage, even at the hard point

Where it matters most for the hypothesis: at ±1.0, cfc_gen's ceiling (~351, tightly
347-356) is statistically identical to memoryless_gen's (~350). Adding a co-evolved
memory timescale buys nothing measurable. This extends 029's negative: across every
pole setting tested (single-start, modest grid, wide grid) memory matches memoryless
and never beats it.

## Finding 3 — on this harness, memory's value on poles is elusive

Synthesising 029 + 030: the pole domain, even "no velocity", is stubbornly
reactive-friendly (the two different-length poles' instantaneous angles partially
leak velocity; bounded horizons forgive imperfect control). Memory clearly pays only
where the task STRUCTURALLY forces holding information across time with no positional
proxy: the T-maze (019, cfc solves in 1 generation, reactive caps at chance). The
bridge-back answer stands: memory pays where the task needs it, and no pole variant
we have built needs it enough to show it.

## Prior art

Gruau F2 generalisation is the standard hard DPNV metric, and its memory requirement
is real for the FULL wide-range benchmark (hundreds of conditions spanning large
ranges, shorter horizons). Our modest 9-point grid with a 12-neuron CfC at 200k evals
either solves reactively (easy velocities) or defeats both nets (wide velocities),
so it does not reproduce the discriminating regime. See
`../docs/RESEARCH_PRIOR_ART.md`.

## Caveat / next

- **n=5, two velocity points only.** A velocity SWEEP (e.g. ±0.4, ±0.6, ±0.8) might
  find a middle regime where memory solves and memoryless fails. If it exists it is
  narrow, and 029/030 suggest the effect would be small. Deferred as low-value versus
  the clean T-maze result.
- **The batched-population NIF (faber-tweann 23f9dc2) is the enabler** for a proper
  sweep (many conditions x seeds x velocities); this experiment ran per-individual and
  was slow (cfc runs ~40 min each). A sweep should use the batched path.
- The clean memory-pays result remains the T-maze (019).
