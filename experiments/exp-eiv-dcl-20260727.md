---
artifacts: []
closed: true
config_note: Frozen manifest SHA256 4d905abd9f7e4a5d1b961383a5310df252296c0a34746bdd27eec1920c7b24f5.
  Pilot seed 17, 500 steps; full confirmation 8 paired seeds, 2000 steps. Raw tensors
  with experiment-only HR-zero mask correction; population normalization disabled.
cost: null
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
interpretation:
  at: '2026-08-18T21:08:44+00:00'
  failure_mode: claim_false
  lesson: 'Retire eiv_heldout_hr_r2 as a PRIMARY diagnostic for Phase-A objectives:
    the seed-matched UNTRAINED architecture already scores 0.9601, leaving under +0.04
    of headroom, so this hypothesis''s committed ''+0.20 R2 over untrained'' was unreachable
    by construction and the observed -0.00747 (0.9601 -> 0.9526) refutes the magnitude
    claim without saying anything about DCL. What the same seed-17 500-step pilot
    DOES show is that the DCL package is the strongest retrieval arm in the campaign:
    duplicate-aware Recall@5 rose 0.017456 -> 0.107232 (+0.0898, ~6.1x over untrained)
    and effective rank 2.6624 -> 4.2995 (+1.637), while cross-modal HSIC fell 0.05258
    -> 0.00657 and peak VRAM was 5.207 GiB. The run was closed at the pilot because
    rank 4.2995 missed the absolute rank>=5 go/no-go, so the 8-seed 2000-step confirmation
    was never permitted — DCL''s retrieval advantage is established only at 500 steps
    on one seed, and the open problem is anisotropy (rank<5), not decodability.'
  transfers_to:
  - hyp-eiv-dcl-hsic-20260727
  - hyp-eiv-vicreg-20260727
  - hyp-eiv-soft-cca-20260727
  - hyp-eiv-native-kta-vicreg-20260727
  - hyp-eiv-multipositive-dcl-20260728
  - hyp-eiv-multipositive-spectral-entropy-20260728
  - hyp-eiv-dcl-vs-hard-infonce-fp32-20260728
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
recipe_fingerprint: {}
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

## Interpretation
**Failure mode:** `claim_false`

Retire eiv_heldout_hr_r2 as a PRIMARY diagnostic for Phase-A objectives: the seed-matched UNTRAINED architecture already scores 0.9601, leaving under +0.04 of headroom, so this hypothesis's committed '+0.20 R2 over untrained' was unreachable by construction and the observed -0.00747 (0.9601 -> 0.9526) refutes the magnitude claim without saying anything about DCL. What the same seed-17 500-step pilot DOES show is that the DCL package is the strongest retrieval arm in the campaign: duplicate-aware Recall@5 rose 0.017456 -> 0.107232 (+0.0898, ~6.1x over untrained) and effective rank 2.6624 -> 4.2995 (+1.637), while cross-modal HSIC fell 0.05258 -> 0.00657 and peak VRAM was 5.207 GiB. The run was closed at the pilot because rank 4.2995 missed the absolute rank>=5 go/no-go, so the 8-seed 2000-step confirmation was never permitted — DCL's retrieval advantage is established only at 500 steps on one seed, and the open problem is anisotropy (rank<5), not decodability.

Transfers to: [[hyp-eiv-dcl-hsic-20260727]], [[hyp-eiv-vicreg-20260727]], [[hyp-eiv-soft-cca-20260727]], [[hyp-eiv-native-kta-vicreg-20260727]], [[hyp-eiv-multipositive-dcl-20260728]], [[hyp-eiv-multipositive-spectral-entropy-20260728]], [[hyp-eiv-dcl-vs-hard-infonce-fp32-20260728]]

## Notes
Closed on preregistered seed-17 500-step pilot because effective rank 4.2995 failed the rank>=5 go/no-go gate; no 8-seed confirmation was permitted. Primary R2 changed -0.00747, far below the committed +0.20. Retrieval improved materially but does not rescue the primary prediction.

#dcl #baseline #contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
