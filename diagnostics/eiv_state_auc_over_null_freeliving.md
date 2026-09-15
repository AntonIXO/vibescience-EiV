---
direction: higher_better
id: eiv_state_auc_over_null_freeliving
name: eiv_state_auc_over_null_freeliving
noise_k: 2.0
noise_n: 6
noise_note: 'MEASURED 2026-09-03 (exp-f-freeliving-feature-ceiling-20260903, g5 --fold-seeds
  5): raw_multiscale/rf on LifeSnaps-only rows re-scored under 6 seeded subject-group
  partitions; fold sd 0.0133 on the 49-subject clean pool, 0.0094 on the 9-subject
  held-out pool. Partition floor of a frozen feature readout; training-seed variance
  of an encoder cell is additional and unmeasured.'
noise_sigma: 0.0133
noise_source: measured
requires_control: null
topic_tags:
- optihealth
- readout
- control
unit: AUC minus within-window permutation-null AUC
---

## Description
Momentary flow-vs-stress AUC over its own within-window label-permutation null, scored ONLY on FREE-LIVING cohorts (LifeSnaps EMA, and any other unscripted field corpus), subject-grouped folds, on the g5 harness rows. The twin of eiv_state_auc_over_null_lab, registered 2026-09-03 for the same reason: on the held-out pool LifeSnaps reads 0.474 (n=442) while WESAD reads 0.973, so the pooled number hid that free-living state was at chance. This is the cell the product actually needs and the only one where a new lever can matter.

*Unit:* `AUC minus within-window permutation-null AUC` — *higher_better*

#optihealth #readout #control
