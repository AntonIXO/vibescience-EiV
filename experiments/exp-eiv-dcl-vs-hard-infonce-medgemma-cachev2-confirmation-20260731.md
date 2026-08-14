---
artifacts: []
closed: true
config_note: Corrected v3 direct confirmation on local RTX 5070. Required google/medgemma-1.5-4b-it;
  FrozenHash class/fallback removed and fallback requests fail. Padding-agnostic final-attended-token
  pooler. Cache v2 content-addressed. Train 4962 files SHA 147594f44538c5d907cf275f62324300da4f7ae1878ed9d154d0b6303c989708;
  validation 401 files SHA 2fdcc63138df7a84287e5729a1beac2b9a1dca3fb583ca566c487b07e60b73f6.
  Ten direct cache-vs-singleton audits min cosine 0.999848247 and cache-vs-mixed min
  0.999728560. Source implementation digest 8585f0ae7166d72307cbe70a4492048288d6c8e75093fda9e3db3d932fddda2e;
  protocol SHA 4410688a3e3b5bbc20f3fa663a5ece0e9421160141da789cfdc7634ac9727dd6; split
  SHA 43dcafef4a0a4a0e4cae3c6bca0b067599ae61b35d1ea1983e4a16ab4ac2f2f5; config SHA
  f0fd24b056e077670a266cb42e33e458d4bb3d7122f8cd068796013f9a776104. Effective batch92
  (45/12/35), 500 steps=46k samples, float32, hard one-positive labels, population
  stats disabled. Smoke excluded; exactly five paired seeds, sequential; test locked.
crash_reason: ''
created_at: '2026-07-31T08:23:27+00:00'
diagnostics_measured:
- after: 0.12119700748129673
  before: 0.11820448877805487
  diagnostic_id: eiv_duplicate_recall5
- after: 34.2
  before: 35.0
  diagnostic_id: eiv_crossmodal_median_rank
- after: 6.677569109773012
  before: 7.001256654720504
  diagnostic_id: eiv_effective_rank
- after: 0.19894814471300437
  before: 0.20790035241705002
  diagnostic_id: eiv_sensor_median_cosine
- after: 0.08658472865586973
  before: 0.08057253297618858
  diagnostic_id: eiv_crossmodal_alignment_gap
- after: 1.0832000000000002
  before: 1.0832000000000002
  diagnostic_id: eiv_false_negative_pairs_per_batch
- after: 233.5956873078
  before: 230.31084711160003
  diagnostic_id: eiv_training_runtime_sec
- after: 9.001953125
  before: 9.001953125
  diagnostic_id: eiv_peak_vram_gib
- after: 0.9651719152274388
  before: 0.9639167899189498
  diagnostic_id: eiv_heldout_hr_r2
- after: 0.9997285604476929
  before: 0.9997285604476929
  diagnostic_id: eiv_text_cache_batch_invariance_cosine
external_run: /root/optiHealth-EiV/outputs/experiments/infonce_vs_dcl_softtoken_20260731_v3
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5+frozen-8585f0ae7166d723
hypothesis_id: hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731
id: exp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-confirmation-20260731
observed_effects:
- delta: 0.002992518703
  diagnostic_id: eiv_duplicate_recall5
  direction: up
- delta: -0.8
  diagnostic_id: eiv_crossmodal_median_rank
  direction: down
- delta: -0.323687544947
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: -0.008952207704
  diagnostic_id: eiv_sensor_median_cosine
  direction: down
- delta: 0.00601219568
  diagnostic_id: eiv_crossmodal_alignment_gap
  direction: up
- delta: 0.0
  diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: none
- delta: 3.2848401962
  diagnostic_id: eiv_training_runtime_sec
  direction: up
- delta: 0.0
  diagnostic_id: eiv_peak_vram_gib
  direction: none
