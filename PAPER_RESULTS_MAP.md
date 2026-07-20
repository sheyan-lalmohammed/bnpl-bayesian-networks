# Paper → Code Map

Every numbered table and figure in the thesis, mapped to the notebook that produces it and the
committed output file that holds the reported numbers. Paths are relative to the repository root.

Legend for output files: `bootstrap_results*.csv` files each contain the mean and 95%/90% bootstrap
confidence interval for Accuracy, FPR, FNR, and per-class Precision/Recall/F1 across the decision
thresholds 0.15–0.50 — i.e. exactly one paper table each.

---

## Methods — data construction, samples, discretization

| Paper item | Notebook | Output |
|------------|----------|--------|
| Build SQLite database from raw Lending Club CSVs | `00_raw_database/main_database_creation.ipynb` | `loans.db` |
| BNPL-like filtering, age inference, FICO buckets, macro join, sample construction | `01_data_preparation/data_cleaning.ipynb` | `rebalanced_age_data*.csv`, `macro_data.csv`, summary-statistics CSVs |
| Table 1 & 2 — Age-group by sample (breakdown %, post-balancing counts) | `01_data_preparation/data_cleaning.ipynb`, `summary_of_rebalanced_data.ipynb` | `reference_outputs/age_group.csv` |
| KBins (k-means, 3-bin) discretization | `01_data_preparation/discretizer_original.ipynb` / `discretizer_sample1.ipynb` / `discretizer_sample2.ipynb` | `rebalanced_age_data_*discretized*.csv` |
| Dynamic (5 time-slice) variable construction | `01_data_preparation/subsample_creation_bnpl_dynamic.ipynb`; dynamic build cells inside stage 04/05 notebooks | `bnpl_loans_with_age_change.csv` |

## Methods / Appendix — summary statistics & macro figures

| Paper item | Notebook | Output |
|------------|----------|--------|
| Table 3 — FICO bucket by age group | `01_data_preparation/data_exploration.ipynb` | `reference_outputs/summary_statistics/` |
| Table 4 — Default & fully-paid by age group | `01_data_preparation/data_exploration.ipynb` | `reference_outputs/summary_statistics/` |
| Table 5 — Credit grading by age group | `01_data_preparation/data_exploration.ipynb` | `reference_outputs/summary_statistics/` |
| Figure 1 — Default rate vs. lagged GDP growth | `01_data_preparation/data_exploration.ipynb` | `figures/default_rate_gdp_growth.png` |
| Appendix A.2 — Default rate vs. unemployment / housing / inflation / fed funds | `01_data_preparation/data_exploration.ipynb` | (figures generated inline) |
| Appendix A.3 — Defaults & FICO buckets by age (Samples 1 & 2) | `01_data_preparation/summary_of_rebalanced_data.ipynb` | (generated inline) |
| Discretization bin/CPD reference tables | `01_data_preparation/discretizer_*.ipynb` | `reference_outputs/cpd_tables/` |

## Analysis — Naive Bayesian Networks (stage 02)

The single notebook `bn_static_naive.ipynb` reproduces all four naive variants by swapping the
`cols`/`edges` block (Personal, Loan & Credit, Macroeconomic, Mixed). Output files are named by
variant.

| Paper table | Variant | Notebook | Output file |
|-------------|---------|----------|-------------|
| Table 6 — Naive Personal BN (Original) | Personal (`grade, emp_length, home_ownership, purpose, age_group`) | `02_naive_bayesian_networks/original_sample/bn_static_naive.ipynb` | `original_sample/bootstrap_results_personal.csv` |
| Table 7 — Naive Loan & Credit BN (Original) | Loan+Credit (`loan_amnt, int_rate, installment, pub_rec, revol_bal, revol_util, total_acc, annual_inc, delinq_2yrs, inq_last_6mths, open_acc, fico_bucket`) | `02_naive_bayesian_networks/original_sample/bn_static_naive.ipynb` | `original_sample/bootstrap_results.csv` |
| Table 8 — Macroeconomic BN (Original) | Macro (`gdp_growth, unemployment, inflation, fedfunds, housing_price`) | `02_naive_bayesian_networks/sample1/bn_static_naive.ipynb` (macro cell) | `sample1/bootstrap_results_macro.csv` |
| Table 9 — Mixed BN (Original) | Mixed (macro + credit + personal subset) | `02_naive_bayesian_networks/*/bn_static_naive.ipynb` (mixed cell) | `sample1/bootstrap_results_credit.csv` (credit/mixed variant) |
| Appendix A.4 — Personal BN, Sample 1 | Personal | `02_naive_bayesian_networks/sample1/bn_static_naive.ipynb` | `sample1/bootstrap_results_personal.csv` |
| Appendix A.4 — Personal BN, Sample 2 | Personal | `02_naive_bayesian_networks/sample2/bn_static_naive.ipynb` | `sample2/bootstrap_results_personal.csv` |
| Example CPD tables (Section A.4) | — | — | `sample1/example_cpd_tables/` |

