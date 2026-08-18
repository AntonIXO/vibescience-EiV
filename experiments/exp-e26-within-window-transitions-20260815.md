---
artifacts: []
closed: true
config_note: 'Runner: scratch/e26_within_window_transitions.py -> logs/e26_within_window_transitions.json.
  Pure CPU, minutes. Corpus git_sha e849eb7, seed 0.


  All states read through the production path (load_sample_metadata + state_series).
  Feature builder IDENTICAL to E16/E18/E24 so any difference is the DESIGN, not the
  features. GroupKFold by subject_id.


  1274 transition-bearing windows found. State-pair frequency: (flow,stress) 681,
  (neutral,stress) 399, (flow,neutral) 392. Contributing datasets: lifesnaps 871,
  dryad 342, neuro_stress 26, wesad 15, consumer_grade_wearables 11, nurse_stress
  8, pmdata 1. Primary pair = flow vs stress.'
cost: null
crash_reason: ''
created_at: '2026-08-15T07:54:25+00:00'
diagnostics_measured:
- after: 0.6329
  before: 0.5129
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: logs/e26_within_window_transitions.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_within_window_beats_clock
  passed: true
- evidence: ''
  gate_id: g2_shuffle_control_dead
  passed: true
- evidence: ''
  gate_id: g3_trait_held_constant_by_design
  passed: true
git_ref: ''
hypothesis_id: hyp-e26-within-window-transitions-20260815
id: exp-e26-within-window-transitions-20260815
interpretation:
  at: '2026-08-18T21:08:42+00:00'
  failure_mode: null
  lesson: 'Within-window transitions are the first design in this project where physiology
    beats every available baseline on the state axis, and it works because the trait
    confound is removed by SAMPLING rather than by statistical control: flow vs stress
    epochs from the same subject on the same day give HR AUC 0.6329 vs clock 0.5129
    (+0.1200) and vs its own within-window shuffle 0.5151 (+0.1178), majority 0.520,
    on n=3248 epochs / 87 subjects / 662 windows with GroupKFold by subject — the
    per-user constant that beat every prior arm (0.7381 in E16) is exactly 0.5 here
    by construction. Only the W=120 arm is citable: W=15/30/60 sit at 0.5427/0.5381/0.5524
    and their shuffles are unstable (0.4425/0.4730), and adding clock features HURTS
    (hr+clock 0.5786 < hr alone 0.6329), so the signal is a slow ~2-hour physiological
    signature, not acute peri-ping reactivity. Engineering consequence: TripletStateDataset
    mines on infer_state_label, one majority-collapsed label per window, so all 1274
    transition windows have their internal contrast discarded before Phase B sees
    it. Caveats for reuse: 0.6329 is an in-the-wild-grade detector (never quote it
    beside WESAD''s lab 0.8497) and lifesnaps (871) + dryad (342) supply 1213 of the
    1274 windows.'
  transfers_to:
  - hyp-e27-encoder-on-transitions-20260815
  - hyp-e28-segment-triplet-retrain-20260815
  - hyp-e16-peri-ema-window-20260815
  - hyp-e18-within-subject-state-20260815
key_numbers:
  auc_clock_w120: 0.5129
  auc_hr_w120: 0.6329
  auc_hr_w15: 0.5427
  auc_hr_w30: 0.5381
  auc_hr_w60: 0.5524
  auc_hrclock_w120: 0.5786
  auc_shuffle_w120: 0.5151
  auc_shuffle_w60: 0.4958
  majority: 0.52
  margin_over_clock_w120: 0.12
  margin_over_shuffle_w120: 0.1178
  n_epochs_w120: 3248.0
  n_subjects: 87.0
  n_transition_windows: 1274.0
  n_windows_w120: 662.0
  pair_flow_neutral: 392.0
  pair_flow_stress: 681.0
  pair_neutral_stress: 399.0
observed_effects:
- delta: 0.12
  diagnostic_id: eiv_affect_multichannel_increment
  direction: up
parent_experiment_id: exp-e25-triplet-degeneracy-premise-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-e26-within-window-transitions-20260815]].
Branches from [[exp-e25-triplet-degeneracy-premise-20260815]].
External run: logs/e26_within_window_transitions.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.5129 → after 0.6329  (Δ +0.12, up ✓ predicted up)

## Gates
- `g1_within_window_beats_clock`: PASS
- `g2_shuffle_control_dead`: PASS
- `g3_trait_held_constant_by_design`: PASS

**Verdict:** supports

