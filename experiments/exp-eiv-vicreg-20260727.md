---
artifacts: []
closed: true
config_note: Frozen manifest SHA256 4d905abd9f7e4a5d1b961383a5310df252296c0a34746bdd27eec1920c7b24f5.
  Validation-only pilot covariance weight {0.5,1,2}, seed 17, 500 steps; then immutable
  selected config and 8 paired 2000-step seeds.
crash_reason: ''
created_at: '2026-07-27T21:34:11+00:00'
diagnostics_measured:
- after: 0.9380921709796335
  before: 0.9526048159349805
  diagnostic_id: eiv_heldout_hr_r2
- after: 0.007481296758104738
  before: 0.10723192019950124
  diagnostic_id: eiv_duplicate_recall5
- after: 0.0565104227851777
  before: 0.00657176351615465
  diagnostic_id: eiv_crossmodal_hsic
- after: 2.659574260700471
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
hypothesis_id: hyp-eiv-vicreg-20260727
id: exp-eiv-vicreg-20260727
key_numbers: {}
observed_effects:
- delta: -0.014512644955
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
- delta: -0.099750623441
  diagnostic_id: eiv_duplicate_recall5
  direction: down
- delta: 0.049938659269
  diagnostic_id: eiv_crossmodal_hsic
  direction: up
- delta: -1.639938982637
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: 0.015625
  diagnostic_id: eiv_peak_vram_gib
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_duplicate_recall5: true
    eiv_effective_rank: false
    eiv_heldout_hr_r2: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-eiv-vicreg-20260727]].
At commit `fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e`.
External run: outputs/experiments/contrastive_losses_20260727/runs

## Measurements
- [[eiv_heldout_hr_r2]]: before 0.9526048159349805 → after 0.9380921709796335  (Δ -0.0145126, down ✗ predicted none)
- [[eiv_duplicate_recall5]]: before 0.10723192019950124 → after 0.007481296758104738  (Δ -0.0997506, down ✓ predicted down)
- [[eiv_crossmodal_hsic]]: before 0.00657176351615465 → after 0.0565104227851777  (Δ +0.0499387, up)
- [[eiv_effective_rank]]: before 4.2995132433370555 → after 2.659574260700471  (Δ -1.63994, down ✗ predicted up)
- [[eiv_peak_vram_gib]]: before 5.20703125 → after 5.22265625  (Δ +0.015625, up)

**Verdict:** refutes

## Notes
Closed after all three pilots failed collapse screening (effective rank 2.356-3.416; cosine 0.940-0.989). Logged after is literature default covariance weight 1.0. R2 delta -0.01451 is within the magnitude-note +/-0.02 null band, but rank and Recall@5 worsened as predicted for retrieval; no full confirmation was allowed.

#vicreg #control #collapse #contrastive #hsic #cca #wearable-ml #decorrelation #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
