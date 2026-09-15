---
artifacts:
- logs/emission_v11full_supa.json
- logs/emission_v11full_dense.json
- logs/v11_scoring.log
- logs/v11_phase_a.log
- logs/v11_phase_b.log
- logs/v11_phase_c.log
- scratch/run_v11_chain.sh
- scratch/score_v11.sh
- outputs/adapters/v11full_c/manifest.json
closed: true
config_note: 'v11 FULL CHAIN A->B->C on the synced corpus [14092,1440,12], merge git_sha
  b659855, 21 datasets. Runner scratch/run_v11_chain.sh; scoring scratch/score_v11.sh.


  ALL THREE STAGES RC=0 with ZERO tracebacks/OOM (grep -ciE ''traceback|outofmemory|CUDA
  out of memory'' returns 0 on each of logs/v11_phase_{a,b,c}.log). Adapter outputs/adapters/v11full_c
  written; manifest asserts 20-dataset filter, patch_pool_mode=masked_avg, prompt
  json_slots_prompt_fit256.md, seed 43, n_train_rows 9555, tensor shape [14092,1440,12],
  git_sha b659855ed6fc.


  PROFILES: phase_a_12ch_v11full -> phase_b_12ch_v11full -> lora_proxy_12ch_v11full.
  Roster 19 datasets A/B (dryad + nurse_stress excluded), 20 for C (nurse_stress restored:
  its exclusion is a state-scoring rule and C is json-slot supervised).


  SUPERVISION REACH VERIFIED BEFORE LAUNCH: label-epochs 44.2% -> 94.7%, EDA 33.5%
  -> 100%, six previously-zero-reach channels restored (valence 0 -> 40,893, arousal
  0 -> 6,457, protocol_condition, exam_session_code, stress_raw, stimulus_class).
  Admitted pool carries state on 59.4% of windows, affect on 16.8% from 7 sources.


  UPSAMPLING priced pre-run: short lab cohorts 10.3% -> 29.2% of the sampler, valence
  sources 17.6% -> 37.8%, lifesnaps 50.8% -> 36.2%, capped at 7x.


  EVAL: data/eval_v3_1min rebuilt from the current tensor (sha 3e51df88, 6,745 strict-clean
  of 14,092) before scoring. Capture = scratch/emission_diversity_audit.py, supabase
  200 rows batch 1 rng seed 0, parse_ok 200/200.'
cost:
  gpu_hours: 0.9
  wall_hours: 1.1
crash_reason: ''
created_at: '2026-08-30T08:21:53+00:00'
diagnostics_measured:
- after: 5.49
  before: 2.836
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: -0.035
  before: 0.739
  diagnostic_id: eiv_gen_peak_corr
- after: 21.277
  before: 3.573
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT propose another ROSTER or SAMPLING variant for the fresh-chain collapse.
  This arm removed the roster confound completely (44.2% -> 94.7% label reach, 33.5%
  -> 100% EDA, six zero-reach channels restored) and the collapse got WORSE, not better:
  corr 0.307 -> -0.035, dense MAE 3.573 -> 21.277. The roster was a real defect and
  a real confound; it is not the cause.'
- Do NOT read 'all stages RC=0, loss falling' as evidence a chain is healthy. Phase
  A fell 2.699 -> -15.772, Phase C ~1.2 -> ~0.5, zero tracebacks, zero OOM -- and
  the adapter emits one value on 80.5% of rows with corr -0.035. Loss curves cannot
  see a constant-output collapse; only an emission capture can.
- Do NOT quote the 3.19 bpm full-chain seed band as cover for this result. The band
  applies to comparisons between healthy arms. g1 was an ABSOLUTE bar (beat the trivial)
  and the arm missed it by 1.535 bpm, while corr -0.035 and top1 0.805 are outside
  anything a seed band describes.
- Do NOT run more full A->B->C chains on a new corpus until the collapse mechanism
  is isolated. Five fresh chains have now failed the same way (ab_ctrl, ab_wl, c_patch120
  x2, baseline_sync, v11full) across three different corpora and three different rosters,
  while frozen-A/B C-only families stay healthy. The next spend must be the C-ONLY
  design off a frozen encoder, which is one variable and ~3 GPU-hours.
