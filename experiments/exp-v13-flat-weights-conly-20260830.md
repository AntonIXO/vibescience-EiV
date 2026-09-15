---
artifacts:
- outputs/adapters/v13flat_c
- logs/v13_driver.log
- logs/v13_phase_c.log
- logs/emission_v13flat_supa_summary.json
- logs/emission_v13flat_dense_summary.json
- logs/v13_pool_admission.json
- scratch/run_v13_flat.sh
- scratch/_pool_admission_diff.py
closed: true
config_note: 'v13flat: lora_proxy_12ch_v13flat = v12conly with ALL 20 dataset_filter
  weights at 1.0. Same frozen encoder outputs/checkpoints/v11full/phase_b, same 20-ds
  roster, same band04 law, masked_avg pooling, fit256 prompt, seed 43. Verified pre-launch
  by dict diff: only dataset_filter + notes differ. 2600 steps, final loss 1.207,
  RC=0, 0 tracebacks/OOM. Scored: supabase n=200 batch1 rng0 + dense-128.'
cost:
  gpu_hours: 1.0
  wall_hours: 0.95
crash_reason: ''
created_at: '2026-08-30T10:55:01+00:00'
diagnostics_measured:
- after: 4.658
  before: 4.384
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.13
  before: -0.097
  diagnostic_id: eiv_gen_peak_corr
- after: 24.611
  before: 21.585
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT re-run sampler-weight variants (capping, partial flattening, per-dataset
  reweighting) against this collapse: full flattening to 1.0 on the identical recipe
  left all three gates failed (corr 0.130 vs 0.60, skill -0.704, dense 24.6 vs <8.0).
  The lever is exhausted at its maximal setting.'
- 'Do NOT read supabase MAE without corr+top1_mass: v13flat has top1_mass 0.98 on
  hr_raw — MAE 4.658 measures where the constant sits, not skill.'
- Do NOT treat 'RC=0, loss 2.13→1.21, 0 tracebacks' as health — this collapsed arm
  trained exactly that cleanly.
external_run: ''
former_ids: []
gate_results:
- evidence: 'supabase hr_raw corr = 0.130 vs required >= 0.60. v12conly read -0.097,
    healthy ab_ctrl_s43 reads +0.760 on this exact bundle. Flat weights moved corr
    by +0.227 out of the ~0.86 gap — 26% of the way, nowhere near the gate. top1_mass
    0.98 (198/200 rows emit the same value): still a constant emitter.'
  gate_id: g1-flat-weights-restore-correlation
  passed: false
- evidence: 'supabase hr_raw MAE 4.658 vs trivial 3.954 -> skill -0.704. WORSE than
    v12conly''s 4.384 (skill -0.429). Healthy reference on same bundle: 2.692 (skill
    +1.262).'
  gate_id: g2-beats-trivial
  passed: false
- evidence: dense-128 hr_raw MAE 24.611 vs required < 8.0 (trivial 7.774). WORSE than
    v11full (21.277) and v12conly (21.585). distinct=11, top1_mass 0.383 — the constant
    fragmented into a few values but tracks nothing (corr 0.023). The 13.8+ bpm dense
    deficit did not move under flat weights, exactly the preregistered signature that
    upsampling is NOT the cause.
  gate_id: g3-dense-recovers
  passed: false
git_ref: ''
hypothesis_id: the-c-stage-collapse-is-caused-by-upsampling-186858
id: exp-v13-flat-weights-conly-20260830
interpretation:
  at: '2026-08-30T10:55:34+00:00'
  failure_mode: claim_false
  lesson: 'Sampler upsampling of short lab cohorts is NOT what collapses Phase C on
    the 20-dataset roster: flattening every dataset weight to 1.0 (the lever''s maximal
    setting, single variable vs v12conly) left the constant emitter intact (top1 0.98,
    corr 0.130, dense MAE 24.6 vs trivial 7.8). Four levers are now eliminated on
    this problem — encoder, roster/hparams, frozen-encoder, sampler weights — which
    by the preregistered decision rule points at the C POOL ITSELF: InstructionDataset
    admits by min_coverage alone (no min_hr_coverage / hr_coverage_denominator), so
    the collapsed pools carry HRmin p05=29 vs the healthy band04 pool''s 330, and
    9 cohorts contribute 100% windows under 60 HR-minutes. Next test needs no GPU
    first: diff the admitted pools (healthy 6-ds vs 20-ds) on HR-coverage and target
    distributions, then one C-only arm with the pool gated to look like band04''s.'
  transfers_to:
  - the-c-stage-collapse-is-caused-by-upsampling-186858
key_numbers:
  dense_distinct: 11.0
  dense_mae: 24.611
  dense_top1: 0.383
  dense_trivial: 7.774
  supa_corr: 0.13
  supa_mae: 4.658
  supa_skill: -0.704
  supa_top1: 0.98
  supa_trivial: 3.954
  train_final_loss: 1.207
