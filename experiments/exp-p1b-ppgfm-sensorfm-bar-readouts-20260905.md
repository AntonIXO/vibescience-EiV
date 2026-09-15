---
artifacts:
- logs/g5_ppgfm_wesad.json
- logs/g5_ppgfm_nurse_stress.json
- logs/g5_ppgfm_case.json
- logs/p1_ppgfm_validation.json
- logs/p1_wesad_stress_probe.json
- logs/p0_ppg_inventory.json
- logs/p0_raw_ppg_acquisition.json
- scratch/g5_pool_ceiling.py
- scratch/p1_ppgfm_extract.py
- scratch/p1_ppgfm_validation.py
- scratch/p1_wesad_stress_probe.py
- src/encoders/ppgfm/
- src/data/ppg/
- outputs/ppgfm/
closed: true
config_note: 'Branch track-p/raw-ppg (uncommitted). Stores: outputs/ppgfm/{pulse_ppg,pulse_ppg_random,papagei_s,papagei_s_random}
  (Pulse-PPG 60 s @ 50 Hz windows -> one row per minute; PaPaGei 10 s @ 125 Hz segments
  -> per-minute mean, >=3 segments). Readout: scratch/g5_pool_ceiling.py --pool clean
  --datasets {wesad|nurse_stress|case} --ppgfm <4 stores> --no-encoder --fold-seeds
  6 --device-slice; PCA-50 fit label-free on all 86,019 embedded minutes; W=30 and
  {30,120,240} mean+std+count; ridge (alpha 1, standardised) + HistGradientBoosting;
  100 linear / 48 tree within-window permutations; GroupKFold(5) by subject. Join
  validated at lag 0 (100% overlap of store minutes with measured-HR minutes) on every
  clocked corpus; CASE joined on minute_rel anchored at the window''s first HR epoch.
  CPU-only, 4 threads (shared host, LightSem agreement). g0 half 1 already in logs/p1_ppgfm_validation.json.'
cost:
  gpu_hours: 1.0
  wall_hours: 3.5
crash_reason: ''
created_at: '2026-09-04T22:48:28+00:00'
diagnostics_measured:
- after: -0.0078
  before: -0.1188
  diagnostic_id: eiv_state_auc_over_null_freeliving
- after: 0.2352
  before: 0.4786
  diagnostic_id: eiv_state_auc_over_null_lab
- after: 0.0966
  before: 0.0
  diagnostic_id: eiv_state_auc_trained_minus_random
- after: 0.369
  before: 0.2724
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do NOT score a PPG-FM state arm with mean+std pooled over a span and nothing else:
  on WESAD it reads AUC 0.53 while the current-minute embedding reads 0.91 on identical
  rows. Any FM-vs-features comparison needs a recency block (ppgfm_last / ppgfm_msl)
  to match the 3 recency features in the handcrafted set.'
- 'Do NOT use nurse_stress as a free-living state cell for any representation claim,
  solo or pooled: every zero-parameter arm reads below chance subject-held-out (rule-out
  nurse-stress-scored-solo-...). A gate written as ''beat the features by X'' passes
  there for any arm near zero.'
- 'Do NOT read PaPaGei-S numbers as a tower result on wrist PPG: it fails g0 on ECG-truth
  HR (0.632, below its own random twin 0.530) and its random twin beats it on WESAD
  stress (LOSO macro-F1 0.841 vs 0.741; g5 ppgfm_last/ridge 0.897 vs 0.867). PaPaGei''s
  learned weights subtract from a random ResNet1D-MoE on E4 wrist data.'
- 'Do NOT fund a WESAD-only PPG-FM state arm: the cell is saturated by 15 HR/HRV features
  (0.984) and the union with the FM sits inside 2 sigma of the features (delta -0.010,
  twin union -0.011).'
- 'Do NOT re-run dryad_ifh_affect through either tower with --allow-narrowed-band
  and call it the published front end: at 19.6 Hz both filter designs are undefined
  (rule-out dryad-ifh-affect-samsung-ppg-is-19-6-hz-...).'
