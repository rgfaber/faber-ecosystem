# 055 — [P7 rung 3] Selecting on a MISALIGNED proxy decouples from true quality ONLY when improvement costs are asymmetric: on a 2D coevolution game whose true quality is conjunctive (q = min(x1,x2)), a compensatory sum-proxy is BENIGN under symmetric mutation costs (reaches comparable true min, if anything marginally higher: proxy 74.9 vs aligned 73.7) but BITES under asymmetric costs (aligned reaches true min 53.5 vs the proxy's 50.6, disjoint CIs, n=30), because the proxy pumps the cheap dimension and abandons the expensive one -- the true bottleneck -- producing grotesquely lopsided champions (|x1-x2| median 34.5 vs aligned's 1.5). The graded aligned benchmark (054) reveals the decoupling that the proxy's own reading hides.

**Verdict:** confirmed (conditional decoupling) + method (design-gated redesign from a tautology + direction-aware fix) · **Date:** 2026-07-23
**Programme:** 7 (Coevolution / self-play) — rung 3 of the ladder. Pure-Erlang numbers game, no net/NIF.
Report as a methodological demonstration (De Jong numbers games; reward/proxy misspecification).
Scoped to this synthetic conjunctive quality structure.

## Why this rung exists

053: don't measure progress by co-fitness (blind). 054: a fixed benchmark must be graded (a too-weak
one saturates). This rung: when the proxy you SELECT ON is misaligned with true quality, does the
population improve the proxy while true quality stagnates? True quality here is CONJUNCTIVE (good at
BOTH dimensions, q=min); a plausible researcher's proxy is COMPENSATORY (the sum/mean, which one big
dimension can prop up). Whether that misalignment bites was genuinely open, which is the point.

## Method

2D coevolution, two (mu+lambda)=30 populations; true win rule conjunctive: P(A beats B) =
logistic((min_A - min_B)/tau), tau=0.3. A 2x2 design changing only the fitness PROXY and the mutation
COST: selection proxy {**aligned** = min-scored fitness | **proxy** = mean(sum)-scored fitness} x cost
regime {**symmetric** = both dims mutate sigma=0.3 | **asymmetric** = x1 sigma=0.3, x2 sigma=0.03, so
raising the second dimension is 10x costlier}. Each generation the champion's TRUE quality (min) is
read via a frozen GRADED aligned benchmark (per 054). R=150, n=30 runs/cell. A static 36-player panel
confirms min and mean rank lopsided players discordantly (30 discordant pairs; mean ranks (110,10)
above (50,50) while min ranks below) -- a runner UNIT TEST verifying the scoring, NOT a finding.

## Method note (two redesigns, both pre-signing)

The DESIGN gate (Fable) first demolished a static-panel "alignment" draft as a tautology (two
aggregation functions differ by construction; Kendall tau desk-computable; unreachable null; zero
coevolution content) and prescribed this proxy-DRIVEN-SELECTION design with a live null (a sum-proxy
might be benign under symmetric costs). Then an exploratory run (n=30) produced the predicted pattern
but the verdict mislabelled it: the symmetric "comparable = CIs overlap" test is DIRECTION-BLIND, and
a tiny disjoint gap with the proxy marginally HIGHER (a benign outcome) wrongly failed it. Corrected
to a direction-aware test (a "bite" requires aligned STRICTLY higher than proxy), pre-committed, rerun.
Run 1 kept as `055-raw-run1-direction-blind-exploratory.txt`.

## Results (n=30, R=150)

| cell | true min-quality at R (median, 95% CI) | champion lopsidedness |x1-x2| |
|---|---|---|
| aligned selection, symmetric costs | 73.7 [73.4, 74.2] | 0.3 |
| proxy (sum) selection, symmetric costs | 74.9 [74.4, 75.4] | 3.4 |
| aligned selection, asymmetric costs | **53.5 [53.5, 53.6]** | 1.5 |
| proxy (sum) selection, asymmetric costs | **50.6 [50.5, 50.7]** | **34.5** |

Aligned selection escalates true min in both regimes (kill gate passes).

## Finding — a compensatory proxy is benign under symmetric costs, harmful under asymmetric ones

- **Symmetric costs:** the sum-proxy does NOT decouple. It reaches comparable true min (74.9 vs
  aligned's 73.7 -- if anything marginally higher), because when both dimensions are equally easy to
  improve, pushing the sum pushes the min too. A benign misalignment.
- **Asymmetric costs:** the sum-proxy BITES. It reaches lower true min (50.6 vs aligned's 53.5,
  disjoint CIs) and its champions are grotesquely lopsided (|x1-x2| median 34.5 vs aligned's 1.5): the
  proxy rewards pumping the cheap dimension x1 and abandons the expensive x2, which is exactly the
  conjunctive bottleneck. Selecting on the proxy improves the proxy while true quality stagnates.

The lesson has teeth: reward/proxy misspecification is CONDITIONAL. A plausible compensatory proxy is
safe when all sub-goals are equally easy, and dangerous precisely when they are not -- the regime real
problems live in. And it is silent: the proxy's own reading looks like progress; only a fixed,
graded, ALIGNED benchmark (054) exposes that true quality has stalled.

## Scope and where P7 stands

Scoped to this 2D conjunctive game, single operator, one proxy (sum). P7's benchmark methodology is
now three rules deep: (053) co-fitness is blind, use a fixed benchmark; (054) the benchmark must be
graded (strength); (055) if the SELECTION proxy is misaligned with true quality, it decouples the
population from true quality under asymmetric improvement costs, and only a graded aligned benchmark
reveals it. The instrument is hardening rung by rung before the embodied pursuit-evasion rung, where
"true quality" (catching a range of evaders) and any shaped training proxy must not be conflated.
Next rungs: intransitivity/cycling, disengagement, then the neural embodied game toward Flatland.

## Pointers

- Raw: `055-raw-benchmark-alignment.txt` + `055-raw-run1-direction-blind-exploratory.txt`.
- Runner + engine pin + manifest: `faber-programmes/programmes/p7_coevolution/exp055_coevolution_benchmark_alignment/`.
- Charter: [`../plans/CHARTER_P7_COEVOLUTION.md`](../plans/CHARTER_P7_COEVOLUTION.md).
