---
direction: higher_better
id: eiv_nonhr_channel_grouped_r2
name: eiv_nonhr_channel_grouped_r2
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags: []
unit: r2
---

## Description
Subject-grouped (GroupKFold by subject_id, out-of-fold) RidgeCV R2 of a linear probe on the soft-token prefix predicting a NON-HR canonical channel (respiratory_rate and sleep_prob_deep are the two with enough presence to score). Companion to eiv_heldout_hr_r2, which covers hr_mean. Exists because the encoder demonstrably reads hr_mean (grouped R2 +0.974) while both non-HR channels score NEGATIVE, i.e. worse than predicting their own mean. Baseline to quote alongside it: the always-predict-own-median MAE in the same population. Measured 2026-08-07 on data/eval_v2 with the 12ch_v2 Phase B checkpoint: respiratory_rate -0.0757, sleep_prob_deep -0.1847, against hr_mean +0.9750.

*** 2026-09-01 MIS-FILED PAIR -- 2 OF THIS DIAGNOSTIC'S 5 EVALUATIONS ARE NOT R2 VALUES ***
exp-e13-nof1-calibration-20260814 records before -1.676 / after +1.322 under this id. Those are an hrv SKILL IN BPM, not an R2. The experiment's own interpretation quotes the number verbatim: 'hrv is NOT established at this data volume: skill +1.322 CI[0.179,2.483] (n_test 137)'. R2 is bounded above by 1.0, and this diagnostic's documented range for non-HR channels is negative, so +1.322 is impossible here on two independent grounds.

DO NOT AGGREGATE THIS DIAGNOSTIC WITHOUT EXCLUDING THAT PAIR. With only 5 evaluations on record, one mis-filed pair is 40% of the population, in a different unit.

AND DO NOT QUOTE +1.322 AS AN ACHIEVED RESULT IN ANY UNIT. exp-e13's finding is that hrv skill is NOT established at this data volume: the +1.322 concentrates entirely in fold 5 (+4.02) while folds 1-4 pool to +0.613 CI[-0.555,1.680], and fold 5 rails RidgeCV to the grid edge even on an extended 1e-6..1e6 alpha grid. The experiment's sound headline is a different number -- hr_raw walk-forward OOS MAE 2.689, skill +1.641 CI[1.049,2.235], the campaign's first supabase-side beat of the production emission path.

*Unit:* `r2` — *higher_better*
