---
direction: higher_better
id: eiv_momentary_state_auc_over_null
name: eiv_momentary_state_auc_over_null
noise_k: 2.0
noise_n: 6
noise_note: 'MEASURED 2026-09-03 (exp-c1-heldout-pool-ceiling-20260903, scratch/g5_pool_ceiling.py
  --fold-seeds 5): the same frozen features re-scored under 6 seeded subject-group
  partitions on the split-honest held-out pool (666 rows, 26 subjects). Per-cell fold_auc_sd
  ranges 0.005 (ridge) to 0.018 (rf); 0.0155 is the tree-family value the headline
  cells use and replaces the 0.0050 arm-vs-arm upper bound registered earlier. This
  is the PARTITION floor of a frozen readout, not a training-seed floor: a trained-encoder
  cell additionally carries Phase A seed variance, which is still unmeasured on this
  diagnostic. Read per-cell fold_auc_sd in logs/g5_heldout_pool.json when a single
  cell is at stake.'
noise_sigma: 0.0155
noise_source: measured
requires_control: null
topic_tags:
- control
- baseline
- wearables
- n-of-1
unit: auc
---

## Description
Out-of-fold AUC for classifying the MOMENTARY state label (the dominant transition pair, flow vs stress) from features computed over [epoch-W, epoch), MINUS the mean of that arm's OWN within-window shuffle null. Subject-grouped (GroupKFold), W=120 min. See the 2026-08-18 amendment in this record: the null is READOUT-DEPENDENT and must be recomputed per readout, with at least two readout families reported for any 'representation X does/doesn't carry state' conclusion.

*Unit:* `auc` — *higher_better*

#control #baseline #wearables #n-of-1