observed_effects:
- delta: 0.274
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: 0.227
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 3.026
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v12-conly-frozen-encoder-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[the-c-stage-collapse-is-caused-by-upsampling-186858]].
Branches from [[exp-v12-conly-frozen-encoder-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 4.384 → after 4.658  (Δ +0.274, up)
- [[eiv_gen_peak_corr]]: before -0.097 → after 0.13  (Δ +0.227, up ✓ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 21.585 → after 24.611  (Δ +3.026, up)

## Gates
- `g1-flat-weights-restore-correlation`: **FAIL** — supabase hr_raw corr = 0.130 vs required >= 0.60. v12conly read -0.097, healthy ab_ctrl_s43 reads +0.760 on this exact bundle. Flat weights moved corr by +0.227 out of the ~0.86 gap — 26% of the way, nowhere near the gate. top1_mass 0.98 (198/200 rows emit the same value): still a constant emitter.
- `g2-beats-trivial`: **FAIL** — supabase hr_raw MAE 4.658 vs trivial 3.954 -> skill -0.704. WORSE than v12conly's 4.384 (skill -0.429). Healthy reference on same bundle: 2.692 (skill +1.262).
- `g3-dense-recovers`: **FAIL** — dense-128 hr_raw MAE 24.611 vs required < 8.0 (trivial 7.774). WORSE than v11full (21.277) and v12conly (21.585). distinct=11, top1_mass 0.383 — the constant fragmented into a few values but tracks nothing (corr 0.023). The 13.8+ bpm dense deficit did not move under flat weights, exactly the preregistered signature that upsampling is NOT the cause.

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `claim_false`

Sampler upsampling of short lab cohorts is NOT what collapses Phase C on the 20-dataset roster: flattening every dataset weight to 1.0 (the lever's maximal setting, single variable vs v12conly) left the constant emitter intact (top1 0.98, corr 0.130, dense MAE 24.6 vs trivial 7.8). Four levers are now eliminated on this problem — encoder, roster/hparams, frozen-encoder, sampler weights — which by the preregistered decision rule points at the C POOL ITSELF: InstructionDataset admits by min_coverage alone (no min_hr_coverage / hr_coverage_denominator), so the collapsed pools carry HRmin p05=29 vs the healthy band04 pool's 330, and 9 cohorts contribute 100% windows under 60 HR-minutes. Next test needs no GPU first: diff the admitted pools (healthy 6-ds vs 20-ds) on HR-coverage and target distributions, then one C-only arm with the pool gated to look like band04's.

Transfers to: [[the-c-stage-collapse-is-caused-by-upsampling-186858]]

## Key numbers
- `dense_distinct`: 11
- `dense_mae`: 24.611
- `dense_top1`: 0.383
- `dense_trivial`: 7.774
- `supa_corr`: 0.13
- `supa_mae`: 4.658
- `supa_skill`: -0.704
- `supa_top1`: 0.98
- `supa_trivial`: 3.954
- `train_final_loss`: 1.207

## Artifacts
- `outputs/adapters/v13flat_c`
- `logs/v13_driver.log`
- `logs/v13_phase_c.log`
- `logs/emission_v13flat_supa_summary.json`
- `logs/emission_v13flat_dense_summary.json`
- `logs/v13_pool_admission.json`
- `scratch/run_v13_flat.sh`
- `scratch/_pool_admission_diff.py`

## Do not retry
- Do NOT re-run sampler-weight variants (capping, partial flattening, per-dataset reweighting) against this collapse: full flattening to 1.0 on the identical recipe left all three gates failed (corr 0.130 vs 0.60, skill -0.704, dense 24.6 vs <8.0). The lever is exhausted at its maximal setting.
- Do NOT read supabase MAE without corr+top1_mass: v13flat has top1_mass 0.98 on hr_raw — MAE 4.658 measures where the constant sits, not skill.
- Do NOT treat 'RC=0, loss 2.13→1.21, 0 tracebacks' as health — this collapsed arm trained exactly that cleanly.

## Notes
Fourth consecutive refutation on this problem (roster, frozen-encoder, now upsampling; plus exp-051 discriminator). Preregistered decision rule for the g1-FAIL + dense~20 branch: upsampling is NOT the cause; the remaining difference between every healthy and every collapsed C arm is the C POOL ITSELF. InstructionDataset gates on min_coverage alone (accepts neither min_hr_coverage nor hr_coverage_denominator), so collapsed pools carry HRmin p05=29 vs healthy band04's p05=330, <60min share 9.8% vs 2.0%, with 9 cohorts at 100% short windows. Next probe is free: compare admitted C pools (v10conly 3-ds filter vs 20-ds) on window count, HR-coverage and target-value distributions — no training needed. Wall 57 min (12:56:35–13:53:31).

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
