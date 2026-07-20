# 014 — DXNN2's own shallow tuner does NOT solve XOR at a matched budget. The crude tuner's depth was not waste.

**Verdict:** confirmed (signed, and it inverts what 013 expected) · **Date:** 2026-07-20
**Uses:** the tuning_selection / tuning_duration modules built this session
(roadmap item 2), the DXNN solve in 012, the cross-method result in 013.

## What 013 predicted, and why this is a surprise

Insight 013 measured the DXNN path at ~20,125 evaluations to solve XOR and
flagged the number as inflated: our tuner perturbed *every* neuron for a fixed
60 attempts, where DXNN2 uses `tuning_selection` (perturb a random subset) and
`tuning_duration` (size the effort per agent). The stated expectation was that
implementing DXNN2's real tuner would *lower* the cost and let us separate
genuine topology-search premium from our tuner's crudeness.

We built both modules (`tuning_selection`, `tuning_duration`, faithful ports of
DXNN2) and re-measured. The expectation was wrong. DXNN2's own default tuner
does not reduce the cost of solving XOR — **it does not solve XOR at all** in
the same generation budget.

## The experiment

Both arms identical except the tuner. 30 agents, run to exactly 50 generations
(no early stop), 3 runs each. Fitness > ~2.0 means RMSE < 0.5, i.e. solved;
fitness 1.0 is the do-nothing baseline (outputs ~0 for every input).

| Arm | tuning_selection | tuning_duration | Solved | Best fitness at gen 50 | Evals |
|---|---|---|---|---|---|
| Crude | `all` | `const 60` | **3/3** (gen 11-13) | 4.3, 58, 180 (all solved) | ~20,000 |
| DXNN-proper | `dynamic_random` | `wsize_proportional 0.5` (~11 attempts) | **0/3** | **1.199, 1.200, 1.198** | ~15,500 |

Raw data: `014-raw-tuner-comparison.txt`.

The DXNN-proper arm is not "slower" — it is **stuck**. All three runs plateau
at fitness ≈ 1.199, barely above the 1.0 do-nothing baseline, and get there
with *fewer* evaluations than the crude arm spends solving (shallower tuning is
cheaper per generation). Cheaper per generation buys nothing when it never
climbs.

## The mechanism

XOR needs a *precise* weight combination in a very small network. Two ways to
find it:

1. **Deep per-agent hill-climbing** — the crude tuner runs 60 all-neuron
   perturbation attempts per agent per generation. That is enough local search
   for one lifetime to land on the precise weights. Selection then keeps the
   solver. Solves in ~12 generations.

2. **Shallow tuning + generational selection** — DXNN2's default perturbs a
   random subset ~11 times. Each agent barely moves, so the burden of finding
   precise weights falls on selection across generations. On a tiny
   precise-weight problem, generational selection is an inefficient optimiser:
   the population plateaus at a mediocre local region (fitness ~1.2) and cannot
   assemble the exact weights from weak per-agent nudges.

So the 60-attempt depth that 013 called a "blunt instrument inflating the 37x"
was not waste. On XOR it is the thing that solves at all.

## The honest decomposition 013 asked for — and could not get

013 wanted to split the 37x (DXNN vs weight-only domain_sdk) into "real
topology-search cost" vs "our tuner being cruder than DXNN2's." We cannot make
that split by swapping in DXNN2's tuner, because DXNN2's tuner does not produce
a solve to measure. The ~20,125 evaluations stands as the cost *with the tuner
that works*. The shallow tuner has no finite cost to compare — it does not
converge in this budget.

## What this does NOT claim

This is a signed result, but it is scoped. It is **not** "DXNN2's tuner is
broken" and it is **not** "Sher was wrong." DXNN2 solves XOR. Two open threads
keep this honest:

1. **Budget / population.** We fixed 30 agents and 50 generations. DXNN2's own
   XOR runs may use a larger population or many more generations, where slow
   generational selection eventually assembles the weights. We have not matched
   Sher's exact XOR parameters. This measures *our* budget, not DXNN2's.

2. **Possible over-sparse port.** `dynamic_random` selects an age-weighted
   subset and then `random_subset` halves it again. For a 1-3 neuron XOR net
   that can mean perturbing effectively nothing some attempts. If our
   `dynamic_random` is sparser than DXNN2's intent, that alone would explain
   the plateau. Worth diffing against the vendored source before generalising.

So the claim is precise: **at a matched 30-agent / 50-generation budget on XOR,
deep all-neuron tuning solves reliably and DXNN2's shallow default plateaus.**

## Consequence for the platform

- **XOR is the wrong regime to judge a tuner on.** It is a small
  precise-weight problem — exactly where deep per-agent search dominates and
  shallow-subset tuning is starved. Shallow tuning is *designed* for larger
  networks where perturbing every neuron every attempt would be wasteful and
  where topology diversity, not weight precision, is the bottleneck. We will
  only see `dynamic_random` earn its keep on a bigger problem (pole balancing,
  then double-pole). That is the next measurement, and it is where 013's and
  014's tension resolves.

- **Default left as DXNN2-faithful.** The genotype default stays
  `dynamic_random` + `wsize_proportional` (Sher's default). The XOR solve test
  (`xor_evolves_tests`) pins the deep `all` + `const 60` config explicitly and
  documents why. We do not tune the default to the smoke-test problem.

- **The interesting question is now pole balancing.** Prediction to test: on
  pole balancing the ordering flips or narrows — shallow tuning stops being
  starved because the network is larger and topology search matters more. If it
  does not flip, our port is suspect (thread 2 above).
