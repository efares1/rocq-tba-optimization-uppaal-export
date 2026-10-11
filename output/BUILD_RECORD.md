# Build record of the snapshot tagged `v1.0`

Snapshot: the commit tagged `v1.0` of this repository (the tag is moved to the commit that
contains this file). Earlier transcripts are not part of the repository.

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

## PDFs (as in the commit)
| File | Pages | SHA-256 |
|---|---|---|
| `paper2.pdf` (= `output/pdf/paper2.pdf`) | 19 | `6f00baa930a0475c5c39ebb19a6e60650694c665d772678c9d3983b4a4b91619` |
| `paper2_supp.pdf` (= `output/pdf/paper2_supp.pdf`) | 7 | `003abe07cdf0cf3347cbecc2e52abbea28311c8a9e2874fb19273a0fef84ed40` |

Rebuild with `build.ps1` (Windows, MiKTeX) or `reproduce.sh` (proofs, tool, evaluation).
PDF byte hashes depend on build timestamps; the page counts and extracted text are stable.
