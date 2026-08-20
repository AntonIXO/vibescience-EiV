---
artifacts:
- logs/a1_state_eval.json
- logs/a1_state_eval_nonurse.json
- logs/a1_gate_results.json
- logs/a1_hr_do_no_harm.json
- logs/state_supply_12547.json
- logs/a1_campaign.log
- scratch/a1_eval_state_encoders.py
- scratch/a1_null_scope_nonurse.py
- scratch/a1_score_gates.py
- scratch/analog_state_campaign.yaml
- outputs/checkpoints/phase_a_12ch_analog_hard_s42/phase_a
- outputs/checkpoints/phase_a_12ch_analog_soft_s42/phase_a
- outputs/checkpoints/phase_a_12ch_analog_ordinal_s42/phase_a
- outputs/checkpoints/phase_a_12ch_analog_patch_s42/phase_a
closed: true
config_note: 'Corpus: data/canonical [12547,1440,12], manifest git_sha ac7d2b1c, min_coverage
  0.0 (training-time gating), admission denom `available`, splits GROWN to 600 subjects
  no-churn (verified this session: /tmp/no_churn.py, CHURN NONE). Supply re-measured
  on THIS corpus (logs/state_supply_12547.json): 7,849 labelled windows, 1,432 non-one-hot
  (18.2%), 88,323 state epochs {neutral 69561, stress 13181, flow 5581}, 539 subjects,
  22,189 patch cells at PL120/ST60. Arms: phase_a_12ch_analog_{hard,soft,ordinal,patch}
  x seeds {42,43} via scratch/analog_state_campaign.yaml (deep-copies of configs/training.yaml
  profiles + seed; generator scratch/gen_analog_campaign.py). All arms: medgemma-1.5-4b-it
  text targets, hr_coverage_denominator recording, min_hr_coverage 0.05, 10-dataset
  analog allowlist, caption hr_only, 2000 steps bf16 RTX 5070. Eval: scratch/a1_eval_state_encoders.py
  — E26 transition rows recomputed on this corpus, RF + linear readouts, each vs its
  own >=24-perm within-window null; raw_shallow + raw_patch_grid reference arms recomputed
  same-session because the corpus changed (stored comparators void per references/controlled-ab-runs.md).
  GATES: g1_patch_over_null (blocking): patch arm enc_token over_null >= +0.04 AND
  z >= +2.0 under at least one readout family, both seeds, AND over_null > 0 under
  both families. g2_control_anchored (blocking): hard arm enc_token over_null <= +0.02
  under RF on both seeds — a positive control result here means the corpus change,
  not the objective, moved the metric, and the ladder deltas are unattributable. g3_ordering
  (non-blocking): patch > soft > hard on mean over_null (RF, 2-seed mean). g4_geometry_bound_honesty
  (non-blocking): report every arm against the same-session raw_shallow and raw_patch_grid
  numbers; if any enc arm EXCEEDS raw_shallow, that is an anomaly to investigate,
  not a win to claim. g5_seed_check (blocking, procedural): sha256 of temporal_encoder.pt
  differs between s42/s43 of the same arm and phase_config.json records the intended
  seed — the E15 identical-checkpoint trap. Wall-clock budget ~8 x 15 min GPU + eval.
  Post-run: seed-verify, eval each arm, close with computed verdict.'
cost:
  gpu_hours: 0.7
  wall_hours: 3.5
crash_reason: ''
created_at: '2026-08-19T09:58:11+00:00'
diagnostics_measured:
- after: -0.0059
  before: -0.0293
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do NOT score eiv_momentary_state_auc_over_null on the full 12,547-corpus transition
  pool: 43% of rows are nurse_stress shift-schedule labels (12 subjects, 21 windows
  x ~82 rows) and even the zero-parameter raw arms lose significance there (raw_shallow
  z+7.14 clean -> +1.73 full). Rule-out that-the-full-corpus-12-547-window-flow-vs-d55a19.
  Exclude nurse_stress (or any schedule-carried cohort) from the transition eval pool,
  or fix the null to weight windows equally.'
