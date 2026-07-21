# Programme 1 — Capabilities (Representation)

**Status: CLOSED at insight 024.** The founding programme. Execution plan and
full experiment ledger: [`PLAN_EXPERIMENT_PROGRAMME.md`](PLAN_EXPERIMENT_PROGRAMME.md).
Corpus: insights 013-024.

## The axis

**Representation**: what the phenotype network can express, holding the search
fixed (a truncation-selection GA plus a memetic tuner). The variants measured:

- **Tuner depth** — shallow default (`dynamic_random` + `wsize_proportional`) vs
  deep crude (`all` + `const N`).
- **Substrate** — standard neuron vs LTC/CfC (continuous-time / liquid).
- **Memory mechanism** — none vs memory-in-the-wiring (recurrent) vs
  memory-in-the-neuron (LTC internal state) vs delay-line (FIFO across cycles).
- **Topology** — fixed vs topology-evolving (DXNN path).

## What it established

The arc is **capability value = task-match x budget x optimizer**:

- A capability only pays if the task needs it; an unexploitable capability is a
  pure cost (013 topology ~37x; 017/018 memory as a tax at low budget). This is
  the No Free Lunch corollary and NEAT's complexification result, replicated.
- The right tuner depends on problem structure: shallow wins smooth control, deep
  wins precise-weight problems like XOR (014/015).
- Memory pays decisively where the task is genuinely non-Markov and the budget
  can exploit it (019, T-maze: CfC solves in one generation).
- The feedforward "memory" solver is a short **delay line** (a linear chain),
  while the XOR solver is a convergent DAG (023): mechanism confirmed by wiring.

## Why it closed

Insight 024: the truncation-GA does not solve DPNV at ~165k evaluations (n=10),
with any memory mechanism; fitness is lottery-like with no reliable memory
benefit. That is not a budget wall, it is an **optimizer** wall. The programme had
characterised the capability space as far as its fixed optimizer allowed, and the
third factor (optimizer) took over. Programme 2 opens there.

## Live residue (feeds the DPNV fork)

Two threads from P1 remain scientifically open and now belong to the fork spine:

- **Fixed-tau CfC** (`network_evaluator:create_cfc_feedforward`) randomizes the
  time constant and never evolves it: a memory-representation handicap. Co-evolving
  tau (EXP-026) is the **representation** leg of the DPNV fork, and its natural
  home is P3 (meta-adaptation of the substrate's own parameter).
- **Recurrent residual stalls** — the two-phase reset (EXP_020) reduced but did
  not eliminate synchronous-cycle desync at high generations. Finishing this
  promotes the recurrent variant from fragile to menu-able (product work, per the
  Neurolab-as-a-Service thesis).

## Prior art

See [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md): NFL
(Wolpert & Macready 1997), NEAT complexification (Stanley & Miikkulainen 2002),
memetic balance (Moscato; Neri et al.), DPNV benchmark (Wieland 1991; Gomez &
Miikkulainen 1999), LTC/CfC (Hasani et al. 2021/2022), T-maze (Ziemke & Thieme
2002), TDNN / reservoir memory (Waibel 1989; Jaeger 2001/2002).

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map
- [`PLAN_EXPERIMENT_PROGRAMME.md`](PLAN_EXPERIMENT_PROGRAMME.md) — the execution ledger
- [`CHARTER_P2_SEARCH_STRATEGIES.md`](CHARTER_P2_SEARCH_STRATEGIES.md) — where the wall handed off
- [`CHARTER_P3_META_LEARNING.md`](CHARTER_P3_META_LEARNING.md) — the tau residue's home
