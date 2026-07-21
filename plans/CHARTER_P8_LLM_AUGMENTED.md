# Programme 8 — LLM-augmented Evolution (Augmentation)

**Status: HORIZON (coupling).** The coupling of the evolutionary engine to
language and symbolic cognition, and the bridge between faber (neuroevolution) and
Spartan (LLM cognition). The linguistic bookend to P9's sensorimotor one. Corpus:
to be assigned.

> One engine, two couplings. P9 couples evolution to the body; P8 couples it to
> language. Both faber (a DXNN2 rewrite) and Spartan (Gene Sher's agent framework,
> and Raf's hecate-spartan arena work, insights 001-014) descend from the same
> lineage; P8 is where the two research lines meet. See
> [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md).

## The axis

**Augmentation**: an LLM enters the evolutionary loop as an intelligent operator,
holding the underlying search structure recognisable. The LLM can play several
roles, each a distinct experiment:

- **LLM as variation operator** — the LLM proposes mutations or recombinations
  (of topology, of code, of strategy) instead of blind random variation
  (ELM, Promptbreeder, EvoPrompt).
- **LLM as objective / reward author** — the LLM writes or shapes the fitness
  function, or supplies a behavioural descriptor for QD where hand-coding one is
  hard (Eureka; bridges P4).
- **Neuroevolution of LLM-agent structure** — evolve the topology, routing, and
  composition of a Spartan cognitive conglomerate (a population of minds is an
  evolvable object; "auto-spawn committees per fact class" is a selection problem).
- **Neuro-symbolic agents** — evolved small networks as fast reflexive controllers
  under an LLM deliberative layer (dual-process).

## Why this programme exists

It is the single most distinctive story on the map: nobody else is combining
neuroevolution, LLM cognition, a federated BEAM mesh, and a commons. It unifies the
two research lines Raf already runs (faber insights 014-025; Spartan insights
001-014), gives BEAM Campus a capstone narrative, and connects to a live backend
(the Melious provider carousel supplies the LLM; faber supplies the genotype). It
is also where evolution stops being a weight-finder and becomes a search over
*programs and strategies*, which is the frontier the field is currently opening.

## Central questions

1. Does an **LLM variation operator** find solutions in fewer evaluations than
   random mutation on a task where structured proposals should help (code-like or
   compositional genomes)?
2. Can **neuroevolution improve a Spartan conglomerate** (committee topology /
   routing) measurably over a hand-designed one, judged on the arena tasks?
3. Can an LLM supply usable **behavioural descriptors** for a P4 QD run on a task
   where hand-coding the descriptor is the bottleneck?

## Where to start (when activated)

- **P8-a: LLM-as-mutation** on a small symbolic-regression or code genome, versus
  random mutation, matched evaluations. The cleanest isolation of the operator role.
- **P8-b: evolve a Spartan committee** structure against an arena task, reusing the
  hecate-spartan harness and the Melious carousel.

## The BEAM-native angle

The LLM backend (Melious) and the mesh (Macula) are already in Raf's stack; an LLM
call is a mesh RPC, and a population of LLM-augmented agents is a supervised set of
processes each holding a provider handle. The Spartan conglomerate is already a
BEAM system of minds; evolving it is a change of selection, not of substrate.

## Prior art (report as replication, not discovery)

- Lehman, Meyerson et al. 2022/2023, "Evolution through Large Models" (ELM).
- Fernando et al. 2023, Promptbreeder; Guo et al. 2023, EvoPrompt.
- Romera-Paredes et al. 2023, FunSearch (Nature) — LLM + evolutionary search
  discovers new constructions.
- Ma et al. 2023, Eureka (LLM writes reward functions; bridges P4).

## Dependencies

The capstone. You do not LLM-augment a search that cannot yet clear DPNV, so P8
depends on the DPNV fork (P2/P3/P4) delivering a reliable base engine first, plus
the Spartan/Melious integration for the agent-structure arm. A genuine horizon
programme, chartered now so the bridge to Spartan is explicit on the map.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map
- [`CHARTER_P4_OBJECTIVES.md`](CHARTER_P4_OBJECTIVES.md) — LLM-supplied descriptors / rewards
- [`CHARTER_P9_DARS.md`](CHARTER_P9_DARS.md) — the other coupling
- hecate-spartan arena work (insights 001-014) — the LLM-cognition line P8 bridges to