- 'Do NOT attribute clean-pool enc_token over_null gains (+0.02..+0.04 RF band) to
  the analog state objectives: the HARD control (s43 z+2.39) and the OLD-CORPUS v5d
  incumbent (z+1.84, trained before the analog ladder existed and before the corpus
  grew) sit in the same band. The band is generic to (this pool x RF readout), not
  to any supervision change. Ridge reads of the same arms are null (+0.001..+0.033,
  mostly z<1).'
- 'Do NOT rerun the 4-objective x 2-seed ladder at 2 seeds expecting separation on
  this metric: the objective-to-objective spread is inside the instrument''s seed
  band (control moved +/-0.03 across seeds; enc_token RF null sd ~0.033 full pool,
  ~0.014 clean pool). Separation would need either a tighter instrument or >=4 seeds
  per arm.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: analog-state-supervision-a1-on-the-12-547-c5f5eb
id: exp-047-analog-state-supervision-a1-on-663b08
interpretation:
  at: '2026-08-19T16:36:12+00:00'
  failure_mode: test_invalid
  lesson: 'READ THE ''supports'' VERDICT AS DIRECTION-ONLY — the committed magnitude
    failed (see gates in notes). Two instrument findings replaced the predicted one:
    (1) full-pool contamination by nurse_stress (rule-out d55a19); (2) the clean-pool
    RF band is encoder-generic at 2 seeds. FOLLOW-UP SAME DAY (B wave, Anton-directed):
    after fixing head persistence (Phase A discarded its trained state heads at save;
    fixed 2026-08-19) and adopting state supervision into Phase B (state_weight/state_patch_weight
    on the pair stream, heads warm-started from A; plus TWO silent-drop bugs fixed:
    Phase B ignored profile caption_style entirely, and PL60 at batch 16 OOMs the
    5070), the TRAINED patch head''s own logits (zero fitted parameters, clean pool,
    200-perm within-window null) scored: A-stage PL60 s43 +0.0163 z+2.80 p.005, A→B
    PL60 s43 +0.0189 z+3.23 p.0000 — the strongest trained-component state cells on
    record, and B PRESERVED/slightly improved the A-stage readout rather than destroying
    it (the E28-era fear). But seed-mates are null (s42: A +0.006 z+1.1, B +0.003
    z+0.5) and PL120 is weak both stages, so this is ONE SEED clearing, not a confirmed
    lever: 1-hour patches (PL60/ST30, 47 patches) look like the best current candidate
    and need >=4 seeds on the clean pool before any claim. Artifacts: logs/b_wave_state_eval.json
    (both stages), scratch/b_wave_eval.py, chains outputs/checkpoints/b_patch{120,60}_s{42,43}.'
  transfers_to:
  - analog-state-supervision-a1-on-the-12-547-c5f5eb
key_numbers:
  clean_pool_hard_s43_rf_z: 2.39
  clean_pool_n: 2272.0
  clean_pool_patch_s43_rf_over_null: 0.0408
  clean_pool_patch_s43_rf_z: 2.62
  clean_pool_raw_shallow_rf_over_null: 0.0735
  clean_pool_raw_shallow_rf_z: 7.14
  clean_pool_v5d_incumbent_rf_over_null: 0.0267
  clean_pool_v5d_incumbent_rf_z: 1.84
  full_pool_n: 4001.0
  full_pool_nurse_stress_row_share: 0.432
  full_pool_patch_s42_rf_over_null: 0.0019
  full_pool_raw_shallow_rf_z: 1.73
  hr_probe_seed_band_r2_width: 0.38
  n_arms: 8.0
  rf_null_sd_full_pool: 0.033
observed_effects:
- delta: 0.0234
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_momentary_state_auc_over_null: true
provenance: {}
recipe_fingerprint:
  interventions: analog-state-supervision-ladder
verdict: supports
---