external_run: logs/emission_v11full_supa.json
former_ids: []
gate_results:
- evidence: FAIL, and not marginally. supabase hr_raw MAE 5.490 vs trivial 3.954 ->
    skill -1.535 bpm. The gate asked for skill > 0; the arm is WORSE than always predicting
    the median, and worse than baseline_sync (-1.066) which was the failure this chain
    existed to fix. Against the v10conly family median (2.836) the gap is +2.654 bpm,
    which does sit inside the 3.19 bpm full-chain re-seed band -- but the band is
    irrelevant here, because the gate was 'beats the trivial', an absolute bar, and
    it is missed by 1.535 bpm.
  gate_id: g1-supabase-beats-trivial
  passed: false
- evidence: 'FAIL, decisively, and this is the informative one. supabase hr_raw corr
    = -0.035 against a >= 0.60 bar. Not merely low -- ZERO relationship with truth,
    and marginally negative. Paired with top1_mass 0.805 (the arm emits a single value
    on 80.5% of the 200 rows, distinct 10) this is a CONSTANT-OUTPUT COLLAPSE, not
    a degraded regression. baseline_sync''s 0.307 was a partial collapse; this is
    a complete one. hr_peak agrees: corr -0.047, skill -11.601. The chain trained
    clean (RC=0, no OOM, loss falling 2.699 -> -15.772 at A and ~1.2 -> ~0.5 at C)
    and still produced a model that ignores its input.'
  gate_id: g2-corr-not-collapsed
  passed: false
- evidence: 'PASS, asserted from the shipped manifest before any emission was scored:
    dataset_filter has 20 datasets, n_train_rows 9555, tensor [14092,1440,12], git_sha
    b659855ed6fc, patch_pool_mode masked_avg, prompt json_slots_prompt_fit256.md,
    seed 43. Pre-run reach measurement: label-epochs 94.7% (vs band04''s 44.2%), EDA
    100% (vs 33.5%), and the six previously-zero-reach channels all restored. So the
    profile did exactly what its notes claim -- the supervision reached the model.
    That is precisely what makes g1/g2 interpretable: the roster confound is REMOVED
    and the deployment failure survived it.'
  gate_id: g3-supervision-actually-reaches
  passed: true
- evidence: 'FAIL, catastrophically. dense-128 hr_raw MAE 21.277 vs trivial 7.774
    -> skill -13.503, against the v10conly median of 3.573 and a bar of ''no worse
    than 1.0 bpm''. The dense seed band is 1.483 (avg) / 2.261 (masked), so this is
    roughly 8x outside it. corr -0.099, top1_mass 0.344. Non-blocking by design, but
    it removes the last benign reading: the collapse is not specific to the sparse
    deployment population, it is present on the dense population the model was mostly
    trained on.'
  gate_id: g4-dense-no-harm
  passed: false
