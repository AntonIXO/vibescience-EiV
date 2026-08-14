---
created_at: '2026-07-11T04:03:50+00:00'
former_ids: []
gates: []
id: rhr-cusum-adds-earlywarning
interventions:
- rhr-cusum-detector
paper_refs:
- mishra-2020-presymptomatic
- alavi-2022-realtime-alerting
- quer-2021-detect
predicted_effects:
- diagnostic_id: insights_yield
  direction: up
  magnitude_note: '>=1 corroborated episode on real 365d data'
- diagnostic_id: corroboration
  direction: up
  magnitude_note: flagged episodes co-elevate resp_rate or drop spo2/hrv
- diagnostic_id: fp_rate_null
  direction: none
  magnitude_note: stays ~silent on shuffled-null data
- diagnostic_id: new_deps
  direction: none
  magnitude_note: '0 new deps: numpy/pandas/statsmodels only'
problem_id: oh-missing-earlywarning-rhythm
problem_tags:
- missing-early-warning
- missing-circadian
- missing-directionality
status: confirmed
supersedes: null
topic_tags:
- anomaly-detection
- resting-hr
- illness-early-warning
- optihealth
- wearables
- n-of-1
---

## Statement
A Snyder-lab-style resting-HR anomaly detector (RHR-Diff residual vs personal rolling baseline + online CuSum, corroborated by respiratory_rate / spo2 / hrv) will surface personally meaningful, independently-corroborated elevated-resting-physiology episodes that no current module reports.

## Rationale
hr_resting has 156 days and respiratory_rate/spo2/hrv overlap it. Mishra 2020 + Alavi 2022 show RHR residual + CuSum is the canonical, no-train, single-subject early-warning method. day_vector is a symmetric whole-day Mahalanobis outlier — it does not do resting-physiology online deviation, so this is additive.

## Plan
1) build analysis/resting_anomaly.py: per-metric rolling-median baseline + MAD residual, online CuSum over hr_resting; 2) corroborate each flagged window with resp_rate up / spo2 down / hrv down; 3) FDR/duration gate; 4) null-shuffle false-positive check; 5) wire into config + main_worker; 6) deploy via _redeploy.sh.

## Links
Tests problem [[oh-missing-earlywarning-rhythm]].
Applies [[rhr-cusum-detector]].
Predicts [[insights_yield]] (up).
Predicts [[corroboration]] (up).
Predicts [[fp_rate_null]] (none).
Predicts [[new_deps]] (none).
Cites [[mishra-2020-presymptomatic]], [[alavi-2022-realtime-alerting]], [[quer-2021-detect]].

#anomaly-detection #resting-hr #illness-early-warning #optihealth #wearables #n-of-1 #missing-early-warning #missing-circadian #missing-directionality
