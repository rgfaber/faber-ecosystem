# 054 — [P7 rung 2] Even a FIXED benchmark goes blind if it is too weak: on the 1D arms-race game, a benchmark whose reference opponents are clustered near the start SATURATES after ~13% of the run and reports zero further progress (late-window reading range 0.0000, dead flat) while the trait climbs a further ~31 of its ~35 total units; a graded benchmark of adequate range keeps resolving throughout (late-window range 0.130, disjoint CIs, n=30). 053's rule "use a fixed benchmark, not co-fitness" needs a caveat: the benchmark must also be GRADED with adequate range, or it silently reports stagnation during real, ongoing progress.

**Verdict:** confirmed (benchmark strength) + method (design-gated redesign + metric fix) · **Date:** 2026-07-23
**Programme:** 7 (Coevolution / self-play) — rung 2 of the ladder.
**Uses:** the exact 1D stochastic transitive numbers game signed in 053 (no net/NIF). Report as
replication of the graded-benchmark / dominance methodology (Cliff & Miller; Nolfi & Floreano).
Scoped to this game; tests benchmark STRENGTH, not alignment (a separate later rung).

## Why this rung exists

053 established the first coevolution rule: never measure progress by co-fitness (it is blind to
it), use a FIXED benchmark. But "fixed" is not sufficient. If the benchmark's reference opponents are
too weak, the champion clears them early, the benchmark saturates at its ceiling, and from then on it
reports "no more progress" even as the trait keeps climbing without bound. This is exactly the failure
that killed the pursuit-evasion opener (a random/weak benchmark saturates); this rung isolates it on
the 1D game where true progress is directly observable, so the benchmark can be checked against a
truth we can see.

## Method

The 053 game unchanged (1D, mu=lambda=30, sigma=0.3, tau=0.3, x0=50, R=150, n=30; true progress =
champion x, observable). Two FROZEN benchmarks: **graded** (references spanning x0 .. 2x the pilot's
final x, so the champion never clears it) and **weak** (references clustered in [x0, x0+5], cleared
almost immediately). Each reports the champion's mean win-probability against its reference set per
generation. The strength failure appears only AFTER saturation, so metrics are measured in the LATE
window (2nd half): the unsaturated fraction, and the reading's RANGE (how much it can still move).

## Method note (two corrections, both pre-run)

The DESIGN gate (Fable) demolished a first design (a 2D "alignment" test) as fatally circular:
benchmark references scored by the game's own rule collapse to a constant opponent, the min-rule
keeps the population balanced (no imbalance to exploit), and a Spearman-over-generations statistic is
tautological. It prescribed this benchmark-STRENGTH design instead (STRENGTH and ALIGNMENT are
orthogonal; alignment is a later rung). Then VALIDATION (n=5, before any signed run) caught that a
late-window Spearman ALSO fails: a saturated benchmark creeps up infinitesimally (0.999 -> 0.9999),
so its rank order still rises and Spearman reads 1.0 despite being flat. The metric was corrected to
the late-window RANGE (magnitude, not rank) and pre-committed before the signed run.

## Results (n=30, R=150)

| benchmark | unsaturated fraction (median) | late-window reading range (median, 95% CI) |
|---|---|---|
| graded (adequate range) | 1.00 | **0.130** [0.123, 0.139] |
| weak (references near the start) | 0.13 | **0.0000** [0.0000, 0.0000] |

Blindness gap: the champion's trait climbed a median of **30.8** further AFTER the weak benchmark
first saturated (the total escalation is ~35, from x=50 to ~85), so the weak benchmark was blind to
about seven-eighths of the run's real progress.

## Finding — a fixed-but-weak benchmark reports stagnation during a real arms race

The weak benchmark is fixed, frozen, non-coevolving, everything 053 asked for, and it is still wrong:
it saturates after ~13% of the run and its reading is thereafter dead flat (range 0.0000), so it
reports no progress while the trait climbs ~31 more units. The graded benchmark, differing only in
the RANGE of its references, resolves progress throughout (range 0.130, disjoint CIs). The lesson is
sharp: escaping co-fitness (053) is necessary but not sufficient; the fixed benchmark must also be
GRADED with references that stay ahead of the population, or it goes silently blind. This is precisely
the fix the deferred pursuit-evasion rung needs: a graded hall-of-fame, not a naive/random opponent set.

## Scope and what this licenses

Tests benchmark STRENGTH (does a too-weak, saturating benchmark misread progress). It does NOT test
benchmark ALIGNMENT (does a benchmark whose scoring is decoupled from true quality misrank
multi-dimensional players?) -- that is orthogonal and needs a coevolution-free ranking test on a
hand-built player panel, a later rung. Scoped to this 1D game, single operator. The P7 methodology is
now two rules deep (co-fitness is blind; a fixed benchmark must be graded), hardening the instrument
before the embodied rungs and eventually Flatland.

## Pointers

- Raw: `054-raw-benchmark-strength.txt`.
- Runner + engine pin + manifest: `faber-programmes/programmes/p7_coevolution/exp054_coevolution_benchmark_strength/`.
- Charter: [`../plans/CHARTER_P7_COEVOLUTION.md`](../plans/CHARTER_P7_COEVOLUTION.md).
