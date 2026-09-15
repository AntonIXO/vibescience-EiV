---
artifacts:
- logs/g5_recon.json
- logs/h_geometry.json
- logs/h_phase_a.log
- logs/chain4.log
- scratch/run_h.sh
closed: true
config_note: 'scratch/run_h.sh. Phase A only, identical PL60 geometry / roster / split=train
  / seed 42 to v41rebased, with exactly three keys changed and asserted: reconstruction_weight
  1.0, contrastive_weight 0.0, split train. The objective is AIM-style: a linear head
  on `fused` predicts the patch mean of the cells the dataset''s OWN artificial dropout
  removed (mask_ratio 0.15), supervised only where (m_union & m_inherited) is true
  -- inherited gaps are never a target, because asking the model to invent an unworn
  hour is not reconstruction. Scored by the unchanged g5 harness on the clean pool
  with the random twin. FIRST ATTEMPT CRASHED at step 0: the head emits one scalar
  per (channel, patch) and under encoder_format_version with_presence_v1 `fused` carries
  24 channels (12 values then 12 presence flags) against a 12-channel target -- "The
  size of tensor a (24) must match the size of tensor b (12)". The head now keeps
  the value half.'
cost:
  gpu_hours: 0.77
  wall_hours: 1.4
crash_reason: ''
created_at: '2026-09-03T00:08:15+00:00'
diagnostics_measured:
- after: 0.0674
  before: 0.0852
  diagnostic_id: eiv_state_auc_pool_ceiling
- after: 0.0158
  before: 0.034
  diagnostic_id: eiv_state_auc_trained_minus_random
- after: 7.0255
  before: 9.2609
  diagnostic_id: eiv_effective_rank
- after: 0.0674
  before: 0.0852
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do not propose an AIM/MAE ''sensor pretraining sprint'' at this corpus size on
  the strength of SensorFM or LSM-2. Measured here at 2e7 minutes: best encoder cell
  +0.0674 against a handcrafted bar of +0.1515, a gap of 0.114 = seven fold-seed sds,
  and BELOW its own random twin under single-span ridge. SensorFM''s 34/35 result
  is at 1e12 minutes with near-linear joint scaling; we are four to five orders of
  magnitude short and in the regime where its own smallest model shows dimensional
  collapse.'
- Do not read this arm's eff_rank 7.025 as an encoder-geometry regression. The geometry
  probe measures the PROJECTOR's soft tokens, and with contrastive_weight 0 the projector
  gets no gradient at all -- norm_std 0.0000, cos_p50 0.959, i.e. it is at initialisation
  by construction. Any future contrastive_weight 0 arm must report geometry with that
  caveat or not report it.
- Do not run g2 (the HR half of the bar) for this checkpoint without a reason g1 does
  not already supply.
external_run: logs/g5_recon.json
former_ids: []
gate_results:
- evidence: 'PREDICTED TO FAIL AND IT FAILED. Best reconstruction encoder cell +0.0674
    (enc_ms/gb, z +5.47) against a bar of raw_multiscale/rf +0.1515 + 0.03 = +0.1815.
    It is not close: the gap is 0.114, roughly seven times the 0.0155 fold-seed sd.
    Under single-span ridge the arm reads +0.0347 against its own twin''s +0.0533,
    i.e. BELOW the untrained architecture. Both estimator families, both spans, each
    against its own permutation null.'
  gate_id: g1
  passed: false
- evidence: 'NOT RUN, by the design''s own rule: the HR bar needs the t1b attention
    head retrained over this tower''s tokens (~30 GPU-min) and run_h.sh states that
    g1''s answer decides whether that is worth the card. g1 failed by 0.114, so it
    is not. Recorded as unmet rather than skipped -- the HR half of the SensorFM bar
    is UNTESTED for this objective, and a future session wanting it should repoint
    scratch/t1b_heads_v41_tower.py''s BUNDLE/CACHE at outputs/checkpoints/v41recon.'
  gate_id: g2
  passed: false
