# this article — evaluation report (2026-10-11)

Labels: **fresh** = run during this revision; **archived** = recorded earlier in
`evaluation/runs/`, re-read and recomputed but not re-run; **source** = read
from source files; **kernel** = Rocq theorem checked by `coqc`/`coqchk`.

## 1. Generated F/R/N group (archived, recomputed)

Source: `evaluation/runs/2026-10-10-full-eval-v1/ours_results.tsv`
(five-run medians, Ubuntu 26.04/WSL 2, i7-8650U, OCaml 5.4.0, Spot 2.16,
300 s limit, tool default front end = recur + weak until).

| Quantity | Recomputed | In the article |
|---|---|---|
| attempted / completed | 31 / 29 (R7, R8 time out) | same |
| transitions after reset completion (optimizer input) | 20,230 | 20,230 |
| after optimization | 1,512 (−92.53 %) | 1,512 |
| after explicit export | 3,221 (+113.03 %) | 3,221 |
| symbolic transitions after export | 996 | not stated in aggregate |
| fewer transitions after optimization / unchanged | 19 / 10 | same |
| exports that grow | 6 | same |
| active clocks | 77 → 75 | same |
| removed by R family / R6 | 18,441 / 14,985 of 18,718 | same |
| F / N removed | 60 / 217 | same |

R6: 64 locations, 15,625 transitions, 6 clocks → 6 rounds → 640 transitions
→ export 1,837 explicit transitions, 63 invariant locations, 3,360 diagonal
atoms, 448 symbolic transitions (archived). All numbers in the article match.

## 2. CASAAL as an external output-size reference (archived data, recomputed)

Source: `evaluation/casaal_exclusive.tsv` (CASAAL with mutually exclusive
events) against our optimized output. Over the 27 configurations completed
by both tools with an exact CASAAL encoding (F3, F13 are hatted and have no
exact CASAAL encoding; R7, R8 time out for us):

- locations and symbolic transitions coincide in **24/27**;
- F5, F6: ours 7 locations / 25 symbolic transitions, CASAAL 4 / 15 (ours larger);
- F14: ours 6 / 14, CASAAL 8 / 18 (ours smaller);
- clocks coincide except F11 (ours 1, CASAAL 2; reduced by the optimizer from 2)
  and F8 (ours 0, CASAAL 1; due to the upstream recur rewriting, not credited
  to the optimizer);
- R6: 64 / 448 / 6 for both.

Note: `evaluation/casaal_results.tsv` (CASAAL without mutual exclusion)
gives different transition counts (R6: 4,096) and only 11/27 matches; the
exclusive run is the one matching our one-event-per-position semantics.
A new subsection "External output-size reference" states this in the article.
Language agreement is not claimed here (the earlier translation article).

## 3. Synthetic suite and ablation (fresh)

`evaluation/run_synthetic_ablation_v2.sh` re-run on 2026-10-11 into
`evaluation/runs/2026-10-11-synthetic-ablation-fresh/` with the same sources
(SHA-256 of `optim.ml` and `synthetic_tba_bench.ml` identical to the archived v6 run).

- `leave_one_out.tsv`, `mechanism_examples.txt`: structural columns identical to v6.
- `pass_trace.tsv`, `parameterized_trace.tsv`: identical except time/heap columns.
- Leave-one-out aggregates (reachable locations, transitions, active clocks):
  full 30/33/9; −backward synthesis 31/34/10; −forward 33/36/9;
  −contradiction removal 40/43/11; −unsatisfiable labels 31/34/9;
  −no-incoming sources 30/39/10; −normalization 30/33/12; −dead resets 30/33/13;
  −synchronous clocks 32/35/11; −transition subsumption 30/35/9;
  −equivalent locations 31/35/9. All agree with the article.

The requested mechanism records already exist and are measured:
backward tightening (x≤5 → x≤2), forward lower bound (x≥2 added),
forward contradiction cascade (7 → 4 edges; 7 retained without forward
propagation), backward contradiction cascade, normalization enabling
dead-reset removal, clock merging followed by a contradiction in a later
round. The optimizer was not modified.

## 4. Regression tests (fresh)

`evaluation/run_boundary_tests.sh` + `check_boundaries.py`: 18/18 finite-word
boundary checks pass with the tool rebuilt from `tool/` on 2026-10-11.

## 5. UPPAAL (re-run 2026-10-11)

