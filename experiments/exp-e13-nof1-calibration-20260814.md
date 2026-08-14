---
artifacts: []
closed: true
config_note: 'E13 N-of-1 head calibration, CPU-only (~seconds), zero GPU. Features:
  E12 cache scratch/tmp_e12_feats_supa.npz (L34 hidden at slot value positions, self-generated
  supabase prefixes, batch-1 discipline inherited from frozen captures v9band04_s42_emissions_supabase.json).
  Rows time-ordered by sample_metadata start_time (single subject 9e73d06e, days 2025-10-10
  onward). Forward-chaining expanding-window: min_train 60, 5 folds over remaining
  rows. Heads RidgeCV(24 alphas) standardized, linear only (E28). Trivials per fold:
  train-median-forward AND day-persistence (previous captured day''s truth); skill
  scored vs the BEST trivial per fold. Controls: shuffled-target refit, alpha rail
  check. Emission-path pairing: recompute emission hr MAE restricted to test rows.
  Script scratch/e13_nof1_head_calibration.py -> logs/e13_nof1_head_calibration.json.'
crash_reason: ''
created_at: '2026-08-14T11:44:57+00:00'
diagnostics_measured:
- after: 1.322
  before: -1.676
  diagnostic_id: eiv_nonhr_channel_grouped_r2
- after: 2.689
  before: 2.85
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
external_run: ''
gate_results:
- evidence: 'PASS numerically but fragile: hrv_raw walk-forward 5-fold OOS MAE 5.709
    vs best trivial (persistence 7.031; median 8.847), skill +1.322 CI95[0.179, 2.483],
    corr 0.703, n_test 137. CAVEAT (see g2): skill concentrates in fold 5 (+4.02);
    folds 1-4 pooled +0.613 CI[-0.555, 1.680] — not CI-positive without the final
    fold.'
  gate_id: g1-peruser-hrv-oos-skill
  passed: true
- evidence: 'FAIL on alpha rail: hrv fold-5 RidgeCV selected grid-edge alpha 0.01;
    extended-grid diagnostic (logspace −6..6, 40 pts, scratch/tmp_e13_alpha_diag.py)
    STILL rails at 1e-6 — fold 5 wants ~zero regularization, and it is the fold carrying
    the skill. Shuffle control clean (−2.024 ≤ 0), n_test 137 ≥ 60. Blocking fail
    stands: the hrv claim is fold-dependent + degenerate-regularization.'
  gate_id: g2-controls-clean
  passed: false
- evidence: 'PASS: hr_raw per-user head OOS MAE 2.689 vs emission path 2.980 on the
    SAME 140 test rows (skill vs trivial +1.641 CI[1.049, 2.235], corr 0.747, alphas
    interior). First supabase-side result to beat the production emission path. Note
    hr_raw shuffle ''skill'' +0.208 — artifact of shrunk-to-mean shuffled model vs
    median trivial, not signal (real head +1.641 ≫ +0.208).'
  gate_id: g3-hr-raw-beats-emission
  passed: true
- evidence: 'PASS by letter, not spirit: hr_peak skill vs best trivial +0.201 but
    CI[-0.933, 1.275] includes zero; corr 0.205; alphas railed HIGH (49619..1e5 —
    model shrinking toward mean = giving up). Head beats emission on paired rows (17.412
    vs 19.747) but neither beats trivial median 17.613 meaningfully. hr_peak remains
    unsolved within-user.'
  gate_id: g4-peak-recovery
  passed: true
git_ref: ''
hypothesis_id: n-of-1-head-calibration-linear-ridgecv-heads-fit-on-the-deployment-user-s-own-su
id: exp-e13-nof1-calibration-20260814
observed_effects:
- delta: 2.998
  diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
- delta: -0.161
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-e12-coverage-aug-heads-20260814
prediction_match:
  overall: true
  per_diagnostic:
    eiv_nonhr_channel_grouped_r2: true
    eiv_supabase_hr_raw_mae_bpm: true
provenance: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[n-of-1-head-calibration-linear-ridgecv-heads-fit-on-the-deployment-user-s-own-su]].
Branches from [[exp-e12-coverage-aug-heads-20260814]].