- delta: 0.001255125308
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
- delta: 0.0
  diagnostic_id: eiv_text_cache_batch_invariance_cosine
  direction: none
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_crossmodal_median_rank: true
    eiv_duplicate_recall5: true
    eiv_effective_rank: false
    eiv_false_negative_pairs_per_batch: true
    eiv_heldout_hr_r2: false
    eiv_peak_vram_gib: true
    eiv_sensor_median_cosine: true
    eiv_text_cache_batch_invariance_cosine: true
    eiv_training_runtime_sec: false
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731]].
At commit `fix/soft-token-norm-scale@955c1b5+frozen-8585f0ae7166d723`.
External run: /root/optiHealth-EiV/outputs/experiments/infonce_vs_dcl_softtoken_20260731_v3

## Measurements
- [[eiv_duplicate_recall5]]: before 0.11820448877805487 → after 0.12119700748129673  (Δ +0.00299252, up ✓ predicted up)
- [[eiv_crossmodal_median_rank]]: before 35.0 → after 34.2  (Δ -0.8, down ✓ predicted down)
- [[eiv_effective_rank]]: before 7.001256654720504 → after 6.677569109773012  (Δ -0.323688, down ✗ predicted up)
- [[eiv_sensor_median_cosine]]: before 0.20790035241705002 → after 0.19894814471300437  (Δ -0.00895221, down ✓ predicted down)
- [[eiv_crossmodal_alignment_gap]]: before 0.08057253297618858 → after 0.08658472865586973  (Δ +0.0060122, up)
- [[eiv_false_negative_pairs_per_batch]]: before 1.0832000000000002 → after 1.0832000000000002  (Δ +0, flat ✓ predicted none)
- [[eiv_training_runtime_sec]]: before 230.31084711160003 → after 233.5956873078  (Δ +3.28484, up ✗ predicted none)
- [[eiv_peak_vram_gib]]: before 9.001953125 → after 9.001953125  (Δ +0, flat ✓ predicted none)
- [[eiv_heldout_hr_r2]]: before 0.9639167899189498 → after 0.9651719152274388  (Δ +0.00125513, up ✗ predicted none)
- [[eiv_text_cache_batch_invariance_cosine]]: before 0.9997285604476929 → after 0.9997285604476929  (Δ +0, flat ✓ predicted none)

**Verdict:** supports

## Notes
Independent primary-agent audit authenticated all 10 reports, 21-artifact manifests/checkpoints per run, paired initialization/evaluation/caption/exposure invariants, fresh train/eval cache corpus hashes, and bit-for-bit aggregate recomputation. Frozen source digest remained 8585f0ae7166d723...; required google/medgemma-1.5-4b-it and fallback=false in every report; FrozenHash class/runtime branch absent. External turn interruption killed the first seed31 DCL attempt at step150 before evaluation; it is preserved verbatim under interrupted_runs and the exact seed was restarted once without outcome selection. Directionally DCL mean Recall@5 rose 0.11820449→0.12119701 (+0.00299252), median rank improved 35.0→34.2, alignment gap rose +0.006012, and median cosine decreased. However, preregistered superiority failed: Recall gain <+0.010, exact one-sided sign-flip p=0.34375, paired bootstrap 95% CI [-0.00847880,+0.01346633], and effective-rank delta -0.32369 below the -0.25 guard. Every DCL effective rank remained >=5, resource/integrity gates passed, peak reserved VRAM 9.00195 GiB, no OOM/nonfinite. Magnitude/confirmation gate failed despite any direction-only computed verdict. test_unlocked=false; locked test was not run. Conclusion: corrected genuine-MedGemma evidence does not establish DCL as better than hard InfoNCE; retain neither as a winner based on this comparison, while preserving weak directional retrieval evidence and the fail-loud cache/pooling fixes.

#contrastive #dcl #infonce #medgemma #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #cache-provenance #padding #text-encoder #contrastive-loss #subject-heldout #cache-provenance #optihealth-eiv #representation-learning

#contrastive #dcl #infonce #medgemma #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #cache-provenance #padding #text-encoder #contrastive-loss #subject-heldout #cache-provenance #optihealth-eiv #representation-learning
