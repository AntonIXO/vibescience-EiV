---
artifacts: []
closed: true
config_note: 'E11 arm A (offline linear head probe), zero training. Checkpoint outputs/checkpoints/v9band04_s42/phase_c
  (BASELINE). TRAIN features: oracle-prefix teacher-forced forwards (E8 machinery,
  scratch/diag_e24_lm_hidden_peak.py pattern) on ~1400 dense strict-clean rows EXCLUDED
  from the frozen eval draws, L34 hidden at value positions for hr_raw/hr_peak/hrv_raw.
  EVAL features: SELF-prefix forwards — replay each stored generated text from outputs/probes/v9band04_s42_emissions_{n128,supabase}.json
  up to each slot''s value start, batch 1. Heads: RidgeCV (24 alphas), standardized,
  LINEAR ONLY per E28 rule-out. Controls: donor-prefix permutation on eval rows (g2),
  shuffled-target refit. Script scratch/e11_linear_head_probe.py -> logs/e11_linear_head_probe.json;
  GPU ~15-25 min in systemd-run scope MemoryMax=20G.'
cost: null
crash_reason: ''
created_at: '2026-08-14T10:01:59+00:00'
diagnostics_measured:
- after: 1.748
  before: -2.88
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 4.521
  before: 0.0
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: n_train=700 self-generated dense prefixes. Dense-128 hr_peak head MAE
    15.198 < min(always-median 16.946, hr_raw-derived 17.168) — MAE condition met
    — but paired bootstrap CI95 of per-row |err| difference = [-0.135, +3.643], includes
    zero (mean skill +1.748). Sign-flip is directional but not CI-solid; per prereg
    this is a FAIL. Shuffle control clean (17.00).
  gate_id: g1-dense-peak-head-skill
  passed: false
- evidence: 'Donor-prefix permutation on n128: hr_peak donor MAE 19.719 vs real 15.198
    (gap +4.521); hr_raw gap +4.593 (6.435 vs 1.842); hrv_raw gap +8.713 (19.466 vs
    10.753). Conditioning is per-sample, not a population prior.'
  gate_id: g2-donor-gap-positive
  passed: true
- evidence: 'supabase-200 hr_peak head MAE 24.806 vs trivial always-median 17.84 (prereg
    est 17.59; measured on scored subset), skill -6.967 CI [-9.67, -4.172]. WORSE
    with more dense training data: n_train=40 gave skill -1.737, n=700 gives -6.967
    — covariate shift (heads overfit the dense feature manifold), not data volume.
    hrv same pattern: supa skill -5.362, corr 0.084. Only hr_raw transfers: supa MAE
    3.008 vs trivial 4.015, skill +1.007 CI [0.538, 1.533], corr 0.716 — positive
    but does NOT beat the existing emission path (2.85).'
  gate_id: g3-supabase-transfer
  passed: false
- evidence: 'hrv_raw dense: corr 0.7175 > 0.60 PASS (beats emission-path 0.592), MAE
    10.753 vs trivial 18.216, skill +7.462 CI [3.73, 10.927] — decisively positive
    dense. supa hrv MAE 13.675 vs trivial 8.314 FAIL. hr_raw dense R2 0.8997 vs bar
    0.90 — misses by 0.0003 (marginal; corr 0.9523, MAE 1.842 vs trivial 7.162). Net:
    dense controls essentially pass, deployment side fails.'
  gate_id: g4-hrv-and-raw-controls
  passed: false
