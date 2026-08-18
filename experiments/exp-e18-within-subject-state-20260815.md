---
artifacts: []
closed: true
config_note: 'Runner: scratch/e18_within_subject_state.py (+ scratch/canon_utils.py
  for correct bpm denormalisation). Pure CPU, no GPU, no model weights — runs in parallel
  with the GPU encoder arm. Corpus git_sha e849eb7, tensor [11318,1440,12]. Seed 0.


  Executes runbook rb-w2-within-subject-peri-ping-state-20260815 (priority 10).


  Per-subject walk-forward (expanding window), never pooled. Trivials computed per
  subject on the same held-out tail: running positive rate, persistence, clock-only.
  Primary = AUC(HR/HRV+clock) minus max(those three), median over powered subjects
  with 2000-sample bootstrap CI95.'
cost: null
crash_reason: ''
created_at: '2026-08-14T22:21:44+00:00'
diagnostics_measured:
- after: 0.0037
  before: 0.0
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry:
- 'Per-user (N-of-1) state/affect heads on peri-ping HR/HRV features for this corpus,
  at any window in 30-1440 min. Measured 12 arms across 3 targets: best median per-subject
  skill +0.0037 (CI spans zero), 4 arms reliably NEGATIVE, best frac_positive 0.526
  vs a 0.60 bar. Do not re-run per-user affect heads on HR/HRV features without NEW
  label supply or a NEW modality.'
- 'Short peri-ping windows (<=120 min) for affect in ANY grouping regime. Now measured
  worst in BOTH cross-subject (E16) and within-subject (E18): within-subject W=30
  is the most negative arm on all three targets. The acute-autonomic-reactivity premise
  is dead on this corpus.'
external_run: ''
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_median_subject_skill_over_strongest_trivial
  passed: false
- evidence: ''
  gate_id: g2_fraction_subjects_positive
  passed: false
- evidence: ''
  gate_id: g3_within_subject_W_profile_shape
  passed: false
git_ref: ''
hypothesis_id: hyp-e18-within-subject-state-20260815
id: exp-e18-within-subject-state-20260815
interpretation:
  at: '2026-08-18T21:08:40+00:00'
  failure_mode: claim_false
  lesson: 'The N-of-1 escape hatch for affect is SHUT, powered: across 12 arms (3
    targets x 4 windows) of per-subject walk-forward against each subject''s own strongest
    trivial (mean trivial AUC ~0.60 from base rate + persistence + clock), the best
    median per-subject skill is +0.0037 (tired W=480, CI95[-0.0242,+0.0314], 13x under
    the 0.05 MDE), best frac_positive 0.526 against a 0.60 bar, and 4 of 12 arms have
    CI95 ENTIRELY below zero — adding peri-ping HR/HRV makes roughly two-thirds of
    subjects worse. Short windows are the worst arms within-subject too, exactly opposite
    to the predicted flattening (tense -0.0938@W30 -> -0.0455@W1440; tired -0.0751@W30
    -> +0.0037@W480; dryad -0.1092@W30 -> -0.0174@W1440), making this the third independent
    measurement against acute peri-ping autonomic reactivity on this corpus. Do not
    re-run per-user affect heads on HR/HRV features here without NEW label supply
    (supabase has zero state labels) or a NEW modality (EDA, accelerometry/context,
    sleep-timing irregularity); this says nothing about hrv/rr/temp channel reporting
    or the excursion axis, where E19 confirmed a large lever.'
  transfers_to:
  - hyp-e14-within-subject-affect-supply-20260814
  - hyp-e16-peri-ema-window-20260815
  - hyp-affect-multichannel-supply-20260814
  - hyp-e22-encoder-representation-affect-20260815
  - hyp-e24-lab-protocol-subdaily-state-20260815
  - hyp-e26-within-window-transitions-20260815
key_numbers:
  best_frac_positive: 0.526
  best_median_skill_tired_W480: 0.0037
  dryad_W120_frac_positive: 0.105
  dryad_W120_median_skill: -0.0655
  dryad_W30_median_skill: -0.1092
  mean_strongest_trivial_auc: 0.6
  n_arms_all_negative_or_zero: 12.0
  n_arms_ci_entirely_below_zero: 4.0
  tense_W1440_ci_hi: -0.0159
  tense_W1440_median_skill: -0.0455
  tense_W30_median_skill: -0.0938
  tired_W30_median_skill: -0.0751
observed_effects:
- delta: 0.0037
  diagnostic_id: eiv_affect_multichannel_increment
  direction: up
parent_experiment_id: exp-e17-unified-readout-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[hyp-e18-within-subject-state-20260815]].
Branches from [[exp-e17-unified-readout-20260815]].

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.0 → after 0.0037  (Δ +0.0037, up ✓ predicted up)

## Gates
- `g1_median_subject_skill_over_strongest_trivial`: **FAIL**
- `g2_fraction_subjects_positive`: **FAIL**
- `g3_within_subject_W_profile_shape`: **FAIL**

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `claim_false`

