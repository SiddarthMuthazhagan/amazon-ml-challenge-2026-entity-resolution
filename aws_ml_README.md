# Amazon ML Challenge 2026 — Business Entity Resolution

Solution for the Business Entity Resolution track (Amazon ML Challenge 2026, hosted via Unstop).

## Problem
Match business records from a reference source (Source 1) to records in two independent sources (Source 2, Source 3) that refer to the same real-world business — handling noise (abbreviations, transliteration, typos, address variation) and an open-set country feature (train: US/India, test: +France).

## Approach
1. **Normalization** — lowercase, strip accents/diacritics, remove legal suffixes (Pvt Ltd, LLC, etc.)
2. **Blocking** — inverted index (country → token → entity_ids) on name and address tokens, avoiding brute-force comparison across millions of records
3. **Candidate scoring** — weighted fuzzy similarity (RapidFuzz token_sort_ratio) on name (70%) and address (30%)
4. **Threshold tuning** — macro-averaged F0.5 (matching the competition's exact metric, including singleton-case scoring) swept across thresholds to find the optimal cutoff
5. **Output** — `matching_results.tsv` and `candidate_pairs.tsv` generated per the required schema and validated with the provided `validate_submission.py`

## Results
- Best threshold: 0.76 (macro F0.5 on a training sample: 0.5715)
- Leaderboard F0.5: 0.502

## How to run
1. Place challenge data under `dataset/train/` and `dataset/test/`
2. `pip install -r requirements.txt`
3. Run `pipeline.ipynb` top to bottom
4. Outputs land in `output/matching_results.tsv` and `output/candidate_pairs.tsv`

## Notes
- Dataset files are not included in this repo per competition terms.
- Built under strict constraints: no external APIs, no external datasets, no internet lookups — only provided challenge data.
