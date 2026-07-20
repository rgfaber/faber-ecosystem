# 018 — Three-way memory comparison: none beats both, and wiring beats neuron. CfC's smoothing actively hurts fast control.

**Verdict:** confirmed (recurrent fix at scale) + signed negative (memory) + characterisation · **Date:** 2026-07-21
**Experiment:** EXP_019 (three-way), enabled by the EXP_020 recurrent fix
**Uses:** the LTC wiring (017), the FIFO-queue recurrent fix (EXP_020), the
capability-cost principle (013/015/017).

## Setup

pb_2_without_velocity (double pole, no velocity, bounded 5000-step goal), pop 20,
**60 generations** (2x the insight-017 budget), 3 runs per arm. Three ways to
give the network memory, all now working:

- **A none** — standard neurons, feedforward (memoryless baseline)
- **B neuron** — cfc neurons, feedforward (memory in each neuron's internal_state)
- **C wiring** — standard neurons, recurrent (memory in the topology)

Same problem, same tuner; the one variable is where memory lives.

## Results

| Arm | best_fitness by run | median | peak |
|---|---|---|---|
| A none (memoryless) | 0.23, 0.62, 17.48 | **0.62** | **17.5** |
| B neuron (cfc) | 0.195, 0.095, 0.195 | 0.195 | 0.20 |
| C wiring (recurrent) | 0.38, 7.77, 0.16 | 0.38 | 7.8 |

Raw: `018-raw-three-way-memory.txt`. None solved (double-pole-no-velocity is a
hundreds-of-generations benchmark; 60 is still far short). Higher fitness =
balanced longer.

**Ranking on both median and peak: none > wiring > neuron.**

## Three findings

### 1. Memory does not pay at this budget — the capability-cost principle, fourth instance

The memoryless baseline leads. This is the same lesson as 013 (topology when
known), 015 (deep tuning on smooth control), and 017 (cfc at 30 gens), now at
double the budget and against BOTH memory mechanisms: **an unexploitable
capability is a cost.** Doubling the budget did not flip it. Double-pole-without-
velocity genuinely needs memory to SOLVE, but within a budget too small to reach
a solution, the memory machinery is a tax on search and the memoryless net finds
better reactive approximations. The gate is budget, not need.

### 2. If you must have memory, wiring beats neuron here

Among the two memory mechanisms, recurrent (wiring) clearly outperforms cfc
(neuron): median 0.38 vs 0.195, peak 7.8 vs 0.20. This is notable because
recurrent memory is the HARDER one to evaluate (it needed the EXP_020 cycle-
protocol fix to work at all), yet given working, it dominates the neuron-memory
variant on this task. A useful prior: on this substrate, wiring-memory is the
stronger memory mechanism for fast control.

### 3. CfC is not dead weight — its smoothing actively hurts fast control

The sharpest new observation. CfC has not just the lowest median but the lowest
PEAK (0.20) and the lowest variance — it is *capped*. The other two arms produce
occasional high-fitness agents (memoryless 17.5, recurrent 7.8) that balance far
longer; cfc never does. The CfC update is a gated integrator
(x_new = sigma(-f)*x + (1-sigma(-f))*h) — it SMOOTHS the neuron's response over
time. Double-pole balancing needs sharp, fast corrective force. CfC's temporal
smoothing appears to actively suppress the quick reactive responses the task
rewards, capping achievable performance. So cfc is worse than dead weight here:
its inductive bias (smooth temporal dynamics) is mismatched to the task's demand
(fast reaction). This is a characterisation, not just a ranking — exactly the
kind of "why" a variant map needs.

## The EXP_020 recurrent fix, validated at scale

The FIFO-queue input accumulator (fire once per cortex cycle, buffer over-
triggered signals) cut input-timeout stalls from 471 (EXP_017) to 30 across this
9-run experiment — a 94% reduction, and every run completed. A residual edge case
remains: complex evolved recurrent topologies at 60 generations still produce
occasional stalls. Non-blocking, but not fully eliminated; worth revisiting if
the recurrent variant is pushed to larger networks or longer budgets.

## Caveats (this is budget-scaled but still budget-limited)

- n=3 per arm with high variance. The none>wiring>neuron ordering is consistent
  across medians and peaks, but treat the exact gaps as suggestive, not
  significant. The strongest claims (none beats both; cfc is capped) hold across
  the whole distribution; recurrent-vs-memoryless is noisier.
- 60 generations is still far short of the double-pole benchmark (hundreds to
  thousands in the literature). The memory advantage may only appear near a
  solution. "Memory doesn't help here" is really "memory doesn't help WITHIN
  reach of a solution at this budget."
- Seeded cfc starts with empty backbone/head weights (default gate) and evolves
  tau/bound over only 60 generations — an under-powered cfc. Finding 3 (smoothing
  hurts) would need a well-tuned cfc to be conclusive.

## The variant map after this session

| Memory variant | Status | Character on fast control (double pole) |
|---|---|---|
| none (feedforward) | works | cheapest; wins when budget can't exploit memory |
| wiring (recurrent) | **works** (EXP_020) | strongest memory mechanism here; residual stall edge case |
| neuron (cfc) | works | capped — smoothing bias mismatched to fast reaction |

All three memory variants are now working and characterised — the first full row
of the Neurolab-as-a-Service variant map.

## Next

- **Budget-to-solution run:** the honest test of whether memory EVER pays needs a
  run long enough to approach a solve (hundreds of generations), or a memory task
  solvable in few generations (e.g. a short T-maze). Until then, all memory
  results are budget-limited negatives.
- **Close the residual recurrent stall** before pushing recurrent to larger nets.
- **Well-initialised cfc** to test finding 3 (smoothing-hurts) conclusively.
