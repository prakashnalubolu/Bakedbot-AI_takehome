# BakedBot AI - Take Home Exam
<img width="1359" height="231" alt="image" src="https://github.com/user-attachments/assets/0e13512f-b8e0-4bfa-8034-d4cd01e2c5bb" />
<img width="1338" height="407" alt="image" src="https://github.com/user-attachments/assets/4c7bba0e-0615-4192-812f-9f87ba72fc94" />
## What this does
- Loads the provided CSV of cannabis strains.
- Indexes `dominant_terpenes + flavor_notes + effects` using BM25.
- Takes a free-text query, retrieves the **top 3** strains, and generates a brief recommendation.
- Ranks using `final_score = BM25 * (1 + α*score_norm) * (1 + β*count_norm)` where:
  - `score_norm = max(0, (avg_review_score-3)/2)` maps 3.0 → 0.0 and 5.0 → 1.0
  - `count_norm = log1p(review_count) / log1p(max_review_count)` scales volume 0...1
  - α and β default to 0.25 (gentle, interpretable boost).

## Tokenization
- Lowercased, alphanumeric split via regex.
- Light synonym expansion for terpenes/effects to help recall
  (e.g., "citrus" nudges limonene, "sleep" matches "sleepy"/"insomnia").
  
## How I ranked results
- Scored all strains with the provided BM25 (k1=1.5, b=0.75) using the tokens above.
Applied a gentle review boost:
- score_norm = max(0, (avg_review_score-3)/2) and count_norm = log1p(review_count)/log1p(max_review_count)
- final = BM25 * (1 + 0.25*score_norm) * (1 + 0.25*count_norm)
Sorted by final, returned top 3, and generated a short recommendation for each.

## Why this structure:
- Matches the exam spec exactly (BM25 on those fields, top 3, include scores + recs).
- Simple and explainable; fast to run in Colab with a tiny Gradio UI.
- Easy to extend later (more synonyms, filters, different boost, or embeddings).

## How to run
Run all the cells and test the App's UI in the last cell
Make sure `sample_cannabis_strains.csv` is in the same directory or update `CSV_PATH`.
