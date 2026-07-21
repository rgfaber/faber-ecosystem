# EXP_029 — does memory pay, once solves are reachable? (bridge back to Programme 1)

Pre-registered in insight 028's "next". Under the weak GA/ES (018/024) memory was
a TAX because nothing solved DPNV; sep-CMA-ES (028) now solves the memory version,
so memory's value can finally be isolated.

## Hypothesis

DPNV is non-Markov, so a MEMORYLESS feedforward net should fail it while a CfC
(memory) net solves it. **Prediction:** memoryless solves the Markov (with-velocity)
pole but fails the no-velocity pole; memory makes the no-velocity pole solvable —
i.e. memory pays exactly when the task is non-Markov. What would surprise us:
memoryless also solves the no-velocity task (DPNV-1000 is not really memory-forcing).

## Method

Hold optimizer (sep_cma_es) and budget (lambda 100, 400k evals) fixed; vary the
net and whether velocity is observed. n=10, three arms:
- memoryless_markov — feedforward, double pole WITH velocity (sensor 6). Positive control.
- memoryless_dpnv — feedforward, double pole NO velocity (sensor 3). The test.
- cfc_dpnv — CfC + co-evolved tau, NO velocity (replicates 028).

Memoryless = `create_feedforward` (no neuron_meta -> stateless eval, reset no-op):
genuinely reactive, no velocity in sensor 3. Runner `exp029_tests.erl` (throwaway).

## Kill criterion

If memoryless_dpnv solves at a rate indistinguishable from cfc_dpnv, memory does
NOT pay on this task and DPNV-1000 is too weak a memory test (do not re-interpret
as memory-pays). If memoryless_dpnv ~ 0/10 while cfc_dpnv solves, memory is
necessary.

## Feed

n=10 per arm. Raw: `insights/029-raw-memory-pays.txt`.

```
memoryless_markov (+velocity, Markov)   10/10 solved   (positive control OK)
memoryless_dpnv   (no velocity)          7/10 solved   <-- SURPRISE
cfc_dpnv          (memory, no velocity) 10/10 solved
```

memoryless_dpnv 7/10 vs cfc_dpnv 10/10: Fisher one-tailed p~0.11 (NOT significant).

## Insight

**Memory does NOT demonstrably pay on DPNV-1000.** A memoryless feedforward net
solves it 7/10 (n.s. vs CfC 10/10); DPNV-1000 is too weak a memory test
(reactively solvable), the OPTIMIZER was the lever. Memory pays where the task
needs it (T-maze 019), not here. Full write-up:
[`insights/029-memory-does-not-pay-on-dpnv-optimizer-was-the-lever.md`](../../insights/029-memory-does-not-pay-on-dpnv-optimizer-was-the-lever.md).
