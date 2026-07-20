# Data files

The large data files used by this project are **not** committed to git — several are hundreds of MB
to multiple GB and exceed GitHub's file-size limits. This folder is a staging area; the notebooks
read and write data by bare filename **in their own working directory**, so you generally place the
regenerated CSV next to the notebook that needs it (or symlink it).

## 1. Source data (obtain externally)

| File | Approx. size | Source |
|------|--------------|--------|
| `accepted_2007_to_2018Q4.csv` | ~1.5 GB | Lending Club "accepted loans 2007–2018Q4" (public; e.g. Kaggle: *wordsforthewise/lending-club*) |
| `rejected_2007_to_2018Q4.csv` | ~1.1 GB | Lending Club "rejected loans 2007–2018Q4" (same source) |

Download these and place them next to `00_raw_database/main_database_creation.ipynb`.

## 2. Generated data (produced by the notebooks)

Run the stages in order; each file below is created by an earlier stage and consumed by a later one.

| File | Created by | Consumed by |
|------|-----------|-------------|
| `loans.db` | `00_raw_database/main_database_creation.ipynb` | `01_data_preparation/data_cleaning.ipynb` |
| `macro_data.csv` | `data_cleaning.ipynb` (also committed under `01_data_preparation/reference_outputs/`) | `data_cleaning.ipynb` (macro join) |
| `rebalanced_age_data.csv` (Sample 1) | `data_cleaning.ipynb` | `discretizer_sample1.ipynb`, `data_exploration.ipynb` |
| `rebalanced_age_data_two.csv` (Sample 2) | `data_cleaning.ipynb` | `discretizer_sample2.ipynb` |
| `rebalanced_age_data_original.csv` (Original) | `data_cleaning.ipynb` | `discretizer_original.ipynb` |
| `rebalanced_age_data_discretized.csv` (Sample 1) | `discretizer_sample1.ipynb` | stage 02/03 `sample1` notebooks |
| `rebalanced_age_data_discretized_two.csv` (Sample 2) | `discretizer_sample2.ipynb` | stage 02 `sample2` notebook |
| `rebalanced_age_data_original_discretized.csv` and `rebalanced_age_data_discretized_original.csv` (Original) | `discretizer_original.ipynb` | stage 02/03/04/05 `original_sample` notebooks |
| `rebalanced_age_data_original_discretized_100FICO.csv` | `discretizer_original.ipynb` (±100 FICO variant) | `04_homogeneous_dbn` FICO100 cells |
| `bnpl_loans_with_age_change.csv` | `subsample_creation_bnpl_dynamic.ipynb` | temporal DBN notebooks (stages 04–05) |

### Naming note (Original sample)

Two Original-sample discretized filenames appear in the code and refer to the same discretized
Original data produced by `discretizer_original.ipynb`:

- `rebalanced_age_data_discretized_original.csv`  — used by `03_static_bn_structure_learning`
- `rebalanced_age_data_original_discretized.csv`  — used by `04_homogeneous_dbn` and `05_nonhomogeneous_dbn`

If you regenerate the Original discretized data under one name, copy it to the other so all Original
notebooks find their input.

## 3. Committed reference outputs

Small, paper-relevant outputs **are** kept in the repo so results can be inspected without rerunning
the full pipeline:

- `01_data_preparation/reference_outputs/summary_statistics/` — numeric & categorical summaries
- `01_data_preparation/reference_outputs/cpd_tables/` — discretization bin edges / CPD reference tables
- `01_data_preparation/reference_outputs/{macro_data,age_group,default_indicator,unemployment_rate_t1_disc}.csv`
- `bootstrap_results*.csv` next to each model notebook — the exact numbers in the paper's tables
