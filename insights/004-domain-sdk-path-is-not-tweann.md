# 004 — The domain_sdk path evolves weights only, not topology

**Verdict:** confirmed · **Date:** 2026-07-20

## Claim

The path that now solves XOR end to end is a genetic algorithm over weights on
a fixed graph. It is not topology-and-weight evolution, which is the entire
point of a TWEANN.

## Evidence

Trained XOR for 30 generations at population 30, then compared the champion's
structure to the declared seed:

```
seed topology declared:   {2,[3],1}
champion layer shapes:    [{3,2},{1,3}]
champion layer count:     2
```

Byte-identical to the seed: 2 inputs → 3 hidden → 1 output, two weight
matrices. No structural change after 30 generations.

`agent_definition:network_topology/0` returns a fixed `{Inputs, Hidden,
Outputs}` and the network is constructed to that shape.
`genome_factory` has `maybe_add_connection/2` but **no `add_neuron`** anywhere
on this path. Topology-growing operators live in faber-tweann's
`topological_mutations.erl`, which the process-per-neuron path uses and this
path does not.

## Significance

This places the working path in the **asterisked column** of Sher's Table 14.1
— CNE, CMA-ES, CoSyNE — the methods that optimise synaptic weights on a
hand-designed topology and explicitly do not evolve structure. Sher's own
argument for TWEANN is that for non-toy problems you cannot predict the
topology in advance.

So the current result is real but narrower than "neuroevolution works here".

## What changes

Do not report XOR as evidence that topology evolution works. It is evidence
that the evaluation loop, the fitness channel and three of five strategies
work.

**Opportunity:** this path is now a clean *control* for Phase 3. Same
environment, same fitness, same harness, one variable changed. If the TWEANN
path later solves XOR in fewer evaluations than fixed-topology weight
evolution, that is direct evidence topology evolution earns its complexity on
this codebase — a more honest first result than chasing Table 14.1 parity.

This argues for landing seeded RNG and an evaluations-to-solve counter
**before** Phase 3, so the control is recorded under the same instrument the
experiment will use.
