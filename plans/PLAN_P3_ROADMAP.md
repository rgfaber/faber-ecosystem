# Programme 3 (Meta-learning) — roadmap

Where P3 stands and the next phase. Charter: [`CHARTER_P3_META_LEARNING.md`](CHARTER_P3_META_LEARNING.md).

## The thesis so far (insights 031-034)

Evolved plasticity (memory-by-LEARNING: a local rule writes the cue into the
weights within an episode) vs CfC (memory-by-STORAGE: the cue sits in a neuron's
continuous state). On the T-maze:

- **031** — equal on the easy task: both solve delay-2 10/10 in ~1 generation.
- **032/033** — plasticity scales more gracefully with the memory horizon: both
  solve to delay 64, but CfC's evals-to-solve grow ~8x (130->1038) vs plastic ~2.6x
  (120->312). A **cost** gap.
- **034** — plasticity is noise-robust: at sensor noise sigma=0.5 CfC FAILS (0/8),
  plastic holds (8/8). A **capability** gap.

**Emerging thesis:** an evolved local learning rule is a more robust memory
mechanism than a fixed continuous-time state -- cheaper to find as the horizon
grows, and it survives noise. Two things it does NOT yet establish: (a) WHY (the
mechanism is hypothesised, not measured), and (b) whether the advantage holds for
richer rules and harder memory tasks. That is the next phase.

## Infra already built

- `network_evaluator:evaluate_with_plasticity/3` — global ABCD-Hebbian, process-free.
- `sep_cma_es`, `tmaze_sim` (delay via params), the batched CfC-population NIF.
- The experiment-viz pattern (emit JSON -> Artifact dashboard).
- Parallel runs via git worktree (033+034 ran that way).

## Next phase — three experiments, prioritised

### EXP-035 — the mechanism probe (do FIRST; cheap; closes the honesty gap)

**Question:** WHY does noise break CfC and not plasticity? 034's mechanism is a
hypothesis. Measure it directly.
**Method:** take a SOLVED CfC agent and a SOLVED plastic agent (best genomes from
032/031), run them on the noisy T-maze, and instrument the MEMORY carrier over a
trial: for CfC the hidden internal_state, for plastic the weight displacement. Plot
the "held cue signal" (state / imprint magnitude, and its sign-consistency with the
cue) across the corridor at sigma 0 / 0.3 / 0.5. Prediction: the CfC state's cue
signal degrades / flips under noise across the corridor, the plastic imprint does
not. No evolution -- just instrumented replay, like the 031 latch trace.
**Decides:** turns 034's tentative mechanism into a measured finding (or refutes it).
**Infra:** a capture harness (reuse the 031 trace pattern); no new src.

### EXP-036 — richer plasticity rules (does the lead widen? does it unlock harder tasks?)

**Question:** the current rule is one GLOBAL {A,B,C,D,Eta}. Do richer rules do more?
**Arms (rule families):**
- **Oja's rule** — dW = Eta*(pre*post - post^2*w): self-normalising, stable, avoids
  the weight blow-up the ABCD clamp currently guards.
- **Per-layer rule** — separate {A,B,C,D,Eta} for input->hidden vs hidden->output
  (genome +5).
- **Reward-modulated (neuromodulated)** — gate the update by the trial reward, the
  classic lifetime-learning rule; the T-maze already returns reward at the junction.
**Method:** compare rule families on the T-maze under the 034 noise sweep and the
033 delay sweep (the regimes that already rank mechanisms). Solve rate + evals.
**Decides:** whether richer local rules widen plasticity's robustness lead, and
which family is the P3 workhorse.
**Infra:** `evaluate_with_plasticity` needs a rule-TYPE parameter (Oja / ABCD) and,
for per-layer, per-layer coefficients; for reward-modulated, the eval loop must
thread the reward into the update. Real src work, kept elvis-clean.

### EXP-037 — combined stressors + a harder memory task

**Question:** do the two stressors compound (delay AND noise), and does the plastic
advantage survive a task that needs MORE than one held bit?
**Arms/method:**
- **Combined** — the 033 delay sweep WITH the 034 noise, one grid. Prediction: CfC
  collapses faster than either stressor alone; plastic holds longer.
- **Harder task** — a multi-cue / sequential-recall T-maze variant (remember 2 cues,
  or a cue sequence), which stresses memory CAPACITY not just duration. A new scape.
**Decides:** whether the P3 thesis generalises beyond single-bit, single-stressor
memory.
**Infra:** combined reuses 033a/033b; the harder task needs a new scape module
(`seq_tmaze_sim` or similar), the biggest new piece.