- evidence: 'One variable, asserted by the driver before launch: the injected config
    differs from configs/training.yaml on exactly {contrastive_weight, reconstruction_weight,
    split} for phase_a_12ch_v41pl60 and nothing else -- same PL60/ST30 geometry, same
    roster, same seed 42, same sensor_micro_batches 4, same mask_ratio 0.15 (the driver
    also asserts mask_ratio > 0, since the artificial dropout IS the supervision).
    Phase A completed rc=0 with zero tracebacks.'
  gate_id: g3
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale
id: exp-h-reconstruction-sensorfm-bar-20260903
interpretation:
  at: '2026-09-03T00:09:21+00:00'
  failure_mode: claim_false
  lesson: 'THE GATE SEMANTICS ARE INVERTED HERE AND THE MACHINE VERDICT SHOULD NOT
    BE READ LITERALLY. This hypothesis PREDICTED a null: "reconstruction pretraining
    will NOT clear the SensorFM bar". Its gates were written as "beats the bar --
    predicted to FAIL", so g1 failing is the hypothesis being CORRECT, and the engine''s
    `directional_only` / gates-failed output reads the opposite way. The substantive
    result: best reconstruction encoder cell +0.0674 against raw_multiscale/rf''s
    +0.1515, a gap of 0.114 = about seven fold-seed sds, with the arm BELOW its own
    random twin under single-span ridge (+0.0347 vs +0.0533). At 2e7 minutes, swapping
    Phase A''s caption objective for AIM-style masked reconstruction does not produce
    an encoder that beats 15 handcrafted features, and it adds LESS over the untrained
    architecture than the caption objective does (+0.0158 vs +0.0340). SensorFM''s
    34/35 result is at 1e12 minutes with near-linear joint data-x-capacity scaling;
    four to five orders of magnitude below it we are in the regime where its own smallest
    variant shows dimensional collapse, and that is what we measure. ACT ON IT: OVERVIEW.md
    Part X''s "sensor pretraining sprint" is closed for this corpus size on a preregistered
    null rather than on an opinion, and the remaining routes to a better encoder are
    more DATA or a richer supervisory signal from another modality (Track E) -- not
    another self-supervised objective on the same 14,041 windows. TWO METHOD NOTES.
    (1) An arm with contrastive_weight 0 leaves the PROJECTOR with no gradient, so
    its geometry probe (eff_rank 7.025, norm_std exactly 0.0, cos_p50 0.959) measures
    an untrained projector and not an encoder collapse -- report geometry with that
    caveat or not at all. (2) Writing gates in the "predicted to FAIL" direction is
    honest but fights the verdict engine; a future null-shaped hypothesis should phrase
    its blocking gate as the condition that must HOLD for the null (e.g. "best cell
    <= bar + 0.03") so that a passing gate and a supported hypothesis mean the same
    thing.'
  transfers_to:
  - e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
  - c3b-caption-label-clauses-are-what-put-state-in-the-encoder
key_numbers:
  bar_raw_multiscale_rf: 0.1515
  best_recon_encoder_cell: 0.0674
  eff_rank: 7.0255
  fold_seed_sd: 0.0155
  g1_threshold: 0.1815
  gap_to_bar: 0.1141
  infonce_eff_rank: 9.2609
  infonce_trained_minus_twin_multi_gb: 0.034
  projector_cos_p50: 0.9592
  projector_norm_std: 0.0
  recon_single_ridge: 0.0347
  recon_single_ridge_twin: 0.0533
  recon_trained_minus_twin_multi_gb: 0.0158
observed_effects:
- delta: -0.0178
  diagnostic_id: eiv_state_auc_pool_ceiling
  direction: down
- delta: -0.0182
  diagnostic_id: eiv_state_auc_trained_minus_random
  direction: down
- delta: -2.2354
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: -0.0178
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
parent_experiment_id: exp-c1-heldout-pool-ceiling-20260903
prediction_match:
  overall: true
  per_diagnostic:
    eiv_effective_rank: false
    eiv_state_auc_pool_ceiling: true
    eiv_state_auc_trained_minus_random: false
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale]].
Branches from [[exp-c1-heldout-pool-ceiling-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/g5_recon.json

## Measurements
- [[eiv_state_auc_pool_ceiling]]: before 0.0852 → after 0.0674  (Δ -0.0178, down ✗ predicted none)
- [[eiv_state_auc_trained_minus_random]]: before 0.034 → after 0.0158  (Δ -0.0182, down ✗ predicted up)
- [[eiv_effective_rank]]: before 9.2609 → after 7.0255  (Δ -2.2354, down ✗ predicted up)
- [[eiv_momentary_state_auc_over_null]]: before 0.0852 → after 0.0674  (Δ -0.0178, down)

## Gates
- `g1`: **FAIL** — PREDICTED TO FAIL AND IT FAILED. Best reconstruction encoder cell +0.0674 (enc_ms/gb, z +5.47) against a bar of raw_multiscale/rf +0.1515 + 0.03 = +0.1815. It is not close: the gap is 0.114, roughly seven times the 0.0155 fold-seed sd. Under single-span ridge the arm reads +0.0347 against its own twin's +0.0533, i.e. BELOW the untrained architecture. Both estimator families, both spans, each against its own permutation null.
- `g2`: **FAIL** — NOT RUN, by the design's own rule: the HR bar needs the t1b attention head retrained over this tower's tokens (~30 GPU-min) and run_h.sh states that g1's answer decides whether that is worth the card. g1 failed by 0.114, so it is not. Recorded as unmet rather than skipped -- the HR half of the SensorFM bar is UNTESTED for this objective, and a future session wanting it should repoint scratch/t1b_heads_v41_tower.py's BUNDLE/CACHE at outputs/checkpoints/v41recon.
- `g3`: PASS — One variable, asserted by the driver before launch: the injected config differs from configs/training.yaml on exactly {contrastive_weight, reconstruction_weight, split} for phase_a_12ch_v41pl60 and nothing else -- same PL60/ST30 geometry, same roster, same seed 42, same sensor_micro_batches 4, same mask_ratio 0.15 (the driver also asserts mask_ratio > 0, since the artificial dropout IS the supervision). Phase A completed rc=0 with zero tracebacks.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `claim_false`

THE GATE SEMANTICS ARE INVERTED HERE AND THE MACHINE VERDICT SHOULD NOT BE READ LITERALLY. This hypothesis PREDICTED a null: "reconstruction pretraining will NOT clear the SensorFM bar". Its gates were written as "beats the bar -- predicted to FAIL", so g1 failing is the hypothesis being CORRECT, and the engine's `directional_only` / gates-failed output reads the opposite way. The substantive result: best reconstruction encoder cell +0.0674 against raw_multiscale/rf's +0.1515, a gap of 0.114 = about seven fold-seed sds, with the arm BELOW its own random twin under single-span ridge (+0.0347 vs +0.0533). At 2e7 minutes, swapping Phase A's caption objective for AIM-style masked reconstruction does not produce an encoder that beats 15 handcrafted features, and it adds LESS over the untrained architecture than the caption objective does (+0.0158 vs +0.0340). SensorFM's 34/35 result is at 1e12 minutes with near-linear joint data-x-capacity scaling; four to five orders of magnitude below it we are in the regime where its own smallest variant shows dimensional collapse, and that is what we measure. ACT ON IT: OVERVIEW.md Part X's "sensor pretraining sprint" is closed for this corpus size on a preregistered null rather than on an opinion, and the remaining routes to a better encoder are more DATA or a richer supervisory signal from another modality (Track E) -- not another self-supervised objective on the same 14,041 windows. TWO METHOD NOTES. (1) An arm with contrastive_weight 0 leaves the PROJECTOR with no gradient, so its geometry probe (eff_rank 7.025, norm_std exactly 0.0, cos_p50 0.959) measures an untrained projector and not an encoder collapse -- report geometry with that caveat or not at all. (2) Writing gates in the "predicted to FAIL" direction is honest but fights the verdict engine; a future null-shaped hypothesis should phrase its blocking gate as the condition that must HOLD for the null (e.g. "best cell <= bar + 0.03") so that a passing gate and a supported hypothesis mean the same thing.

Transfers to: [[e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling]], [[c3b-caption-label-clauses-are-what-put-state-in-the-encoder]]

## Key numbers
- `bar_raw_multiscale_rf`: 0.1515
- `best_recon_encoder_cell`: 0.0674
- `eff_rank`: 7.0255
- `fold_seed_sd`: 0.0155
- `g1_threshold`: 0.1815
- `gap_to_bar`: 0.1141
- `infonce_eff_rank`: 9.2609
- `infonce_trained_minus_twin_multi_gb`: 0.034
- `projector_cos_p50`: 0.9592
- `projector_norm_std`: 0
- `recon_single_ridge`: 0.0347
- `recon_single_ridge_twin`: 0.0533
- `recon_trained_minus_twin_multi_gb`: 0.0158

## Artifacts
- `logs/g5_recon.json`
- `logs/h_geometry.json`
- `logs/h_phase_a.log`
- `logs/chain4.log`
- `scratch/run_h.sh`

## Do not retry
- Do not propose an AIM/MAE 'sensor pretraining sprint' at this corpus size on the strength of SensorFM or LSM-2. Measured here at 2e7 minutes: best encoder cell +0.0674 against a handcrafted bar of +0.1515, a gap of 0.114 = seven fold-seed sds, and BELOW its own random twin under single-span ridge. SensorFM's 34/35 result is at 1e12 minutes with near-linear joint scaling; we are four to five orders of magnitude short and in the regime where its own smallest model shows dimensional collapse.
- Do not read this arm's eff_rank 7.025 as an encoder-geometry regression. The geometry probe measures the PROJECTOR's soft tokens, and with contrastive_weight 0 the projector gets no gradient at all -- norm_std 0.0000, cos_p50 0.959, i.e. it is at initialisation by construction. Any future contrastive_weight 0 arm must report geometry with that caveat or not report it.
- Do not run g2 (the HR half of the bar) for this checkpoint without a reason g1 does not already supply.

## Notes
THE POWERED NULL LANDED WHERE IT WAS PREREGISTERED TO LAND. Replacing Phase A's InfoNCE-on-captions with AIM-style masked reconstruction, one variable, same PL60 geometry and roster and seed:

  best reconstruction encoder cell   +0.0674  (enc_ms/gb, z +5.47)
  the bar, raw_multiscale/rf         +0.1515  (15 handcrafted features, no training)
  g1 needed                          +0.1815
  gap                                 0.1141  = ~7 fold-seed sds

For comparison the InfoNCE arm reads +0.0852. So both Phase A objectives land at roughly half the handcrafted readout, and swapping one for the other moves the encoder by -0.0178 -- less than the difference between estimator families on the same features.

TRAINED MINUS ITS OWN TWIN IS THE SHARPER NUMBER: reconstruction +0.0158 against InfoNCE's +0.0340, and NEGATIVE under single-span ridge (+0.0347 vs a twin's +0.0533). Reconstruction adds less over the untrained architecture than the caption objective does, which is the opposite of what the "captions are the wrong objective" reading would predict.

WHAT THE GEOMETRY NUMBER IS AND IS NOT. eff_rank 7.025 vs 9.26 looks like a collapse and is an artefact of the arm's own design: the probe reads the PROJECTOR, and contrastive_weight 0 leaves the projector with no gradient (norm_std exactly 0.0, cos_p50 0.959 -- it is at init). The encoder+fusion stack g5 reads is unaffected. This is why run_h.sh printed geometry as "reported, NOT a veto".

CONSEQUENCE. OVERVIEW.md Part X's "sensor pretraining sprint" is closed for this corpus size, on a preregistered null rather than on an opinion. The remaining routes to a better encoder are more DATA (which we do not have) or a richer supervisory signal from another modality (Track E), not a different self-supervised objective on the same 14,041 windows.

#optihealth #jepa #control #readout #baseline #optihealth-eiv #representation-learning #matched-baseline #masking
