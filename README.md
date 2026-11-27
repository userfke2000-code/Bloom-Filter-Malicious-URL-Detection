# Bloom Filter for URL Checking (Probability & Statistics, Project 2)

A Bloom filter implemented from scratch and tested on the abuse.ch URLhaus
malicious-URL list. We measure memory and false-positive rate for different
filter sizes (m) and numbers of hash functions (k), and compare against theory.

## Folder layout
- `data/`      provided raw `urlhaus.csv` and generated train/test splits
- `database/`  SQLite database holding the master list (generated)
- `sql/`       schema and analytical queries
- `src/`       all code
- `notebooks/` exploration and plots
- `reports/`   result tables, figures, final report

## How to run
1. Use the provided `data/urlhaus.csv` URLhaus dump, last updated 2026-11-22 18:47 UTC. The original `/downloads/csv/` endpoint now returns HTTP 410; keep this exact copy so the dataset, counts, and reported results remain reproducible.
2. Run, in order (random seed fixed at 42 everywhere):

```bash
pip install -r requirements.txt
python src/inspect_dataset.py      # data-quality numbers
python src/clean_dataset.py        # dedupe, 80/20 train/test split
python src/load_db.py              # master list -> SQLite
python src/experiment_fpr.py       # theory vs observed FPR grid + plots
python src/benchmark_lookup.py     # Bloom vs set vs SQL: speed and memory
```

## Design
- Members = the 80% training split (n = 139,832 URLs).
- Non-members (to measure false positives) = 200,000 synthetic URLs plus the 20% held-out
  real URLs. Neither group is in the filter, so every "hit" is a false positive.
- Hashing: one blake2b call -> (h1, h2); positions are (h1 + i*h2) mod m (double hashing).
- Theory: FPR = (1 - e^(-kn/m))^k, optimal k = (m/n) ln 2.

## Status
- [x] Data cleaning and split
- [x] Bloom filter + counting Bloom filter classes (`src/bloom_filter.py`)
- [x] SQLite master list
- [x] Theory-vs-observed experiment
- [x] Lookup/memory benchmark
- [ ] 5+ analytical SQL queries and ER diagram
- [ ] Data-quality log, notebook, report, presentation
- [ ] Tests for the counting Bloom filter (deletion)
