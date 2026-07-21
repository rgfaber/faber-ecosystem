# 029 — Memory does NOT demonstrably pay on DPNV once the optimizer is strong: a MEMORYLESS feedforward net solves double-pole-no-velocity 7/10 under sep-CMA-ES, statistically indistinguishable from CfC memory (10/10). The optimizer was the lever; DPNV-1000 is too weak a memory test.

**Verdict:** signed negative + correction (the premise that DPNV requires memory is
refuted at this setting) + closes the bridge back to Programme 1 · **Date:** 2026-07-21
**Programme:** 1 x 2 (bridge). **Uses:** `sep_cma_es`, `network_evaluator`
(process-free bridge), `pb_sim`. Follows 028; re-examines 018/024.

## The question

Under the weak GA/ES (018/024), memory looked like a TAX on DPNV because nothing
solved it, so memory only added cost. 028 gave us an optimizer (sep-CMA-ES) that
solves the CfC-memory version 10/10. So we can finally ask Programme 1's blocked
question: **does memory itself pay, once solves are reachable?**

## Method

Hold the optimizer (sep_cma_es) and budget (lambda 100, 400k evals) fixed; vary
the network and whether the task observes velocity. Three arms, n=10:

- **memoryless_markov** — plain feedforward, double pole WITH velocity (sensor
  variant 6, Markov). Positive control: the memoryless setup must work when memory
  is not needed.
- **memoryless_dpnv** — plain feedforward, double pole NO velocity (variant 3). The
  test: can a purely reactive net solve the "non-Markov" task?
- **cfc_dpnv** — CfC + co-evolved tau, NO velocity (replicates 028).

Memoryless = `create_feedforward` (no neuron_meta), so `evaluate_with_state` runs
stateless and reset is a no-op: genuinely reactive, verified (no state leak, no
velocity in variant 3). Raw: `029-raw-memory-pays.txt`.

## Results

| Arm | net | task | solved | evals-to-solve |
|---|---|---|---|---|
| memoryless_markov | feedforward | +velocity (Markov) | 10/10 | 3.5-10.8k |
| memoryless_dpnv | feedforward | no-velocity | **7/10** | 2-17k |
| cfc_dpnv | CfC memory | no-velocity | 10/10 | 6.7-20k |

## Finding 1 — a MEMORYLESS net solves DPNV 7/10 (the premise was wrong)

The expectation (and the textbook framing of DPNV as the canonical non-Markov
benchmark) was that a reactive feedforward net cannot solve it. It solves it 7/10.
So DPNV at THIS setting — a single benign initial condition, 1000-step goal,
survive-only fitness (without_damping) — is substantially **reactively solvable**;
it is only weakly non-Markov. This echoes insight 016 (single-pole-no-velocity was
too weak to prove recurrence beats feedforward); double-pole-no-velocity at 1000
steps is also too weak. Likely mechanism: two poles of different lengths give
partially distinct instantaneous configurations, and a bounded horizon from a
benign start does not force velocity estimation. The literature's memory
requirement bites in the HARDER variants (generalisation from many initial states,
Gruau F2; or 100,000-step horizons), which we did not test.

## Finding 2 — memory does NOT demonstrably pay on DPNV

CfC memory 10/10 vs memoryless 7/10 is **not statistically significant** (Fisher
exact one-tailed p ~ 0.11, two-tailed ~ 0.21). Memory shows a numerical edge but,
at n=10, it cannot be distinguished from the memoryless net. So the honest answer
to "does memory pay once solves are reachable?" is: **not on DPNV-1000.**

## Finding 3 — the optimizer was the DPNV lever, not memory

The dominant factor across the whole arc is the OPTIMIZER. Under the plain ES
(026), CfC-random-tau was 0/10; under sep-CMA-ES, memoryless is 7/10 and CfC is
10/10. Swapping to a covariance-aware optimizer lifts BOTH network types from 0 to
7-10/10. Memory adds at most a modest, non-significant reliability increment on top.
This reframes 028's fork resolution: the optimizer leg was major and real; the
representation/memory leg, while significant under the WEAK optimizer (026: 5/10 vs
0/10), is not necessary once the optimizer is strong, because the task is reactively
solvable.

## Finding 4 — this CONFIRMS Programme 1's thesis (capability = task-match x budget x optimizer)

Memory's payoff is task-dependent and optimizer-dependent, exactly as Programme 1
argued. Memory pays DECISIVELY on the T-maze (019: cfc solves in 1 generation,
reactive caps at chance) because that task genuinely forces holding one bit across
a delay. It does NOT pay on DPNV-1000 because that task does not force memory at
this horizon. The bridge-back answer is not "memory pays" but "memory pays where
the task needs it, and DPNV-1000 does not need it."

## Prior art

Replication, not discovery. DPNV needing memory is the standard framing (Wieland
1991; Gomez & Miikkulainen 1999), but that requirement is for the GENERALISATION /
long-horizon variants; a single-start bounded-horizon survive task is much weaker,
and reactive solvability of easy pole variants is well known. The task-dependence
of memory value is NEAT/NFL (see `../docs/RESEARCH_PRIOR_ART.md`).

## Caveat / next

- **n=10.** The key non-result (7/10 vs 10/10 n.s.) rests on n=10; a larger n could
  reveal a small real memory edge, but it would still be small.
- **EXP-029b (to actually test memory on poles):** a genuinely non-Markov pole
  variant — generalisation fitness (balance from many random initial states) or a
  100,000-step horizon — where a reactive controller must fail. Only there can
  "memory pays" be tested on this domain. Until then, the T-maze (019) is the clean
  memory-pays result.
- The `sep_cma_es` win (028) stands; this insight sharpens what it means (optimizer,
  not memory, cracked DPNV-1000).
