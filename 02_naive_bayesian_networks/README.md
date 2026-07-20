# Stage 02 — Naive Bayesian Networks

Star-graph BNs where every predictor points into `default_indicator`. One notebook
(`bn_static_naive.ipynb`) reproduces all four variants by swapping the `cols`/`edges` block:
Personal, Loan & Credit, Macroeconomic, and Mixed (paper models B_p, B_LC, B_M, B_MI).

- `original_sample/` → **Tables 6–9** (main body). `bootstrap_results_personal.csv` = Table 6 (Personal),
  `bootstrap_results.csv` = Table 7 (Loan & Credit).
- `sample1/` → Appendix A.4 (+ `bootstrap_results_macro.csv`, `bootstrap_results_credit.csv`,
  example CPD tables in `example_cpd_tables/`).
- `sample2/` → Appendix A.4.

**Input:** the discretized CSV for the corresponding sample (see `../data/README.md`).