external_run: ''
former_ids: []
gate_results:
- evidence: 'Pulse-PPG only. PPG-DaLiA ECG-truth HR, subject-held-out ridge: MAE 10.6379
    vs always-median 22.7271 (ratio 0.468 <= 0.5); random twin 13.7019 (0.603). PaPaGei
    14.3324 (0.632) FAILS and is below its twin 12.0127 -> reported, not gated. WESAD
    binary stress LOSO, their unit (60-s rows) and their probe (StandardScaler + L2
    logistic, C grid {0.01..100} on macro-F1, subject-grouped inner CV): Pulse-PPG
    macro-F1 0.839 / AUC 0.918 vs published 0.876 (naive 0.411 vs their 0.4065) ->
    within +/-0.05. Pulse-PPG twin 0.622; PaPaGei 0.741 vs its twin 0.841. logs/p1_ppgfm_validation.json,
    logs/p1_wesad_stress_probe.json.'
  gate_id: g0-instrument-validity
  passed: true
- evidence: 'WESAD clean pool, 449 rows / 15 subjects, GroupKFold(5): pre-registered
    ppgfm_ms:pulse_ppg/gb AUC 0.7445 (< 0.90). Recency geometry added as named arms
    after a smoke reading (mean+std over 30 min erases the current condition; current-minute
    ridge reads 0.91): ppgfm_last/gb 0.8729, ppgfm_msl/gb 0.7749 -- still < 0.90.
    Second clause PASSES: union_ppg_msl/gb over null +0.4685 vs raw_multiscale/gb
    +0.4786, delta -0.0101 inside 2 sigma_lab 0.013 (twin union +0.4673). Trained
    beats twin: ppgfm_last +0.3690 vs +0.2724 (gb), +0.3723 vs +0.2010 (ridge). logs/g5_ppgfm_wesad.json.'
  gate_id: g1-lab-cell
  passed: false
- evidence: 'nurse_stress solo, 1,685 rows / 21 windows / 12 subjects, GroupKFold(5),
    own within-window null. Pre-registered ppgfm_ms:pulse_ppg/gb over null -0.0078
    (ridge +0.0067); recency ppgfm_msl/gb +0.0244 (ridge +0.0246). Twin clause FAILS:
    msl/gb twin +0.0321 (trained - twin = -0.008), ridge twin +0.0316 (-0.007); ms/gb
    twin +0.0513 (-0.059). Clock clause FAILS: ppgfm_msl/gb AUC 0.4796 vs clock/gb
    0.6421 (clock over its own null -0.033: the within-window null already carries
    the shift schedule). Features clause passes only on an INVERTED bar: raw_multiscale/gb
    over null -0.1188 (AUC 0.369), raw_shallow/ridge 0.2945, raw_grid/gb 0.2806 --
    every zero-parameter arm below chance subject-held-out; fold_auc_sd 0.04-0.09
    against the registered sigma 0.0133. The cell is degenerate (NEXT.md §4: a gate''s
    literal text passing on a number the arm did not earn). logs/g5_ppgfm_nurse_stress.json.'
  gate_id: g2-free-living-cell
  passed: false
git_ref: track-p/raw-ppg@10d9063+dirty
hypothesis_id: p1b-frozen-ppg-fm-embeddings-clear-the-sensorfm-bar-on-nurse-stress-free-living-and-wesad-lab
id: exp-p1b-ppgfm-sensorfm-bar-readouts-20260905
interpretation:
  at: '2026-09-05T00:08:57+00:00'
  failure_mode: test_invalid
  lesson: 'The free-living half of the SensorFM bar cannot be tested on this corpus:
    dryad is 19.6 Hz and nurse_stress''s label inverts between subjects (features
    below chance), so g2''s ''up'' is an inverted bar and not evidence -- the free-living
    claim needs a new corpus (WHOOP 4.0 at 437 Hz with EMA, or a purchase). The lab
    half is a real negative: a frozen Pulse-PPG minute embedding reads WESAD stress
    at 0.87 -- clearly above its random twin (+0.10/+0.18), unlike every custom encoder
    tried -- but 15 HR/HRV features read 0.98 and the union does not move, so waveform
    morphology adds nothing to HR/HRV statistics for scripted lab stress. Two durable
    method facts: (1) span mean+std pooling erases the current condition (0.53 vs
    0.91 for the current minute) -- an FM state arm needs a recency block; (2) PaPaGei-S''s
    learned weights lose to their own random init on wrist PPG on every target, so
    ''a pretrained PPG FM'' is not one thing -- Pulse-PPG is the wrist encoder. The
    one positive cell (CASE finger PPG, FM > features and twin at n=77) names the
    next test, not a result.'
  transfers_to:
  - p1-frozen-ppg-foundation-model-embeddings-clear-the-sensorfm-bar-on-the-free-living-cell
  - p3-medgemma-soft-tokens-over-frozen-ppg-fm-tokens-match-an-attention-head