The N-of-1 escape hatch for affect is SHUT, powered: across 12 arms (3 targets x 4 windows) of per-subject walk-forward against each subject's own strongest trivial (mean trivial AUC ~0.60 from base rate + persistence + clock), the best median per-subject skill is +0.0037 (tired W=480, CI95[-0.0242,+0.0314], 13x under the 0.05 MDE), best frac_positive 0.526 against a 0.60 bar, and 4 of 12 arms have CI95 ENTIRELY below zero — adding peri-ping HR/HRV makes roughly two-thirds of subjects worse. Short windows are the worst arms within-subject too, exactly opposite to the predicted flattening (tense -0.0938@W30 -> -0.0455@W1440; tired -0.0751@W30 -> +0.0037@W480; dryad -0.1092@W30 -> -0.0174@W1440), making this the third independent measurement against acute peri-ping autonomic reactivity on this corpus. Do not re-run per-user affect heads on HR/HRV features here without NEW label supply (supabase has zero state labels) or a NEW modality (EDA, accelerometry/context, sleep-timing irregularity); this says nothing about hrv/rr/temp channel reporting or the excursion axis, where E19 confirmed a large lever.

Transfers to: [[hyp-e14-within-subject-affect-supply-20260814]], [[hyp-e16-peri-ema-window-20260815]], [[hyp-affect-multichannel-supply-20260814]], [[hyp-e22-encoder-representation-affect-20260815]], [[hyp-e24-lab-protocol-subdaily-state-20260815]], [[hyp-e26-within-window-transitions-20260815]]

## Key numbers
- `best_frac_positive`: 0.526
- `best_median_skill_tired_W480`: 0.0037
- `dryad_W120_frac_positive`: 0.105
- `dryad_W120_median_skill`: -0.0655
- `dryad_W30_median_skill`: -0.1092
- `mean_strongest_trivial_auc`: 0.6
- `n_arms_all_negative_or_zero`: 12
- `n_arms_ci_entirely_below_zero`: 4
- `tense_W1440_ci_hi`: -0.0159
- `tense_W1440_median_skill`: -0.0455
- `tense_W30_median_skill`: -0.0938
- `tired_W30_median_skill`: -0.0751

## Do not retry
- Per-user (N-of-1) state/affect heads on peri-ping HR/HRV features for this corpus, at any window in 30-1440 min. Measured 12 arms across 3 targets: best median per-subject skill +0.0037 (CI spans zero), 4 arms reliably NEGATIVE, best frac_positive 0.526 vs a 0.60 bar. Do not re-run per-user affect heads on HR/HRV features without NEW label supply or a NEW modality.
- Short peri-ping windows (<=120 min) for affect in ANY grouping regime. Now measured worst in BOTH cross-subject (E16) and within-subject (E18): within-subject W=30 is the most negative arm on all three targets. The acute-autonomic-reactivity premise is dead on this corpus.

## Notes
POWERED NULL, AND IT CLOSES THE STATE QUESTION IN BOTH GROUPING REGIMES.

The N-of-1 escape hatch is shut. E16 and E17 showed cross-subject affect is trait-dominated (per-user constant AUC 0.7381 vs best physiological 0.5818). The obvious rescue was that the trait constant is absorbed by construction within a subject, so a per-user head should reveal the physiological signal underneath. It does not. Across 12 arms (3 targets x 4 windows), per-subject walk-forward against each subject's OWN strongest trivial:

  best median skill  +0.0037  (tired W=480), CI95 [-0.0242,+0.0314], spans zero, 13x under the 0.05 MDE
  best frac_positive 0.526    against a 0.60 bar — a coin flip
  4 of 12 arms have CI95 ENTIRELY BELOW ZERO

Adding peri-ping HR/HRV to a per-user baseline makes roughly two-thirds of subjects WORSE. The per-subject trivials are not weak (mean strongest trivial AUC ~0.60, built from base rate + persistence + clock); physiology subtracts from them.

MY PREDICTED SHAPE WAS WRONG, AND THE CORRECTION IS THE MOST USEFUL PART. I committed to the within-subject W-profile FLATTENING or favouring SHORTER windows, reasoning that the cross-subject long-window advantage was trait-like daily rhythm that is constant within a person. Observed the exact opposite: skill improves MONOTONICALLY with W within subjects too (tense -0.0938@30 -> -0.0455@1440; tired -0.0751@30 -> +0.0037@480; dryad -0.1092@30 -> -0.0174@1440). Short peri-ping windows are the WORST arms in BOTH regimes. That is now three independent measurements (E16 cross-subject window sweep, E17 local-vs-global readout, E18 within-subject window sweep) all pointing away from acute peri-ping autonomic reactivity in this corpus.

WHAT THIS MEANS FOR THE PRODUCT. The state slot has no measured support at any grouping regime, any window, any readout. Recommendation: DROP `state` from the emitted schema, or emit it explicitly as a non-sensor-derived field, rather than shipping a slot whose content is worse than the user's own base rate. This also retires the 'PerUserHeadStore state head' as the surviving product lever named in the E14 closure — E14's positive residue was on DAILY features against median-forward/persistence trivials; when the clock is added to the trivial set and the split is walk-forward, it does not survive.

SCOPE — WHAT IS NOT RULED OUT. (a) NEW label supply: optihealth_supabase still has ZERO state labels, and a dense within-user EMA stream at much higher ping rates is a different data regime. (b) NEW modalities absent here — EDA, accelerometry/context, sleep-timing irregularity. (c) Non-affect targets: this says nothing about hrv/rr/temp channel reporting or the excursion axis, where E19 just confirmed a large lever. Do not read this as 'the encoder is useless' — read it as 'affect is not in HR/HRV on this corpus'.

INSTRUMENT NOTE: the first run was serial and far too slow. Parallelised across SUBJECTS (joblib loky, n_jobs=12, RF forced to n_jobs=1 inside to avoid nested oversubscription); the refactor reproduced W=30 tense at exactly -0.0938, confirming it is faithful.

#wearables #n-of-1 #baseline #control #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
