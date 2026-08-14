---
created_at: '2026-07-11T16:18:46+00:00'
former_ids: []
gates: []
id: mcd-beats-lw-under-masking
interventions:
- mcd-covariance
paper_refs:
- fran-tonini-2026
predicted_effects:
- diagnostic_id: anomaly_precision_at_k
  direction: up
  magnitude_note: primary; expect a few pp gain at moderate contamination
- diagnostic_id: anomaly_auroc
  direction: up
  magnitude_note: secondary; may not hold at high contamination on small n
problem_id: day-vector-anomaly-masking
problem_tags:
- masking
- covariance-contamination
status: confirmed
supersedes: null
topic_tags:
- robust-covariance
- anomaly-detection
---

## Statement
Replacing Ledoit-Wolf with MCD covariance in the Mahalanobis day-vector scorer raises anomaly precision@k under masking (correlated multi-day outliers).

## Rationale
MCD (breakdown 0.5) fits covariance on the clean core and excludes outliers, so masking cannot inflate the reference covariance the way shrinkage does.

## Plan
Semi-synthetic correlated-masking injection on real prod day-vectors; sweep k,eps; 30 seeds.

## Links
Tests problem [[day-vector-anomaly-masking]].
Applies [[mcd-covariance]].
Predicts [[anomaly_precision_at_k]] (up).
Predicts [[anomaly_auroc]] (up).
Cites [[fran-tonini-2026]].

#robust-covariance #anomaly-detection #masking #covariance-contamination