## Phase status (035-037 DONE, 2026-07-22)

- **035** DONE — mechanism measured: CfC state is leaky (~25x decay to a ~4% margin
  even noiseless), plastic imprint stable (~100%). 033/034 mechanism now measured.
- **036** DONE — ran Oja (not per-layer/reward-mod): Oja is SIMPLER (1-param), and
  LESS robust than ABCD (breaks 2/8 at sigma=0.5 where ABCD holds 8/8). Correction:
  expressiveness (param count), not the self-normalising property, buys robustness;
  the genuine "richer rule" win needs MORE params than ABCD.
- **037** DONE — combined delay x noise: stressors COMPOUND for CfC (0/8 at
  sigma=0.5 both delays, fitness falls with delay); plastic solves the whole grid
  8/8. Thesis (learning > storage for robust memory) holds on horizon, noise, both.

## Open chapter — EXP-038: does expressiveness buy CAPACITY? (active)

036 said the real richer-rule test needs MORE params than ABCD; the roadmap's
"harder task" is a capacity task. EXP-038 unites them: a new **multi-cue T-maze**
(`multi_cue_tmaze_sim`, two held bits — recall cue A at junction 1, cue B at
junction 2) against four arms — none (ceiling), cfc (multi-dim state), abcd
(global 5-param), **pc** (per-connection ABCD, each synapse its own {A,B,C,D},
new `evaluate_with_plasticity` rule `{pc, CoeffLayers, Eta}`). Hypothesis: one
global rule cannot specialise synapses to two independent bits, so global ABCD
ceilings where CfC's state and the expressive per-connection rule break through.
A clean negative (global ABCD copes fine, or even pc cannot) is equally valuable.
Infra built: per-connection plasticity (network_evaluator, + unit tests),
multi_cue_tmaze_sim scape. `none` confirmed at exactly 50 (memoryless ceiling).

## 038-039 outcome (DONE, 2026-07-22)

- **038** — capacity ALONE does not separate the mechanisms: on the two-bit
  multi-cue T-maze, storage (cfc_reset) and per-connection plastic both solve 7/8;
  the pc>abcd edge was n.s. A signed correction: the naive-CfC 2/8 was a
  reset-protocol confound (cross-trial interference), not a capacity limit.
- **039** — capacity + NOISE separates decisively: at sigma=0.3 per-connection
  solves 8/10, global ABCD 1/10, storage 0/10 (Fisher p~0.0007). The decisive axis
  under the hardest stress is per-SYNAPSE expressiveness, not storage-vs-learning
  (global plasticity is as broken as storage). Confirms 036 at its strongest.

**P3 thesis (031-039):** the gaps between memory mechanisms open only as stressors
COMBINE, and the top of the hierarchy is the most EXPRESSIVE local rule
(per-connection ABCD), not "learning" in general. Storage < global-plastic <
per-connection-plastic, visible under noise and decisive under capacity+noise.

## 040 outcome — interference hypothesis REFUTED (DONE, 2026-07-22)

The 035-style cross-talk probe REFUTED the interference story: per-connection solvers
have the HIGHEST readout cross-talk (0.82), global ABCD the LOWEST (0.26) — inverted.
Cross-talk (magnitude leak) does not track robustness because decisions are SIGN-based
and survive large leak. The probe was also on the wrong population (sigma=0 solvers):
CfC's noiseless solvers saturate at +/-1 margins yet are least robust (039). So a
STATIC representational probe cannot explain the noise result. Reframe: per-connection's
advantage is most likely EVOLVABILITY of the noise-robust region (echoes 020), not a
static property.

## 041 outcome — evolvability CONFIRMED (DONE, 2026-07-22)

The learning-curve trace (new sep_cma_es trace option) confirmed 040's reframe: under
sigma=0.3 all three arms converge to a SHARED ~76 (1.5-bit) basin by gen 50 (cfc even
leads early), then only per-connection climbs out (76.9 -> 89.3 over gen 50-200);
global ABCD reaches ~85, cfc stalls ~82. The advantage is the EVOLVABILITY of the
noise-robust two-bit region, not a static property. Method self-correction: the first
summary window (gen 150->300) was too late and hid the effect; the divergence is
gen 50-200.

## Candidate next (EXP-042) — dimensions or representation?

- **Per-LAYER rule as a middle point:** does global ABCD fail because of too few
  DIMENSIONS or too little REPRESENTATIONAL freedom? Give abcd a per-layer rule
  (2x5 params). If it lands between abcd and pc, expressiveness scales smoothly; if it
  stays with abcd, the escape needs full per-connection freedom.
