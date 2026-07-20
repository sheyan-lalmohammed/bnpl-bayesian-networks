# Stage 00 — Raw database creation

Loads the raw public Lending Club exports into a single SQLite database used by all later stages.

- **`main_database_creation.ipynb`** — reads `accepted_2007_to_2018Q4.csv` and
  `rejected_2007_to_2018Q4.csv`, writes them into `loans.db`.

**Inputs (obtain externally, see `../data/README.md`):** `accepted_2007_to_2018Q4.csv`,
`rejected_2007_to_2018Q4.csv`
**Output:** `loans.db`
