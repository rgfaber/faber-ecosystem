# EXP_NNN — <one-line title>

The shape every experiment plan copies. `NNN` = the insight number this run is
expected to produce. Delete these italic notes when filling it in.

## Hypothesis

*One or two sentences. A falsifiable prediction, with a direction. State what
would surprise us.*

## Why now / what it unblocks

*Where this sits on the programme critical path. What it depends on, what it
opens.*

## Method

- **Problem:** *which morphology / scape, and its landscape structure*
- **Method path:** *DXNN or domain_sdk*
- **Substrate:** *standard / LTC-CfC*
- **Tuner:** *shallow default / deep crude / both (A-B)*
- **Held constant:** *pop size, generations, survival rate, activation, seeds*
- **The one variable:** *what actually changes between arms*

## Config

*Exact constraint + population_monitor options, enough to reproduce. The runner
is a throwaway `test/integration/*_experiment_tests.erl` invoked via --module;
it is NOT committed to the suite (it would bloat wall-clock). The config here IS
the permanent record.*

*Two harness rules, learned the hard way in EXP_016 (insight 016):*

- ***Bound evaluation cost.** A large "solve" goal (e.g. balance 100000 steps)
  makes solving evaluations so expensive that runs time out. Use a modest goal
  (a few thousand steps) so every evaluation is cheap. A shorter goal still
  discriminates the capability under test.*
- ***Kill a timed-out population.** `population_monitor:terminate/2` is a no-op;
  a run that hits the wall-clock cap leaves its agent phenotypes running in the
  shared VM, poisoning every later run. The runner MUST kill the population (and
  ideally its agent tree) on timeout — and better still, bound the cost so
  timeouts never happen.*

## Kill criterion

*The pre-registered condition under which we stop and call it. Prevents moving
the goalposts after seeing data. E.g. "if the recurrent net does not beat the
feedforward net on the no-velocity variant in N generations across 5 seeds, the
recurrent-eval fix is suspect, not the hypothesis."*

## Feed

*Appended live during the run. The raw `{reason, gen, evals, best_fitness,
wall_ms}` stream, plus the arm summaries. Raw file: `insights/NNN-raw-*.txt`.*

```
(paste run results here as they land)
```

## Insight

*Link to `insights/NNN-*.md` once written. One-line result summary here.*