## Interpretation
Within-window transitions are the first design in this project where physiology beats every available baseline on the state axis, and it works because the trait confound is removed by SAMPLING rather than by statistical control: flow vs stress epochs from the same subject on the same day give HR AUC 0.6329 vs clock 0.5129 (+0.1200) and vs its own within-window shuffle 0.5151 (+0.1178), majority 0.520, on n=3248 epochs / 87 subjects / 662 windows with GroupKFold by subject — the per-user constant that beat every prior arm (0.7381 in E16) is exactly 0.5 here by construction. Only the W=120 arm is citable: W=15/30/60 sit at 0.5427/0.5381/0.5524 and their shuffles are unstable (0.4425/0.4730), and adding clock features HURTS (hr+clock 0.5786 < hr alone 0.6329), so the signal is a slow ~2-hour physiological signature, not acute peri-ping reactivity. Engineering consequence: TripletStateDataset mines on infer_state_label, one majority-collapsed label per window, so all 1274 transition windows have their internal contrast discarded before Phase B sees it. Caveats for reuse: 0.6329 is an in-the-wild-grade detector (never quote it beside WESAD's lab 0.8497) and lifesnaps (871) + dryad (342) supply 1213 of the 1274 windows.

Transfers to: [[hyp-e27-encoder-on-transitions-20260815]], [[hyp-e28-segment-triplet-retrain-20260815]], [[hyp-e16-peri-ema-window-20260815]], [[hyp-e18-within-subject-state-20260815]]

## Key numbers
- `auc_clock_w120`: 0.5129
- `auc_hr_w120`: 0.6329
- `auc_hr_w15`: 0.5427
- `auc_hr_w30`: 0.5381
- `auc_hr_w60`: 0.5524
- `auc_hrclock_w120`: 0.5786
- `auc_shuffle_w120`: 0.5151
- `auc_shuffle_w60`: 0.4958
- `majority`: 0.52
- `margin_over_clock_w120`: 0.12
- `margin_over_shuffle_w120`: 0.1178
- `n_epochs_w120`: 3248
- `n_subjects`: 87
- `n_transition_windows`: 1274
- `n_windows_w120`: 662
- `pair_flow_neutral`: 392
- `pair_flow_stress`: 681
- `pair_neutral_stress`: 399

## Notes
THE FIRST DESIGN IN THIS PROJECT WHERE PHYSIOLOGY BEATS EVERY AVAILABLE BASELINE ON THE STATE AXIS.

flow vs stress, both epochs from the SAME subject on the SAME day, minutes apart. n=3248 epochs, 87 subjects, 662 windows, GroupKFold by subject:
  W=120  clock 0.5129 | HR 0.6329 | shuffle 0.5151 | majority 0.520
HR alone is +0.1200 over clock and +0.1178 over its own within-window shuffle.

WHY THIS MATTERS MORE THAN THE ABSOLUTE NUMBER. Every previous state arm lost to the per-user constant (E16: 0.7381 vs best physiological 0.5818). Here that constant is EXACTLY 0.5 by construction, because both epochs share a subject. The confound is removed by the sampling design rather than by a statistical control, and physiology wins.

WINDOW SHAPE, CONSISTENT WITH EVERY OTHER RESULT IN THIS LINE: only W=120 clears (0.6329); W=15/30/60 sit at 0.5427/0.5381/0.5524. Longer aggregation wins again — the fourth independent measurement pointing away from acute peri-ping reactivity and toward a slower, ~2-hour physiological signature.

hr+clock (0.5786) is WORSE than hr alone (0.6329). Three clock features cost more variance than they contribute once trait and day are fixed — the clock is near-useless here (0.5129), which is itself evidence the design has removed the circadian confound too.

THE ENGINEERING CONSEQUENCE IS CONCRETE AND ACTIONABLE. TripletStateDataset (src/training/datasets.py:2149) mines triplets on infer_state_label — ONE majority-collapsed label per window. All 1274 transition windows are therefore reduced to a single state and their internal contrast is DISCARDED before Phase B ever sees it. This probe shows that discarded contrast carries real, shuffle-controlled, subject-held-out signal. The miner should be changed to draw anchor/positive/negative from WITHIN-window epoch pairs, which simultaneously supplies non-degenerate triplets (E25: states span 6-8 datasets at purity 0.475-0.545) and removes the trait axis the margin currently wastes itself on.

HONEST LIMITS. (1) 0.6329 is a modest detector, not a product-grade one — it is the honest in-the-wild range the literature reports (0.55-0.75), not WESAD's 0.8497 lab number, and the two must never be quoted together. (2) lifesnaps contributes 871 of 1274 transition windows and dryad 342, so this is predominantly a two-cohort result. (3) Shuffle at W=15/30 reads 0.4425/0.4730, outside the +/-0.05 band on the LOW side — instability at small n rather than leakage, but it means only the W=120 arm should be cited. (4) This is a shallow RF on summary features; E22 showed the frozen encoder adds little on lifesnaps affect, so a Phase B retrain is justified by the SUPPLY argument, not by an expectation that the encoder will multiply this number.

#wearables #epoch-resolution #baseline #control #contrastive #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
