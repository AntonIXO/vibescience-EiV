---
direction: higher_better
id: eiv_momentary_state_auc_over_null
name: Momentary within-window state AUC over its own within-window shuffle null
requires_control: null
topic_tags:
- control
- baseline
- wearables
- n-of-1
unit: auc_delta
---

## Description
Out-of-fold AUC for classifying the MOMENTARY state label (the dominant transition pair, flow vs stress) from features computed over [epoch-W, epoch), MINUS the mean of that arm's OWN within-window shuffle null. Subject-grouped (GroupKFold), W=120 min.

WHY THE NULL IS SUBTRACTED RATHER THAN QUOTED SEPARATELY. On this task the shuffle null is NOT 0.5. Measured nulls sit at 0.519-0.555 depending on the feature set, because a high-dimensional feature matrix plus subject-grouped folds lets a flexible classifier exploit window identity. exp-e28 recorded arms at 0.5237 and 0.5325 that LOOK like weak positives and are in fact BELOW their own nulls (0.5548, 0.5482). Quoting a bare AUC on this task has already produced one near-miss reading of a negative result, so the null is part of the metric.

THE ONLY ADMISSIBLE NULL IS WITHIN-WINDOW. Permute the A/B assignment INSIDE each transition window (>=24 permutations, report mean and sd). This holds subject AND day constant, which is the entire point of the transition design: cross-subject affect is trait-dominated (a per-user constant scores AUC 0.7381 while the best physiological arm reaches 0.5818, exp-e16), so any null that permutes across subjects or days is far too easy and will pass noise.

MUST BE REPORTED WITH: (a) the raw AUC and the null mean +- sd, never the difference alone; (b) the z-score and the permutation p; (c) n epochs, n subjects, n windows and the positive rate; (d) the feature dimensionality, because the null RISES with it (8 features -> null 0.5189, 9216 features -> null 0.5495) and two arms of different width are not comparable on raw AUC.

EVALUATION POPULATION: within-window transition epochs only — two DIFFERENT states, SAME subject, SAME day, minutes apart. Reference n=3226 epochs, 87 subjects, 660 windows, pos 0.481.

REFERENCE VALUES (2026-08-15, exp-e29, commit b34ed44, all on those rows):
  raw_shallow    (15 feats, E26's percentiles)      AUC 0.6430  null 0.5441+-0.0211  => +0.0989, z +4.69
  raw_patch_grid  (8 feats, encoder geometry only)  AUC 0.6309  null 0.5189+-0.0422  => +0.1120, z +2.66
  enc_token    (9216 feats, trained v5c encoder)    AUC 0.5202  null 0.5495+-0.0351  => -0.0293, z -0.84

WHAT THAT TRIPLE ESTABLISHES, and why this diagnostic is worth a permanent slot: the encoder's PATCH GEOMETRY preserves momentary state (raw-on-grid keeps essentially all the shallow signal from 8 features and zero learned parameters) while the encoder's LEARNED WEIGHTS destroy it (same geometry, trained, falls below its own null). Any future state arm — supervised head, objective reweighting, architecture change — is scored on this metric against this triple, so 'the encoder cannot represent state' can never again be asserted when what was actually measured is 'the objective never asked for it'.

CPU-only, no GPU and no adapter required for the raw arms, so it can gate a state-objective change BEFORE any training run is spent — the same role eiv_peak_excursion_increment_r2 plays for window/architecture changes.

*Unit:* `auc_delta` — *higher_better*

#control #baseline #wearables #n-of-1