key_numbers:
  case_features_gb_auc: 0.5012
  case_n: 77.0
  case_ppgfm_msl_gb_auc: 0.6931
  case_ppgfm_msl_twin_gb_auc: 0.4538
  case_subjects: 11.0
  dryad_hz: 19.61
  g0_dalia_ratio_papagei: 0.632
  g0_dalia_ratio_papagei_twin: 0.53
  g0_dalia_ratio_pulse: 0.468
  g0_dalia_ratio_pulse_twin: 0.603
  g0_wesad_macro_f1_pulse: 0.839
  g0_wesad_published: 0.876
  mde_freeliving: 0.027
  nurse_clock_gb_auc: 0.6421
  nurse_features_gb_over_null: -0.1188
  nurse_fold_sd_max: 0.0928
  nurse_ppgfm_ms_gb_over_null: -0.0078
  nurse_ppgfm_msl_gb_over_null: 0.0244
  nurse_ppgfm_msl_twin_gb_over_null: 0.0321
  wesad_features_gb_auc: 0.9839
  wesad_ppgfm_last_gb_auc: 0.8729
  wesad_ppgfm_last_twin_gb_auc: 0.7709
  wesad_ppgfm_ms_gb_auc: 0.7445
  wesad_sigma_lab: 0.013
  wesad_union_delta_over_null: -0.0101
observed_effects:
- delta: 0.111
  diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: up
- delta: -0.2434
  diagnostic_id: eiv_state_auc_over_null_lab
  direction: down
- delta: 0.0966
  diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
- delta: 0.0966
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_state_auc_over_null_freeliving: true
    eiv_state_auc_over_null_lab: false
    eiv_state_auc_trained_minus_random: true
provenance: {}
recipe_fingerprint:
  git_ref: track-p/raw-ppg@10d9063+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[p1b-frozen-ppg-fm-embeddings-clear-the-sensorfm-bar-on-nurse-stress-free-living-and-wesad-lab]].
At commit `track-p/raw-ppg@10d9063+dirty`.

## Measurements
- [[eiv_state_auc_over_null_freeliving]]: before -0.1188 → after -0.0078  (Δ +0.111, up ✓ predicted up)
- [[eiv_state_auc_over_null_lab]]: before 0.4786 → after 0.2352  (Δ -0.2434, down ✗ predicted none)
- [[eiv_state_auc_trained_minus_random]]: before 0.0 → after 0.0966  (Δ +0.0966, up ✓ predicted up)
- [[eiv_momentary_state_auc_over_null]]: before 0.2724 → after 0.369  (Δ +0.0966, up)

