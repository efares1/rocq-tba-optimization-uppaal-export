# Build record of the snapshot tagged `v1.1`

Snapshot: the commit tagged `v1.1` of this repository, which is the revision described by
the article. The tag `v1.0` is an earlier snapshot and is not the described revision. File
digests of the sources, proofs, evaluation tables and PDFs are in `SNAPSHOT_MANIFEST.txt`.

| Component | Version / value |
|---|---|
| Rocq Prover | 9.0.1 (Rocq Platform 2025.08, OCaml 4.14.2), `coqc`, `coqchk` |
| OCaml (tool build), dune | 5.4.0, 3.20.2 (WSL 2, Ubuntu) |
| Spot (`ltl2tgba`) | 2.16 |
| TeX | MiKTeX 25.12, pdfTeX 3.141592653-2.6-1.40.28, class `ieeeaccess.cls` |

## Proofs
`output/rocq/` was produced from a fresh copy of `proof/` and `tool/` with all compiled
objects removed first. `make` (including extraction to `tool/src/optim.ml`, identical to the
repository file), the audit `Audit_Revision_20261010.v` (17 `Print Assumptions` blocks,
including the two `_recur_weak_with` theorems) and `coqchk` on the ten modules all exit 0.
SHA-256 of every `proof/*.v` file is in `output/rocq/build_info.txt`.

## Evaluation re-run of 2026-10-12/13
After the explicit printing of negated event atoms (`-explicit-neg`) and the weakened
backend contract, the full evaluation was repeated; the archived runs are
`evaluation/runs/2026-10-12-full-eval-v2/`, `evaluation/runs/2026-10-13-generated-split/`
and `evaluation/runs/2026-10-13-tchecker-before-after/`. Report: `PAPER2_EVALUATION_REPORT.md`,
section 11.

## Merging of locations under the entry invariant, 2026-10-13
`merge_states_all` (`MTL_to_TBA_Optimizations.v`) compares the guards of two locations by
subsumption under the entry invariant of the merged location (`entry_lb_loop_sound`) instead of
by equality. Fresh build of `proof/` (no compiled objects), audit and `coqchk` on the ten
modules exit 0; the audit output and the `coqchk` context summary are identical to the previous
ones (four standard axioms for `merge_states_all_accepts`); `tool/src/optim.ml` was regenerated
by the extraction and pruning of the Makefile. `evaluation/test_entry_invariant_merging.sh`
checks the optimized sizes of F1, F11, F12, F13 and the TChecker checks P1, P2, S.
The tables and aggregate figures of the article are those of the run of 2026-10-12/13 until the
full evaluation is repeated with this optimizer.

## PDFs (as in the commit)
| File | Pages | SHA-256 |
|---|---|---|
| `paper2.pdf` (= `output/pdf/paper2.pdf`) | 20 | `a448c6ed9dd0e3784b4d9761256fe703fae677f916b0a0781a928f9266e9468d` |
| `paper2_supp.pdf` (= `output/pdf/paper2_supp.pdf`) | 7 | `1ac2d59dbe6fa524fcd7b1a8c16d766ca7fc217f118e9981712f823faccd0111` |

Rebuild with `build.ps1` (Windows, MiKTeX) or `reproduce.sh` (proofs, tool, evaluation).
PDF byte hashes depend on build timestamps; the page counts and extracted text are stable.
