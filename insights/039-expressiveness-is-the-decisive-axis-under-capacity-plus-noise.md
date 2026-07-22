# 039 — Per-connection expressiveness is the DECISIVE axis under capacity + noise: on the two-bit multi-cue T-maze WITH sensor noise, per-connection plasticity solves 8/10 at sigma=0.3 where global ABCD manages 1/10 and CfC storage 0/10 (Fisher p~0.0007). 038's suggestive edge (n.s. at sigma=0) becomes decisive under combined stress. Refines the P3 thesis: under the hardest stress the winning axis is per-SYNAPSE expressiveness, not the storage-vs-learning dichotomy — global plasticity is as broken as storage here.

**Verdict:** confirmed + inflection (resolves 038's open edge) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-039. **Uses:** `multi_cue_tmaze_sim`,
per-connection rule `{pc,...}` (faber-tweann c25ba72), `sep_cma_es`. n=10.

## The question

038 showed capacity ALONE does not separate the mechanisms (cfc_reset and pc both
7/8 at sigma=0; the pc-over-abcd edge was n.s.). 034/037 showed noise separates on
a SINGLE bit. Does combining them — two bits AND noise — finally separate, and is
the separating axis storage-vs-learning or expressiveness?

## Method

The 038 two-bit multi-cue T-maze WITH Gaussian sensor noise (sigma 0.3 / 0.5),
arms cfc_reset (storage, per-trial state reset) / abcd (global 5-param) / pc
(per-connection, 240+1 params), sep-CMA-ES, n=10, solve 90. Reset is on the CLEAN
cue channel, the network sees NOISY sensors (034/036 protocol). Raw:
`039-raw-capacity-noise.txt`.

## Results

| arm | sigma=0.3 | sigma=0.5 |
|---|---|---|
| cfc_reset (storage) | 0/10 (mean 78.2) | 0/10 (74.0) |
| abcd (global plastic) | 1/10 (82.0) | 0/10 (77.3) |
| pc (per-connection) | **8/10 (89.8)** | 0/10 (83.7) |

At sigma=0.3 the 8 pc solves sit at 90.0-91.0; the 2 misses at 87.5 are near-hits.
Global abcd teeters: one solve (90.0), two 89.5 near-misses, the rest collapse to
76-84. Storage never gets close.

## Finding 1 — capacity + noise separates cleanly where capacity alone did not

At sigma=0 (038) all three reach 7/8 (storage) to 7/8 (pc): a tie. Add sigma=0.3 and
they fan out completely: pc 8/10, abcd 1/10, cfc_reset 0/10. pc vs abcd is Fisher
p~0.0007; pc vs cfc_reset likewise. The two stressors had to be COMBINED to reveal
the ranking — neither capacity nor (two-bit) noise alone did it.

## Finding 2 — the decisive axis is per-SYNAPSE expressiveness, not storage-vs-learning

The headline is NOT "plasticity beats storage". Global plasticity (abcd 1/10) is
essentially as broken as storage (cfc_reset 0/10). What survives is the PER-CONNECTION
rule (8/10). So under the hardest combined stress the winning axis is expressiveness
— each synapse tuning its own {A,B,C,D} to protect its bit against noise — not the
learning-vs-storage dichotomy that organised 031-037. A single global rule cannot
specialise enough to hold two noisy bits; per-synapse coefficients can.

## Finding 3 — 038's n.s. edge is now decisive (and confirms 036)

038 found pc > abcd at sigma=0 but only 7/8 vs 5/8 (Fisher p~0.28, n.s.). Under
combined stress the same ordering becomes 8/10 vs 1/10 (p~0.0007). This confirms
036's "expressiveness buys robustness" at its strongest: the value of extra plastic
parameters is invisible on easy regimes and decisive on hard ones — exactly the
"unexploitable capability is a cost, exploitable capability is decisive" pattern seen
across the programme (cf 013/015/017 for the cost side, here for the payoff side).

## Finding 4 — at sigma=0.5 all fail, but the ordering holds

sigma=0.5 breaks everyone (0/10) — two noisy bits at that level is past all three
mechanisms' reach at this budget. But the mean fitness preserves the ranking: pc 83.7
> abcd 77.3 > cfc_reset 74.0. pc degrades most gracefully even where it cannot solve.

## The P3 map (031-039)

| regime | winner | gap |
|---|---|---|
| easy, 1 bit (031) | tie | none |
| long horizon, 1 bit (032/033) | plastic (cost) | both solve |
| noise, 1 bit (034) | plastic (capability) | storage 0/8 |
| delay+noise, 1 bit (037) | plastic | storage 0/8, compounds |
| capacity, 2 bits (038) | tie (fair reset) | none |
| **capacity+noise, 2 bits (039)** | **per-connection** | abcd 1/10, storage 0/10 |

The gaps open only as stressors COMBINE, and the top of the hierarchy is not
"learning" in general but the most EXPRESSIVE local rule.

## Caveat / next

- **n=10; one task, delay 4, two bits, one noise model.** The sigma=0.3 separation is
  large and significant; sigma=0.5 is a floor (all fail) so it only ranks by mean.
- **Why does global ABCD fail where per-connection holds?** Measure it (a 035-style
  probe): does the global rule's single imprint direction force the two bits to
  interfere, while per-connection dedicates disjoint synapse groups? That mechanism
  probe is the natural EXP-040.
- **The capstone** (learning ACROSS a task distribution; reward-modulated three-factor
  rules) remains the P3 prize; per-connection is now the established workhorse to
  carry into it.
- Feeds the P3 dashboard (capacity+noise panel).
