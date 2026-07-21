# Faber Research Programmes — Master Charter

The index and mandate for the faber neuroevolution research agenda. Nine
programmes, each a charter of its own. This document is the map; each
`CHARTER_PN_*.md` is one territory.

Faber is a BEAM-native neuroevolution engine (an Erlang rewrite of Gene Sher's
DXNN2) and the disciplined research corpus around it (`insights/`, signed and
monotonically numbered). The programmes below are offered to the **BEAM Campus**
research commons as its neuroevolution track; `beam-campus-net` surfaces them as
the public face. Codeberg is canonical.

---

## The map: one engine, two couplings

The seven core programmes (P1-P7) are the **evolutionary engine**: the seven
near-orthogonal axes of the neuroevolution design space. P8 and P9 are its two
**couplings**, to language and to the body:

```
        P9                    P1 .. P7                    P8
  DARS / Physical AI    the evolutionary engine     LLM-augmented
  (the body, at scale)     seven design axes          (language)
  distributed robots,   representation, search,     LLM operators,
  swarms, embodied      meta, objectives, scale,    Spartan bridge,
  evolution, sim-to-real  encoding, coevolution     neuro-symbolic
```

Sensorimotor ground on one side, symbolic and linguistic cognition on the other,
the evolutionary engine in the middle. That is the symbol-grounding problem
rendered as a research map, and it is the identity that distinguishes a BEAM
Campus neuroevolution agenda from a generic one.

| # | Programme | Axis (what it varies, holding the rest fixed) | Status |
|---|-----------|-----------------------------------------------|--------|
| [P1](CHARTER_P1_CAPABILITIES.md) | Capabilities | **Representation**: what the phenotype can express | CLOSED @024 |
| [P2](CHARTER_P2_SEARCH_STRATEGIES.md) | Search Strategies | **Operators**: variation + selection (the optimizer) | **ACTIVE** |
| [P3](CHARTER_P3_META_LEARNING.md) | Meta-learning | **Meta-adaptation**: learn to learn, evolved plasticity | NEXT |
| [P4](CHARTER_P4_OBJECTIVES.md) | Objectives | **Selection pressure**: what fitness rewards (QD, novelty) | OPEN |
| [P5](CHARTER_P5_SCALE_SUBSTRATE.md) | Scale / Substrate | **Distribution of the search**: fleet, mesh, federated evolution | OPEN |
| [P6](CHARTER_P6_ENCODING_DEVELOPMENT.md) | Encoding / Development | **Genotype→phenotype map**: direct vs indirect | OPEN |
| [P7](CHARTER_P7_COEVOLUTION.md) | Coevolution / Self-play | **Interaction**: fitness from contest; open-endedness | OPEN (horizon) |
| [P8](CHARTER_P8_LLM_AUGMENTED.md) | LLM-augmented Evolution | **Augmentation**: LLM in the loop; the Spartan bridge | HORIZON (coupling) |
| [P9](CHARTER_P9_DARS.md) | DARS / Physical AI | **Embodiment at scale**: distributed robots, swarms, sim-to-real | HORIZON (coupling) |

The axes are chosen to be near-orthogonal so insights compose: a finding on one
axis holds while the others vary. P6 is separate from P1 because the same
phenotype can be reached by a direct or an indirect encoding (encoding changes
scalability and regularity, not expressiveness). P7 is separate from P4 because
P4 changes the pressure on a fixed problem while P7 makes the problem itself move.
P9 is separate from P5 because P5 distributes the **search** (populations across
compute nodes) while P9 distributes the **robots** (many bodies acting in the
world); they compose (federated evolution of swarm controllers).

---

## Map versus execution (the discipline)

The charters are the **public map**; execution stays disciplined to **one active
front at a time**. Right now that front is P2. Every other charter carries an
honest status: a dormant charter states plainly that it is not started and names
a concrete first experiment, so the agenda invites contributors without ever
pretending to momentum it does not have.

- **CLOSED** — arc complete, corpus written (P1).
- **ACTIVE** — the current execution front (P2).
- **NEXT** — chartered, first experiment defined, starts when the active front closes (P3).
- **OPEN** — chartered, has a good first experiment and the tested harness, not started (P4, P5, P6, P7).
- **HORIZON** — chartered, but depends on a prerequisite that does not yet exist (P8 and P9, the two couplings). Named so the dependency is explicit.

