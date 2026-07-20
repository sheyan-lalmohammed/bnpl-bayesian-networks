# Stage 01 — Data preparation

Turns the raw Lending Club database into the BNPL-like proxy datasets, the three age-group samples,
the discretized model inputs, and the descriptive tables/figures.

| Notebook | Purpose | Paper items |
|----------|---------|-------------|
| `data_cleaning.ipynb` | BNPL-like filtering, age inference, FICO buckets, lagged macro join, sample construction, summary statistics | Methods; Tables 1–2 |
| `data_exploration.ipynb` | Aggregate tables and macro-vs-default figures | Tables 3–5; Figure 1; Appendix A.2 |
| `summary_of_rebalanced_data.ipynb` | Per-sample default/FICO breakdowns | Appendix A.3 |
| `discretizer_original.ipynb` | KBins (k-means, 3-bin) discretization — Original sample | — |
| `discretizer_sample1.ipynb` | Discretization — Sample 1 | — |
| `discretizer_sample2.ipynb` | Discretization — Sample 2 | — |
| `subsample_creation_bnpl_dynamic.ipynb` | Expands static data to 5 quarterly time slices for the DBNs | Methods ("Extending Static Data to Dynamic Components") |

**Inputs:** `loans.db` (from stage 00).
**Outputs:** `rebalanced_age_data*.csv`, `*_discretized*.csv`, `macro_data.csv`, and the files in
`reference_outputs/` (committed).
