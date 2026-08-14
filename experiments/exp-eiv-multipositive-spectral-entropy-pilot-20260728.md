---
artifacts: []
closed: true
config_note: 'Frozen validation-only RTX 5070 pilot. Comparator: exact-caption multi-positive
  DCL, weight 0. Candidates: same objective plus native sensor-only batch-Gram spectral-entropy
  penalty weights 0.01, 0.03, 0.10. Pilot seed 17, exactly 500 steps, all four configs
  run sequentially before aggregation. Primary gate: effective-rank delta >= +0.25
  and absolute treatment rank >= 5.0. Retention gates: Recall@5 delta >= -0.005, median-rank
  delta <= +2, median cosine <= 0.90. Mechanism/resource gates: finite and higher
  final normalized spectral entropy, zero caption-equivalent false negatives, no OOM/nonfinite,
  peak reserved VRAM <11.25 GiB, treatment runtime <=110% comparator. Select highest
  Recall@5 among eligible; within 0.002 choose lower weight. Confirmation seeds 31/37/41/43/47
  only if pilot earns immutable lock. Locked test remains untouched unless confirmation
  passes. population_stats_mode=disabled. Protocol SHA256 9884ac3df783c5c9bc192ae9c72877e4af317a3370bda7d69ac4c2bb24e481a1.'
crash_reason: ''
created_at: '2026-07-28T09:36:08+00:00'
diagnostics_measured:
- after: 4.517719665813096
  before: 4.380875185347812
  diagnostic_id: eiv_effective_rank
- after: 0.3860361874103546
  before: 0.3687906861305237
  diagnostic_id: eiv_sensor_spectral_entropy
- after: 0.10723192019950124
  before: 0.10723192019950124
  diagnostic_id: eiv_duplicate_recall5
- after: 39.0
  before: 38.0
  diagnostic_id: eiv_crossmodal_median_rank
- after: 0.32908814220649996
  before: 0.13460704168386672
  diagnostic_id: eiv_sensor_median_cosine
- after: 0.08283564393657908
  before: 0.090314803175803
  diagnostic_id: eiv_crossmodal_alignment_gap
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_false_negative_pairs_per_batch
- after: 0.9254086572386528
  before: 0.952753995821638
  diagnostic_id: eiv_heldout_hr_r2
- after: 5.2109375
  before: 5.2109375
  diagnostic_id: eiv_peak_vram_gib
- after: 114.24245741600316
  before: 114.35292421000486
  diagnostic_id: eiv_training_runtime_sec
external_run: file:///root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_spectral_20260728
gate_results: []
git_ref: working-tree@multi-positive-spectral-manifest-9884ac3d
hypothesis_id: hyp-eiv-multipositive-spectral-entropy-20260728
id: exp-eiv-multipositive-spectral-entropy-pilot-20260728
observed_effects:
- delta: 0.136844480465
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: 0.01724550128
  diagnostic_id: eiv_sensor_spectral_entropy
  direction: up
- delta: 0.0
  diagnostic_id: eiv_duplicate_recall5
  direction: none
- delta: 1.0
  diagnostic_id: eiv_crossmodal_median_rank
  direction: up
- delta: 0.194481100523
  diagnostic_id: eiv_sensor_median_cosine
  direction: up
- delta: -0.007479159239
  diagnostic_id: eiv_crossmodal_alignment_gap
  direction: down
- delta: 0.0
  diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: none
- delta: -0.027345338583
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
- delta: 0.0
  diagnostic_id: eiv_peak_vram_gib
  direction: none
- delta: -0.110466794002
  diagnostic_id: eiv_training_runtime_sec
  direction: down
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_effective_rank: true
    eiv_sensor_spectral_entropy: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-eiv-multipositive-spectral-entropy-20260728]].
At commit `working-tree@multi-positive-spectral-manifest-9884ac3d`.
External run: file:///root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_spectral_20260728

## Measurements
- [[eiv_effective_rank]]: before 4.380875185347812 → after 4.517719665813096  (Δ +0.136844, up ✓ predicted up)
- [[eiv_sensor_spectral_entropy]]: before 0.3687906861305237 → after 0.3860361874103546  (Δ +0.0172455, up ✓ predicted up)
- [[eiv_duplicate_recall5]]: before 0.10723192019950124 → after 0.10723192019950124  (Δ +0, flat)
- [[eiv_crossmodal_median_rank]]: before 38.0 → after 39.0  (Δ +1, up)
- [[eiv_sensor_median_cosine]]: before 0.13460704168386672 → after 0.32908814220649996  (Δ +0.194481, up)
- [[eiv_crossmodal_alignment_gap]]: before 0.090314803175803 → after 0.08283564393657908  (Δ -0.00747916, down)
- [[eiv_false_negative_pairs_per_batch]]: before 0.0 → after 0.0  (Δ +0, flat)
- [[eiv_heldout_hr_r2]]: before 0.952753995821638 → after 0.9254086572386528  (Δ -0.0273453, down)
- [[eiv_peak_vram_gib]]: before 5.2109375 → after 5.2109375  (Δ +0, flat)
- [[eiv_training_runtime_sec]]: before 114.35292421000486 → after 114.24245741600316  (Δ -0.110467, down)

**Verdict:** supports

## Notes
Direction-only Vibescience observation uses the preregistered grid's maximum primary-response arm, weight 0.10, versus the paired weight-zero multi-positive DCL comparator. This is not a selected/eligible winner: the frozen campaign aggregate returned gate_passed=false and selected=null. Primary effective rank rose only 4.380875->4.517720 (delta +0.136844), below required +0.25 and below absolute 5.0. Weights 0.01 and 0.03 reduced rank by -0.072124 and -0.129266. All candidates therefore failed both mandatory rank gates; no confirmation_lock or test_unlock was created and confirmation/test were not run. Mechanism prediction did move directionally for weight 0.10: final batch normalized spectral entropy 0.368791->0.386036, but this did not transfer at required magnitude to held-out native effective rank. Weight 0.10 retained Recall@5 exactly (0.107232) but worsened median rank 38->39, median cosine 0.134607->0.329088, alignment gap 0.090315->0.082836, and secondary HR R2 0.952754->0.925409. Weight 0.03 had best retrieval, Recall@5 0.117207 (+0.009975), but rank fell to 4.251609. All arms used zero caption-equivalent false negatives while auditing 584 extra-positive directed pairs across 225/500 batches, had no OOM/nonfinite, peak 5.210938 GiB, and treatment runtime ratios <=1.0142. Scientific artifacts and 104 enumerated hashes verified. Pilot summary SHA256 72e26ca967e892fb01b58633c32f21409f2e1b2cfd7ec0ea4c46e31d3a601a67; protocol SHA256 9884ac3df783c5c9bc192ae9c72877e4af317a3370bda7d69ac4c2bb24e481a1. population_stats_mode remained disabled. Interpret any tool 'supports' verdict strictly as directional prediction match; the preregistered magnitude/eligibility result is a failed pilot.

#multi-positive #dcl #spectral-entropy #effective-rank #batch-gram #contrastive #sensor-text #false-negatives #cross-modal #anti-collapse #representation-collapse #anisotropy #false-negatives #optihealth-eiv #subject-heldout

#multi-positive #dcl #spectral-entropy #effective-rank #batch-gram #contrastive #sensor-text #false-negatives #cross-modal #anti-collapse #representation-collapse #anisotropy #false-negatives #optihealth-eiv #subject-heldout
