---
created_at: '2026-08-14T11:44:49+00:00'
gates:
- blocking: true
  description: 'hrv_raw per-user head, forward-chained OOS on time-ordered supa rows
    (expanding window, min-train 60): skill vs BEST trivial (max of train-median-forward
    and day-persistence) paired bootstrap CI95 > 0. Persistence is mandatory as trivial
    for N-of-1 time series.'
  id: g1-peruser-hrv-oos-skill
- blocking: true
  description: 'Controls: shuffled-target refit destroys OOS skill (<=0); alpha not
    railed at grid edge; n_test >= 60 pooled across folds.'
  id: g2-controls-clean
- blocking: false
  description: hr_raw per-user head OOS MAE beats the production emission path on
    the same test rows (emission supa hr MAE 2.85 reference; compute emission MAE
    restricted to test rows for exact pairing).
  id: g3-hr-raw-beats-emission
- blocking: false
  description: 'hr_peak per-user head OOS skill vs best trivial > 0 (E11/E12 dense-fit
    heads: −7.0 / −10.6 on supa).'
  id: g4-peak-recovery
id: n-of-1-head-calibration-linear-ridgecv-heads-fit-on-the-deployment-user-s-own-su
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
  magnitude_note: 'supa hrv head skill vs trivial: E12 mix −1.676 → positive (predict
    +1..+3 MAE-units vs best trivial) if L34 features on self-generated supabase prefixes
    carry per-user hrv signal'
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: per-user hr_raw head OOS MAE vs emission path 2.85 — predict modest
    improvement or parity; parity already validates the head-merge architecture for
    hrv
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
problem_tags: []
status: inconclusive
supersedes: null
topic_tags:
- readout
- deployment-distribution
- optihealth
- baseline
- collapse
---

## Statement
N-of-1 head calibration: linear RidgeCV heads fit ON the deployment user's own supabase L34 features (self-generated prefixes, E12 cache) achieve positive out-of-sample skill under forward-chaining time splits — beating both always-train-median and day-persistence trivials for hrv_raw, and matching or beating the emission path for hr_raw — establishing per-user calibration as the deployment architecture for scalar slots.

## Rationale
E12 closed directional_only: coverage masks explain only ~20% of the dense→supa gap; aug-mix training closed 69% of the hrv gap-to-trivial but never crossed it, and hurt hr slots. Then a metadata check showed ALL 223 supabase rows share subject_id 9e73d06e-1006-4266-bde7-85659f5de69d — the deployment distribution IS one user (the N-of-1 platform premise). Cross-population fitting was solving the wrong problem: the honest question is whether L34 features from self-generated supabase prefixes support a WITHIN-user linear readout. E12 cached those features (scratch/tmp_e12_feats_supa.npz, 200 rows with truths), so the test is CPU-only, seconds, zero GPU. Honest trivials for one subject's time series: train-median forward AND day-persistence (Anton's standing bar: never claim skill over a trivial baseline you didn't run). Forward-chaining prevents temporal leakage. E28 linear-only rule-out respected.

## Plan


## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Predicts [[eiv_nonhr_channel_grouped_r2]] (up).
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g1-peruser-hrv-oos-skill` (blocking): hrv_raw per-user head, forward-chained OOS on time-ordered supa rows (expanding window, min-train 60): skill vs BEST trivial (max of train-median-forward and day-persistence) paired bootstrap CI95 > 0. Persistence is mandatory as trivial for N-of-1 time series.
Gate `g2-controls-clean` (blocking): Controls: shuffled-target refit destroys OOS skill (<=0); alpha not railed at grid edge; n_test >= 60 pooled across folds.
Gate `g3-hr-raw-beats-emission` (advisory): hr_raw per-user head OOS MAE beats the production emission path on the same test rows (emission supa hr MAE 2.85 reference; compute emission MAE restricted to test rows for exact pairing).
Gate `g4-peak-recovery` (advisory): hr_peak per-user head OOS skill vs best trivial > 0 (E11/E12 dense-fit heads: −7.0 / −10.6 on supa).

#readout #deployment-distribution #optihealth #baseline #collapse
