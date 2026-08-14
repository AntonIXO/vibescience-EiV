---
artifacts: []
closed: true
config_note: 'Re-close of the archived 2026-07-31 v3 confirmation against structured
  gates. No new compute: measurements and gate outcomes are read verbatim from the
  frozen artifacts.'
crash_reason: ''
created_at: '2026-08-01T08:23:27+00:00'
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
gate_results:
- evidence: mean dRecall@5=+0.00299 < required +0.010
  gate_id: mean_recall_delta
  passed: false
- evidence: exact one-sided sign-flip p=0.34375 (>= 0.05)
  gate_id: exact_signflip_p
  passed: false
- evidence: paired bootstrap CI95=[-0.0084788029925187, 0.013466334164588534] crosses
    zero
  gate_id: bootstrap_ci95_lower
  passed: false
- evidence: mean d effective rank negative across seeds
  gate_id: mean_effective_rank_delta
  passed: false
- evidence: no seed regressed worse than +2
  gate_id: per_seed_median_rank_delta
  passed: true
- evidence: all DCL median cosine <= 0.90
  gate_id: dcl_cosine
  passed: true
- evidence: peak VRAM within the 11.25 GiB ceiling, no OOM
  gate_id: vram
  passed: true
- evidence: runtime within 1.10x of paired InfoNCE
  gate_id: runtime
  passed: true
- evidence: deployment gate reported True
  gate_id: deployment_gate_every_dcl_rank_at_least_5
  passed: true
git_ref: fix/soft-token-norm-scale@955c1b5+frozen-8585f0ae7166d723
hypothesis_id: hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-gated-20260801
id: exp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-confirmation-gated-20260801
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
parent_experiment_id: exp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-confirmation-20260731
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
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-gated-20260801]].
Branches from [[exp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-confirmation-20260731]].
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

## Gates
- `mean_recall_delta`: **FAIL** — mean dRecall@5=+0.00299 < required +0.010
- `exact_signflip_p`: **FAIL** — exact one-sided sign-flip p=0.34375 (>= 0.05)
- `bootstrap_ci95_lower`: **FAIL** — paired bootstrap CI95=[-0.0084788029925187, 0.013466334164588534] crosses zero
- `mean_effective_rank_delta`: **FAIL** — mean d effective rank negative across seeds
- `per_seed_median_rank_delta`: PASS — no seed regressed worse than +2
- `dcl_cosine`: PASS — all DCL median cosine <= 0.90
- `vram`: PASS — peak VRAM within the 11.25 GiB ceiling, no OOM
- `runtime`: PASS — runtime within 1.10x of paired InfoNCE
- `deployment_gate_every_dcl_rank_at_least_5`: PASS — deployment gate reported True

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
Re-close of the frozen v3 confirmation with the preregistered thresholds enforced. Direction on the primary diagnostic matched (+0.00299), but the superiority gates did not clear, so this is directional_only: it does NOT establish DCL as better than hard InfoNCE. test_unlocked remained False. No new compute was run; every number is read from the archived confirmation_summary.json.

#contrastive #dcl #infonce #medgemma #hsic #cca #vicreg #wearable-ml #denominator #padding #text-encoder #optihealth-eiv #matched-baseline #cache-provenance #contrastive-loss #subject-heldout #representation-learning
