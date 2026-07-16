# Capstone Report — Refresh / Content Opportunity Scoring

- **Author:** Hamza
- **Lane:** Refresh / Content Opportunity Scoring
- **Repo:** https://github.com/DataWithHamza/Flyrank-ML-Internship
- **Date:** July 2026
  

## 0. Abstract

This project investigates which website pages should be prioritized for content refresh using historical search and engagement data. The analysis was performed on the anonymized FlyRank ML Internship dataset using engineered page-level features while excluding identifiers and leakage-prone fields. A transparent baseline scoring rule was compared with machine learning models, with Random Forest providing stronger predictive performance on the same evaluation setup. The resulting ranked queue helps content teams identify pages that may deserve manual review before investing editing time. These findings are based on observed historical patterns and are intended as decision-support rather than proof that refreshing a page will improve search performance.

## 1. Problem framing

This project supports the decision of which content pages should be reviewed first for refresh. The goal is to help a content or SEO team prioritize limited review capacity using observed search and engagement signals.

The unit of analysis is one content page (content_id). Each page is evaluated using its previous 90-day observation window. The output is a ranked refresh queue that helps reviewers decide which pages deserve attention first.

The intended users are content strategists and SEO reviewers. They use the ranked list to prioritize pages for review, refresh, or further investigation.

A false positive means reviewer time is spent on a page that did not actually require attention. A false negative means a genuinely declining page may continue losing search visibility because it was not reviewed.

Machine learning helps because many observed signals (traffic, engagement, content quality, and search metrics) interact together. A model combines these signals more consistently than a simple hand-written rule while remaining a decision-support system rather than an automated decision maker.

## 2. Data safety

The project uses the anonymized FlyRank ML Internship starter dataset (content_refresh_anonymized.csv), which contains approximately 30,000 content pages. The analysis is performed at the page level using a fixed 90-day observation window. No client names, URLs, page titles, keywords, or other identifying information are included.

I deliberately excluded client_id and content_id from the feature set because they are pseudonymous identity keys used only for grouping and joining records. Including them as model features could allow the model to memorize individual pages or clients instead of learning general patterns.

I also excluded label-derived information from the feature set. The target was derived from trend_direction, while trend_pct was not used because it is closely related to the label and could introduce leakage. During the leakage audit, I confirmed that no columns containing the label, future information, or FlyRank product decision fields (such as health_score, priority_score, or action_type) were present in the final feature vector.

All features were collected from the same historical observation window and represent information that would have been available before making a recommendation. The project follows public-safe practices throughout, and no client-identifying information appears anywhere in the work/ directory or exported outputs.

## 3. Baseline

Before training a machine learning model, I created a transparent baseline scoring rule for the Refresh / Content Opportunity Scoring lane. The purpose of this rule was to rank pages that may need review using simple observed signals instead of learned patterns.

The baseline combined signals such as:
- Low CTR
- Declining traffic trend
- Older content
- Poor average search position

Each page received an action score based on these measured indicators, and the pages were ranked from highest to lowest priority. This baseline provides an easy-to-understand decision-support method that any reviewer can inspect.

For evaluation, I compared the baseline and the machine learning model using the same dataset and the same validation split.

| Method | Result |
|---------|--------|
| Baseline Action Score | Precision@50 = **0.24** |
| Random Forest Model | Precision = **0.588** |

The machine learning model produced stronger ranking performance than the transparent baseline. This result is observed on the available validation data and should be treated as decision-support rather than proof that the recommendations will always improve search performance.

## 4. Model / Analysis

This project uses a Random Forest classifier because it can capture non-linear relationships between features without requiring strong assumptions about the data. It also performed better than the transparent baseline rule during evaluation.

The model uses historical page signals such as search volume, competition, CPC, word count, character count, impressions, clicks, sessions, CTR, average position, engagement rate, scroll rate, AI traffic percentage, content age, and freshness-related features.

Categorical variables such as content type, search intent, freshness tier, impression tier, and position tier were one-hot encoded before training.

The following fields were intentionally excluded:

- client_id (used only for grouped validation)
- content_id (identifier only)
- trend_direction
- trend_pct
- any future-window or label-derived columns

The prediction target is whether a page should be prioritized for content refresh based on historical performance signals.

## 5. Evaluation

The model was evaluated using the same dataset and prediction task as the baseline rule.

To avoid optimistic evaluation, a grouped validation split based on client_id was used so that pages from the same client were not present in both training and testing.

The baseline achieved approximately:

- Precision@50 = 0.24

The Random Forest model achieved approximately:

- Precision = 0.59

The learned model produced more accurate rankings than the transparent baseline while using the same available information.

Most prediction errors occurred on pages with mixed signals, where some engagement metrics were strong but visibility metrics suggested declining performance. These cases require human review before any action is taken.

## 6. Interpretation

The model consistently relied on historical visibility and engagement signals.

Features such as impressions, clicks, sessions, CTR, average position, and content freshness appeared to carry the strongest observed relationship with the refresh decision.

Older pages with declining visibility generally received higher priority scores.

Some pages with low traffic but stable engagement were ranked lower than expected, showing that not every low-traffic page should automatically be refreshed.

These observations are measured patterns from this dataset and should be treated as decision-support rather than proof of causation.

## 7. Recommendation

The ranked output should be used by SEO specialists or content editors to decide which pages deserve manual review first.

High-ranked pages should be reviewed for:

- outdated information
- missing content
- poor search visibility
- weak CTR
- declining engagement

The recommendations should not be applied automatically.

Every recommended page should be manually reviewed before any content changes are made.

The model provides decision-support based on observed historical data and does not guarantee that refreshing a page will improve rankings.

## 8. Reproducibility

The project can be reproduced from a fresh repository clone using the provided pipeline.

Example workflow:


pip install -r requirements.txt
python scripts/run_all.py



---

## Acknowledgments

Built on the FlyRank ML Internship dataset.

Data source:
https://flyrank.ai

Data source:

https://flyrank.ai

All analyses were performed on anonymized public-safe data, and no client-identifying information appears in this repository.
