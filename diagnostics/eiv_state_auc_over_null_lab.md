---
direction: higher_better
id: eiv_state_auc_over_null_lab
name: eiv_state_auc_over_null_lab
noise_k: 2.0
noise_n: 6
noise_note: 'MEASURED 2026-09-03 (exp-f-freeliving-feature-ceiling-20260903, g5 --fold-seeds
  5): raw_multiscale on WESAD-only rows (449 rows, 15 subjects) under 6 seeded subject-group
  partitions; fold sd 0.0054 (rf) / 0.0067 (gb); the larger is registered. Encoder
  cells on the same rows are far noisier (fold sd 0.03-0.04) because 15 subjects give
  small folds; read the per-cell fold_auc_sd in logs/g5_clean_wesad.json.'
noise_sigma: 0.0067
noise_source: measured
requires_control: null
topic_tags:
- optihealth
- readout
- control
unit: AUC minus within-window permutation-null AUC
---

## Description
Momentary flow-vs-stress AUC over its own within-window label-permutation null, scored ONLY on LAB-PROTOCOL cohorts (WESAD and any other scripted-induction corpus), subject-grouped folds, on the g5 harness rows. Registered 2026-09-03 because exp-c1 showed the pooled diagnostic averages WESAD 0.973 with LifeSnaps 0.474: a pooled state AUC is a mixture of two different problems and is no longer admissible as a gate. Report with n and the cohort list; a cohort under 30 rows is skipped, not scored.

*Unit:* `AUC minus within-window permutation-null AUC` — *higher_better*

#optihealth #readout #control
