# 016 — Recurrent evaluation works, but single-pole-without-velocity is too weak a test to prove recurrence beats feedforward.

**Verdict:** confirmed (fix validated) + inconclusive (the comparison it was meant
to settle) · **Date:** 2026-07-20
**Experiment:** [EXP_016](../plans/experiments/EXP_016_pole_without_velocity.md)
**Uses:** the recurrent-evaluation fix built this session (faber-tweann f16228f),
the pole scape from 015.

## What was built and what it validated

Recurrent network evaluation now works. A recurrent (feedback) edge deadlocked
on cycle 0 — the target waited for a signal its source had not produced. Fix
(faithful to DXNN2 neuron:prep/1): the source seeds each recurrent target with a
default [0.0] at link time; from cycle 1 the real feedback flows. Proven
deterministically in `recurrent_neuron_tests` — a self-recurrent neuron carries
state across cycles (same input 2.0 -> output 2.0 then 3.0, impossible for a
feedforward net).

The fix also surfaced and fixed a latent bug: bias is stored as a weight-spec
`{W,Delta,LR,Params}`, but `extract_bias` returned the whole tuple, so the
memetic tuner's perturb handler crashed with badarith (`bias + Number`) on any
bias-carrying agent. It had been silently killing such agents in feedforward
runs too.

So the capability is real. The question EXP_016 asked — does recurrence *beat*
feedforward on a memory problem — is a separate matter, and the answer here is
inconclusive.

## The experiment and why it did not settle it

`pb_1_without_velocity`: the network observes only `[CPos, PAngle1]`, not their
velocities. Two arms, identical but for `connection_architecture`. pop 20,
max_gen 50, 5 runs each. Solve = balance to the 100000-step goal.

| Arm | Solved | Fitness by run |
|---|---|---|
| feedforward | 1/5 | 82k, 148k, 168k, 199k, **750k** (the solve) |
| recurrent | 0/5 | 84k, 322k, 394k, **timeout**, **timeout** |

Raw: `016-raw-recurrent-vs-feedforward.txt`.

Two things went wrong, and the pre-registered kill criterion named both in
advance:

1. **The task is too weakly non-Markov.** Feedforward SOLVED it (1/5) and
   balanced well on the rest. The kill criterion said exactly this: "if
   feedforward solves the no-velocity task, it is not requiring memory as
   built." The literature agrees — single-pole-without-velocity is near-Markov
   and solvable by reactive control; the genuine memory benchmark is the DOUBLE
   pole without velocity (Wieland; Gomez & Miikkulainen). We tested the wrong
   pole.

2. **A wall-clock confound.** The 100000-step goal makes a long-balancing
   evaluation expensive. Recurrent nets balance LONGER (see below), so two runs
   blew the 280s per-run cap and registered as 0.0 — and, worse, a timed-out
   run left its population executing in the shared VM, starving the runs after
   it. The recurrent arm's numbers are contaminated by this, not just by
   intrinsic cost.

So EXP_016 cannot claim recurrence wins or loses. It is a null result on the
comparison, by design-fault, not by nature.

## The one real signal

Setting the solve/no-solve metric aside and reading fitness (which accumulates
per balanced step): recurrent's completed runs reached 322k and 394k, both
**above feedforward's best non-solve (199k)**. Recurrence let the network
balance *longer* even when it did not reach the coarse 100k solve bar. That is a
hint in the hypothesised direction — recurrence helps sustained balancing — but
it is a hint from three completed runs against a confounded arm, not a result.

## Method lessons (for the corpus / future EaaS)

Two harness rules, now baked into the experiment template:

- **Bound evaluation cost.** A goal of 100000 steps makes solving evaluations so
  expensive that runs time out. Experiments must use a modest goal (e.g. a few
  thousand steps) so every evaluation is cheap and no run hits the wall-clock
  cap. A shorter goal still discriminates memory from reactivity.
- **Kill a timed-out population.** `population_monitor` does not tear down the
  agent phenotypes it spawned (`terminate/2` is a no-op; in-flight agents on a
  timeout keep running). A run that times out must kill the population AND its
  agent tree, or it poisons every subsequent run in the same VM. Until the
  monitor does this itself, the runner must — and better still, bound the cost
  so timeouts never happen.

## What this changes

- **The decisive memory test is EXP_019: double pole, no velocity.** That is the
  real non-Markov benchmark. Run it with a bounded goal. Single-pole-no-velocity
  is retired as a memory discriminator.
- **Recurrent evaluation is available** for every downstream experiment,
  including the LTC/CfC comparison (EXP_017), which was blocked on it.
- **Deferred, still open:** a flush/reseed handshake between memetic tuning
  attempts. Recurrent nets currently start attempt 2+ from the previous
  attempt's final recurrent state rather than a fresh [0.0]. Non-deadlocking and
  it did not distort this (already-inconclusive) run, but it is not a clean
  reset and should be closed before fine comparisons on memory tasks.
