---
artifacts: []
closed: true
config_note: Frozen manifest SHA256 4d905abd9f7e4a5d1b961383a5310df252296c0a34746bdd27eec1920c7b24f5.
  Validation-only pilot decorrelation weight {0.1,0.5,1}, seed 17, 500 steps; then
  immutable selected config and 8 paired 2000-step seeds.
cost: null
crash_reason: ''
created_at: '2026-07-27T21:34:10+00:00'
diagnostics_measured:
- after: 0.9629765654784871
  before: 0.9526048159349805
  diagnostic_id: eiv_heldout_hr_r2
- after: 0.012468827930174564
  before: 0.10723192019950124
  diagnostic_id: eiv_duplicate_recall5
- after: 0.05692527987537055
  before: 0.00657176351615465
  diagnostic_id: eiv_crossmodal_hsic
- after: 2.248700040871224
  before: 4.2995132433370555
  diagnostic_id: eiv_effective_rank
- after: 5.22265625
  before: 5.20703125
  diagnostic_id: eiv_peak_vram_gib
do_not_retry: []
external_run: outputs/experiments/contrastive_losses_20260727/runs
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e
hypothesis_id: hyp-eiv-soft-cca-20260727
id: exp-eiv-soft-cca-20260727
interpretation:
  at: '2026-08-18T21:08:45+00:00'
  failure_mode: null
  lesson: 'Soft CCA''s EMA-covariance decorrelation on a 128D loss head does not buy
    held-out-subject HR decodability and actively destroys the native representation:
    R2 rose only +0.0104 (0.95260 -> 0.96298) against a preregistered >=+0.02 bar,
    while Recall@5 COLLAPSED 0.10723 -> 0.01247 (-0.0948, i.e. ~4.7x worse than the
    -0.02 tolerance) and effective rank fell 4.2995 -> 2.2487. All three decorrelation
    weights {0.1, 0.5, 1.0} failed both anti-collapse gates (effective rank 2.249-2.437,
    median cosine 0.99966-0.99994) — near-total directional collapse at every setting.
    THE MECHANICAL ''supports'' VERDICT IS AN ARTIFACT of the single directional R2
    prediction; the headline result is a failed magnitude bar plus a collapsed representation.
    Do not re-run this weight grid; a loss-head objective that leaves the evaluated
    native embeddings unconstrained is the diagnosed bypass.'
  transfers_to:
  - hyp-eiv-vicreg-20260727
  - hyp-eiv-native-kta-vicreg-20260727
  - hyp-eiv-multipositive-spectral-entropy-20260728
key_numbers: {}
observed_effects:
- delta: 0.010371749544
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
- delta: -0.094763092269
  diagnostic_id: eiv_duplicate_recall5
  direction: down
- delta: 0.050353516359
  diagnostic_id: eiv_crossmodal_hsic
  direction: up
- delta: -2.050813202466
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: 0.015625
  diagnostic_id: eiv_peak_vram_gib
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_duplicate_recall5: false
    eiv_effective_rank: false
    eiv_heldout_hr_r2: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-eiv-soft-cca-20260727]].
At commit `fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e`.
External run: outputs/experiments/contrastive_losses_20260727/runs

## Measurements
- [[eiv_heldout_hr_r2]]: before 0.9526048159349805 → after 0.9629765654784871  (Δ +0.0103717, up ✓ predicted up)
- [[eiv_duplicate_recall5]]: before 0.10723192019950124 → after 0.012468827930174564  (Δ -0.0947631, down ✗ predicted none)
- [[eiv_crossmodal_hsic]]: before 0.00657176351615465 → after 0.05692527987537055  (Δ +0.0503535, up)
- [[eiv_effective_rank]]: before 4.2995132433370555 → after 2.248700040871224  (Δ -2.05081, down ✗ predicted up)
- [[eiv_peak_vram_gib]]: before 5.20703125 → after 5.22265625  (Δ +0.015625, up)

**Verdict:** supports

## Interpretation
Soft CCA's EMA-covariance decorrelation on a 128D loss head does not buy held-out-subject HR decodability and actively destroys the native representation: R2 rose only +0.0104 (0.95260 -> 0.96298) against a preregistered >=+0.02 bar, while Recall@5 COLLAPSED 0.10723 -> 0.01247 (-0.0948, i.e. ~4.7x worse than the -0.02 tolerance) and effective rank fell 4.2995 -> 2.2487. All three decorrelation weights {0.1, 0.5, 1.0} failed both anti-collapse gates (effective rank 2.249-2.437, median cosine 0.99966-0.99994) — near-total directional collapse at every setting. THE MECHANICAL 'supports' VERDICT IS AN ARTIFACT of the single directional R2 prediction; the headline result is a failed magnitude bar plus a collapsed representation. Do not re-run this weight grid; a loss-head objective that leaves the evaluated native embeddings unconstrained is the diagnosed bypass.

Transfers to: [[hyp-eiv-vicreg-20260727]], [[hyp-eiv-native-kta-vicreg-20260727]], [[hyp-eiv-multipositive-spectral-entropy-20260728]]

## Notes
Closed after all three pilots failed both anti-collapse gates (effective rank 2.249-2.437; cosine 0.99966-0.99994). Logged after is literature-default/best-R2 weight 0.5. The +0.01037 R2 missed +0.02 while Recall@5 collapsed from 0.1072 to 0.0125 and rank fell.

#soft-cca #decorrelation #multiview #contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