git_ref: ''
hypothesis_id: e11-a-linear-scalar-regression-head-127ffe
id: exp-e11-linear-head-20260814
interpretation:
  at: '2026-08-18T21:08:39+00:00'
  failure_mode: recipe_conditional
  lesson: 'A linear RidgeCV head on L34 self-prefix features reads real per-sample
    signal on the DENSE population but is dead as a deployment lever: dense hr_peak
    MAE 15.198 beats both trivials (always-median 16.946, hr_raw-derived 17.168) yet
    its paired bootstrap CI95 [-0.135, +3.643] includes zero (mean skill +1.748, prereg
    FAIL); hrv dense is decisively positive (MAE 10.753 vs trivial 18.216, skill +7.462
    CI [3.73,10.927], corr 0.7175 beating the emission path''s 0.592); hr_raw dense
    is near-perfect (MAE 1.842, corr 0.9523, R2 0.8997 missing the 0.90 bar by 0.0003);
    and donor-prefix permutation gaps of +4.521 (peak), +4.593 (hr_raw), +8.713 (hrv)
    with a clean shuffle control (17.00) prove the conditioning is per-sample, not
    a population prior. What kills it is deployment transfer, and the mechanism is
    measured, not guessed: supabase hr_peak skill -6.967 CI [-9.67,-4.172] and hrv
    -5.362, and MORE dense training data made deployment WORSE (n_train=40 gives -1.737,
    n_train=700 gives -6.967) — monotone evidence of coverage covariate shift in the
    L34 feature manifold rather than a data-volume problem. Only hr_raw transfers
    at all (supabase skill +1.007 CI [0.538,1.533], corr 0.716) and it still loses
    to the existing emission path (MAE 3.008 vs 2.85), so do not re-run dense-only
    head fitting; the direct successor is coverage-augmented head training (mask dense
    rows to supabase-like coverage before generation), still with zero LM training.'
  transfers_to:
  - e11-s-supabase-transfer-failure-is-5472c8
  - n-of-1-head-calibration-linear-ridgecv-a45392
  - hyp-e9-lmhead-lora-20260814
  - hyp-e10-hrv-localization-20260814
  - the-hr-peak-readout-cap-is-an-a61025
  - hrv-raw-fails-on-deployment-for-the-624895
key_numbers: {}
observed_effects:
- delta: 4.628
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
- delta: 4.521
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: up
parent_experiment_id: exp-e9-lmhead-lora-20260814
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_donor_gap_bpm: true
    eiv_gen_peak_skill_bpm: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[e11-a-linear-scalar-regression-head-127ffe]].
Branches from [[exp-e9-lmhead-lora-20260814]].

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -2.88 → after 1.748  (Δ +4.628, up ✓ predicted up)
- [[eiv_gen_peak_donor_gap_bpm]]: before 0.0 → after 4.521  (Δ +4.521, up ✓ predicted up)

## Gates
- `g1-dense-peak-head-skill`: **FAIL** — n_train=700 self-generated dense prefixes. Dense-128 hr_peak head MAE 15.198 < min(always-median 16.946, hr_raw-derived 17.168) — MAE condition met — but paired bootstrap CI95 of per-row |err| difference = [-0.135, +3.643], includes zero (mean skill +1.748). Sign-flip is directional but not CI-solid; per prereg this is a FAIL. Shuffle control clean (17.00).
- `g2-donor-gap-positive`: PASS — Donor-prefix permutation on n128: hr_peak donor MAE 19.719 vs real 15.198 (gap +4.521); hr_raw gap +4.593 (6.435 vs 1.842); hrv_raw gap +8.713 (19.466 vs 10.753). Conditioning is per-sample, not a population prior.
- `g3-supabase-transfer`: **FAIL** — supabase-200 hr_peak head MAE 24.806 vs trivial always-median 17.84 (prereg est 17.59; measured on scored subset), skill -6.967 CI [-9.67, -4.172]. WORSE with more dense training data: n_train=40 gave skill -1.737, n=700 gives -6.967 — covariate shift (heads overfit the dense feature manifold), not data volume. hrv same pattern: supa skill -5.362, corr 0.084. Only hr_raw transfers: supa MAE 3.008 vs trivial 4.015, skill +1.007 CI [0.538, 1.533], corr 0.716 — positive but does NOT beat the existing emission path (2.85).
- `g4-hrv-and-raw-controls`: **FAIL** — hrv_raw dense: corr 0.7175 > 0.60 PASS (beats emission-path 0.592), MAE 10.753 vs trivial 18.216, skill +7.462 CI [3.73, 10.927] — decisively positive dense. supa hrv MAE 13.675 vs trivial 8.314 FAIL. hr_raw dense R2 0.8997 vs bar 0.90 — misses by 0.0003 (marginal; corr 0.9523, MAE 1.842 vs trivial 7.162). Net: dense controls essentially pass, deployment side fails.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