- **Confirmatory optimiser arm:** repeat 041 with plain (mu,lambda)-ES to check the
  basin-escape profile is not sep-CMA-ES-specific.
- **Then the capstone:** learning ACROSS a task distribution (reward-modulated
  three-factor rules); per-connection is the established workhorse to carry into it.

## Capstone I DONE (042, 2026-07-22) — existence CONFIRMED

The deferred third factor shipped (`evaluate_with_neuromod/4`) + a lifetime-learning
scape (`reversal_bandit_sim`). Reward-gated plasticity LEARNS and RE-LEARNS a hidden,
swapping arm: both global and per-connection solve 8/8 at ~90-91 vs fixed 50. Traces
show explore->lock->[reversal]->re-lock. Lifetime learning is tractable, not a wall.
On this EASY 1-bit task global is cheaper than per-connection (expressiveness unneeded
= cost, as everywhere).

## Capstone II (EXP-043) — the fair three-way contest

Probabilistic reversal (good arm pays ~0.8, bad ~0.2) so one trial is noisy and the
agent must INTEGRATE over trials -- this excludes reactive win-stay-lose-shift and
lets STORAGE compete (a recurrent state given reward+last-action as INPUT, RL^2-style).
Arms: cfc (storage), nm_global, **nm_per_layer** (absorbs the old EXP-042
dimensions-vs-representation question), nm_pc. Randomised reversal points + initial
arm = the task distribution. Question: does the memory-arc thesis TRANSFER -- learning
> storage on robustness, per-connection decisive when hard, via evolvability?
Standing side-check (from old 042): confirm any headline with plain (mu,lambda)-ES.

## Capstone II DONE (043, 2026-07-22) — thesis does NOT transfer (a null)

Probabilistic reversal with reward fed as input (new `prob_reversal_bandit_sim`): all
four mechanisms (cfc storage, global, per-layer, per-connection plasticity) TIE at
~92-93, 8/8. "Learning > storage" was a memory-under-stress phenomenon, not universal:
given reward-as-input a recurrent state learns as well as reward-gated plasticity.
Expressiveness a cost again (pc ~2x evals); per-layer sits with global (old-042 moot
here). Like 038, an easy task ties -- discrimination needs stress.

## EXP-044 DONE — reversal frequency does NOT separate (null holds)

Reversal-frequency sweep (1/3/7 per life): all three mechanisms degrade together
(~4 pts, to ~86-88); only a weak, n.s. hint that pc is most robust and global least.
Two lifetime stressors now (043 reward-SNR, 044 frequency) fail to reproduce the
memory-arc separations. Interpretation: memory stressors attack the STORED signal
(mechanism-specific fragility), lifetime stressors attack the TASK (mechanism-neutral).
One untested 034-analogue remains: feedback-sensor noise (EXP-045). Programme ripe for
synthesis (SYNTHESIS_P3).

## EXP-044 (superseded above) — the STRESSED lifetime task (find the separator, if any)

Mirror the memory arc: add the analogue of 039's noise to the lifetime task and look
for the regime where a mechanism separates. Candidates: sensor noise on the
reward/action feedback (corrupts the RL^2 state's evidence), MORE arms (harder
credit assignment), or REPEATED reversals (faster re-adaptation demand). Prediction
(from the memory arc): under enough feedback noise, storage (whose state integrates a
now-corrupted signal) should degrade before reward-gated plasticity -- the lifetime
echo of 034. If nothing separates, the honest conclusion is that mechanism choice is a
memory-under-stress matter, not a lifetime-learning one.

## The deep question (beyond this phase)

All of 031-037 is memory WITHIN an episode (hold a cue). The ultimate P3 test is
learning ACROSS episodes / a task distribution -- true learning-to-learn, where the
evolved plasticity discovers a rule that adapts to tasks it never saw in evolution
(the Baldwin/Chalmers programme). That needs a task DISTRIBUTION, not one T-maze, and
is the natural capstone once the rule-family and mechanism questions settle.

## Sequence and rationale

1. **035 first** — cheap, no evolution, closes the mechanism honesty gap; makes
   033/034 a measured story, not a suggestive one.
2. **036 next** — the active front: richer rules are the substantive P3 direction and
   the one most likely to widen the thesis.
3. **037 after** — compounding + capacity, once 036 says which rule family to carry.
4. **learning-to-learn** — the capstone, when the mechanism and rule questions settle.

One active experiment at a time (the programme discipline). Each is a signed insight,
positive or negative; the emerging thesis (learning > storage for robust memory) is
strong enough that a clean NEGATIVE (a richer CfC, or a task where storage wins)
would be as valuable as another confirmation.
