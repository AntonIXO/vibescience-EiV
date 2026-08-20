---
created_at: '2026-08-19T13:08:51+00:00'
former_ids: []
id: that-the-full-corpus-12-547-window-flow-vs-d55a19
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-19T13:08:51+00:00'
topic_tags:
- state-supervision
- control
- baseline
---

## Claim
That the FULL-corpus (12,547-window) flow-vs-stress transition pool is a valid evaluation population for eiv_momentary_state_auc_over_null. It is not: the min_coverage-0.0 re-merge grew nurse_stress 18 -> 102 windows, and nurse_stress rows now make up 43% of the transition pool (1,729 of 4,001 rows from just 12 subjects / 21 windows at ~82 rows per window). nurse_stress state labels are shift-schedule-carried (clock-alone AUC 0.6358 on that cohort, E-series), and its giant windows soften the within-window permutation null. Any arm scored on the full pool - including the zero-parameter reference arms - reads as null regardless of what it carries.

## Method
exp-047-analog-state-supervision-a1-on-663b08 scoping control (scratch/a1_null_scope_nonurse.py): re-ran the identical E26-transition protocol with nurse_stress excluded, re-scoring the raw reference arms and the trained encoder arms on the clean pool, each against its own >=24-perm within-window null under both readout families.

## Evidence
Same protocol, same session, same estimators (RF-300 + RidgeClassifier), only the pool varying. FULL pool (n=4001, 107 subj, 43% nurse_stress): raw_shallow RF over_null +0.037 z+1.73, raw_patch_grid RF +0.045 z+1.61 (p .083) - E29's geometry bound, z+2.66 on its own corpus, loses significance. CLEAN pool (nurse_stress excluded, n=2272, 95 subj): raw_shallow RF +0.074 z+7.14 / ridge +0.104 z+10.93 - fully recovered. Trained arms move the same way: soft_s43 enc_token RF -0.037 z+1.10 full -> +0.030 z+2.23 clean; patch_s42 +0.002 z+0.06 full -> +0.027 z+1.88 clean; soft_s42 +0.001 full -> +0.032 z+2.43 clean; ordinal_s42 -0.046 z-1.58 full -> +0.037 z+2.64 clean. Logs: logs/a1_state_eval.json (full), logs/a1_state_eval_nonurse.json (clean), runner scratch/a1_null_scope_nonurse.py.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#state-supervision #control #baseline