git_ref: ''
hypothesis_id: a-full-a-b-c-retrain-whose-roster-actually-052f9a
id: exp-v11-full-corpus-chain-20260830
interpretation:
  at: '2026-08-30T08:23:05+00:00'
  failure_mode: claim_false
  lesson: 'THE ROSTER WAS A REAL DEFECT AND A REAL CONFOUND, AND IT IS NOT THE CAUSE.
    This arm removed it completely -- label-epoch reach 44.2% -> 94.7%, EDA 33.5%
    -> 100%, six previously-zero-reach channels restored, asserted from the shipped
    manifest before a single emission was scored -- and the deployment collapse got
    WORSE, not better: corr 0.307 -> -0.035, supabase skill -1.066 -> -1.535, dense
    MAE 3.573 -> 21.277. My prediction (2.8-4.0 bpm, corr >= 0.60) was wrong in both
    magnitude and sign of usefulness, exactly as the calibration warning I attached
    to it said it might be.


    THE SHAPE OF THE FAILURE IS THE DIAGNOSTIC. top1_mass 0.805 with distinct 10 and
    corr -0.035 is not a degraded regression -- it is a CONSTANT-OUTPUT COLLAPSE.
    The adapter emits one value on 80.5% of rows and its output has no relationship
    to the input. baseline_sync at corr 0.307 was a partial collapse; this is complete.
    Both hr_raw and hr_peak agree, and it is present on the DENSE population too (corr
    -0.099), so it is not a sparse-deployment artifact.


    FIVE FRESH CHAINS, THREE CORPORA, THREE ROSTERS, SAME FAILURE. ab_ctrl (6.03),
    ab_wl (20.28), c_patch120 x2 (23.6/24.6), baseline_sync (5.02), v11full (5.49)
    -- across 12,547 / 13,518 / 14,092 windows and 3-ds / 6-ds / 20-ds rosters. Every
    C-only family off a FROZEN A/B init is healthy on the same draws (v9band04 3.04,
    v10conly 2.74-3.54). The variable common to all six failures and absent from all
    healthy arms is RETRAINING PHASE A/B AT ALL. Corpus, roster, sampling and supervision
    reach have now each been varied without breaking that pattern.


    WHY NOTHING WARNED US, and this is the reusable part: all three stages exited
    RC=0 with zero tracebacks and zero OOM, and the losses fell normally (Phase A
    2.699 -> -15.772, Phase C ~1.2 -> ~0.5). A falling loss is fully compatible with
    a projector whose output the LM has learned to ignore. Only an emission capture
    can see this. Treat ''the chain completed and the loss went down'' as evidence
    of nothing about deployment.


    CONSEQUENCE: stop funding roster/sampling variants of the fresh chain. The next
    spend is the C-ONLY design off the frozen v11 phase_b at 3 seeds (~3 GPU-hours),
    which is one variable against the 0.80 bpm C-only band and directly tests whether
    a v11-corpus ENCODER can support a healthy C stage at all. If C-only off v11 phase_b
    is healthy, the defect is in the C stage''s interaction with a freshly-trained
    encoder; if it collapses too, the defect is in Phase A/B training on any post-12,547
    corpus and the encoder is where to look.'
  transfers_to:
  - the-window-level-ce-state-term-trains-on-a35561
key_numbers:
  dense_corr: -0.099
  dense_mae: 21.277
  dense_skill: -13.503
  dense_trivial: 7.774
  label_epoch_reach_after: 0.947
  label_epoch_reach_before: 0.442
  n_train_rows: 9555.0
  supabase_corr: -0.035
  supabase_distinct: 10.0
  supabase_mae: 5.49
  supabase_skill: -1.535
  supabase_top1_mass: 0.805
  supabase_trivial: 3.954
  v10conly_median: 2.836
