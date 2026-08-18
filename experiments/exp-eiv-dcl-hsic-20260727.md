---
artifacts: []
closed: true
config_note: Frozen manifest SHA256 4d905abd9f7e4a5d1b961383a5310df252296c0a34746bdd27eec1920c7b24f5.
  Validation-only pilot grid weight {0.03,0.1,0.3} x RBF bandwidth multiplier {0.5,1,2},
  seed 17, 500 steps; then immutable selected config and 8 paired 2000-step seeds.
cost: null
crash_reason: ''
created_at: '2026-07-27T21:34:09+00:00'
diagnostics_measured:
- after: 0.963059850262493
  before: 0.9526048159349805
  diagnostic_id: eiv_heldout_hr_r2
- after: 0.11221945137157108
  before: 0.10723192019950124
  diagnostic_id: eiv_duplicate_recall5
- after: 0.005515145147853017
  before: 0.00657176351615465
  diagnostic_id: eiv_crossmodal_hsic
- after: 4.544117363736664
  before: 4.2995132433370555
  diagnostic_id: eiv_effective_rank
- after: 5.21484375
  before: 5.20703125
  diagnostic_id: eiv_peak_vram_gib
do_not_retry: []
external_run: outputs/experiments/contrastive_losses_20260727/runs
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e
hypothesis_id: hyp-eiv-dcl-hsic-20260727
id: exp-eiv-dcl-hsic-20260727
interpretation:
  at: '2026-08-18T21:08:44+00:00'
  failure_mode: null
  lesson: 'TREAT THIS ''supports'' AS DIRECTIONAL-ONLY — the campaign actually failed:
    all nine preregistered pilots (weight {0.03,0.1,0.3} x RBF bandwidth {0.5,1,2},
    seed 17, 500 steps) missed the absolute rank>=5 gate with ranks spanning only
    4.386-4.544, so no configuration was selected and no confirmation lock was created.
    The logged arm is the exploratory best-raw-R2 candidate w=0.1/bw=0.5, whose R2
    gain of +0.01046 (0.9526 -> 0.9631) missed the committed +0.02, and whose held-out
    cross-modal HSIC moved the WRONG WAY (0.00657 -> 0.00552) — i.e. the proposed
    nonlinear-dependence mechanism was not even engaged, so any R2 movement here is
    unexplained by HSIC. On the objective bake-off ordering for the primary retrieval
    diagnostic, DCL+HSIC is nominally the best arm (eiv_duplicate_recall5 0.112219
    vs DCL 0.107232) but the gap is only +0.00499 on a single seed — far too small
    to trust as a ranking, and bought for +0.245 effective rank that still leaves
    the collapse gate unmet. Do not spend confirmation GPU on HSIC weight/bandwidth
    search; the binding constraint is rank<5, not the dependence term.'
  transfers_to:
  - hyp-eiv-dcl-baseline-20260727
  - hyp-eiv-native-kta-vicreg-20260727
  - hyp-eiv-vicreg-20260727
  - hyp-eiv-soft-cca-20260727
  - hyp-eiv-multipositive-spectral-entropy-20260728
key_numbers: {}
observed_effects:
- delta: 0.010455034328
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
- delta: 0.004987531172
  diagnostic_id: eiv_duplicate_recall5
  direction: up
- delta: -0.001056618368
  diagnostic_id: eiv_crossmodal_hsic
  direction: down
- delta: 0.2446041204
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: 0.0078125
  diagnostic_id: eiv_peak_vram_gib
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_crossmodal_hsic: false
    eiv_duplicate_recall5: false
    eiv_heldout_hr_r2: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-hsic-20260727]].
At commit `fix/soft-token-norm-scale@955c1b5d19b4+manifest-4d905abd9f7e`.
External run: outputs/experiments/contrastive_losses_20260727/runs

## Measurements
- [[eiv_heldout_hr_r2]]: before 0.9526048159349805 → after 0.963059850262493  (Δ +0.010455, up ✓ predicted up)
- [[eiv_duplicate_recall5]]: before 0.10723192019950124 → after 0.11221945137157108  (Δ +0.00498753, up ✗ predicted none)
- [[eiv_crossmodal_hsic]]: before 0.00657176351615465 → after 0.005515145147853017  (Δ -0.00105662, down ✗ predicted up)
- [[eiv_effective_rank]]: before 4.2995132433370555 → after 4.544117363736664  (Δ +0.244604, up)
- [[eiv_peak_vram_gib]]: before 5.20703125 → after 5.21484375  (Δ +0.0078125, up)

**Verdict:** supports

## Interpretation
TREAT THIS 'supports' AS DIRECTIONAL-ONLY — the campaign actually failed: all nine preregistered pilots (weight {0.03,0.1,0.3} x RBF bandwidth {0.5,1,2}, seed 17, 500 steps) missed the absolute rank>=5 gate with ranks spanning only 4.386-4.544, so no configuration was selected and no confirmation lock was created. The logged arm is the exploratory best-raw-R2 candidate w=0.1/bw=0.5, whose R2 gain of +0.01046 (0.9526 -> 0.9631) missed the committed +0.02, and whose held-out cross-modal HSIC moved the WRONG WAY (0.00657 -> 0.00552) — i.e. the proposed nonlinear-dependence mechanism was not even engaged, so any R2 movement here is unexplained by HSIC. On the objective bake-off ordering for the primary retrieval diagnostic, DCL+HSIC is nominally the best arm (eiv_duplicate_recall5 0.112219 vs DCL 0.107232) but the gap is only +0.00499 on a single seed — far too small to trust as a ranking, and bought for +0.245 effective rank that still leaves the collapse gate unmet. Do not spend confirmation GPU on HSIC weight/bandwidth search; the binding constraint is rank<5, not the dependence term.

Transfers to: [[hyp-eiv-dcl-baseline-20260727]], [[hyp-eiv-native-kta-vicreg-20260727]], [[hyp-eiv-vicreg-20260727]], [[hyp-eiv-soft-cca-20260727]], [[hyp-eiv-multipositive-spectral-entropy-20260728]]

## Notes
Closed after all nine preregistered pilots failed rank>=5 (range 4.386-4.544), so no configuration was selected and no confirmation lock was created. Logged after values are the exploratory best raw-R2 candidate w=0.1,bw=0.5; its +0.01046 R2 missed the committed +0.02, HSIC moved down, and rank remained below gate.

#hsic #kernel #dcl #contrastive #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