## Gates
- `g0-instrument-validity`: PASS — Pulse-PPG only. PPG-DaLiA ECG-truth HR, subject-held-out ridge: MAE 10.6379 vs always-median 22.7271 (ratio 0.468 <= 0.5); random twin 13.7019 (0.603). PaPaGei 14.3324 (0.632) FAILS and is below its twin 12.0127 -> reported, not gated. WESAD binary stress LOSO, their unit (60-s rows) and their probe (StandardScaler + L2 logistic, C grid {0.01..100} on macro-F1, subject-grouped inner CV): Pulse-PPG macro-F1 0.839 / AUC 0.918 vs published 0.876 (naive 0.411 vs their 0.4065) -> within +/-0.05. Pulse-PPG twin 0.622; PaPaGei 0.741 vs its twin 0.841. logs/p1_ppgfm_validation.json, logs/p1_wesad_stress_probe.json.
- `g1-lab-cell`: **FAIL** — WESAD clean pool, 449 rows / 15 subjects, GroupKFold(5): pre-registered ppgfm_ms:pulse_ppg/gb AUC 0.7445 (< 0.90). Recency geometry added as named arms after a smoke reading (mean+std over 30 min erases the current condition; current-minute ridge reads 0.91): ppgfm_last/gb 0.8729, ppgfm_msl/gb 0.7749 -- still < 0.90. Second clause PASSES: union_ppg_msl/gb over null +0.4685 vs raw_multiscale/gb +0.4786, delta -0.0101 inside 2 sigma_lab 0.013 (twin union +0.4673). Trained beats twin: ppgfm_last +0.3690 vs +0.2724 (gb), +0.3723 vs +0.2010 (ridge). logs/g5_ppgfm_wesad.json.
- `g2-free-living-cell`: **FAIL** — nurse_stress solo, 1,685 rows / 21 windows / 12 subjects, GroupKFold(5), own within-window null. Pre-registered ppgfm_ms:pulse_ppg/gb over null -0.0078 (ridge +0.0067); recency ppgfm_msl/gb +0.0244 (ridge +0.0246). Twin clause FAILS: msl/gb twin +0.0321 (trained - twin = -0.008), ridge twin +0.0316 (-0.007); ms/gb twin +0.0513 (-0.059). Clock clause FAILS: ppgfm_msl/gb AUC 0.4796 vs clock/gb 0.6421 (clock over its own null -0.033: the within-window null already carries the shift schedule). Features clause passes only on an INVERTED bar: raw_multiscale/gb over null -0.1188 (AUC 0.369), raw_shallow/ridge 0.2945, raw_grid/gb 0.2806 -- every zero-parameter arm below chance subject-held-out; fold_auc_sd 0.04-0.09 against the registered sigma 0.0133. The cell is degenerate (NEXT.md §4: a gate's literal text passing on a number the arm did not earn). logs/g5_ppgfm_nurse_stress.json.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `test_invalid`

The free-living half of the SensorFM bar cannot be tested on this corpus: dryad is 19.6 Hz and nurse_stress's label inverts between subjects (features below chance), so g2's 'up' is an inverted bar and not evidence -- the free-living claim needs a new corpus (WHOOP 4.0 at 437 Hz with EMA, or a purchase). The lab half is a real negative: a frozen Pulse-PPG minute embedding reads WESAD stress at 0.87 -- clearly above its random twin (+0.10/+0.18), unlike every custom encoder tried -- but 15 HR/HRV features read 0.98 and the union does not move, so waveform morphology adds nothing to HR/HRV statistics for scripted lab stress. Two durable method facts: (1) span mean+std pooling erases the current condition (0.53 vs 0.91 for the current minute) -- an FM state arm needs a recency block; (2) PaPaGei-S's learned weights lose to their own random init on wrist PPG on every target, so 'a pretrained PPG FM' is not one thing -- Pulse-PPG is the wrist encoder. The one positive cell (CASE finger PPG, FM > features and twin at n=77) names the next test, not a result.

Transfers to: [[p1-frozen-ppg-foundation-model-embeddings-clear-the-sensorfm-bar-on-the-free-living-cell]], [[p3-medgemma-soft-tokens-over-frozen-ppg-fm-tokens-match-an-attention-head]]

## Key numbers
- `case_features_gb_auc`: 0.5012
- `case_n`: 77
- `case_ppgfm_msl_gb_auc`: 0.6931
- `case_ppgfm_msl_twin_gb_auc`: 0.4538
- `case_subjects`: 11
- `dryad_hz`: 19.61
- `g0_dalia_ratio_papagei`: 0.632
- `g0_dalia_ratio_papagei_twin`: 0.53
- `g0_dalia_ratio_pulse`: 0.468
- `g0_dalia_ratio_pulse_twin`: 0.603
- `g0_wesad_macro_f1_pulse`: 0.839
- `g0_wesad_published`: 0.876
- `mde_freeliving`: 0.027
- `nurse_clock_gb_auc`: 0.6421
- `nurse_features_gb_over_null`: -0.1188
- `nurse_fold_sd_max`: 0.0928
- `nurse_ppgfm_ms_gb_over_null`: -0.0078
- `nurse_ppgfm_msl_gb_over_null`: 0.0244
- `nurse_ppgfm_msl_twin_gb_over_null`: 0.0321
- `wesad_features_gb_auc`: 0.9839
- `wesad_ppgfm_last_gb_auc`: 0.8729
- `wesad_ppgfm_last_twin_gb_auc`: 0.7709
- `wesad_ppgfm_ms_gb_auc`: 0.7445
- `wesad_sigma_lab`: 0.013
- `wesad_union_delta_over_null`: -0.0101

## Artifacts
- `logs/g5_ppgfm_wesad.json`
- `logs/g5_ppgfm_nurse_stress.json`
- `logs/g5_ppgfm_case.json`
- `logs/p1_ppgfm_validation.json`
- `logs/p1_wesad_stress_probe.json`
- `logs/p0_ppg_inventory.json`
- `logs/p0_raw_ppg_acquisition.json`
- `scratch/g5_pool_ceiling.py`
- `scratch/p1_ppgfm_extract.py`
- `scratch/p1_ppgfm_validation.py`
- `scratch/p1_wesad_stress_probe.py`
- `src/encoders/ppgfm/`
- `src/data/ppg/`
- `outputs/ppgfm/`

## Do not retry
- Do NOT score a PPG-FM state arm with mean+std pooled over a span and nothing else: on WESAD it reads AUC 0.53 while the current-minute embedding reads 0.91 on identical rows. Any FM-vs-features comparison needs a recency block (ppgfm_last / ppgfm_msl) to match the 3 recency features in the handcrafted set.
- Do NOT use nurse_stress as a free-living state cell for any representation claim, solo or pooled: every zero-parameter arm reads below chance subject-held-out (rule-out nurse-stress-scored-solo-...). A gate written as 'beat the features by X' passes there for any arm near zero.
- Do NOT read PaPaGei-S numbers as a tower result on wrist PPG: it fails g0 on ECG-truth HR (0.632, below its own random twin 0.530) and its random twin beats it on WESAD stress (LOSO macro-F1 0.841 vs 0.741; g5 ppgfm_last/ridge 0.897 vs 0.867). PaPaGei's learned weights subtract from a random ResNet1D-MoE on E4 wrist data.
- Do NOT fund a WESAD-only PPG-FM state arm: the cell is saturated by 15 HR/HRV features (0.984) and the union with the FM sits inside 2 sigma of the features (delta -0.010, twin union -0.011).
- Do NOT re-run dryad_ifh_affect through either tower with --allow-narrowed-band and call it the published front end: at 19.6 Hz both filter designs are undefined (rule-out dryad-ifh-affect-samsung-ppg-is-19-6-hz-...).

## Notes
Three cells, four towers, 36 arms each, identical rows to g5's construction. LAB (WESAD 449/15): features 0.984; Pulse-PPG current-minute 0.873 (gb) beats its random twin 0.771 by +0.10 (ridge +0.18) -- the first representation in this project to clear its own twin on a state cell -- but not the features; union inside 2 sigma of features. Pre-registered ppgfm_ms geometry 0.745. FREE-LIVING (nurse_stress solo 1,685/12): degenerate -- features below chance (0.29-0.47), fold sd to 0.09, FM arms at their twins and below the clock (0.48 vs 0.64); g2 fails on the twin and clock clauses, its features clause passes only on an inverted bar. The engine's 'up' on the primary diagnostic (-0.119 -> -0.008) is that inverted bar, not a win. SECONDARY (CASE finger 1000 Hz, 77/11, rel-joined): the one cell where the FM beats both the features (0.50) and its twin (0.45): Pulse-PPG msl/gb 0.693 (z +2.5), PaPaGei last/gb 0.645 vs twin 0.49 -- suggestive at n=77, unpowered, reported not gated. g0: Pulse-PPG passes on ECG-truth HR (0.468) and reproduces its own WESAD stress row within 0.04 macro-F1 under their probe at their 60-s unit (LOSO instead of their 11/2/2 split); PaPaGei fails g0 and loses to its twin everywhere on wrist data. Dryad (the pre-registered free-living cell) measured 19.6 Hz -> rule-out; WHOOP 4.0 (437 Hz) is the only plausible future free-living cell and has no capture yet. Instrument notes: join validated at lag 0 per corpus; PCA-50 fit label-free on 86-89k minutes (EV 0.90-0.98); CASE joined on minute_rel; gb guarded against an all-constant fold (CASE clock block); RF capped at 4 threads (shared host).

#wearables #readout #control #baseline #optihealth #optihealth-eiv #representation-learning #matched-baseline
