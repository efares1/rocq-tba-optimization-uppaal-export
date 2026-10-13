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

## PDFs (as in the commit)
| File | Pages | SHA-256 |
|---|---|---|
| `paper2.pdf` (= `output/pdf/paper2.pdf`) | 19 | `249517447415f74168eced18d0dd40fb7221b785eb93134b1ada6a8fd9261b75` |
| `paper2_supp.pdf` (= `output/pdf/paper2_supp.pdf`) | 7 | `01993da89725cdecfd71aab6b05c938c6ff414dc36e8e05df77d4489f0e941cf` |

Rebuild with `build.ps1` (Windows, MiKTeX) or `reproduce.sh` (proofs, tool, evaluation).
PDF byte hashes depend on build timestamps; the page counts and extracted text are stable.
