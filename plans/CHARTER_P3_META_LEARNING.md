# Programme 3 — Meta-learning (Meta-adaptation)

**Status: ACTIVE.** Insights 031-034 done; the plastic-neuron path
(`network_evaluator:evaluate_with_plasticity/3`, faber-tweann 9ea366e) is committed
and validated. **Emerging thesis:** evolved plasticity (memory-by-learning) is a more
robust memory mechanism than a CfC state (memory-by-storage) -- it scales more
gracefully with the memory horizon (032/033) and survives sensor noise where CfC
fails (034). Next phase (mechanism probe, richer rules, harder tasks) planned in
[`PLAN_P3_ROADMAP.md`](PLAN_P3_ROADMAP.md). Corpus: 031-034, continuing.

## The axis

**Meta-adaptation**: the system adapts its own learning, one level above P1
(representation) and P2 (operators). Where P1 varies what the network expresses
and P2 varies how the search moves, P3 varies **how the learner itself changes**,
across scales from within-lifetime to across-run.

Sub-themes it owns:

- **Evolved plasticity** — evolve a local learning rule (Hebbian and variants),
  not the weights; the network then *learns* within its lifetime (Chalmers 1990).
- **Lifetime learning + Baldwin/Lamarckian** — memetic local tuning. Note: DXNN2's
  memetic tuner (insight 014) is already a P3 primitive smuggled into P1.
- **Learned / meta-adapted search parameters** — operator rates, step sizes, and
  substrate parameters adapted from experience.
- **Transfer / curriculum** — carrying structure across a distribution of tasks.

## Two seams to hold (or the axis rots)

- **P2 self-adaptation vs P3 meta-learning.** The (mu,lambda)-ES already
  self-adapts its step size. Keep that as P2's *engine* (a mechanism inside a
  fixed optimizer form). Reserve P3 for *learning to learn*: evolved rules, meta
  across tasks. Otherwise the boundary blurs.
- **EXP-026 sits on the P1/P3 seam.** Co-evolving CfC tau is not "which optimizer"
  (P2); it is adapting the substrate's own timescale parameter, which is
  meta-adaptation. It is run inside P2's ES vector for convenience, but
  conceptually it is P3's first result and the **representation** leg of the DPNV
  three-way fork.

## Central questions

1. Does **evolved plasticity** (a Hebbian rule discovered by evolution) solve the
   memory tasks (T-maze, DPNV) that fixed evolved weights struggle with? A network
   that *learns* the cue in-episode is a different mechanism from one that stores
   it in a recurrent weight.
2. Is co-evolving tau (EXP-026) enough to break the DPNV wall? If yes, the wall was
   representation, and meta-adaptation of substrate parameters is the lever.

## Where to start (the good first experiment)

- **The near-term hook: evolved plasticity for memory.** Instead of evolving
  recurrent weights that store the T-maze cue, evolve a plasticity rule and let
  the network learn the cue online. Testable on the exact T-maze / pole suite
  already built. This gives P3 an immediate first experiment without waiting on a
  task-distribution setup, and it is a direct rival to P1's fixed-weight memory
  variants.
- **EXP-026** (co-evolve tau) is the other entry, already queued in P2.

## The BEAM-native angle

Plastic networks update state per input under message passing, which the
process-per-neuron substrate already does; a learning rule is a local update at
each neuron process, exactly the granularity the engine is built at. Lifetime
learning maps cleanly onto the per-neuron actor.

## Prior art (report as replication, not discovery)

- Chalmers 1990, "The evolution of learning: an experiment in genetic
  connectionism" — evolving the learning rule itself.
- Baldwin effect; Hinton & Nowlan 1987, "How learning can guide evolution."
- Soltoggio, Bullinaria et al., evolved neuromodulated plasticity; Risi & Stanley,
  adaptive HyperNEAT (bridges P6).
- Meta-learning framing: learning to learn (Thrun & Pratt); self-adaptation in ES
  (Beyer & Schwefel) as the P2 boundary case.

## Dependencies

Depends on P2's fixed-topology weight-only arena for the tau experiment; the
plasticity experiment needs a plastic-neuron variant (a local-update handler on
the neuron process, small new infra). Prerequisite for P8 (meta-optimizing an
LLM-augmented loop) and relevant to P9 (lifetime adaptation on real robots).

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map and the DPNV fork
- [`CHARTER_P1_CAPABILITIES.md`](CHARTER_P1_CAPABILITIES.md) — the tau residue
- [`CHARTER_P2_SEARCH_STRATEGIES.md`](CHARTER_P2_SEARCH_STRATEGIES.md) — self-adaptation seam, EXP-026
- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md)
