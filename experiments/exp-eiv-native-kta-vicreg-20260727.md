---
artifacts: []
closed: true
config_note: New immutable follow-up manifest SHA256 d2ff7c52c049f2336f223f53e03e1d180d4c8c5fe9b8f7df971afc9f54c44dda.
  Engineering smoke seed9201 excluded. Scientific pilot seed17, 500 steps, lambda
  {0.3,1,3}, bandwidth multiplier1. Raw tensors, corrected HR-zero mask, population
  normalization disabled. Confirmation only if strict committed go gates pass.
cost: null
crash_reason: ''
created_at: '2026-07-27T22:36:39+00:00'
diagnostics_measured:
- after: 4.909673654359584
  before: 2.659574260700471
  diagnostic_id: eiv_effective_rank
- after: 0.11970074812967581
  before: 0.007481296758104738
  diagnostic_id: eiv_duplicate_recall5
- after: 0.9446488568851492
  before: 0.9380921709796335
  diagnostic_id: eiv_heldout_hr_r2
- after: 0.006616
  before: 0.0565104227851777
  diagnostic_id: eiv_crossmodal_hsic
- after: 5.23046875
  before: 5.22265625
  diagnostic_id: eiv_peak_vram_gib
do_not_retry: []
external_run: outputs/experiments/native_rbf_kta_vicreg_20260727/runs
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5d19b4+manifest-d2ff7c52c049
hypothesis_id: hyp-eiv-native-kta-vicreg-20260727
id: exp-eiv-native-kta-vicreg-20260727
interpretation:
  at: '2026-08-18T21:08:45+00:00'
  failure_mode: null
  lesson: 'Applying duplicate-aware RBF kernel-target alignment + variance/covariance
    regularization DIRECTLY to native embeddings is the right lever on collapse but
    is under-powered at this setting: effective rank rose 2.6596 -> 4.9097 (delta
    +2.2501), which still failed BOTH the preregistered rank>=6.0 bar and the original
    unrelaxed rank>=5 gate, and cross-modal HSIC moved the WRONG way (0.05651 -> 0.006616)
    against a predicted rise. HR R2 held (0.9381 -> 0.9446, bar >=0.94) and Recall@5
    recovered 0.00748 -> 0.11970 (bar >=0.08723), so the collapse fix does not cost
    decodability or retrieval. NOTE THE MECHANICAL VERDICT IS MISLEADING: the record
    is filed ''supports'' on the direction-only rank delta, but the experiment''s
    own notes state no configuration passed all gates, no confirmation lock or 8-seed
    run was permitted, and the magnitude claim is NOT confirmed. Re-trying is worthwhile
    but only OUTSIDE the tested grid: lambda 1.0 and 3.0 both WORSENED rank and HR
    R2, so scaling the same penalty is already ruled out; vary bandwidth multiplier
    (fixed at 1) or change the penalty family instead.'
  transfers_to:
  - hyp-eiv-vicreg-20260727
  - hyp-eiv-soft-cca-20260727
  - hyp-eiv-multipositive-spectral-entropy-20260728
key_numbers: {}
observed_effects:
- delta: 2.250099393659
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: 0.112219451372
  diagnostic_id: eiv_duplicate_recall5
  direction: up
- delta: 0.006556685906
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
- delta: -0.049894422785
  diagnostic_id: eiv_crossmodal_hsic
  direction: down
- delta: 0.0078125
  diagnostic_id: eiv_peak_vram_gib
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_crossmodal_hsic: false
    eiv_duplicate_recall5: false
    eiv_effective_rank: true
    eiv_heldout_hr_r2: false
    eiv_peak_vram_gib: false
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-eiv-native-kta-vicreg-20260727]].
At commit `fix/soft-token-norm-scale@955c1b5d19b4+manifest-d2ff7c52c049`.
External run: outputs/experiments/native_rbf_kta_vicreg_20260727/runs

## Measurements
- [[eiv_effective_rank]]: before 2.659574260700471 → after 4.909673654359584  (Δ +2.2501, up ✓ predicted up)
- [[eiv_duplicate_recall5]]: before 0.007481296758104738 → after 0.11970074812967581  (Δ +0.112219, up ✗ predicted none)
- [[eiv_heldout_hr_r2]]: before 0.9380921709796335 → after 0.9446488568851492  (Δ +0.00655669, up ✗ predicted none)
- [[eiv_crossmodal_hsic]]: before 0.0565104227851777 → after 0.006616  (Δ -0.0498944, down ✗ predicted up)
- [[eiv_peak_vram_gib]]: before 5.22265625 → after 5.23046875  (Δ +0.0078125, up ✗ predicted none)

**Verdict:** supports

## Interpretation
Applying duplicate-aware RBF kernel-target alignment + variance/covariance regularization DIRECTLY to native embeddings is the right lever on collapse but is under-powered at this setting: effective rank rose 2.6596 -> 4.9097 (delta +2.2501), which still failed BOTH the preregistered rank>=6.0 bar and the original unrelaxed rank>=5 gate, and cross-modal HSIC moved the WRONG way (0.05651 -> 0.006616) against a predicted rise. HR R2 held (0.9381 -> 0.9446, bar >=0.94) and Recall@5 recovered 0.00748 -> 0.11970 (bar >=0.08723), so the collapse fix does not cost decodability or retrieval. NOTE THE MECHANICAL VERDICT IS MISLEADING: the record is filed 'supports' on the direction-only rank delta, but the experiment's own notes state no configuration passed all gates, no confirmation lock or 8-seed run was permitted, and the magnitude claim is NOT confirmed. Re-trying is worthwhile but only OUTSIDE the tested grid: lambda 1.0 and 3.0 both WORSENED rank and HR R2, so scaling the same penalty is already ruled out; vary bandwidth multiplier (fixed at 1) or change the penalty family instead.

Transfers to: [[hyp-eiv-vicreg-20260727]], [[hyp-eiv-soft-cca-20260727]], [[hyp-eiv-multipositive-spectral-entropy-20260728]]

## Notes
Closed at the preregistered seed17 pilot gate. Values use projected VICReg default as before and closest native follow-up lambda=0.3 as after. Direction-only primary effective-rank delta is positive, but the committed absolute rank>=6.0 prediction failed (observed 4.9097), the original rank>=5 gate also failed, and no configuration passed all R2/retrieval/alignment/geometry gates. Lambda 1 and 3 worsened rank and HR R2. Therefore no confirmation lock or 8-seed run was permitted; any computed 'supports' verdict must not be interpreted as confirmation of the magnitude claim.

#contrastive #hsic #cca #vicreg #wearable-ml #kernel-alignment #cross-modal #anti-collapse #contrastive-loss #subject-heldout #representation-learning #optihealth-eiv
