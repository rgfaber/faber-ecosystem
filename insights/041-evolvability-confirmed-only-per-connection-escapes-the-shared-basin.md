# 041 — Evolvability CONFIRMED (040's reframe holds): under capacity+noise all three mechanisms converge to a SHARED ~76 (one-and-a-half-bit) plateau by generation 50 — CfC even leads early — and then only per-connection climbs OUT of it (76.9 -> 89.3 over gen 50-200) toward the two-bit solution, while global ABCD climbs partway (-> 85.4) and CfC storage stalls (-> 81.6). The advantage is not a static property (040) but the EVOLVABILITY of the noise-robust two-bit region: the richer per-connection parameterization makes that region reachable by the search where the simpler rules' searches converge to the shared basin.

**Verdict:** confirmed (040 reframe supported) + method self-correction · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-041. **Uses:** `sep_cma_es` trace option (new,
faber-tweann 51f655e), `multi_cue_tmaze_sim`. sigma=0.3, n=8, 300 generations.

## The question

040 refuted the static-interference story and reframed per-connection's noise
robustness as EVOLVABILITY: only pc can FIND a noise-robust two-bit solution (039).
Test it by instrumenting the SEARCH. Under sigma=0.3, trace each arm's best fitness
per generation. Does pc keep improving where global ABCD and storage stall?

## Method

Added an optional per-generation trace to sep_cma_es (returns history =
[{Gen, BestFitness}]). Ran cfc_reset / abcd / pc under sigma=0.3, n=8, 300 gens, and
averaged the learning curves. Raw: `041-raw-evolvability.txt`.

## Results — the mean learning curve

| gen | cfc_reset | abcd | pc |
|---|---|---|---|
| 10 | 62.0 | 64.4 | 64.4 |
| 20 | **70.5** | 67.4 | 66.3 |
| 50 | 75.6 | 75.7 | 76.9 |
| 100 | 76.4 | 77.9 | 81.8 |
| 150 | 78.3 | 81.1 | 86.3 |
| 200 | 79.6 | 83.6 | 89.3 |
| 300 | 81.6 | 85.4 | 89.6 |

## Finding 1 — a SHARED early basin, then divergence

All three rise together to ~76 by generation 50 (cfc 75.6, abcd 75.7, pc 76.9). CfC
even LEADS early (gen 20: 70.5 vs pc 66.3) -- storage finds the one-bit solution
fastest. ~76 is a one-and-a-half-bit plateau (one bit held well, the second poorly).
The three do not differ in this first phase; the difference is entirely in what
happens AFTER.

## Finding 2 — only per-connection escapes the basin (the evolvability signal)

From gen 50 to 200 the curves fan out: pc climbs 76.9 -> 89.3 (+12.4), abcd 75.7 ->
83.6 (+7.9), cfc 75.6 -> 79.6 (+4.0). CfC essentially stalls at the 1.5-bit basin;
global ABCD escapes partway; per-connection climbs almost to the 2-bit solve line.
The search TRAJECTORIES differ, not just the endpoints: only the richest
parameterization keeps finding improvements out of the shared basin. This is the
evolvability property 040 predicted -- confirmed.

## Finding 3 — method self-correction: the summary window was wrong

My first pass reported "late (gen 150->300) improvement" and found it near-identical
across arms (~3-4), concluding all three flatline together. That window is TOO LATE:
by gen 150 pc has already done most of its climbing and is converging (it plateaus at
gen ~200). The divergence lives in gen 50-200, which the 150->300 window misses. The
full mean curve, not a two-point summary, is what revealed the effect. A reminder to
look at the whole trajectory before summarising it (cf 006/020, where coarse metrics
misled early).

## Finding 4 — fast-but-capped vs slower-but-climbing

CfC is the FASTEST to a decent score (leads at gen 20) but the most capped (stalls at
~80). Per-connection is SLOWER to start (trails at gen 20) but climbs highest. Storage
gets going quickly and stops; expressive learning starts slower and keeps going. The
same "unexploitable-capability-is-a-cost / exploitable-capability-pays-late" shape
seen across the programme, here as a speed-vs-ceiling trade in the SEARCH.

## What is now established (031-041)

- Learning beats storage on ROBUSTNESS (noise 034, combined 037), not capacity (038).
- Under the HARDEST stress (capacity+noise) the decisive axis is per-SYNAPSE
  expressiveness (039), and its advantage is the EVOLVABILITY of the noise-robust
  two-bit region (041): all mechanisms reach a shared 1.5-bit basin, only
  per-connection reliably climbs out. NOT static interference (040, refuted).

## Caveat / next

- **n=8, one task, sigma=0.3, one optimiser.** The divergence is clear in the mean
  curve; per-run variance is not analysed here. A different optimiser (plain ES) might
  show a different basin-escape profile -- worth one confirmatory arm.
- **Why can pc escape and abcd not?** The remaining mechanism question: is it the
  extra DIMENSIONS (search geometry) or the extra REPRESENTATIONAL freedom (a better
  optimum exists only for pc)? A test: give abcd a per-LAYER rule (2x5 params, a
  middle point) -- if it lands between abcd and pc, expressiveness scales smoothly; if
  it stays with abcd, the jump needs full per-connection freedom.
- **Capstone still open:** learning ACROSS a task distribution; per-connection carries.
- Feeds the P3 dashboard (learning-curve panel).
