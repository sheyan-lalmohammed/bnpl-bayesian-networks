# Stage 04 — Homogeneous DBN with structure learning

Five-slice Dynamic Bayesian Networks whose intra- and inter-slice edges are learned once and shared
across time.

- `original_sample/dbs_homogenous.ipynb` → **Table 18** (`bootstrap_results_dbn.csv`),
  **Table 19** (`bootstrap_results_structure_learning_DBN_loose_structure.csv`), and **Appendix A.6**
  including the ±100-point FICO variant (`..._FICO100.csv`).
- `development/` — exploratory / earlier DBN notebooks (`dbn_homogenous.ipynb`, `dbn_training.ipynb`,
  `dbn_training_static_bayesian_network.ipynb`) kept for provenance; not required to reproduce the
  final tables.

**Input:** `rebalanced_age_data_original_discretized.csv` (see `../data/README.md`).
