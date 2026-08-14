---
artifacts: []
closed: true
config_note: Frozen manifest SHA256 4d905abd9f7e4a5d1b961383a5310df252296c0a34746bdd27eec1920c7b24f5.
  Pilot seed 17, 500 steps; full confirmation 8 paired seeds, 2000 steps. Raw tensors
  with experiment-only HR-zero mask correction; population normalization disabled.
crash_reason: ''
created_at: '2026-07-27T21:34:09+00:00'
diagnostics_measured:
- after: 0.9526048159349805
  before: 0.9600761136156915
  diagnostic_id: eiv_heldout_hr_r2
- after: 0.10723192019950124
  before: 0.017456359102244388
  diagnostic_id: eiv_duplicate_recall5
- after: 0.00657176351615465
  before: 0.05257623018765575
  diagnostic_id: eiv_crossmodal_hsic
- after: 4.2995132433370555
  before: 2.662434122846062
  diagnostic_id: eiv_effective_rank
- after: 5.20703125
  before: 0.0
  diagnostic_id: eiv_peak_vram_gib
do_not_retry: []
external_run: outputs/experiments/contrastive_losses_20260727/runs
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e
hypothesis_id: hyp-eiv-dcl-baseline-20260727
id: exp-eiv-dcl-20260727
key_numbers: {}
observed_effects:
- delta: -0.007471297681
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
- delta: 0.089775561097
  diagnostic_id: eiv_duplicate_recall5
  direction: up
- delta: -0.046004466672
  diagnostic_id: eiv_crossmodal_hsic
  direction: down
- delta: 1.637079120491
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: 5.20703125
  diagnostic_id: eiv_peak_vram_gib
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_duplicate_recall5: true
    eiv_effective_rank: true
    eiv_heldout_hr_r2: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-baseline-20260727]].
At commit `fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e`.
External run: outputs/experiments/contrastive_losses_20260727/runs

## Measurements
- [[eiv_heldout_hr_r2]]: before 0.9600761136156915 → after 0.9526048159349805  (Δ -0.0074713, down ✗ predicted up)
- [[eiv_duplicate_recall5]]: before 0.017456359102244388 → after 0.10723192019950124  (Δ +0.0897756, up ✓ predicted up)
- [[eiv_crossmodal_hsic]]: before 0.05257623018765575 → after 0.00657176351615465  (Δ -0.0460045, down)
- [[eiv_effective_rank]]: before 2.662434122846062 → after 4.2995132433370555  (Δ +1.63708, up ✓ predicted up)
- [[eiv_peak_vram_gib]]: before 0.0 → after 5.20703125  (Δ +5.20703, up)

**Verdict:** refutes

## Notes
Closed on preregistered seed-17 500-step pilot because effective rank 4.2995 failed the rank>=5 go/no-go gate; no 8-seed confirmation was permitted. Primary R2 changed -0.00747, far below the committed +0.20. Retrieval improved materially but does not rescue the primary prediction.

#dcl #baseline #contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
