---
direction: higher_better
id: eiv_nonhr_channel_grouped_r2
name: EiV non-HR channel subject-grouped probe R2
requires_control: null
topic_tags: []
unit: r2
---

## Description
Subject-grouped (GroupKFold by subject_id, out-of-fold) RidgeCV R2 of a linear probe on the soft-token prefix predicting a NON-HR canonical channel (respiratory_rate and sleep_prob_deep are the two with enough presence to score). Companion to eiv_heldout_hr_r2, which covers hr_mean. Exists because the encoder demonstrably reads hr_mean (grouped R2 +0.974) while both non-HR channels score NEGATIVE, i.e. worse than predicting their own mean. Baseline to quote alongside it: the always-predict-own-median MAE in the same population. Measured 2026-08-07 on data/eval_v2 with the 12ch_v2 Phase B checkpoint: respiratory_rate -0.0757, sleep_prob_deep -0.1847, against hr_mean +0.9750.

*Unit:* `r2` — *higher_better*
