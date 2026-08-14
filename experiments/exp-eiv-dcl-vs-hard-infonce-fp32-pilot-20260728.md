---
artifacts: []
closed: true
config_note: 'Frozen matched pilot on local RTX 5070. Historical Phase-A actual execution:
  float32, effective stratified batch 102 (50 LifeSnaps/12 PMData/40 wildcard), 500
  optimizer steps/51,000 samples per arm, seeds 17/23/29, hard labels in both arms,
  main+aux objective differ only through decoupled_loss. Raw tensor; population_stats_mode=disabled;
  corrected HR-zero mask. Subject-disjoint train/val/test split; test locked. Manifest
  implementation digest dc45212c44f09693b9d03b1c838c4ceb4cd33409ad0588f28f8c06ac6f8673d7;
  protocol SHA 93fa06a385bf9bf6b2f2381b941cd44c04f27baf00cb4f8405a277c60e29bcd5; split
  SHA 43dcafef4a0a4a0e4cae3c6bca0b067599ae61b35d1ea1983e4a16ab4ac2f2f5. Smoke seed17/2
  steps is structurally excluded. Pilot go/no-go and conditional confirmation gates
  are encoded in protocol.json. Scope is 500-step pilot, not legacy 2,000-step convergence.'
crash_reason: ''
created_at: '2026-07-28T11:10:58+00:00'
diagnostics_measured:
- after: 10.314453125
  before: 10.314453125
  diagnostic_id: eiv_peak_vram_gib
external_run: /root/optiHealth-EiV/outputs/experiments/infonce_vs_dcl_softtoken_20260728
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5+frozen-dc45212c44f09693
hypothesis_id: hyp-eiv-dcl-vs-hard-infonce-fp32-20260728
id: exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728
observed_effects:
- delta: 0.0
  diagnostic_id: eiv_peak_vram_gib
  direction: none
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_crossmodal_median_rank: false
    eiv_duplicate_recall5: false
    eiv_effective_rank: false
    eiv_false_negative_pairs_per_batch: false
    eiv_heldout_hr_r2: false
    eiv_peak_vram_gib: true
    eiv_sensor_median_cosine: false
    eiv_training_runtime_sec: false
provenance: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-vs-hard-infonce-fp32-20260728]].
At commit `fix/soft-token-norm-scale@955c1b5+frozen-dc45212c44f09693`.
External run: /root/optiHealth-EiV/outputs/experiments/infonce_vs_dcl_softtoken_20260728

## Measurements
- [[eiv_peak_vram_gib]]: before 10.314453125 → after 10.314453125  (Δ +0, flat ✓ predicted none)

**Verdict:** inconclusive

## Notes
Engineering smoke failed symmetrically before initialization or step 1: both hard InfoNCE and DCL tried to precompute 4,962 missing corrected-mask caption embeddings by moving fp32 MedGemma to the 11.51 GiB RTX 5070, then OOMed at 10.314 GiB reserved. No primary retrieval or training diagnostic exists, so this is an implementation/resource blockage, not evidence for or against DCL. Exact compatible cached embeddings already exist under other cache fingerprints; next experiment must use a new campaign root and authenticate/materialize the exact text-cache corpus before running. No retry, output deletion, pilot, confirmation, or test occurred.

#contrastive #dcl #infonce #cross-modal #float32 #rtx-5070 #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #optihealth-eiv #matched-baseline #historical-pipeline #contrastive-loss #subject-heldout #representation-learning

#contrastive #dcl #infonce #cross-modal #float32 #rtx-5070 #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #optihealth-eiv #matched-baseline #historical-pipeline #contrastive-loss #subject-heldout #representation-learning