## Summary
Tests hypothesis [[analog-state-supervision-a1-on-the-12-547-c5f5eb]].

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before -0.0293 → after -0.0059  (Δ +0.0234, up ✓ predicted up)

**Verdict:** supports

## Interpretation
**Failure mode:** `test_invalid`

READ THE 'supports' VERDICT AS DIRECTION-ONLY — the committed magnitude failed (see gates in notes). Two instrument findings replaced the predicted one: (1) full-pool contamination by nurse_stress (rule-out d55a19); (2) the clean-pool RF band is encoder-generic at 2 seeds. FOLLOW-UP SAME DAY (B wave, Anton-directed): after fixing head persistence (Phase A discarded its trained state heads at save; fixed 2026-08-19) and adopting state supervision into Phase B (state_weight/state_patch_weight on the pair stream, heads warm-started from A; plus TWO silent-drop bugs fixed: Phase B ignored profile caption_style entirely, and PL60 at batch 16 OOMs the 5070), the TRAINED patch head's own logits (zero fitted parameters, clean pool, 200-perm within-window null) scored: A-stage PL60 s43 +0.0163 z+2.80 p.005, A→B PL60 s43 +0.0189 z+3.23 p.0000 — the strongest trained-component state cells on record, and B PRESERVED/slightly improved the A-stage readout rather than destroying it (the E28-era fear). But seed-mates are null (s42: A +0.006 z+1.1, B +0.003 z+0.5) and PL120 is weak both stages, so this is ONE SEED clearing, not a confirmed lever: 1-hour patches (PL60/ST30, 47 patches) look like the best current candidate and need >=4 seeds on the clean pool before any claim. Artifacts: logs/b_wave_state_eval.json (both stages), scratch/b_wave_eval.py, chains outputs/checkpoints/b_patch{120,60}_s{42,43}.

Transfers to: [[analog-state-supervision-a1-on-the-12-547-c5f5eb]]

## Key numbers
- `clean_pool_hard_s43_rf_z`: 2.39
- `clean_pool_n`: 2272
- `clean_pool_patch_s43_rf_over_null`: 0.0408
- `clean_pool_patch_s43_rf_z`: 2.62
- `clean_pool_raw_shallow_rf_over_null`: 0.0735
- `clean_pool_raw_shallow_rf_z`: 7.14
- `clean_pool_v5d_incumbent_rf_over_null`: 0.0267
- `clean_pool_v5d_incumbent_rf_z`: 1.84
- `full_pool_n`: 4001
- `full_pool_nurse_stress_row_share`: 0.432
- `full_pool_patch_s42_rf_over_null`: 0.0019
- `full_pool_raw_shallow_rf_z`: 1.73
- `hr_probe_seed_band_r2_width`: 0.38
- `n_arms`: 8
- `rf_null_sd_full_pool`: 0.033

## Artifacts
- `logs/a1_state_eval.json`
- `logs/a1_state_eval_nonurse.json`
- `logs/a1_gate_results.json`
- `logs/a1_hr_do_no_harm.json`
- `logs/state_supply_12547.json`
- `logs/a1_campaign.log`
- `scratch/a1_eval_state_encoders.py`
- `scratch/a1_null_scope_nonurse.py`
- `scratch/a1_score_gates.py`
- `scratch/analog_state_campaign.yaml`
- `outputs/checkpoints/phase_a_12ch_analog_hard_s42/phase_a`
- `outputs/checkpoints/phase_a_12ch_analog_soft_s42/phase_a`
- `outputs/checkpoints/phase_a_12ch_analog_ordinal_s42/phase_a`
- `outputs/checkpoints/phase_a_12ch_analog_patch_s42/phase_a`

