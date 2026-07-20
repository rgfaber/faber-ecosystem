# EXP_016 — Single pole WITHOUT velocity: does recurrence beat feedforward?

The first experiment that requires memory, and therefore the one that motivates
fixing recurrent evaluation. Expected to produce insight 016.

## Hypothesis

On `pb_1_without_velocity` (the network observes only cart position and pole
angle, NOT their velocities), a feedforward network cannot solve the task
reliably because the problem is non-Markov: the same observation maps to
different correct actions depending on which way things are moving. A network
with recurrent connections CAN solve it, because it can integrate velocity
across cycles from the position/angle history.

Prediction, with direction: **feedforward solves 0/5 (or plateaus well below the
goal); recurrent solves a clear majority.** If feedforward also solves easily,
either the task is not actually non-Markov as built, or the goal is too short to
require memory — both are findings.

## Why now / what it unblocks

This is the first problem on the programme whose landscape *requires* a
capability we do not yet have (memory). It is the reason to fix recurrent
evaluation. Fixing that unblocks this (016), the LTC comparison (017), and the
double-pole-no-velocity chaos crown (019) simultaneously.

Dependency: **recurrent network evaluation** — currently a recurrent neuron
deadlocks on the first cycle waiting for a feedback signal nothing has produced.
The fix is first-cycle seeding of recurrent inputs (DXNN2 seeds them with an
initial signal). Tracked in `faber-tweann/ROADMAP.md` item 1.

## Method

- **Problem:** `pb_1_without_velocity` (sensor variant 2: `[CPos, PAngle1]`,
  single pole, DPB_Flag=0). Non-Markov: velocity is hidden.
- **Method path:** DXNN (topology + weights; the network must GROW recurrent
  structure).
- **Substrate:** standard neuron (LTC is EXP-017, held for a clean comparison).
- **Tuner:** shallow default (insight 015 says it is right for control; keep it
  fixed so the only variable is feedforward vs recurrent).
- **The one variable:** `connection_architecture` — `feedforward` (control arm,
  expected to fail) vs `recurrent` (expected to solve). Both arms otherwise
  identical.
- **Held constant:** pop 20, up to ~50 generations, survival 0.4, tanh,
  shallow tuner, same goal step count as the with-velocity run for comparability.

## Config

```
#constraint{
    morphology = pb_1_without_velocity,
    connection_architecture = feedforward | recurrent,   %% the one variable
    neural_afs = [tanh],
    neural_pfns = [none],
    neural_aggr_fs = [dot_product]
    %% tuner: constraint defaults (dynamic_random + wsize_proportional)
}
population_monitor: pop 20, max_generations 50, survival_rate 0.4,
    fitness_goal [1.0e12] (backstop; real stop is goal_reached).
```

## Kill criterion

Pre-registered: if the RECURRENT arm cannot solve `pb_1_without_velocity` in 50
generations across 5 seeds while the WITH-velocity control (EXP-015 config)
solves easily, the recurrent-evaluation fix is the suspect, not the hypothesis —
stop and debug the evaluation path, do not conclude "recurrence does not help."
Conversely if FEEDFORWARD solves the no-velocity task, the task is not actually
requiring memory as built (check the sensor variant and goal length) before
claiming anything about recurrence.

## Feed

*(appended live once recurrent evaluation is fixed and the run executes)*

```
(pending)
```

## Insight

*(pending — insights/016-*.md)*