## Measurements
- [[eiv_nonhr_channel_grouped_r2]]: before -1.676 → after 1.322  (Δ +2.998, up ✓ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 2.85 → after 2.689  (Δ -0.161, down ✓ predicted down)

## Gates
- `g1-peruser-hrv-oos-skill`: PASS — PASS numerically but fragile: hrv_raw walk-forward 5-fold OOS MAE 5.709 vs best trivial (persistence 7.031; median 8.847), skill +1.322 CI95[0.179, 2.483], corr 0.703, n_test 137. CAVEAT (see g2): skill concentrates in fold 5 (+4.02); folds 1-4 pooled +0.613 CI[-0.555, 1.680] — not CI-positive without the final fold.
- `g2-controls-clean`: **FAIL** — FAIL on alpha rail: hrv fold-5 RidgeCV selected grid-edge alpha 0.01; extended-grid diagnostic (logspace −6..6, 40 pts, scratch/tmp_e13_alpha_diag.py) STILL rails at 1e-6 — fold 5 wants ~zero regularization, and it is the fold carrying the skill. Shuffle control clean (−2.024 ≤ 0), n_test 137 ≥ 60. Blocking fail stands: the hrv claim is fold-dependent + degenerate-regularization.
- `g3-hr-raw-beats-emission`: PASS — PASS: hr_raw per-user head OOS MAE 2.689 vs emission path 2.980 on the SAME 140 test rows (skill vs trivial +1.641 CI[1.049, 2.235], corr 0.747, alphas interior). First supabase-side result to beat the production emission path. Note hr_raw shuffle 'skill' +0.208 — artifact of shrunk-to-mean shuffled model vs median trivial, not signal (real head +1.641 ≫ +0.208).
- `g4-peak-recovery`: PASS — PASS by letter, not spirit: hr_peak skill vs best trivial +0.201 but CI[-0.933, 1.275] includes zero; corr 0.205; alphas railed HIGH (49619..1e5 — model shrinking toward mean = giving up). Head beats emission on paired rows (17.412 vs 19.747) but neither beats trivial median 17.613 meaningfully. hr_peak remains unsolved within-user.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
N-OF-1 CALIBRATION: hr_raw CONFIRMED-grade, hrv PROMISING-BUT-FRAGILE, peak dead. Key discovery driving this arm: ALL 223 supabase rows are one subject (9e73d06e) — deployment is N-of-1 by construction; E11/E12's cross-population fitting was the wrong frame. Results (walk-forward, 5 expanding folds, min_train 60, E12 supa feature cache, CPU seconds): (1) hr_raw: OOS MAE 2.689 beats BOTH trivials (median-fwd 4.33, persistence 5.455) AND the production emission path on identical test rows (2.980), skill +1.641 CI[1.049,2.235], interior alphas, corr 0.747 — the campaign's first supabase-side beat of the emission path. (2) hrv_raw: skill vs persistence +1.322 CI[0.179,2.483], corr 0.703, shuffle clean — BUT skill concentrates in fold 5 (+4.02; folds1-4 +0.613 CI includes 0) and fold-5 alpha rails at grid edge even on extended grid (1e-6) — g2 blocking fail, honest read: suggestive, needs more days of data, not deployable claim. (3) hr_peak: nothing within-user either (skill +0.201 CI includes 0, alphas railed high = model gave up); combined with E11/E12, hr_peak L34 features appear to lack usable peak info under ALL fitting regimes tried — candidate rule-out after one more targeted look. ARCHITECTURE IMPLICATION for Anton: per-user linear heads reading L34 at value positions, continually refit on the user's accumulating history (RidgeCV, seconds of CPU), beat the frozen emission path for hr_raw NOW and plausibly hrv with more data. This is arm-B-shaped (eiv annotate merge) — needs Anton's sign-off, bring numbers not code. Artifacts: scratch/e13_nof1_head_calibration.py, logs/e13_nof1_head_calibration.json, alpha diag scratch/tmp_e13_alpha_diag.py.

#readout #deployment-distribution #optihealth #baseline #collapse
