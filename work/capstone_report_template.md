# Capstone Report — Refresh / Content Opportunity Scoring

- **Author:** Prachi Choudhary
- **Lane:** Refresh / Content Opportunity Scoring
- **Repo:** https://github.com/prachichoudhary2004/FlyRank
- **Date:** September 2026

## 0. Abstract

This project asks whether historical SEO performance signals can be used to identify content pages that deserve review for a potential refresh. The analysis is framed at the client-content pair level using February 2026 information as the feature window and March 2026 performance as the outcome window. An interpretable Decision Tree approach is compared with a transparent rule-based opportunity score using the same evaluation design. The analysis is intended to produce a ranked set of content items for editorial review rather than to establish causality or predict Google's ranking algorithm. The final output is a decision-support action queue containing priority scores, reason codes, and recommended review actions.

## 1. Problem framing

The decision supported by this project is:

> Which content pages should a content team prioritize for human review and possible refresh?

The unit of analysis is a client-content pair.

The output is a ranked priority score and action queue.

A human editor can use the ranking to decide which pages should be reviewed first for issues such as weak click-through performance, weaker search position, or other underperformance signals.

The cost of a wrong call is asymmetric. Prioritizing a page that does not need a refresh can waste editorial time, while failing to identify a page that genuinely needs review can leave a potential content opportunity unattended.

Machine learning is useful here because multiple historical SEO signals can be combined into a repeatable ranking process instead of relying only on manual inspection.

## 2. Data safety

The analysis uses the FlyRank internship search-performance warehouse.

The feature window is February 2026 and the outcome window is March 2026.

The analysis uses aggregated SEO performance signals including:

- February impressions
- February clicks
- February CTR
- February average position
- GSC data availability

Fields derived from the future outcome window are deliberately excluded from the predictive feature set.

Label-derived fields such as `trend_direction` and `trend_pct` are not used as model features because they can encode information about performance movement that may overlap with the outcome being predicted.

Pseudonymous identifiers such as `client_hash_id` and `content_hash_id` are used only to identify and group observations. They are not used as predictive features.

Client-identifying information, domains, URLs, private queries, credentials, and raw private exports are not included in the public analysis.

## 3. Baseline

The baseline is a transparent rule-based opportunity score.

The score combines historical February signals, giving greater priority to:

- lower CTR,
- weaker average search position, and
- lower observed impressions.

The purpose of the baseline is to establish whether an interpretable hand-built ranking provides a useful comparison for the machine-learning approach.

The baseline and model are evaluated using the same evaluation data and ranking-oriented metrics.

### Baseline results

| Metric | Baseline |
|---|---:|
| Average Precision | TBD |
| Precision@K | TBD |
| Recall@K | TBD |
| F1@K | TBD |

The final numerical values should be populated from a fresh successful warehouse run.

## 4. Model / analysis

The selected model is a Decision Tree Classifier.

A decision tree was selected because it is interpretable and can provide an understandable relationship between historical SEO signals and the resulting priority score.

### Features

The predictive feature set consists of:

1. `impressions_feb`
2. `clicks_feb`
3. `ctr_feb`
4. `avg_position_feb`
5. `gsc_available_days_feb`

The following information is deliberately excluded:

- March performance variables
- future outcome information
- client identifiers
- content identifiers
- label-derived trend fields

### Target

The target represents whether an eligible content item went dark during the March outcome window, based on March search-performance availability and impressions.

The target is constructed from the March outcome window and is therefore kept completely separate from the February predictive features.

## 5. Evaluation

The evaluation uses a client-grouped train/test split.

Grouping by client reduces the possibility of observations from the same client appearing in both the training and testing sets.

The feature window precedes the outcome window, so February information is used for prediction while March information is reserved for evaluating the outcome.

The primary evaluation metrics are:

- Average Precision
- Precision@K
- Recall@K
- F1@K

The task base rate is also reported because precision and accuracy can otherwise be misleading when the outcome is imbalanced.

### Model vs baseline

| Metric | Baseline | Decision Tree |
|---|---:|---:|
| Average Precision | TBD | TBD |
| Precision@K | TBD | TBD |
| Recall@K | TBD | TBD |
| F1@K | TBD | TBD |

The final numbers must come from the same held-out evaluation set.

### Error analysis

The main error types are false positives and false negatives.

A false positive represents a content item that the ranking prioritizes but that does not exhibit the defined March outcome.

A false negative represents a content item with the March outcome that was not placed sufficiently high in the priority ranking.

These errors demonstrate why the output should be treated as decision support rather than an automated editorial decision.

## 6. Interpretation

The analysis is designed to identify combinations of historical SEO signals associated with content that may warrant review.

Low CTR can indicate that a page receives visibility but captures relatively few clicks.

A weaker average search position can indicate that a page is not performing strongly in search results relative to other pages.

Impressions provide an indication of observed search visibility and help distinguish pages with different levels of search exposure.

The decision tree provides an interpretable way to combine these signals into a repeatable ranking.

The analysis should not be interpreted as evidence that any individual feature causes a change in search performance.

Any feature importance or model interpretation should be treated as directional evidence from this dataset and evaluation design.

## 7. Recommendation

The output is a ranked content-review queue.

A FlyRank editor could use the queue as follows:

1. Start with the highest-ranked content items.
2. Inspect pages with low CTR for title and metadata review.
3. Inspect pages with weaker search position for content relevance, depth, and internal-linking opportunities.
4. Prioritize pages showing multiple underperformance signals.
5. Apply human editorial judgment before making any content changes.

### Example action categories

| Signal | Suggested action |
|---|---|
| Low CTR | Review title and meta description |
| Weak ranking | Review content depth and relevance |
| Strong visibility with weak capture | Review search-result presentation |
| Multiple weak signals | Prioritize for deeper editorial review |

Confidence should be considered **directional rather than causal**. The ranking is a prioritization aid and does not guarantee that refreshing a page will improve its future performance.

## 8. Reproducibility

The analysis is implemented in the project repository:

`https://github.com/prachichoudhary2004/FlyRank`

The project uses DuckDB for aggregation over the FlyRank warehouse and scikit-learn for the machine-learning component.

The experiment uses:

- Feature window: February 2026
- Outcome window: March 2026
- Random seed: 42
- Validation: client-grouped train/test split
- Model: Decision Tree Classifier

The analysis should be rerun from the notebooks in `work/notebooks/`.

The final repository should contain:

- the assignment notebooks,
- the capstone notebook,
- the final action-playbook output,
- the final metrics output, and
- `submission/paper_url.txt`.

The final metrics reported in this paper should correspond to a fresh successful run of the committed analysis.

## 9. Acknowledgments & data credit

Built on the FlyRank ML Internship dataset.

Data source: https://flyrank.ai
