# Stage 03 — Static BN with structure learning

Hill-Climb Search with a bootstrap edge-stability filter (edges kept if present in > 95% of 100
bootstrap fits), swept over four structure scores (BIC / AIC / BDeu / BDs), under strict vs. loose
variable ordering and two default-edge restrictions.

- `original_sample/bn_struct_learning.ipynb` — BN "1" (required edges into default).
- `original_sample/bn_struct_learning_two.ipynb` — BN "2" (loosened default-edge restriction);
  filenames containing `_two` / `_loose_structure` correspond to this variant.
- Results live in `results_BN1_ordering/` and `results_BN2_restriction/` → **Tables 10–17** and
  **Appendix A.5**. See `../PAPER_RESULTS_MAP.md` for the exact score→table→file mapping.
- `sample1/` — Sample 1 structural-learning notebooks.

**Input:** the discretized Original/Sample-1 CSV (see `../data/README.md`).
