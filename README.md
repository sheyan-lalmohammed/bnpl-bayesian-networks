# Bayesian and Dynamic Bayesian Networks for Credit Risk Modeling in Buy Now, Pay Later

Replication package for the undergraduate thesis:

> **Bayesian and Dynamic Bayesian Networks for Credit Risk Modeling in Buy Now, Pay Later**
> Sheyan Lalmohammed — Wharton Research Scholars, The Wharton School, University of Pennsylvania (May 2025).
> Faculty Advisor: Paul Sabin.

This repository contains the code and the (small) result and reference files needed to reproduce
every table and figure in the paper. It uses [Lending Club](https://www.kaggle.com/datasets/wordsforthewise/lending-club)
peer-to-peer loan data as a proxy for BNPL loans, builds discrete Bayesian Networks (BNs) and
Dynamic Bayesian Networks (DBNs) with [`pgmpy`](https://pgmpy.org/), and evaluates them with a
cost-sensitive bootstrap over multiple classification thresholds.

---

## 1. What is in this repository

The code is organized into numbered stages that follow the paper end to end. Every stage is a
folder of Jupyter notebooks plus the small CSV result files those notebooks produce (the actual
numbers reported in the paper's tables). The large raw/intermediate data files are **not** stored
here (they are multiple GB and exceed GitHub limits) — see [`data/README.md`](data/README.md) for
how to obtain or regenerate them.

```
.
├── 00_raw_database/              Build the SQLite loan database from raw Lending Club CSVs
├── 01_data_preparation/          Clean data, build the BNPL-like proxy + samples, discretize, macro data
│   └── reference_outputs/        Small outputs of stage 01 (macro data, summary stats, CPD tables)
├── 02_naive_bayesian_networks/   Naive BNs (Personal / Loan-Credit / Macro / Mixed)   → Tables 6–9, A.4
├── 03_static_bn_structure_learning/  BNs with Hill-Climb structure learning           → Tables 10–17, A.5
├── 04_homogeneous_dbn/           Homogeneous DBNs with structure learning             → Tables 18–19, A.6
├── 05_nonhomogeneous_dbn/        Partially non-homogeneous DBNs (NH-DBN)              → Tables 20–21
├── figures/                      Standalone figures used in the paper
├── data/                         (git-ignored) staging area for large data files + instructions
├── PAPER_RESULTS_MAP.md          Table-by-table / figure-by-figure map to the exact code + output file
├── requirements.txt              Python dependencies
└── README.md                     This file
```

Inside stages 02–05 the work is split by **sample** (`original_sample`, `sample1`, `sample2`),
matching the three age-group samples defined in Table 1 of the paper (see
[Samples](#4-the-three-samples) below). The **`original_sample`** folders reproduce the tables in
the main body of the paper; `sample1` / `sample2` reproduce the corresponding appendix tables.

---

## 2. Environment setup

The analysis was run with Python 3.11+ and `pgmpy >= 1.0` (the notebooks use the
`pgmpy.estimators.StructureScore` API for `BIC`, `AIC`, `BDeu`, `BDs` and `ExpertKnowledge`).

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab                        # or: jupyter notebook
```

---

## 3. How to reproduce the results

The pipeline is sequential — each stage consumes the output of the previous one. Because the
notebooks read/write data files **by bare filename in their own working directory**, run each
notebook from inside its own folder and place the required input CSV next to it (see the "Inputs"
note in each stage and in [`data/README.md`](data/README.md)).

| Order | Stage | What it does | Key output |
|------|-------|--------------|------------|
| 1 | `00_raw_database` | Loads raw `accepted_*.csv` / `rejected_*.csv` into `loans.db` | `loans.db` (SQLite) |
| 2 | `01_data_preparation/data_cleaning.ipynb` | Filters to BNPL-like loans, infers age & FICO buckets, joins lagged macro data, writes the three rebalanced samples | `rebalanced_age_data*.csv`, `macro_data.csv`, summary statistics |
| 3 | `01_data_preparation/discretizer_*.ipynb` | KBins (k-means, 3 bins) discretization of continuous variables, per sample | `rebalanced_age_data_*discretized*.csv` |
| 4 | `01_data_preparation/data_exploration.ipynb`, `summary_of_rebalanced_data.ipynb` | Aggregate tables and macro-vs-default figures | Tables 3–5, Figure 1, A.1–A.3 |
| 5 | `02_…` → `05_…` | Train each network family and run the cost-sensitive bootstrap | `bootstrap_results*.csv` (the paper's tables) |

For the temporal (DBN / NH-DBN) stages, the static discretized data is first expanded to five
quarterly time steps (`subsample_creation_bnpl_dynamic.ipynb` and the DBN notebooks themselves
build the dynamic variables described in the paper's "Extending Static Data to Dynamic Components").

**Reproducibility note.** The evaluation is a non-parametric bootstrap; exact digits can vary
slightly run-to-run because train/test resampling and Hill-Climb structure search are stochastic.
The committed `bootstrap_results*.csv` files are the runs reported in the paper, so you can diff
your regenerated numbers against them.

---

## 4. The three samples

The paper stress-tests its assumptions against three different age-group distributions (Table 1).
Each maps to a data-file naming convention and to a subfolder in stages 02–05:

| Paper name | Folder suffix | Discretized data file | Notes |
|------------|---------------|-----------------------|-------|
| **Original** | `original_sample` | `rebalanced_age_data_original_discretized.csv` (and `..._discretized_original.csv`) | Age distribution inferred directly from the data; **main-body tables** |
| **Sample 1** | `sample1` | `rebalanced_age_data_discretized.csv` | "Age-change-one" reweighting; appendix tables |
| **Sample 2** | `sample2` | `rebalanced_age_data_discretized_two.csv` | Second reweighting; appendix tables |

---

## 5. Model families (paper sections → folders)

- **Naive BN** (`02_…`): star graph, every predictor → `default_indicator`, four variable sets —
  Personal, Loan & Credit, Macroeconomic, and Mixed.
- **Static BN + structure learning** (`03_…`): Hill-Climb Search with a bootstrap edge-stability
  filter (edges kept if present in > 95% of 100 bootstrap fits), under strict vs. loose variable
  ordering (`_two` files = the loosened default-edge restriction) and four structure scores
  (BIC / AIC / BDeu / BDs).
- **Homogeneous DBN** (`04_…`): five-slice DBN, intra- and inter-slice edges learned once and
  shared across time.
- **NH-DBN** (`05_…`): partially non-homogeneous DBN, edges allowed to differ between slices;
  `nhdbn_strict_order.ipynb` and `nhdbn_loose_order.ipynb`.

See [`PAPER_RESULTS_MAP.md`](PAPER_RESULTS_MAP.md) for the exact notebook and output file behind
each numbered table and figure.

---

## 6. Data availability & citation

The underlying Lending Club "accepted/rejected 2007–2018Q4" dataset is publicly available (e.g. on
Kaggle) and is **not redistributed here**. All derived BNPL-like proxy construction is fully
specified in the stage-01 notebooks. See [`data/README.md`](data/README.md).

If you use this code, please cite the thesis (see the top of this file). Code is released under the
MIT License (see [`LICENSE`](LICENSE)).
