---
artifacts: []
closed: true
config_note: "Runner: scratch/e16_peri_ema_window.py. Pure CPU, no GPU, no model weights.
  Corpus git_sha e849eb7, tensor [11318,1440,12], label_events 102982 rows. Seed 0,
  GroupKFold(5) by subject, RF(300, min_samples_leaf=5) + LogisticRegression linear
  check, >=24 within-subject label permutations on the primary delta.\n\nPre-run facts
  already measured and recorded on the problem (scratch/w1_subdaily_supply.py, scratch/ema_time_reality_check.py):\n
  \ - EMA verified momentary in the LANDED data (epoch mod60 0.019 vs chance 0.0167;
  top-epoch share 0.004; flat minute-of-hour histogram; lifesnaps peaks at the SEMA3
  10/15/20h schedule)\n  - within-window label disagreement, lifesnaps mood: 0.000
  at W<=60 -> 0.776 at W=1440\n  - usable labelled windows (HR cov>=0.5): 9732 @W=30,
  10412 @W=360, 6775 @W=1440\n  - pmdata EXCLUDED as a pre-commitment: 07-09h clustered
  morning diary, top-epoch share 0.060, retrospective not momentary\n\nGates g1 (best-W
  delta over clock-only >= +0.03, within-subject perm p<0.05, MDE 0.028) and g2 (absolute
  AUC >= 0.58 AND above the within-subject constant-predictor AUC) are BLOCKING and
  were committed before any number existed."
crash_reason: ''
created_at: '2026-08-14T21:25:32+00:00'
diagnostics_measured:
- after: 0.0167
  before: 0.0228
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_delta_over_clock
  passed: false
- evidence: ''
  gate_id: g2_absolute_and_trivial
  passed: false
- evidence: ''
  gate_id: g3_interior_maximum
  passed: false
- evidence: ''
  gate_id: g4_cross_target_sign
  passed: true
git_ref: ''
hypothesis_id: hyp-e16-peri-ema-window-20260815
id: exp-e16-peri-ema-window-20260815
key_numbers:
  dryad_W15_delta: 0.0998
  dryad_W15_n_usable: 534.0
  dryad_W60_delta: 0.0015
  dryad_n_pings_total: 3926.0
  lifesnaps_tense_best_subdaily_delta_at_W120: 0.0167
  lifesnaps_tense_delta_W1440: 0.0228
  lifesnaps_tense_delta_W15: -0.0064
  lifesnaps_tense_delta_W240: 0.003
  lifesnaps_tense_delta_W30: 0.0005
  lifesnaps_tense_delta_W480: 0.0217
  lifesnaps_tense_delta_W60: 0.0046
  lifesnaps_tense_hr_clock_auc_W1440: 0.5581
  lifesnaps_tense_hr_only_auc_W1440: 0.4812
  lifesnaps_tense_perm_p_W1440: 0.083
  lifesnaps_tense_w1440_clock_only_auc: 0.5354
  lifesnaps_tense_within_subject_constant_auc: 0.7381
  lifesnaps_tired_delta_W480: 0.034
  lifesnaps_tired_perm_p_W480: 0.0
  lifesnaps_tired_within_subject_constant_auc: 0.6731
  n_pings_lifesnaps: 4418.0
  n_subjects_lifesnaps: 61.0
  within_window_label_disagreement_W1440: 0.776
  within_window_label_disagreement_W60: 0.0
observed_effects:
- delta: -0.0061
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e16-peri-ema-window-20260815]].

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.0228 → after 0.0167  (Δ -0.0061, down ✗ predicted up)

## Gates
- `g1_delta_over_clock`: **FAIL**
- `g2_absolute_and_trivial`: **FAIL**
- `g3_interior_maximum`: **FAIL**
- `g4_cross_target_sign`: PASS

**Verdict:** refutes

