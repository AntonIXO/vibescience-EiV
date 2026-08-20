---
direction: higher_better
id: eiv_momentary_state_auc_over_null
name: Momentary within-window state AUC over its own within-window shuffle null
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
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

*** 2026-08-18 AMENDMENT — THE NULL IS READOUT-DEPENDENT, AND SO IS THE HEADLINE. READ THIS FIRST. ***
exp-e34 (rule-out `the-function-class-account-of-exp-046-that-8-511840`, commit b95c64d) re-ran the identical rows/folds/nulls varying ONLY the estimator, and the arm ordering INVERTED:
    arm                readout    AUC     null            over_null    z
    raw_patch_grid_8   RF      0.6309  0.5189+-0.0422     +0.1120   +2.66
    raw_patch_grid_8   LINEAR  0.5855  0.5711+-0.0130     +0.0143   +1.10
    enc_pca8           RF      0.4950  0.5442+-0.0293     -0.0492   -1.68
    enc_pca8           LINEAR  0.5715  0.5310+-0.0143     +0.0405   +2.83
Under a matched LINEAR readout the TRAINED ENCODER CLEARS its null (z+2.83) while the handcrafted features do NOT (z+1.10). So 'the trained encoder does not carry momentary state' is a statement about (representation x ESTIMATOR), never about the representation alone. Do not quote the RF triple unqualified.
THEREFORE, MANDATORY FROM NOW ON: (i) recompute the shuffle null SEPARATELY FOR EVERY READOUT — RF nulls sit at 0.5189/0.5442 while linear nulls sit at 0.5711/0.5310 on the same features; (ii) report AT LEAST TWO readout families (a tree ensemble and a linear model) for any arm whose conclusion is 'representation X does/doesn't carry state'; (iii) name the estimator in every quoted number.
Why RF flatters handcrafted features here: 8 dense, individually-meaningful, axis-aligned features suit axis-aligned splits, while 8 PCA components of a distributed code do not. That is an instrument property, not a fact about physiology.

WHY THE NULL IS SUBTRACTED RATHER THAN QUOTED SEPARATELY. On this task the shuffle null is NOT 0.5. Measured nulls sit at 0.519-0.571 depending on the feature set AND the readout, because a high-dimensional feature matrix plus subject-grouped folds lets a flexible classifier exploit window identity. exp-e28 recorded arms at 0.5237 and 0.5325 that LOOK like weak positives and are in fact BELOW their own nulls (0.5548, 0.5482). Quoting a bare AUC on this task has already produced one near-miss reading of a negative result, so the null is part of the metric.

THE ONLY ADMISSIBLE NULL IS WITHIN-WINDOW. Permute the A/B assignment INSIDE each transition window (>=24 permutations, report mean and sd). This holds subject AND day constant, which is the entire point of the transition design: cross-subject affect is trait-dominated (a per-user constant scores AUC 0.7381 while the best physiological arm reaches 0.5818, exp-e16), so any null that permutes across subjects or days is far too easy and will pass noise.

MUST BE REPORTED WITH: (a) the raw AUC and the null mean +- sd, never the difference alone; (b) the z-score and the permutation p; (c) n epochs, n subjects, n windows and the positive rate; (d) the feature dimensionality, because the null RISES with it (8 features -> null 0.5189, 9216 features -> null 0.5495) and two arms of different width are not comparable on raw AUC; (e) THE ESTIMATOR, per the 2026-08-18 amendment above.

EVALUATION POPULATION: within-window transition epochs only — two DIFFERENT states, SAME subject, SAME day, minutes apart. Reference n=3226 epochs, 87 subjects, 660 windows, pos 0.481.

REFERENCE VALUES (2026-08-15, exp-e29, commit b34ed44 — ALL RandomForest(300, min_samples_leaf=3, seed 0)):
  raw_shallow    (15 feats, E26's percentiles)      AUC 0.6430  null 0.5441+-0.0211  => +0.0989, z +4.69
  raw_patch_grid  (8 feats, encoder geometry only)  AUC 0.6309  null 0.5189+-0.0422  => +0.1120, z +2.66
  enc_token    (9216 feats, trained v5c encoder)    AUC 0.5202  null 0.5495+-0.0351  => -0.0293, z -0.84
EXTENDED (2026-08-18, exp-046, commit ff14a20, same RF): enc_pca8 (trained, PCA-8) -0.0492 z-1.68; rand_pca8 (RANDOM-INIT, PCA-8) -0.0022 z-0.08 — an untrained encoder sits exactly on its own null under RF; prod_pca8 (production stage: +channel_fusion +projector, PCA-8) -0.0807 z-3.08, the WORST arm, establishing that the 4.3x production-over-encoder advantage measured for hr_peak does NOT transport to a momentary target.

WHAT THAT ESTABLISHES, STATED AT THE RIGHT STRENGTH: the encoder's PATCH GEOMETRY preserves momentary state (raw-on-grid keeps essentially all the shallow signal from 8 features and zero learned parameters). Whether the encoder's LEARNED WEIGHTS destroy it is READOUT-DEPENDENT and currently unresolved — RF says yes, linear says no. What is NOT in dispute: the effect is weak under every readout, and no arm has yet reached the raw_shallow bound of 0.6430.

CPU-only, no GPU and no adapter required for the raw arms, so it can gate a state-objective change BEFORE any training run is spent — the same role eiv_peak_excursion_increment_r2 plays for window/architecture changes.

*Unit:* `auc_delta` — *higher_better*

#control #baseline #wearables #n-of-1
