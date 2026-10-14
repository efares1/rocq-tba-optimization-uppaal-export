# Evaluation

## Run of 2026-10-13/14

`bash run_all.sh` repeats the tool-dependent steps (full and `-noweak` runs,
boundary tests, Spot validation, interface check, acceptance encodings, random
formulas, export policies, TChecker comparisons, random differences, symbolic
before/after) with the tool of `../tool`, after the change of the printing of
the negated event atom (`!"p"`, option `-explicit-neg`, default; `-indep-neg`
is the previous printing) and of the acceptance convention for unmarked CASAAL
automata in `tck_product.py`.  The log is `run_all.log`; the tables are
regenerated with `make_tables.py`, `make_optimizer_table.py`,
`make_optimizer_aggregate_table.py`, `make_symbolic_before_after.py` and
`make_export_policies_table.py`.  Not repeated: the synthetic TBA suite and
the CASAAL runs themselves (their outputs in `casaal_out*` are reused).
`ours_results_low.tsv`, `ours_runs_low.tsv`, `spot_det_low.tsv` and
`machine_low.txt` are one repetition with `-spot-level low`
(`REPS=1 OPTS="-spot-level low" SUFFIX=_low bash run_full_eval.sh`).
The run is archived in `runs/2026-10-14-full-eval-v3/`.

## Files

- `formulas.tsv`: the benchmark formulas (F1–F14, F3b, and the families
  R1–R8 and N1–N8), in the syntax of `mtl2tba` and in the syntax of CASAAL
  (`~` marks a CASAAL encoding that is not equivalent: CASAAL has no hatted
  operators).  R1 is F1 with renamed propositions.
- `run_full_eval.sh`: builds the tool in `tool/_build` (without deleting a
  fixed home-directory path) and runs `mtl2tba -stats` on every
  formula (Linux/WSL, requires Spot, dune, menhir): up to `REPS` attempts per
  formula, stopping after the first non-success; limit 300 s per run (`LIMIT`),
  peak memory (`/usr/bin/time`),
  times of the Spot, optimization, and export stages, the verified check
  `init_free`, and one run with Spot option `-B --small` instead of `-B -D`.
  Writes `ours_runs.tsv` (every run), `ours_results.tsv` (medians, via
  `summarize_runs.py`), `spot_det.tsv` (Spot with `-B -D`), `machine.txt`, and
  the automata in `out/`.  `OPTS=-noweak SUFFIX=_noweak bash run_full_eval.sh`
  runs the ablation without the weak until (`ours_results_noweak.tsv`,
  `spot_det_noweak.tsv`).
- `run_casaal.py`: runs CASAAL (Windows executable) on every formula (option
  `--runs=5`: median of five runs, used for the article); writes
  `casaal_results.tsv` and the relevant manifest (`casaal_manifest.txt`, or
  `casaal_manifest_exclusive.txt` with `--exclusive`), which records supplied
  executable/DLL names, sizes, and SHA-256 hashes. The supplied executable has
  no Windows version metadata, so the hashes identify the tested build. With
  `--exclusive`, the formula is conjoined with
  `[](!(a /\ b))` for every pair of distinct propositions, so that CASAAL
  reads at most one proposition per position, the event semantics of
  `mtl2tba` (common semantic domain); writes `casaal_exclusive.tsv`.
- `make_tables.py`: joins the results into `results.tsv` and regenerates the
  LaTeX tables included in the main article: `results_table.tex`,
  `scaling_table.tex`, and `formulas_table.tex`.
- `boundary_tests.tsv`, `run_boundary_tests.sh`, `check_boundaries.py`:
  implementation checks at strict and non-strict boundaries and with
  overlapping activations.  The tool translates the negation of a safety
  requirement; `check_boundaries.py` simulates the exported automaton on
  finite timed words, with clocks starting at 0 as in UPPAAL, and checks that
  the accepting sink (violation) is reached exactly when expected.  These
  checks validate the implementation; they do not replace the proof.