observed_effects:
- delta: 2.654
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: -0.774
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: 17.704
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[a-full-a-b-c-retrain-whose-roster-actually-052f9a]].
External run: logs/emission_v11full_supa.json

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 2.836 → after 5.49  (Δ +2.654, up ✗ predicted down)
- [[eiv_gen_peak_corr]]: before 0.739 → after -0.035  (Δ -0.774, down ✗ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 3.573 → after 21.277  (Δ +17.704, up)

## Gates
- `g1-supabase-beats-trivial`: **FAIL** — FAIL, and not marginally. supabase hr_raw MAE 5.490 vs trivial 3.954 -> skill -1.535 bpm. The gate asked for skill > 0; the arm is WORSE than always predicting the median, and worse than baseline_sync (-1.066) which was the failure this chain existed to fix. Against the v10conly family median (2.836) the gap is +2.654 bpm, which does sit inside the 3.19 bpm full-chain re-seed band -- but the band is irrelevant here, because the gate was 'beats the trivial', an absolute bar, and it is missed by 1.535 bpm.
- `g2-corr-not-collapsed`: **FAIL** — FAIL, decisively, and this is the informative one. supabase hr_raw corr = -0.035 against a >= 0.60 bar. Not merely low -- ZERO relationship with truth, and marginally negative. Paired with top1_mass 0.805 (the arm emits a single value on 80.5% of the 200 rows, distinct 10) this is a CONSTANT-OUTPUT COLLAPSE, not a degraded regression. baseline_sync's 0.307 was a partial collapse; this is a complete one. hr_peak agrees: corr -0.047, skill -11.601. The chain trained clean (RC=0, no OOM, loss falling 2.699 -> -15.772 at A and ~1.2 -> ~0.5 at C) and still produced a model that ignores its input.
- `g3-supervision-actually-reaches`: PASS — PASS, asserted from the shipped manifest before any emission was scored: dataset_filter has 20 datasets, n_train_rows 9555, tensor [14092,1440,12], git_sha b659855ed6fc, patch_pool_mode masked_avg, prompt json_slots_prompt_fit256.md, seed 43. Pre-run reach measurement: label-epochs 94.7% (vs band04's 44.2%), EDA 100% (vs 33.5%), and the six previously-zero-reach channels all restored. So the profile did exactly what its notes claim -- the supervision reached the model. That is precisely what makes g1/g2 interpretable: the roster confound is REMOVED and the deployment failure survived it.
- `g4-dense-no-harm`: **FAIL** — FAIL, catastrophically. dense-128 hr_raw MAE 21.277 vs trivial 7.774 -> skill -13.503, against the v10conly median of 3.573 and a bar of 'no worse than 1.0 bpm'. The dense seed band is 1.483 (avg) / 2.261 (masked), so this is roughly 8x outside it. corr -0.099, top1_mass 0.344. Non-blocking by design, but it removes the last benign reading: the collapse is not specific to the sparse deployment population, it is present on the dense population the model was mostly trained on.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

THE ROSTER WAS A REAL DEFECT AND A REAL CONFOUND, AND IT IS NOT THE CAUSE. This arm removed it completely -- label-epoch reach 44.2% -> 94.7%, EDA 33.5% -> 100%, six previously-zero-reach channels restored, asserted from the shipped manifest before a single emission was scored -- and the deployment collapse got WORSE, not better: corr 0.307 -> -0.035, supabase skill -1.066 -> -1.535, dense MAE 3.573 -> 21.277. My prediction (2.8-4.0 bpm, corr >= 0.60) was wrong in both magnitude and sign of usefulness, exactly as the calibration warning I attached to it said it might be.

THE SHAPE OF THE FAILURE IS THE DIAGNOSTIC. top1_mass 0.805 with distinct 10 and corr -0.035 is not a degraded regression -- it is a CONSTANT-OUTPUT COLLAPSE. The adapter emits one value on 80.5% of rows and its output has no relationship to the input. baseline_sync at corr 0.307 was a partial collapse; this is complete. Both hr_raw and hr_peak agree, and it is present on the DENSE population too (corr -0.099), so it is not a sparse-deployment artifact.

FIVE FRESH CHAINS, THREE CORPORA, THREE ROSTERS, SAME FAILURE. ab_ctrl (6.03), ab_wl (20.28), c_patch120 x2 (23.6/24.6), baseline_sync (5.02), v11full (5.49) -- across 12,547 / 13,518 / 14,092 windows and 3-ds / 6-ds / 20-ds rosters. Every C-only family off a FROZEN A/B init is healthy on the same draws (v9band04 3.04, v10conly 2.74-3.54). The variable common to all six failures and absent from all healthy arms is RETRAINING PHASE A/B AT ALL. Corpus, roster, sampling and supervision reach have now each been varied without breaking that pattern.

WHY NOTHING WARNED US, and this is the reusable part: all three stages exited RC=0 with zero tracebacks and zero OOM, and the losses fell normally (Phase A 2.699 -> -15.772, Phase C ~1.2 -> ~0.5). A falling loss is fully compatible with a projector whose output the LM has learned to ignore. Only an emission capture can see this. Treat 'the chain completed and the loss went down' as evidence of nothing about deployment.

CONSEQUENCE: stop funding roster/sampling variants of the fresh chain. The next spend is the C-ONLY design off the frozen v11 phase_b at 3 seeds (~3 GPU-hours), which is one variable against the 0.80 bpm C-only band and directly tests whether a v11-corpus ENCODER can support a healthy C stage at all. If C-only off v11 phase_b is healthy, the defect is in the C stage's interaction with a freshly-trained encoder; if it collapses too, the defect is in Phase A/B training on any post-12,547 corpus and the encoder is where to look.

Transfers to: [[the-window-level-ce-state-term-trains-on-a35561]]

## Key numbers
- `dense_corr`: -0.099
- `dense_mae`: 21.277
- `dense_skill`: -13.503
- `dense_trivial`: 7.774
- `label_epoch_reach_after`: 0.947
- `label_epoch_reach_before`: 0.442
- `n_train_rows`: 9555
- `supabase_corr`: -0.035
- `supabase_distinct`: 10
- `supabase_mae`: 5.49
- `supabase_skill`: -1.535
- `supabase_top1_mass`: 0.805
- `supabase_trivial`: 3.954
- `v10conly_median`: 2.836

## Artifacts
- `logs/emission_v11full_supa.json`
- `logs/emission_v11full_dense.json`
- `logs/v11_scoring.log`
- `logs/v11_phase_a.log`
- `logs/v11_phase_b.log`
- `logs/v11_phase_c.log`
- `scratch/run_v11_chain.sh`
- `scratch/score_v11.sh`
- `outputs/adapters/v11full_c/manifest.json`

## Do not retry
- Do NOT propose another ROSTER or SAMPLING variant for the fresh-chain collapse. This arm removed the roster confound completely (44.2% -> 94.7% label reach, 33.5% -> 100% EDA, six zero-reach channels restored) and the collapse got WORSE, not better: corr 0.307 -> -0.035, dense MAE 3.573 -> 21.277. The roster was a real defect and a real confound; it is not the cause.
- Do NOT read 'all stages RC=0, loss falling' as evidence a chain is healthy. Phase A fell 2.699 -> -15.772, Phase C ~1.2 -> ~0.5, zero tracebacks, zero OOM -- and the adapter emits one value on 80.5% of rows with corr -0.035. Loss curves cannot see a constant-output collapse; only an emission capture can.
- Do NOT quote the 3.19 bpm full-chain seed band as cover for this result. The band applies to comparisons between healthy arms. g1 was an ABSOLUTE bar (beat the trivial) and the arm missed it by 1.535 bpm, while corr -0.035 and top1 0.805 are outside anything a seed band describes.
- Do NOT run more full A->B->C chains on a new corpus until the collapse mechanism is isolated. Five fresh chains have now failed the same way (ab_ctrl, ab_wl, c_patch120 x2, baseline_sync, v11full) across three different corpora and three different rosters, while frozen-A/B C-only families stay healthy. The next spend must be the C-ONLY design off a frozen encoder, which is one variable and ~3 GPU-hours.

## Notes
THE ROSTER CONFOUND IS REMOVED AND THE COLLAPSE SURVIVED IT. That is the finding, and it is worth more than the failed gates.

g3 PASSED on the shipped manifest: 20 datasets, 9,555 train rows, tensor [14092,1440,12], masked_avg, fit256 prompt. Reach went 44.2% -> 94.7% of label-epochs and 33.5% -> 100% of EDA, with six previously-zero-reach channels restored. So this chain saw the corpus it advertised.

And it collapsed HARDER than the arm it was meant to fix:
  supabase hr_raw MAE 5.490 vs trivial 3.954 -> skill -1.535 (baseline_sync was -1.066)
  supabase corr -0.035, top1_mass 0.805, distinct 10  <- constant output
  dense-128 MAE 21.277 vs trivial 7.774 -> skill -13.503, corr -0.099
  hr_peak skill -11.601, corr -0.047

All three stages RC=0, zero tracebacks, zero OOM, losses falling normally (A 2.699 -> -15.772; C ~1.2 -> ~0.5). Nothing in the training signal indicated failure.

WHAT THIS ELIMINATES: the roster/supervision-reach explanation for the fresh-chain collapse, which was the most plausible remaining candidate and the reason this arm was funded. Five fresh full chains have now failed the same way across three corpora (12,547 / 13,518 / 14,092) and three rosters (3-ds, 6-ds, 20-ds), while every C-only family off a FROZEN A/B init stays healthy.

WHAT REMAINS: the variable common to all five failures is retraining Phase A/B at all. The discriminating experiment is unchanged and now much better motivated -- C-only off the frozen v11 phase_b, 3 seeds, ~3 GPU-hours, one variable, priced against the 0.80 bpm C-only band.

#deployment-distribution #wearables #baseline #sensor-text #epoch-resolution #collapse #matched-baseline #optihealth-eiv
