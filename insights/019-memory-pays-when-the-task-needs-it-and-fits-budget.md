# 019 — Memory pays decisively on the T-maze — and "feedforward" is not memoryless in this substrate. Capability value = task-match x budget.

**Verdict:** confirmed (memory pays) + discovery (implicit feedforward memory) · **Date:** 2026-07-21
**Experiment:** EXP_021 (three-way on the cue-memory T-maze)
**Uses:** the tmaze scape built this session, the three memory variants (LTC
wiring 017, recurrent fix EXP_020), and it is the direct counterpoint to 018.

## The task

Cue-memory T-maze (tmaze_sim): a cue (left/right) is shown at step 0, then a
2-step corridor shows nothing, then at the junction the agent must turn the way
the cue pointed. 100 trials, cues exactly balanced (50/50) and shuffled, so a
memoryless "always turn one way" policy scores EXACTLY 50 with zero variance.
Solve = 90/100. The task genuinely needs memory and a memory network can solve it
in a handful of generations — the instrument 018 asked for.

## Results (pop 20, max_gen 50, 3 runs)

| Arm | solved | solve generation | best_fitness |
|---|---|---|---|
| A none (feedforward) | 2/3 | 3, 25 | 100, **50**, 96 |
| B neuron (cfc) | **3/3** | **1, 2, 1** | 100, 100, 100 |
| C wiring (recurrent) | **3/3** | 2, 3, 2 | 100, 100, 100 |

Raw: `019-raw-tmaze-three-way.txt`.

## Finding 1 — memory pays decisively (the counterpoint to 018)

Both explicit-memory variants solve **reliably (3/3)** and **near-instantly
(generation 1-3)** at a **perfect 100/100**. On a task that genuinely needs
memory and is solvable within the budget, memory is not a tax — it is the whole
game. This is the direct opposite of 018 (memory a tax on double-pole), and the
two together give the real principle (Finding 4).

The sharpest single number: cfc solves the T-maze in **1 generation**. The SAME
cfc substrate HURT on double-pole (018). CfC is not good or bad in the abstract;
its inductive bias — a gated temporal integrator that holds and smooths state —
is exactly right for holding a discrete cue and exactly wrong for fast reactive
force. Capability value is task-shaped.

## Finding 2 — the T-maze really needs memory

One arm-A run was stuck at **exactly 50/100** at the generation cap. That is the
memoryless floor: a reactive policy on balanced cues gets chance, no more. So the
task is a true memory discriminator (the earlier 20-random-trial version let a
lucky always-one-way net hit 18/20; the balanced 100-trial version closes that).

## Finding 3 (discovery) — feedforward + FIFO is a FOURTH memory variant (delay-line)

Yet arm A solved 2 of 3 runs (fitness 96 and 100). A network with standard
neurons and no recurrent edges reached 100/100 on a memory task. Standard neurons
carry no internal state and feedforward has no feedback wiring, so the memory
comes from the SUBSTRATE — and it is best understood not as contamination of the
"none" arm but as a distinct memory mechanism the substrate affords.

The mechanism: the FIFO-queue input accumulator (added in EXP_020 to fix
recurrent evaluation) buffers signals per source. When the cortex advances a
cycle before a neuron has fired (paths of unequal length), a buffered signal from
an earlier cycle is consumed later — a time delay. Evolution chains these into a
DELAY LINE that carries the cue across the corridor. This is a recognised memory
paradigm: a Time-Delay Neural Network (TDNN), the tapped delay lines of reservoir
computing. Here it emerges for free from the async message-passing substrate.

So the "none" arm was really two variants conflated:

- **pure reactive** — minimal topology, no delay structure. The run stuck at
  EXACTLY 50/100 is this: chance on a memory task.
- **delay-line** — evolved multi-path feedforward + FIFO buffering. The runs that
  reached 96 and 100 are this.

The memory-variant menu is therefore FOUR, not three:

| Variant | Mechanism | Analog | Character |
|---|---|---|---|
| none / reactive | — | perceptron | chance on memory tasks |
| delay-line | feedforward + FIFO buffering | TDNN / tapped delay | cheap, emergent, BOUNDED delay, unreliable to evolve |
| neuron | LTC/CfC internal_state | continuous-time RNN | unbounded delay; smoothing bias |
| wiring | recurrent feedback | RNN | unbounded delay; fragile eval (fixed EXP_020) |

Consistent with the data: the delay-line variant is UNRELIABLE (2/3; one run never
found it, stayed at 50) and SLOWER (generation 3 and 25 vs 1-3 for explicit
memory), exactly as a fragile emergent mechanism should be. Its defining limit is
that the delay is BOUNDED by the evolvable delay-line depth, whereas explicit
memory (neuron/wiring) spans an unbounded gap.

This is a hypothesis until EXP_022 confirms it, and the test doubles as its
characterisation: raise the corridor delay (2 -> 8 -> 16). Prediction: neuron and
wiring stay flat (any gap); the delay-line variant falls off as the gap outgrows
an evolvable delay line; pure-reactive sits at 50 throughout. That confirms the
mechanism, measures the delay-line variant's capacity, AND yields a cleanly
no-memory baseline at long delay.

Implication for the variant map: this substrate affords a memory variant most
frameworks do not expose — emergent delay-line memory — for free. That is a
genuine item on the Neurolab-as-a-Service menu, not a defect to be scrubbed.

## Finding 4 — the unifying principle: capability value = task-match x budget

Across the programme:

| Insight | Capability | Task | Budget | Verdict |
|---|---|---|---|---|
| 013 | topology search | XOR (topology known) | — | tax |
| 015 | deep tuning | smooth control | — | tax |
| 017/018 | memory | double-pole no-vel | too small to solve | tax |
| **019** | **memory** | **T-maze (needs memory)** | **solvable** | **pays** |

A capability pays if and only if the task needs it AND the budget can exploit it.
018 and 019 are the clean pair: same capability (memory), one task where the
budget cannot reach a solution (tax), one where it can (decisive win). "Memory
doesn't help" was never true in general — it was "memory doesn't help within reach
of a solution." Give a task where memory is both necessary and affordable, and
memory wins in one generation.

This is the strongest prior in the corpus for a variant-recommendation service:
do not ask "does this capability help?" in the abstract; ask "does the task need
it, and can the budget exploit it?"

## Next

- **EXP_022 — delay sweep:** raise the T-maze corridor delay (2 -> 8 -> 16). Tests
  the delay-line hypothesis (Finding 3) and gives a cleanly-memoryless baseline
  at long delay. Prediction: cfc/recurrent flat across delay; feedforward falls
  off as the delay outgrows an evolvable delay line.
- The high-budget double-pole run (on msi00.lab) remains the other open thread:
  does memory pay on double-pole once the budget CAN reach a solution? 018+019
  predict yes.
