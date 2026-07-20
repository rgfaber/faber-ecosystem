# 013 — On XOR, topology evolution costs ~37x more than weight-only. As expected.

**Verdict:** confirmed (signed cross-method result) · **Date:** 2026-07-20
**Uses:** the control set up in insight 004, the baseline in 007, the DXNN
solve in 012.

## Claim

The first honest cross-method comparison the programme can make: on XOR, the
DXNN path (evolve topology AND weights, from a minimal net) costs about 37x
more fitness evaluations than the domain_sdk path (evolve weights only, on a
hand-given topology). This is the expected direction, and it is Sher's Table
14.1 lesson reproduced on our own code.

## The numbers

Both count **total fitness evaluations**, every scape call including memetic
tuning attempts. Same problem, same harness, one variable changed.

| Path | What evolves | Start topology | Evaluations to solve XOR (median) |
|---|---|---|---|
| domain_sdk | weights only | given `{2,[3],1}` | **550** (insight 007, n=50) |
| DXNN | topology + weights | minimal, must grow | **20,125** (this session, 5/5) |

DXNN runs: 15256, 20125, 20125, 22220, 25525. All solved. Ratio ≈ **37x**.

## Reading it correctly

This is NOT "topology evolution is bad". It is the known trade-off, stated
precisely:

1. **When the topology is known, evolving it wastes effort.** XOR's solving
   topology is trivial and small; handing it to a weight-only optimiser and
   letting it tune is far cheaper than discovering the structure. Sher's own
   table shows the same: for problems where the topology is known, the
   weight-only methods (CMA-ES*, CoSyNE*, the asterisked ones) win.

2. **The DXNN premium buys generality.** It pays to discover a topology only
   when you do not already know one. XOR is the worst case for it: a known,
   minimal topology. The premium would be justified on a problem where no good
   fixed topology is available, which XOR is not.

So the result confirms the harness measures what it should, and it confirms the
literature's expectation on a problem chosen (originally) as a smoke test, not
as a fair contest for TWEANN.

## A second, sharper finding: our DXNN path is inefficient even for a TWEANN

20,125 evaluations for XOR is HIGH. TWEANN systems typically solve XOR in
hundreds to low thousands. Most of our cost is the memetic tuner: 60 attempts
per agent per generation, perturbing EVERY neuron every attempt. DXNN2 instead
uses `tuning_selection` to perturb a random SUBSET, and `tuning_duration` to
size the effort per agent (roadmap item 2, still missing). Our fixed
all-neuron, 60-attempt tuner is a blunt instrument, and the 37x is inflated by
that inefficiency, not only by the cost of topology search.

So the honest decomposition of the 37x is: part genuine topology-discovery
cost, part our tuner being cruder than DXNN2's. Separating the two requires
implementing tuning_selection/duration and re-measuring. Until then, 37x is an
upper bound on the true topology-search premium, not a clean measurement of it.

## What this does NOT claim

- Nothing about pole balancing (Table 14.1's actual problem) or any task where
  the topology is genuinely unknown. That is where TWEANN is meant to win, and
  we have not measured it.
- No claim that faber is competitive with published systems. 20,125 on XOR
  suggests the opposite until the tuner is fixed.

## Next

- Implement `tuning_selection` (subset perturbation) and `tuning_duration`
  (roadmap 2), then re-measure. This should cut the DXNN evaluation cost
  substantially and give a cleaner topology-search premium.
- Then a problem where the topology is NOT known (pole balancing, Ch 14), which
  is the fair test of whether topology evolution earns its cost.
