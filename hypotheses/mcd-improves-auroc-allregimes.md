---
created_at: '2026-07-11T16:19:10+00:00'
gates: []
id: mcd-improves-auroc-allregimes
interventions:
- mcd-covariance
paper_refs:
- fran-tonini-2026
predicted_effects:
- diagnostic_id: anomaly_auroc
  direction: up
  magnitude_note: primary; must hold at eps=15% too for a blanket swap
problem_id: day-vector-anomaly-masking
problem_tags:
- masking
- covariance-contamination
status: refuted
supersedes: null
topic_tags:
- robust-covariance
- anomaly-detection
---

## Statement
MCD raises anomaly separability AUROC across ALL contamination levels (1-15%).

## Rationale
If MCD is a safe blanket replacement, AUROC should not regress anywhere.

## Plan
Full k x eps sweep incl eps=15%, primary metric = mean AUROC over whole sweep.

## Links
Tests problem [[day-vector-anomaly-masking]].
Applies [[mcd-covariance]].
Predicts [[anomaly_auroc]] (up).
Cites [[fran-tonini-2026]].

#robust-covariance #anomaly-detection #masking #covariance-contamination
