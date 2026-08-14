---
created_at: '2026-07-11T16:18:46+00:00'
id: day-vector-anomaly-masking
paper_refs:
- fran-tonini-2026
problem_tags:
- masking
- covariance-contamination
status: open
title: day-vector anomaly scorer degrades under masking
topic_tags:
- robust-covariance
- anomaly-detection
updated_at: '2026-07-11T16:18:46+00:00'
---

## Description
optihealth_analysis day_vector.py scores multivariate anomaly days with Mahalanobis distance under Ledoit-Wolf shrinkage covariance. Shrinkage pulls toward the CONTAMINATED Gaussian, so multiple simultaneous outlier days can mask each other (inflate covariance along shared dims). Only top-5 anomalies are surfaced to the user, so precision@5 is the deployment-relevant metric.

## Links
Cites [[fran-tonini-2026]].

#robust-covariance #anomaly-detection #masking #covariance-contamination