---

## The current spine: the DPNV three-way fork

Programmes 1 and 2 both hit the **same** wall: neither the truncation-GA (024)
nor a self-adaptive (mu,lambda)-ES (025) solves double-pole-without-velocity
(DPNV), the canonical non-Markov benchmark. That single wall has three competing
explanations, one per axis, and the near-term work is a clean fork that resolves
which:

| Hypothesis | Axis | Programme | Decisive experiment | Result |
|------------|------|-----------|---------------------|--------|
| **Representation** — fixed-random tau cripples the memory timescale | substrate | P3 (via P1 residue) | EXP-026: co-evolve tau with weights | **weak** (026: real under plain ES 5/10 vs 0/10; but 029: memoryless net solves DPNV 7/10 under sep-CMA, n.s. vs CfC 10/10) |
| **Optimizer** — GA/ES too weak for the landscape | operators | P2 | EXP-028: sep-CMA-ES | **THE lever** (028: 0->6-7/10 regardless of memory; +tau 10/10) |
| **Deception** — the fitness gradient misleads | objective | P4 | novelty / QD run on DPNV | **rejected** for DPNV (028 reaches the objective directly) |

**Fork resolved (EXP-028/029).** The dominant leg was the **OPTIMIZER**: sep-CMA-ES
(diagonal; no correlations, no eig NIF) lifts DPNV from 0/10 to 7-10/10 whether or
not the net has memory. EXP-029 then showed a MEMORYLESS feedforward net solves
DPNV-1000 7/10 (n.s. vs CfC 10/10), so the representation/memory leg is weak here:
DPNV-1000 is too weak a memory test (reactively solvable), and deception was ruled
out. Memory pays where the task genuinely needs it (T-maze, insight 019), not on
DPNV-1000. The clean memory test on poles needs a harder non-Markov variant (029b).

---

## The corpus contract

Every programme feeds one unified corpus. No fragmentation, continuous numbering.

- **Plan → Feed → Insight** (see `PLAN_EXPERIMENT_PROGRAMME.md`): the spec before,
  the raw run-stream during, the signed finding after. This triple is the
  proto-EaaS (Neurolab-as-a-Service) contract.
- **Signed insights** are monotonic and never renumbered; negatives are
  first-class; evidence is `file:line` or a measurement.
- **Prior-art grounding** (`docs/RESEARCH_PRIOR_ART.md`): every principle carries a
  pointer to its established source. We report findings as reproducible
  confirmation on the DXNN2 / Erlang substrate, never as discovery. Independent
  replication validates the instrument; that is the honest and sufficient framing.

---

## For BEAM Campus and contributors

This is a commons. A contributor picks an OPEN charter, reads its "Where to start"
section, runs the named first experiment against the already-tested harness, and
writes a signed insight into the shared corpus. The harness (scapes,
morphologies, the Plan→Feed→Insight loop) is built and green; the open problems
are real and small enough to start on in days, not months.

Why BEAM specifically, for every programme: process-per-neuron is Gene Sher's
original insight and native only here; federated evolution across independently
owned nodes (P5) is tractable only on a mesh substrate like Macula; always-on,
fault-tolerant, open-ended runs (P7) suit BEAM supervision; and the small evolved
networks this engine produces are exactly the ones that deploy to fleets of edge
robots (P9) where large models cannot. The commons ethos is not decoration: federated,
non-extractive evolution is a research direction Big-Tech ML structurally will
not take.

---

## Related

- [`../insights/INDEX.md`](../insights/INDEX.md) — the signed insight corpus (013-025+)
- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md) — where each finding sits in the literature
- [`PLAN_EXPERIMENT_PROGRAMME.md`](PLAN_EXPERIMENT_PROGRAMME.md) — Programme 1 execution plan (Capabilities)
- [`PLAN_SEARCH_STRATEGIES.md`](PLAN_SEARCH_STRATEGIES.md) — Programme 2 execution plan (Search Strategies)
- [`PLAN_FABER_FOUNDATION.md`](PLAN_FABER_FOUNDATION.md) — the foundation work programme