A linear RidgeCV head on L34 self-prefix features reads real per-sample signal on the DENSE population but is dead as a deployment lever: dense hr_peak MAE 15.198 beats both trivials (always-median 16.946, hr_raw-derived 17.168) yet its paired bootstrap CI95 [-0.135, +3.643] includes zero (mean skill +1.748, prereg FAIL); hrv dense is decisively positive (MAE 10.753 vs trivial 18.216, skill +7.462 CI [3.73,10.927], corr 0.7175 beating the emission path's 0.592); hr_raw dense is near-perfect (MAE 1.842, corr 0.9523, R2 0.8997 missing the 0.90 bar by 0.0003); and donor-prefix permutation gaps of +4.521 (peak), +4.593 (hr_raw), +8.713 (hrv) with a clean shuffle control (17.00) prove the conditioning is per-sample, not a population prior. What kills it is deployment transfer, and the mechanism is measured, not guessed: supabase hr_peak skill -6.967 CI [-9.67,-4.172] and hrv -5.362, and MORE dense training data made deployment WORSE (n_train=40 gives -1.737, n_train=700 gives -6.967) — monotone evidence of coverage covariate shift in the L34 feature manifold rather than a data-volume problem. Only hr_raw transfers at all (supabase skill +1.007 CI [0.538,1.533], corr 0.716) and it still loses to the existing emission path (MAE 3.008 vs 2.85), so do not re-run dense-only head fitting; the direct successor is coverage-augmented head training (mask dense rows to supabase-like coverage before generation), still with zero LM training.

Transfers to: [[e11-s-supabase-transfer-failure-is-5472c8]], [[n-of-1-head-calibration-linear-ridgecv-a45392]], [[hyp-e9-lmhead-lora-20260814]], [[hyp-e10-hrv-localization-20260814]], [[the-hr-peak-readout-cap-is-an-a61025]], [[hrv-raw-fails-on-deployment-for-the-624895]]

## Notes
REFUTED as preregistered (blocking g1 fails: dense peak skill CI95 [-0.135,+3.643] includes zero), but the most informative refutation of the campaign. WHAT SURVIVED: (1) linear heads on L34 self-prefix features carry real per-sample signal — donor gaps +4.5..+8.7, shuffle destroys everything; (2) hrv_raw dense head decisively positive: MAE 10.75 vs trivial 18.22, skill +7.46 CI [3.73,10.93], corr 0.7175 > emission 0.592; (3) hr_raw head near-perfect dense (MAE 1.84, corr 0.952). WHAT KILLED IT: deployment transfer. Supabase skill: peak -6.97, hrv -5.36; and scaling dense training data made supa WORSE (n40 -1.74 -> n700 -6.97) — monotone evidence of covariate shift between dense-row and sparse-row L34 features, NOT a data-volume problem. This is the 'unlearned sparsity handling' clause of the problem title now localized to the feature manifold: sparse-coverage prefixes put L34 states somewhere dense-fit heads have never seen. hr_raw transfers (supa skill +1.007 CI-positive) but does not beat the existing emission path (3.008 vs 2.85), so no deployment win anywhere. PROVENANCE CORRECTION: config_note said TRAIN features oracle teacher-forced; implemented script SELF-GENERATES train prefixes (batch 1) — train/eval feature distributions matched, strictly cleaner than prereg. Successor: coverage-augmentation head training (mask dense train rows to supabase-like coverage before generation, mix sparse-adjacent datasets) — attacks the measured covariate shift directly, still zero LM training. Artifacts: scratch/e11_linear_head_probe.py, logs/e11_linear_head_probe.json, logs/e11_smoke.json, commit f89e36d.

#readout #deployment-distribution #medgemma #optihealth #collapse #baseline
