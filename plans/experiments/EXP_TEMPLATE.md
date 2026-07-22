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
lives at `test/integration/*_experiment_tests.erl` and is invoked via --module.
It is NOT kept in the committed test suite, because running it on every
`rebar3 eunit` would bloat wall-clock.*

***But it is NOT thrown away.** When the run finishes, ARCHIVE the runner into the
corpus beside its raw feed as `insights/NNN-runner-<slug>.erl`, then remove it
from `test/integration/`. The suite stays fast and the record stays complete.*

*Rationale: the raw feed is produced BY the runner. A runner with a bug yields a
feed that is wrong and internally consistent, and no amount of analysis on the
feed alone can recover the error. Two claims (038, 040) were retracted for
confounds of exactly that kind. Without the archived runner, no one, including
us, can ever check whether the code did what the insight says it did.*

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