- Weak until: the clauses of upper-bounded hatted Until use a weak until,
  as part of the translation (proved: `weak_correct`, and the end-to-end
  theorems `MTL_to_exported_correct_recur_weak_with` and
  `MTL_to_exported_correct0_recur_weak_with`).  The option `-noweak`, used
  for measurements only, keeps a strong Until; `OPTS=-noweak
  SUFFIX=_noweak bash run_full_eval.sh` produces `ours_results_noweak.tsv`,
  which `make_tables.py` compares with `ours_results.tsv`
  (`weak_table.tex`).  The acceptance-encoding experiment extracts the weak
  formula with the default tool option and the strong formula with
  `-noweak`, then runs Spot with each acceptance mode.
- `run_spot_validation.sh`: translation validation of the Spot step, the only
  hypothesis of the end-to-end theorem.  The tool runs unchanged, with
  `-spot spot_tee.sh`, a wrapper that calls `ltl2tgba` with the arguments of
  the tool and keeps the clocked-LTL formula T(f) and the LBTT automaton; an
  independent translator, ltl2ba 1.3 (Gastin and Oddoux), translates T(f)
  and its negation after a common renaming of the atoms, and autfilt checks
  that A x ltl2ba(!T(f)) and complement(A) x ltl2ba(T(f)) are empty, so that
  A accepts exactly the models of T(f); only A is complemented.  The numbers of
  states and transitions read by the tool are compared with those of
  `autfilt`.  ltl2ba must be compiled with enlarged formula buffers (see the
  header of the script), since T(f) exceeds 4096 characters on N7 and N8.
  Result (`spot_validation.tsv`): both checks succeed on the 25
  configurations other than R6 to R8 and N6 to N8, on which ltl2ba does not
  answer within 300 s.
- `run_interface_check.sh`: check of the trusted interface with Spot (option
  `-check` of the tool): Spot reads the same formula from the text given to
  `ltl2tgba` and from a second, independent prefix printer of T(f), and the
  automaton built by the reader is equivalent to the raw output of Spot
  (`autfilt --equivalent-to`).  Result (`interface_check.tsv`): both checks
  pass on 30 configurations; R8 reaches the 300-second Spot timeout.
- `tf_sizes.tsv`: size of the formula T(f) given to Spot (characters, atoms).
- `run_acceptance_eval.sh`: size of the automaton returned by Spot for every
  configuration, with strong Until (`-noweak`) or weak Until (default) in the
  upper-bounded clauses, and
  with state-based (`-B`, used by the tool), transition-based (`-b`), and
  transition-based generalized (`--tgba`) Büchi acceptance; writes
  `acceptance_results.tsv`.  Each Spot invocation has a 120-second limit and
  a 2-second hard-kill grace period.
- `init_comparison.tsv`: measures with and without the option `-init`.
- `check_initial_values.py`: the same condition as the verified check
  `init_free`, on the dot files (kept for reference).
