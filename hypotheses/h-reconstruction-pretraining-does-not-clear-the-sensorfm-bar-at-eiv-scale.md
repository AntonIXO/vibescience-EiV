---
ambition_class: mechanism
created_at: '2026-09-02T21:08:02+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: 'SensorFM bar on state: frozen enc_H probe (both families, own nulls,
    twin) beats raw_multiscale/rf +0.152 by >= 0.03 -- predicted to FAIL'
  diagnostic_id: eiv_state_auc_pool_ceiling
  id: g1
  mde: 0.03
- blocking: true
  description: 'SensorFM bar on HR: attention head over enc_H tokens <= 0.812 - paired
    band on the same 128 rows -- predicted to FAIL'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g2
  mde: 0.15
- blocking: true
  description: 'one variable: identical PL60 geometry, roster, split=train, seed 42;
    only the Phase A objective changes; text cache unused'
  diagnostic_id: eiv_effective_rank
  id: g3
  mde: null
id: h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale
interventions: []
override_reason: ''
paper_refs:
- sensorfm-2026-2605-22759
- lsm2-aim-incomplete-wearable-2506-05321
- jets-2025-jepa-irregular-wearables-openreview
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
predicted_effects:
- diagnostic_id: eiv_state_auc_pool_ceiling
  direction: none
  magnitude_note: encoder-only best cell <= +0.11 over null under both families; raw_multiscale/rf
    stays the ceiling at +0.152; union moves < +0.02.
- diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
  magnitude_note: reconstruction should beat its own random twin by more than InfoNCE's
    +0.034 (predicted +0.04 to +0.06) -- learning something is not the same as clearing
    the bar.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: reconstruction objectives typically raise eff_rank; predicted >=
    10 (v41 9.26). Advisory.
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
- masking
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- optihealth
- jepa
- control
- readout
- baseline
---

## Statement
TRACK H (proposed, NOT funded before A/C/E). Replacing Phase A's InfoNCE-on-HR-captions with an AIM/MAE-style masked-reconstruction pretraining of the SAME PL60 encoder on the SAME 14,041-window corpus (LSM-2's inherited-vs-artificial mask tokens; no text pairs) will NOT clear the SensorFM bar at EiV's data scale: a frozen-embedding linear/GBM probe will NOT beat the 15 handcrafted multi-scale features on momentary state (predicted enc_H best cell <= +0.11 over null vs raw_multiscale/rf +0.152) and will NOT beat the current encoder on HR (attention head hr_raw > 0.812 by more than the head's paired band). Committed as a POWERED NULL: SensorFM's 34/35 result is at 1e12 minutes and 1e8 parameters with near-linear scaling and dimensional collapse at the small end; EiV has ~2e7 minutes and an eff_rank of 9-10, which is SensorFM's collapsed regime. If the null holds, the 'sensor pretraining sprint' OVERVIEW.md Part X recommends is closed for this corpus size and the encoder's job is delegated to distillation (Track E) or to an external pretrained encoder. If it FAILS (probe beats raw features by the band on both families), reconstruction pretraining becomes the Phase A default and every downstream track re-bases on it.

## Rationale
SensorFM (arXiv:2605.22759) and LSM-2 (arXiv:2506.05321) show reconstruction pretraining wins against engineered features -- at scale. JETS shows JEPA works on 3M person-days. Nothing shows either winning at 1.4e4 windows. The vault already eliminated JEPA as a MOMENTARY-STATE fix (rule-out that-a-jepa-latent-prediction-pretraining-7061c5, exp-046) and left it open for caption-pair removal and robustness -- this hypothesis is framed on the SensorFM bar, not on state, to stay inside that rule-out. Pre-registering it as a null is what stops a future session from running it as a hopeful 'sprint' without a bar. Cost if run: ~2 GPU-h Phase A + CPU probes; g5 harness unchanged.

## Plan
Only after Tracks A, C-1 and E have been read. Code: phase_a_objective: reconstruction flag (default off) with LSM-2 mask-state embeddings (observed / inherited-missing / artificial-mask), decoder head over patches, SmoothL1 on observed cells only. Score with g5 (state) and t1b (HR head) unchanged. ~2 GPU-h.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_pool_ceiling]] (none).
Predicts [[eiv_state_auc_trained_minus_random]] (up).
Predicts [[eiv_effective_rank]] (up).
Gate `g1` (blocking): SensorFM bar on state: frozen enc_H probe (both families, own nulls, twin) beats raw_multiscale/rf +0.152 by >= 0.03 -- predicted to FAIL
Gate `g2` (blocking): SensorFM bar on HR: attention head over enc_H tokens <= 0.812 - paired band on the same 128 rows -- predicted to FAIL
Gate `g3` (blocking): one variable: identical PL60 geometry, roster, split=train, seed 42; only the Phase A objective changes; text cache unused
Cites [[sensorfm-2026-2605-22759]], [[lsm2-aim-incomplete-wearable-2506-05321]], [[jets-2025-jepa-irregular-wearables-openreview]], [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]].

#optihealth #jepa #control #readout #baseline #optihealth-eiv #representation-learning #matched-baseline #masking