> The four naive variable sets are exactly those defined in the paper (models `B_p`, `B_LC`, `B_M`,
> `B_MI`). Each `cols` list in the notebook corresponds to one model; re-running a cell overwrites
> that model's output CSV.

## Analysis — Static BN with structure learning (stage 03)

Two notebooks cover the strict/loose ordering and the two default-edge restrictions; each sweeps
the four structure scores (BIC/AIC/BDeu/BDs). `_two` in a filename = the loosened restriction on
required edges into `default` (paper's "Strict/Loose Order BN **2**").

| Paper tables | Notebook | Output files |
|--------------|----------|--------------|
| Table 10, 11 — Strict Order BN 1 & 2, BIC | `03_static_bn_structure_learning/original_sample/bn_struct_learning.ipynb` (+ `_two`) | `results_BN1_ordering/bootstrap_results_structure_learning_BIC.csv`, `results_BN2_restriction/bootstrap_results_structure_learning_BIC_two.csv` |
| Table 12, 13 — Loose Order BN 1 & 2, BIC | `bn_struct_learning.ipynb` (loose-restriction cells), `bn_struct_learning_two.ipynb` | `..._BIC_Structure_two.csv`, `..._BIC_two_loose_structure.csv` |
| Table 14, 15 — Strict Order BN 1 & 2, AIC | same notebooks | `..._AIC.csv`, `..._AIC_two.csv` |
| Table 16, 17 — Loose Order BN 1 & 2, AIC | same notebooks | `..._AIC_Structure_two.csv`, `..._AIC_two_loose_structure.csv` |
| Appendix A.5 — BDeu & BDs (all orders) | same notebooks | `..._BDeu*.csv`, `..._BDs*.csv` in `results_BN1_ordering/` and `results_BN2_restriction/` |
| Figures 6–15 — learned network structures | structure-plot cells in the same notebooks | `bayesian_network.png` (overwritten per plot) |
| Sample 1 structural learning | `03_static_bn_structure_learning/sample1/BNTrainingStructuralLearningFullDataset_*.ipynb` | (generated inline) |

## Analysis — Homogeneous DBN (stage 04)

| Paper item | Notebook | Output |
|------------|----------|--------|
| Table 18 — Strict Order DBN 1, BIC | `04_homogeneous_dbn/original_sample/dbs_homogenous.ipynb` | `original_sample/bootstrap_results_dbn.csv` |
| Table 19 — Loose Order DBN 1, BIC | `dbs_homogenous.ipynb` | `original_sample/bootstrap_results_structure_learning_DBN_loose_structure.csv` |
| Appendix A.6 Table 1 — DBN Loose 1, looser info restriction | `dbs_homogenous.ipynb` | `bootstrap_results_structure_learning_DBN_loose_structure.csv` |
| Appendix A.6 Table 2 — DBN Loose 1, ±100-point FICO swings | `dbs_homogenous.ipynb` (FICO100 cells) | `bootstrap_results_structure_learning_DBN_loose_structure_FICO100.csv` |
| Figures 16–18 — DBN intra/inter structures & validity model | structure-plot cells in `dbs_homogenous.ipynb` | `bayesian_network.png` |
| Development / exploratory DBN notebooks | `04_homogeneous_dbn/development/dbn_homogenous.ipynb`, `dbn_training.ipynb`, `dbn_training_static_bayesian_network.ipynb` | — |

## Analysis — Non-Homogeneous DBN (stage 05)

| Paper table | Notebook | Output |
|-------------|----------|--------|
| Table 20 — Strict Order NH-DBN, BIC | `05_nonhomogeneous_dbn/original_sample/nhdbn_strict_order.ipynb` | `bootstrap_results_nhdbn.csv` |
| Table 21 — Loose Order NH-DBN, BIC | `05_nonhomogeneous_dbn/original_sample/nhdbn_loose_order.ipynb` | `bootstrap_results_nhdbn_two.csv` |
| Figures 20–21 — NH-DBN intra/inter structures | structure-plot cells in the NH-DBN notebooks | `bayesian_network.png` |