- Timed-language comparison with CASAAL, with the timed Büchi emptiness
  checker `tck-liveness` of TChecker 0.8
  (https://github.com/ticktac-project/tchecker, algorithm `couvscc`):
  - `tck_product.py`: builds, in the input format of TChecker, the product of
    automata exported by `mtl2tba` (UPPAAL `.xml`, or `<base>_tba.dot`
    written with `-tba`) and automata printed by CASAAL (`.gv`); two
    processes restrict it to infinite words with one event per position,
    strictly increasing timestamps, and divergent times; the transition marks
    of CASAAL are encoded by committed intermediate locations.  An XML input
    named with the `SYS:` prefix is treated as a system model without its own
    Büchi acceptance obligation.  The script is not verified.
  - `run_casaal_neg.py`: CASAAL on the negation of every formula
    (`casaal_out_neg/`; `casaal_out_x/` holds the automata of the formulas);
    also writes `casaal_manifest_neg.txt`.
  - `run_tck_equivalence.sh`: for every configuration, the products
    P1 = ours(f) x CASAAL(!f), P2 = CASAAL(f) x ours(!f), and
    S = ours(f) x ours(!f) must be empty, and C = ours(f) x CASAAL(f) must not
    be (`tck_equivalence.tsv`).  Result: as expected on F1-F12, R1, N1-N4;
    TChecker does not conclude within 600 s on the others, whose exported
    automata contain difference constraints (F14, R2-R6) or are large (N5,
    N6); our tool exceeds its limit on R7, R8 and on the negations of N7, N8.
  - `run_tck_compiled.sh`: the same checks on the automaton compile(f)
    (after relaxation and reset completion, no invariant, no difference
    constraint) for F14 and R2-R6 (`tck_compiled.tsv`): as expected on F14,
    R2, R3; no conclusion within 600 s on R4-R6.
  - `random_formulas.py`: 100 random formulas (seed 1, depth at most 3,
    propositions p, q, r, bounds up to 3, unbounded operators included) and
    their CASAAL automata (`random_formulas.tsv`, `casaal_out_rand/`);
    `run_random_diff.sh`: P1, P2, S with a limit of 120 s
    (`random_diff.tsv`).  Result (2026-10-13/14): all empty on 96 formulas; on 4
    formulas (X31, X62, X64, X83), some checks do not conclude within the
    limit and the others report emptiness; none is non-empty.  X74 was
    non-empty for P1 with the first version of `tck_product.py`: CASAAL
    prints no acceptance mark for the eventuality <>(q & r), which no
    transition fulfills on the common domain, and the script read an
    automaton without marks as accepting every run.  The script now treats a
    state whose label has an unbounded F or U as non-accepting (the language
    is empty if no state is accepting) when no mark is printed; the regression
    test is `test_tck_product_acceptance.py`.  The reading is an
    interpretation of CASAAL's output.
  - `case_study/liveness_case.sh`: liveness use of the exported automaton:
    the automaton of the negation of [](p -> <> q) composed with a system that
    answers within 4 time units (empty product) and with one that may stay
    busy forever (accepting cycle found) (`case_study/liveness_results.txt`).
- Rewriting of lower bounds under []<> and <>[] (default since v1.7, option
  `-norecur`; proved: `MTL_to_exported_correct0_recur_weak_with`): among the
  benchmark configurations, it changes only F8.  `rerun_f8.sh` re-runs the
  evaluation on F8 and on the random formulas whose automaton changes
  (`random_changed_by_recur.txt`); the scripts accept `ONLY="F8 ..."`, which
  writes `<file>.only`, merged into the result files by `merge_rows.py`.
- `setup_wsl.sh`: one-time installation of Spot, OCaml, dune, and menhir in
  WSL Ubuntu.

## Steps

1. Windows PowerShell as administrator: `wsl --install -d Ubuntu`, reboot,
   open Ubuntu once and create the Linux user.
2. In Ubuntu, from this folder: `sudo bash setup_wsl.sh`
3. In Ubuntu: `bash run_full_eval.sh` (about one hour, most of it in the
   instances that reach the limit), then `bash run_boundary_tests.sh`
4. On Windows: `python run_casaal.py <casaal folder>` and
   `python run_casaal.py --exclusive <casaal folder>`
5. `python make_tables.py`

The machine and the versions used for the article are in `machine.txt`. The setup script uses mutable Ubuntu package and Spot repositories, so rerunning it later may install different versions.

- `run_random_opt.sh` (`random_opt.tsv`): the 100 random formulas of `random_formulas.tsv` through the default pipeline with the optimizer; sizes before and after, exported sizes, `init_free`.
- `run_export_policies.sh` (`export_policies.tsv`, `export_policies_table.tex`): maximal-delay and `-all-copy` exports side by side on every configuration of `formulas.tsv`.
