---
artifacts:
- logs/b_wave_state_eval.json
- logs/a4_eval.log
- logs/a4_campaign.log
- scratch/b_wave_eval.py
- scratch/run_a4_seeds.sh
- outputs/checkpoints/b_patch60_s42/phase_b
- outputs/checkpoints/b_patch60_s43/phase_b
- outputs/checkpoints/b_patch60_s44/phase_b
- outputs/checkpoints/b_patch60_s45/phase_b
- outputs/checkpoints/b_patch60_s46/phase_b
closed: true
config_note: 'Parent: exp-047-analog-state-supervision-a1-on-663b08 (test_invalid
  — this child runs the FIXED harness: trained-head instrument, clean pool, head persistence
  + Phase-B state supervision from commits of 2026-08-19). Corpus data/canonical [12547,1440,12]
  sha ac7d2b1c unchanged. Chains: phase_a_12ch_analog_patch60_s{44,45,46} (PL60/ST30,
  2000 steps, bf16) → b_patch60_s{44,45,46} (1000 steps, state_weight 0.5, state_patch_weight
  0.5, triplet 0.2, batch 8 — b16 OOMs at 47 patches). Runner scratch/run_a4_seeds.sh
  with traceback-grep guards (the EXIT-0-masks-OOM trap fired twice today). Eval:
  scratch/b_wave_eval.py — trained patch head''s own logits at clean-pool transition
  epochs (n=2272, 95 subj, 684 windows, pos .521), flow-vs-stress restricted two-class
  softmax read averaged over covering patches, 200 within-window label perms, zero
  fitted params. Existing 2-seed cells (PL60 B): s42 +0.0028 z+0.48, s43 +0.0189 z+3.23.
  DECISION RULE preregistered in the hypothesis statement: CONFIRM >=3/5 positive
  AND >=2/5 z>=2 AND pooled mean >= +0.008; REFUTE <=1/5 z>=2; else INCONCLUSIVE.
  Secondary non-blocking: per-seed A-vs-B paired deltas (B harm check).'
cost:
  gpu_hours: 0.65
  wall_hours: 0.7
crash_reason: ''
created_at: '2026-08-19T17:39:49+00:00'
diagnostics_measured:
- after: 0.0079
  before: 0.0189
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do NOT buy more PL60/ST30 analog A->B chains at 2000/1000 steps expecting a z>=2
  trained-head state readout: 5 seeds measured {s42 +0.0028 z+0.48, s43 +0.0189 z+3.23,
  s44 +0.0061 z+0.89, s45 +0.0065 z+1.19, s46 +0.0051 z+0.77} - exactly 1/5 clears
  z>=2, pooled mean +0.0079 under the preregistered +0.008 bar. s43 was the seed lottery.
  The RESIDUAL that stays open: all 5 seeds are POSITIVE (sign-test p~=0.031 under
  a fair coin), i.e. a weak consistent ~+0.006 effect may exist BELOW single-seed
  resolution - the honest test for it is a pooled/paired estimator or a bigger corpus
  (K-EmoPhone/StressID), not another seed of the same arm.'
- 'Do NOT launch the prepared C wave (c_patch60, scratch/run_c_wave.sh) on state grounds:
  its premise was a confirmed PL60 state lever and the lever is refuted at this n.
  The script is kept for when the corpus grows.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b
id: exp-048-a4-seed-extension-child-of-exp-a79175
interpretation:
  at: '2026-08-19T18:18:55+00:00'
  failure_mode: noise
  lesson: 's43''s z+3.23 was the seed lottery on top of a much weaker base effect
    — the preregistered 5-seed rule computed REFUTE (1/5 at z>=2, pooled +0.0079 vs
    the +0.008 bar). What the refutation does NOT kill: 10/10 cells (5 seeds x 2 stages)
    are POSITIVE, sign-test p~=0.03 — a consistent ~+0.006 trained-head state effect
    likely exists BELOW single-seed resolution. The lever ''PL60 analog chain solves
    state'' is dead at this corpus; the residual ''weak consistent effect, supply-limited''
    is the thing to test next — with a pooled/paired estimator across seeds or after
    the K-EmoPhone/StressID corpus wave, never with another identical arm. Phase-B
    state adoption is retained (harm check passed 3/5 < 4/5 bar; roughly neutral).
    The prepared C wave stays unlaunched — its premise was the confirmed lever.'
  transfers_to:
  - a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b
  - analog-state-supervision-a1-on-the-12-547-c5f5eb
key_numbers:
  a_stage_mean_over_null: 0.0074
  b_degrades_a_count_of_5: 3.0
  n_seeds: 5.0
  pooled_mean_over_null_b: 0.0079
  prereg_pool_bar: 0.008
  s42_b_z: 0.48
  s43_b_z: 3.23
  s44_b_z: 0.89
  s45_b_z: 1.19
  s46_b_z: 0.77
  seeds_positive_of_5: 5.0
  seeds_z_ge2_of_5: 1.0
  sign_test_p: 0.031