After the licence was renewed, `verifyta.exe` 5.0.0 (rev. 714BA9DB36F49691)
was run on the eight packaged models (`bash run_uppaal.sh`, `-t0 -q`):
the four `E<> P.A4` queries give not satisfied / satisfied / not satisfied /
satisfied (spor_ok, spor_bad, resp_ok, resp_bad), and the four
`A[] not deadlock` queries are satisfied. All eight verdicts are identical to
the archived ones in `evaluation/case_study/expected.txt` and
`uppaal_results.txt`. Output:
`evaluation/case_study/uppaal_results_rerun_2026-10-11.txt`.
These runs give verdicts only (no before/after runtime or zone counts).

## 6. TChecker before/after (archived)

`evaluation/runs/2026-10-10-tchecker-before-after-v3/`: not re-run in this
revision; byte-identical products before/after, same verdicts and explored states.

## 7. Figures S3/S4 of an earlier draft of the supplement — not restored

The earlier Fig. S3 (Example S1, ◇≤5(p U≤2 q), split of a disjunctive
invariant x_u1 ≤ 2 ∨ x_u0 ≤ 5) and Fig. S4 (Example S2, three clocks) were
produced by an earlier version of the front end (Spot options -B -D, option
-init). With the current tool, for both formulas and with or without
`-init`, `-noweak`, `-B -D`, the optimized automaton has no disjunctive
invariant (S1: 4 locations, 7 transitions; S2: 5 locations, 11 transitions;
0 diagonal atoms after export). Restoring the original TikZ as "output of
mtl2tba" would therefore misdescribe the current tool; the decision is left
to the authors; decided on 2026-10-11: not restored. The worked maximal-delay example in
Section IV and the R2 generated-stage figure illustrate the split with
current outputs.

## 8. Generality and initialization wording (source)

All claims of "generic"/"arbitrary TBA" theorems were replaced by: "The
preservation theorems hold for every TBA represented by the formula-indexed
clock interface, independently of whether the automaton was generated by the
MTL translator." The interface paragraph now explains that clocks are the
timed subformulas of a fixed formula `root`, used only as clock names; the
synthetic suite paragraph explains its six-clock root formula.
`init_free_accepts0` holds for every DTA on which the check succeeds; the
former sentences "not a zero-initialization result for arbitrary imported
TBAs" were corrected accordingly (the restriction is that the check may fail,
not that the lemma is specific to generated automata).
The default front end (recur + weak until) is now named with its theorem
`MTL_to_exported_correct0_recur_weak_with` (4 standard axioms, kernel).

## 9. Symbolic before/after and Spot level (added 2026-10-11)

- `evaluation/run_symbolic_before_after.sh` + `make_symbolic_before_after.py`:
  symbolic transitions of the exported automaton with the optimizer disabled
  (`-n 0`) and enabled, both with symbolic grouping. On the 27 configurations
  where the unoptimized export completes: 729 → 356 (51 %); F+N 389 → 228;
  R1–R4 340 → 128; smaller in 17, equal in 10, larger in 0. R5, R6: the
  unoptimized export does not finish in 300 s (optimized: 2.6 s, 40.6 s).
  Symbolic output equals CASAAL's on 24/27 (F5, F6 larger: 25 vs 15; F14
  smaller).
- Earlier attempt to attribute reductions by comparing `compile(f)` with the
  column `sym_trans` was discarded: `sym_trans` is counted after the final
  symbolic grouping, so the comparison mixes representations.
- `-spot-level low` (default unchanged): total time over the 29 completing
  configurations 162 s → 54 s; N8 107.5 s → 9.2 s; sizes identical except F5,
  F6 (symbolic 25 → 27). R6 unaffected (optimizer/export-bound).

## 10. Consistency of the grouped counts in the two articles

`evaluation/run_chain.sh` runs the tool with the front end of the translation
article (`-noweak -norecur`) and the optimizer disabled (`-n 0`), and writes
`evaluation/chain_noweak.tsv` (explicit transitions of compile(f) and symbolic
transitions). On the 23 configurations that complete within 300 s (F1--F14,
R1--R4, N1--N8), both columns equal the "explicit" and "grouped" columns of the
translation article's `grouped_edges.tsv`. R5 does not finish within 300 s
under this setting; R6--R8 do not build. The symbolic counts of Section 5 use the default
front end (weak-until and recurrence rewritings enabled) and are therefore not
comparable with the grouped column of the translation article for the F and R families.
