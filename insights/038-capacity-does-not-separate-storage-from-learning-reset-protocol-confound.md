# 038 — Memory CAPACITY does NOT cleanly separate storage from learning, and my "storage is weakest" read was a RESET-PROTOCOL CONFOUND. On a two-bit multi-cue T-maze: memoryless caps at 50; per-connection plasticity 7/8; global ABCD 5/8; naive CfC only 2/8 — BUT that 2/8 was cross-trial state interference: with a per-TRIAL state reset CfC solves 7/8 (Fisher p~0.04 vs naive), tying per-connection. Lesson: the memory-carrier's reset must match trial structure. Expressiveness still edges global ABCD (7/8 vs 5/8, ~2x faster) but n.s. at n=8; capacity is a WEAKER discriminator than noise (034/037).

**Verdict:** signed correction (hypothesis refuted) + method + finding · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-038. **Builds:** `multi_cue_tmaze_sim` (new
capacity scape), `evaluate_with_plasticity` per-connection rule `{pc, CoeffLayers, Eta}`
(new, faber-tweann), both elvis-clean + unit-tested. `sep_cma_es`.

## The question and the hypothesis (refuted)

036 said the genuine "richer rule" test needs a rule MORE expressive than global
ABCD; the roadmap flagged a "harder task" that stresses CAPACITY not duration. This
unites them: a two-bit task (hold cue A AND cue B). **Hypothesis:** one global rule
cannot specialise synapses to two independent bits, so global ABCD ceilings where
CfC's multi-dim state and a per-connection rule break through. **This was WRONG on
both legs** (see findings 1 and 2).

## Method

New scape `multi_cue_tmaze_sim`: cue A (channel 0, step 0), cue B (channel 1, step 1),
corridor (delay 4), junction 1 -> recall A, junction 2 -> recall B. Balanced over the
4 (A,B) combos so a memoryless policy scores exactly 50; one bit caps at 75; two bits
reach 90. Fitness scaled 0-100. Five arms, sep-CMA-ES, delay 4, n=8 (none n=4):

- **none** — static feedforward (ceiling check).
- **cfc** — CfC state, reset once per EPISODE (parity with 031-037).
- **cfc_reset** — CfC state, reset per TRIAL (the confound control).
- **abcd** — global ABCD plasticity (5 params), weights reset per trial.
- **pc** — per-connection ABCD (each synapse its own {A,B,C,D}; 240+1 params), reset per trial.

Raw: `038-raw-capacity.txt`.

## Results

| arm | solve | mean fit | evals (solvers) | genome dim |
|---|---|---|---|---|
| none | 0/4 | 50.0 | — (never) | 73 |
| cfc (episode-reset) | 2/8 | 89.0 | ~7k | 85 |
| cfc_reset (trial-reset) | **7/8** | 98.4 | ~7k | 85 |
| abcd (global) | 5/8 | 95.3 | ~3.5k | 78 |
| pc (per-connection) | **7/8** | 98.4 | **~3.5k** | 314 |

## Finding 1 — the "storage is weakest" surprise was a RESET-PROTOCOL CONFOUND

Naive CfC solved only 2/8 and 6/8 runs plateaued at exactly 87.5 (one bit clean, the
second at 75%). That looked like a capacity limit of the leaky state. It was not:
`cfc_reset` (identical, but the state is zeroed at each trial start) solves **7/8**
(Fisher p~0.04 vs naive). The 2/8 was cross-trial state INTERFERENCE — the previous
trial's residue corrupting the next. The single-cue tasks (031-037) never exposed
this because there the leaky state self-clears across the corridor before the next
cue; with TWO cues the residue survives to interfere. **Lesson: the memory-carrier's
reset protocol must match the task's trial structure.** The self-clear that sufficed
for single-cue silently fails for multi-cue.

## Finding 2 — capacity does NOT cleanly separate storage from learning

Once the protocol is fair, memory-by-STORAGE (cfc_reset 7/8) and expressive
memory-by-LEARNING (pc 7/8) TIE on the two-bit task. This overturns the hypothesis:
capacity per se is not where storage fails. Contrast noise (034) and combined
stressors (037), where CfC failed 0/8 and plasticity held — those ARE clean
separators. Capacity is a WEAKER discriminator: both mechanisms have room for two
bits; the earlier gaps were about robustness (noise), not capacity.

## Finding 3 — expressiveness still EDGES global ABCD, but n.s. at n=8

Per-connection (7/8, ~3.5k evals) beats global ABCD (5/8) on solve-rate and mean,
and matches it on speed DESPITE a 4x larger genome (314 vs 78 dims) — sep-CMA-ES's
diagonal covariance pays for the per-synapse freedom with no search-cost penalty.
This is consistent with 036's "expressiveness buys robustness", now on capacity. BUT
7/8 vs 5/8 is Fisher p~0.28 — NOT significant at n=8. Honest status: a consistent
edge (rate + speed), not an established win. Needs n>=20 to confirm.

## Finding 4 — plasticity is CHEAPER to evolve than storage here

Both plastic arms that solve do so in ~3.5k evals; cfc_reset needs ~7k (2x). Echoes
032/033 (plasticity cheaper to evolve as the memory demand grows) on a new axis
(capacity), even though both reach the same 7/8 solve-rate.

## What survives, what's corrected

- **Corrected:** the naive-CfC 2/8 is NOT evidence about capacity; it is a protocol
  artifact. Any future multi-trial memory experiment must reset the carrier per trial
  (or justify not doing so). The n=1-style "surprise" did not survive the control
  (cf 026: the n=1 near-total effect that did not survive n=10).
- **Survives:** none=50 (definitive); reset protocol matters for CfC (p~0.04);
  per-connection plasticity at least matches every arm at 314 dims with no speed
  penalty; plasticity cheaper to evolve.
- **The P3 thesis is unchanged and sharpened:** learning beats storage on ROBUSTNESS
  (noise 034, combined 037), not on CAPACITY (038, a tie). The separator is noise,
  not the number of bits.

## Caveat / next

- **n=8; one task, one delay (4), two bits.** The expressiveness edge (pc>abcd) is
  suggestive, not significant — an n>=20 rerun at delay 4 would settle it cheaply.
- **Harder capacity (3-4 bits) or capacity x noise** would test whether per-connection
  pulls ahead where global ABCD and storage both start to fail — the regime where
  expressiveness should finally separate cleanly. That is the natural EXP-039.
- **The capstone** (learning ACROSS a task distribution; reward-modulated three-factor
  rules) remains the P3 prize; the per-connection machinery and the reset-protocol
  lesson both feed it.
- Feeds the P3 dashboard (capacity panel).