## Key numbers
- `dryad_W15_delta`: 0.0998
- `dryad_W15_n_usable`: 534
- `dryad_W60_delta`: 0.0015
- `dryad_n_pings_total`: 3926
- `lifesnaps_tense_best_subdaily_delta_at_W120`: 0.0167
- `lifesnaps_tense_delta_W1440`: 0.0228
- `lifesnaps_tense_delta_W15`: -0.0064
- `lifesnaps_tense_delta_W240`: 0.003
- `lifesnaps_tense_delta_W30`: 0.0005
- `lifesnaps_tense_delta_W480`: 0.0217
- `lifesnaps_tense_delta_W60`: 0.0046
- `lifesnaps_tense_hr_clock_auc_W1440`: 0.5581
- `lifesnaps_tense_hr_only_auc_W1440`: 0.4812
- `lifesnaps_tense_perm_p_W1440`: 0.083
- `lifesnaps_tense_w1440_clock_only_auc`: 0.5354
- `lifesnaps_tense_within_subject_constant_auc`: 0.7381
- `lifesnaps_tired_delta_W480`: 0.034
- `lifesnaps_tired_perm_p_W480`: 0
- `lifesnaps_tired_within_subject_constant_auc`: 0.6731
- `n_pings_lifesnaps`: 4418
- `n_subjects_lifesnaps`: 61
- `within_window_label_disagreement_W1440`: 0.776
- `within_window_label_disagreement_W60`: 0

## Notes
VERDICT: the dilution hypothesis is REFUTED, and the honest headline is that a per-user constant beats every physiological arm.

WHAT WAS PREDICTED vs WHAT HAPPENED. P1 said the best sub-daily delta (W<=240) would exceed both +0.03 and the W=1440 delta. Observed: best sub-daily +0.0167 at W=120 vs +0.0228 at W=1440 — wrong direction, and under the 0.028 MDE, so a POWERED NULL. P2 said delta(W) would have an interior maximum in [30,240]; observed it rises with W and peaks at the full day, with W=15 actually NEGATIVE (-0.0064, worse than the clock alone). The mechanism I proposed — that a 1440-minute average dilutes a minutes-wide autonomic response — is measured false on this corpus. Longer context helps.

THE DECISIVE NUMBER, and it is a trivial baseline. The within-subject constant predictor — predict each subject's own positive rate, using ZERO physiology and ZERO clock — scores AUC 0.7381 on lifesnaps TENSE/ANXIOUS and 0.6731 on TIRED. The best physiological arm anywhere in the sweep is 0.5818. The model is 0.156 AUC BELOW a per-user constant. Cross-subject affect variance in this corpus is dominated by WHO the subject is (trait/base-rate), not by what their heart is doing in any window. Same lesson as the eiv_readout_linear_gap and hr_acc majority-baseline traps: an arm can look non-trivial against chance while a constant predictor buries it.

WHAT SURVIVES AND IS NEW. Once the clock confound is controlled, HR does add a small but null-clearing increment on two targets: lifesnaps TIRED +0.0340 at W=480 (perm p=0.000, null mean -0.0069 std 0.0190) and dryad stress-above-own-median +0.0998 at W=15 (perm p=0.000, null mean -0.0095 std 0.0393). That is a genuine change from E5's daily-summary null (delta -0.0278, p=0.84), and it is attributable to the clock control plus per-ping alignment, NOT to short windows. Caveat on the dryad arm: it survives on only 534 of 3926 pings, i.e. the 13.6% with dense peri-ping HR — a SELECTION effect on coverage, not a timescale effect; the same arm at W=60 (n=1331) collapses to +0.0015.

CONSEQUENCE FOR THE WINDOW DECISION. Sub-daily windows are NOT justified by state detection. The supply argument for shortening (4 labelled cohorts invisible at 24h; within-window label disagreement 0.000 at W<=60 vs 0.776 at W=1440) is real but does not convert into cross-subject affect skill, because the ceiling is not temporal alignment — it is between-subject trait dominance. Future state work must be N-of-1 with a within-subject baseline, where the 0.7381 constant is unavailable to the model by construction. The 24h window stands for now on the encoder/channel-reconstruction case, not on the state case.

INSTRUMENT DEFECT FOUND AND FIXED MID-RUN: scratch/e16_peri_ema_window.py parsed N_PERM and never used it — the sweep shipped without the permutation null it declared. Closed by scratch/e16_perm_null.py (logs/e16_perm_null.json). Any future probe in this family must assert the null actually ran.

Artifacts: logs/e16_peri_ema_window_sweep.json, logs/e16_perm_null.json, outputs/w1_subdaily_supply.csv, scratch/ema_time_reality_check.py.

#wearables #epoch-resolution #baseline #control #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
