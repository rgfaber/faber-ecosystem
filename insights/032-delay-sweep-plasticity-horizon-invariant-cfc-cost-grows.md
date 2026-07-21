# 032 — T-maze delay sweep (4/8/16): both plasticity and CfC solve 10/10 up to delay 16, but plasticity is HORIZON-INVARIANT (flat ~120 evals, perfect fitness) while CfC's evolution cost GROWS with the memory horizon (170->460 evals, fitness dips). Memory-by-learning scales more gracefully than memory-by-storage.

**Verdict:** confirmed + finding (a modest but consistent ranking; both solve) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-032. **Uses:** `evaluate_with_plasticity/3`,
`sep_cma_es`, `tmaze_sim` (delay via sensor/actuator params). Follows 031.

## The question

031 showed plasticity and CfC both solve the delay-2 T-maze 10/10 in ~1 generation
-- too easy to rank the two memory mechanisms. This sweeps the corridor length
(4/8/16), forcing the cue to be held longer, to find where memory-by-learning and
memory-by-storage diverge.

## Method

Same three arms as 031 (plastic / cfc / memoryless), net `2 -> [12] -> 1`,
sep-CMA-ES (lambda 100, 300 gens cap, solve 90/100), n=10 per (arm, delay). Delay
set via `tmaze_sim` params `[Delay]`. Raw: `032-raw-delay-sweep.txt`.

## Results (mean over 10 runs)

| Arm | metric | delay 2 (031) | delay 4 | delay 8 | delay 16 |
|---|---|---|---|---|---|
| plastic | solved | 10/10 | 10/10 | 10/10 | 10/10 |
| plastic | evals-to-solve | ~120 | 120 | 100 | 160 |
| plastic | fitness | 100 | 100.0 | 100.0 | 100.0 |
| cfc | solved | 10/10 | 10/10 | 10/10 | 10/10 |
| cfc | evals-to-solve | ~130 | 170 | 390 | 460 |
| cfc | fitness | ~99 | 98.8 | 99.2 | 98.2 |
| memoryless | solved / fitness | 0/10 · 50.0 | 0/10 · 50.0 | 0/10 · 50.0 | 0/10 · 50.0 |

## Finding 1 — the primary metric does NOT separate them: both scale to delay 16

Both memory mechanisms solve 10/10 at every delay up to 16. Neither degrades in
solve rate as the horizon grows; holding a cue across 16 corridor steps is within
reach of both. So on the headline metric, memory-by-learning and memory-by-storage
are equivalent through delay 16.

## Finding 2 — the secondary metrics rank them: plasticity is horizon-invariant, CfC's cost grows

The evals-to-solve tells them apart. **Plastic is flat**: ~100-160 evals (1-2
generations) and perfect 100.0 fitness at every delay. **CfC's cost climbs** with
the horizon: 130 -> 170 -> 390 -> 460 evals as delay goes 2 -> 4 -> 8 -> 16 (~3.5x),
and its fitness sits just under perfect (98-99). So as the memory horizon grows, the
plastic rule is found just as fast, while the CfC solution gets harder to evolve.
Memory-by-learning scales more gracefully here.

## Finding 3 — a plausible mechanism (stated tentatively)

CfC memory is a decaying internal state (the update is bias/tau-driven when the
sensor input is zero), so a longer hold needs the state dynamics tuned to neither
decay away nor saturate over more steps -- a narrower target as the delay grows,
hence more evals and occasional imperfection. The plastic rule instead re-imprints
the cue into the weights each trial and the imprint persists through the corridor;
empirically that persistence is horizon-robust. The exact evolved encoding is not
pinned (031 showed it is not a simple store-and-hold), so this mechanism is a
hypothesis, not a proof -- the empirical horizon-invariance is the solid claim.

## Finding 4 — the floor is horizon-independent

Memoryless caps at exactly 50.0, 0/10, at every delay. The longer corridor changes
nothing for a memoryless net -- it never had the cue to lose.

## Prior art

Replication + a small comparative note. Evolved plasticity and CfC/LTC are both
established memory mechanisms (031's refs). The delay-sweep comparison -- that an
evolved local rule is more horizon-robust than a fixed continuous-time state on this
benchmark -- is a substrate-specific measurement, modest in size (1-5 generations),
not a general claim about the two model classes.

## Caveat / next

- **Delay 16 is still solvable by both.** The divergence is in evals, not solve
  rate. A longer sweep (32/64) may find where CfC breaks entirely -- or where both
  do. The differences are small (1-5 generations); n=10.
- **Next:** push the delay to 32/64 (does CfC's cost curve go vertical while plastic
  stays flat?), and/or add sensor noise (which should hurt the decaying CfC state
  more than the re-imprinted plastic weights). Either sharpens the ranking.
- Result rendered as a delay-sweep dashboard (evals-to-solve vs delay).
