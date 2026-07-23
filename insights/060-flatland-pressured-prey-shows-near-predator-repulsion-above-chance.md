# 060 — [Flatland rung 2b] The 059 foraging collapse is AT LEAST PARTLY avoidance: the predator-pressured prey shows single-step near-predator REPULSION above chance (reproduced)

Flatland rung 2b (P7 x P4/P5/P6). Adjudicates the mechanism 059 left open: is the predator-driven
foraging collapse AVOIDANCE (adaptive) or DEGRADATION (non-adaptive)? Answer, narrowly and honestly:
AT LEAST PARTLY avoidance -- the pressured prey steps away from a near predator above chance, isolated
from mere predator-sensor exposure, reproduced across two runs. But the claim is deliberately narrow (see
the tripped sanity gate below): it is single-step static-probe REPULSION, not whole-episode fleeing, and
the amount of the collapse it explains is not quantified.

## The finding (the ONE readout that survives)

On an INTERVENTIONAL probe -- freeze the champion prey, place a predator at an ASSIGNED Chebyshev distance
d (no plant present -> predator-only, plant-independent), read the prey's single-step policy move --
`avoid_near` = P(the move increases predator distance at d=2). The analytic chance value is 0.250 (of 4
moves, exactly one increases distance at d=2), and both control types land there.

| champion | avoid_near (run 1 / run 2) |
|---|---|
| NAIVE (059 P0, no predator in training) | 0.250 / 0.250 |
| PROXIMITY-CONTROL (predator sensed, capture DISABLED -> no avoidance incentive) | 0.250 / 0.250 |
| PRESSURED (greedy-hunt, capture kills) | **0.500 / 0.500** |
| PRESSURED-minus-PROXIMITY (bootstrap 95% CI) | +0.250 [0.250,0.500] / +0.250 [0.250,0.625] |

RESOLVED in BOTH runs. The raw per-champion values confirm a genuine upward SHIFT for PRESSURED (run 2:
3x0.25, 7x0.5, 5x0.75, 1x1.0), not a bimodal/discreteness artifact, while both controls sit mostly at
0-0.25. Because the PROXIMITY-control (which sees the predator sensor channel but has no avoidance
incentive, capture disabled) stays at chance, the effect requires the CAPTURE gradient -- it is
avoidance, NOT sensor-channel exposure. A fixed directional bias cannot fake it (any "always move X"
policy averages 0.25 over the four predator directions).

## What this does NOT claim (the gate's required scoping)

- **It is single-step REPULSION, not whole-episode fleeing.** 059's flee-skill (escape-rate vs an ACTIVE
  pursuer over a full episode) was never learned (~0.29). 060's avoid_near is a single-step directional
  move away from a STATICALLY placed predator. These are different measurements: the prey learned static
  near-predator repulsion, NOT episode-level evasion -- which is exactly WHY 059 saw no reactive fleeing.
  060 reconciles with 059; it does not contradict it.
- **The forage readouts are INVALID and dropped.** The pre-registered sanity gate (naive forager's
  `forage_aligned` must be high-and-flat) FAILED: the evolved naive net scored forage_aligned ~0.250 =
  chance on the single-step probe -- the under-converged nets barely express foraging move-by-move (a
  hand-coded greedy-forager scores 1.000, so the probe geometry is correct; the EVOLVED nets are the
  limit). So `forage_conflict` / plant-abandonment cannot be read. No "distance-keeping" or "graded
  plant-abandonment" claim is made -- only avoid_near, which never routes through the broken forage readout.
- **Mediation is not quantified.** avoid_near 0.500 is halfway between chance (0.250) and a competent
  single-step avoider (~1.0). "At least partly avoidance" is the honest ceiling; how much of the 059
  24.5->3.4 foraging collapse this repulsion explains is NOT measured.

**Verdict:** confirmed (near-predator repulsion above chance, reproduced, isolated from sensor exposure) +
scoped-narrow (single-step not episode-level; forage readouts invalid; mediation unquantified) + method
(2 gates: design REDESIGN observational->interventional, claim DO-NOT-SIGN-as-is -> reproduce + descope) ·
**Date:** 2026-07-24
**Programme:** 7 x P5/P6 — Flatland front, rung 2b (mechanism). Prey side only; m=13; scoped to this game.

## Method

Interventional single-step probe on a 9x9 torus (max Chebyshev distance 4): predator placed at assigned
d in {2,3,4}, 7 positions x 4 cardinal directions = 28 configs per (d, geometry); prey energy set mid
(neutral channel); read the argmax policy move. Three champion types, n=16 each, evolved 50 generations
(mu+lambda pop 14) on ecological survival (plants eaten before death). PROXIMITY trains WITH the predator
present but capture DISABLED (predator-sensor exposure, no avoidance incentive) -- the control that
isolates avoidance from sensor-channel exposure. avoid_near read with NO plant (plant-independent).
Two independent runs (fresh randomness); raw per-champion values emitted. Engine `flatland_sim`
(faber-tweann `eaf1081`); process-free `network_evaluator`.

## Caveats (pre-committed honest scope)

- **EA under-convergence** (carried from 059): 50 gen / pop 14; the evolved nets under-express foraging on
  the single-step probe (the tripped forage sanity gate). So avoid_near 0.500 is likely a LOWER BOUND on
  learnable avoidance; a null would be suspect, but a reproduced RESOLVED positive under underpower is
  credible.
- **Single probed distance:** avoid_near is d=2 only (d=4 is the torus max where "further" is undefined,
  d=1 coincides plant with predator). No avoid(d) CURVE is claimed -- just repulsion vs chance at d=2.
- **Percentile bootstrap on 1/28-grid data:** the CI lower bound sits on the grid (0.250); the raw values
  (genuine upward shift, seven grid-steps of median separation) are the real evidence, not the CI endpoint.

## What this buys (and the next rung)

Rung 2b answers 059's open question: the foraging collapse is at least partly ADAPTIVE (near-predator
repulsion), not pure degradation -- an anti-predator behaviour the 058 reactive-flee axis structurally
could not see. The Flatland instrument now reads foraging, reactive-fleeing (058), and near-predator
repulsion (060). The still-missing pieces: a proper avoid(d) GRADIENT and a MEDIATION estimate (how much
of the collapse is avoidance), both blocked by EA under-convergence -- a stronger optimizer / bigger
budget (P2/P5) would unblock them. Rung 3 (many-agent open population) remains the 057-post-mortem locus
for a real arms race.

## Reproduce

`exp060_flatland_avoidance_tests`: `sanity/0` (probe geometry: greedy-forage=1.000; the naive-net forage
gate that trips), `run(#{n=>16, g=>50})` (both runs). Raw feed `060-raw-avoidance.txt` (2 runs +
per-champion avoid_near). Engine `flatland_sim` (faber-tweann `eaf1081`).