observed_effects:
- delta: -0.011
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_momentary_state_auc_over_null: false
provenance: {}
recipe_fingerprint:
  interventions: analog-state-supervision-ladder
verdict: refutes
---

## Summary
Tests hypothesis [[a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b]].

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before 0.0189 → after 0.0079  (Δ -0.011, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `noise`

s43's z+3.23 was the seed lottery on top of a much weaker base effect — the preregistered 5-seed rule computed REFUTE (1/5 at z>=2, pooled +0.0079 vs the +0.008 bar). What the refutation does NOT kill: 10/10 cells (5 seeds x 2 stages) are POSITIVE, sign-test p~=0.03 — a consistent ~+0.006 trained-head state effect likely exists BELOW single-seed resolution. The lever 'PL60 analog chain solves state' is dead at this corpus; the residual 'weak consistent effect, supply-limited' is the thing to test next — with a pooled/paired estimator across seeds or after the K-EmoPhone/StressID corpus wave, never with another identical arm. Phase-B state adoption is retained (harm check passed 3/5 < 4/5 bar; roughly neutral). The prepared C wave stays unlaunched — its premise was the confirmed lever.

Transfers to: [[a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b]], [[analog-state-supervision-a1-on-the-12-547-c5f5eb]]

## Key numbers
- `a_stage_mean_over_null`: 0.0074
- `b_degrades_a_count_of_5`: 3
- `n_seeds`: 5
- `pooled_mean_over_null_b`: 0.0079
- `prereg_pool_bar`: 0.008
- `s42_b_z`: 0.48
- `s43_b_z`: 3.23
- `s44_b_z`: 0.89
- `s45_b_z`: 1.19
- `s46_b_z`: 0.77
- `seeds_positive_of_5`: 5
- `seeds_z_ge2_of_5`: 1
- `sign_test_p`: 0.031

## Artifacts
- `logs/b_wave_state_eval.json`
- `logs/a4_eval.log`
- `logs/a4_campaign.log`
- `scratch/b_wave_eval.py`
- `scratch/run_a4_seeds.sh`
- `outputs/checkpoints/b_patch60_s42/phase_b`
- `outputs/checkpoints/b_patch60_s43/phase_b`
- `outputs/checkpoints/b_patch60_s44/phase_b`
- `outputs/checkpoints/b_patch60_s45/phase_b`
- `outputs/checkpoints/b_patch60_s46/phase_b`

## Do not retry
- Do NOT buy more PL60/ST30 analog A->B chains at 2000/1000 steps expecting a z>=2 trained-head state readout: 5 seeds measured {s42 +0.0028 z+0.48, s43 +0.0189 z+3.23, s44 +0.0061 z+0.89, s45 +0.0065 z+1.19, s46 +0.0051 z+0.77} - exactly 1/5 clears z>=2, pooled mean +0.0079 under the preregistered +0.008 bar. s43 was the seed lottery. The RESIDUAL that stays open: all 5 seeds are POSITIVE (sign-test p~=0.031 under a fair coin), i.e. a weak consistent ~+0.006 effect may exist BELOW single-seed resolution - the honest test for it is a pooled/paired estimator or a bigger corpus (K-EmoPhone/StressID), not another seed of the same arm.
- Do NOT launch the prepared C wave (c_patch60, scratch/run_c_wave.sh) on state grounds: its premise was a confirmed PL60 state lever and the lever is refuted at this n. The script is kept for when the corpus grows.

## Notes
VERDICT BY THE PREREGISTERED RULE: REFUTE (seed lottery). CONFIRM required >=3/5 positive (met: 5/5) AND >=2/5 z>=2 (FAILED: 1/5, s43 only) AND pooled mean >= +0.008 (FAILED: +0.0079). The REFUTE clause (<=1/5 at z>=2) binds. s43's z+3.23 was a lottery draw on top of a much weaker base effect.

SECONDARY (non-blocking, preregistered): B-harm check - B degrades the A-stage readout on 3/5 seeds (s42,s44,s45 down; s43,s46 up), below the 4/5 harm bar: Phase-B state adoption is NOT doing harm, it is roughly neutral at this scale. The Phase-B state supervision code stays (default-off, zero cost to existing profiles).

UNPREDICTED OBSERVATION, recorded at its correct strength: 5/5 seeds positive at BOTH stages (A-stage also 5/5: +0.004..+0.016). Sign-test p ~= 0.031. This is weak evidence of a real ~+0.006 effect below single-seed resolution - NOT claimable as a lever, but it means the right next instrument is a pooled/paired estimator across seeds (or simply more labelled corpus), not more identical arms. Consistent with the session's through-line: every state result today is supply-limited, not architecture-limited.

#state-supervision #wearables #control #epoch-resolution #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