## Do not retry
- Do NOT score eiv_momentary_state_auc_over_null on the full 12,547-corpus transition pool: 43% of rows are nurse_stress shift-schedule labels (12 subjects, 21 windows x ~82 rows) and even the zero-parameter raw arms lose significance there (raw_shallow z+7.14 clean -> +1.73 full). Rule-out that-the-full-corpus-12-547-window-flow-vs-d55a19. Exclude nurse_stress (or any schedule-carried cohort) from the transition eval pool, or fix the null to weight windows equally.
- Do NOT attribute clean-pool enc_token over_null gains (+0.02..+0.04 RF band) to the analog state objectives: the HARD control (s43 z+2.39) and the OLD-CORPUS v5d incumbent (z+1.84, trained before the analog ladder existed and before the corpus grew) sit in the same band. The band is generic to (this pool x RF readout), not to any supervision change. Ridge reads of the same arms are null (+0.001..+0.033, mostly z<1).
- Do NOT rerun the 4-objective x 2-seed ladder at 2 seeds expecting separation on this metric: the objective-to-objective spread is inside the instrument's seed band (control moved +/-0.03 across seeds; enc_token RF null sd ~0.033 full pool, ~0.014 clean pool). Separation would need either a tighter instrument or >=4 seeds per arm.

## Notes
VERDICT REASONING (gates were preregistered in config_note; the hypothesis record carried no formal gate objects, so they are scored here). g1 FAIL: patch enc_token on the preregistered full pool - s42 RF +0.0019/ridge +0.0510 z+1.52, s43 RF -0.0137/ridge -0.0441; nowhere near over_null>=+0.04 with z>=2 on both seeds. g2 FAIL: hard control RF s42 -0.0290 passes but s43 +0.0352 exceeds +0.02 - the control moved by more than the gate width across seeds, making ladder deltas unattributable at n=2 seeds. g3 FAIL: RF 2-seed means soft +0.0191 > hard +0.0031 > patch -0.0059, not the predicted patch>soft>hard. g4 PASS (honesty): no arm approaches the raw_shallow bound (clean pool RF 0.6230/ridge 0.6381 vs best enc 0.6051). g5 PASS: 8 unique checkpoint shas, seeds recorded.

THE INSTRUMENT FINDING THAT DOMINATES THE SCIENTIFIC OUTCOME: the preregistered full pool is CONTAMINATED (rule-out that-the-full-corpus-12-547-window-flow-vs-d55a19) - the corpus re-merge grew nurse_stress 18->102 windows and its shift-schedule rows became 43% of the transition pool, suppressing every arm INCLUDING the zero-parameter references. On the clean pool (nurse_stress excluded, n=2272/95 subj): raw_shallow recovers (RF z+7.14, ridge z+10.93); 6 of 8 trained arms clear their RF nulls (z+1.6..+2.6) - but so does the HARD control (s43 z+2.39) AND the old-corpus v5d incumbent Phase-B encoder (z+1.84), while E29 had measured the incumbent NEGATIVE (-0.0293, z-0.84) on its own pool. So the apparent encoder recovery is a property of (pool x readout), not of the analog objectives - the E34 readout-dependence lesson repeating at pool level. Ridge reads of the same arms remain null.

WHAT SURVIVES: (a) the analog ladder trains stably, costs nothing on the HR axis beyond the known seed band (logs/a1_hr_do_no_harm.json: seed dominates objective; every s42 arm >= trivial, every s43 below - same pattern all four objectives); (b) supply premises for soft/ordinal/patch confirmed at corpus scale (18.2% non-one-hot, 22,189 patch cells); (c) the raw_shallow bound (clean RF +0.0735 over null) remains unreached by any learned representation - shipping the shallow detector for the state slot remains the evidence-backed recommendation, exactly as the falsifier predicted; (d) 8 trained checkpoints + a validated two-readout eval instrument for the A2 wave (LUPI/affect), which was training during this closure.

MDE lesson for the next state arm: with enc_token RF null sd 0.033 (full) / 0.014 (clean) and a control that wobbles +/-0.03 per seed, a 2-seed 4-arm ladder cannot resolve objective effects under ~+0.05. Either evaluate on the clean pool with >=4 seeds, or build a tighter instrument (equal-window weighting in the null, or per-window paired scoring).

#state-supervision #wearables #control #epoch-resolution #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
