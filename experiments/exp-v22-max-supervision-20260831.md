---
artifacts:
- outputs/checkpoints/v22max/phase_a
- logs/v22_driver.log
- logs/v22_phase_a.log
- logs/v22_geometry.json
- scratch/run_v22_max.sh
closed: true
config_note: 'v22max: phase_a_12ch_v20strat + supervision block {label_head_weights
  {diary:0.3, context:0.3}, affect_weight 0.3, mask_unlabelled_state true} on the
  fixed [14041] bundle. Phase A RC=0, 1240.65 s, strat active (92 anchors/batch),
  label heads live (diary 5ch x 3332 cells, context 2ch x ~8890, affect on). Geometry
  gate auto-fired: eff_rank 5.94 < 7.0 -> runner aborted before B/C exactly as designed
  (exit 42). Zero wasted B/C spend.'
cost:
  gpu_hours: 0.35
  wall_hours: 0.37
crash_reason: ''
created_at: '2026-08-31T00:03:09+00:00'
diagnostics_measured:
- after: 5.9358
  before: 7.4385
  diagnostic_id: eiv_effective_rank
- after: 0.1839
  before: 0.1478
  diagnostic_id: eiv_sensor_median_cosine
do_not_retry:
- 'Do NOT add the full supervision block {diary+context+affect+statemask} to Phase
  A in one shot: it costs -1.50 eff_rank (7.44 -> 5.94) on the identical strat-fixed
  profile and re-enters collapse territory. Bisect per-head with the geometry gate
  (~25 min per arm) before any chain.'
- 'Do NOT skip the between-stage geometry gate on supervised-A chains: it converted
  a 2-hour failed chain into a 21-minute verdict here.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL, and the gate did its job: eff_rank 5.94 < 7.0 (v20strat identical-but-unsupervised:
    7.44). The supervision block cost -1.50 rank -- the added heads pull the cloud
    back toward collapse territory. Runner aborted before B/C (exit 42), so the verdict
    cost 21 minutes of A instead of 2 hours of chain.'
  gate_id: g0-supervision-keeps-geometry
  passed: false
- evidence: Not reached -- chain aborted at g0 by design.
  gate_id: g1-corr-healthy
  passed: false
- evidence: Not reached -- chain aborted at g0 by design.
  gate_id: g2-beats-v10-baseline-median
  passed: false
- evidence: Not reached.
  gate_id: g3-dense-advisory
  passed: false
git_ref: ''
hypothesis_id: maximal-label-utilisation-on-the-repaired-9c2265
id: exp-v22-max-supervision-20260831
interpretation:
  at: '2026-08-31T00:03:44+00:00'
  failure_mode: recipe_conditional
  lesson: 'The compound supervision block {diary 0.3 + context 0.3 + affect 0.3 +
    statemask} costs -1.50 eff_rank on the strat-fixed Phase A (7.44 -> 5.94) and
    the geometry gate correctly refused to fund B/C -- a 21-minute verdict instead
    of a 2-hour failed chain. Label utilisation and geometry health are IN TENSION
    at these weights: the mechanism matches Jing et al.''s aux-dominated-variance
    route to dimensional collapse. Next moves, in order of cost: (1) per-head bisection
    (three geometry-gated A arms, 25 min each) to find which head(s) carry the rank
    cost; (2) if all cost rank, integrate labels as DETACHED probes (stop-gradient
    into the heads) so supervision reads the representation without shaping it; (3)
    or halve the weights. The v23 result stands independent: the current best full
    recipe is A(strat, no aux heads) -> C.'
  transfers_to:
  - maximal-label-utilisation-on-the-repaired-9c2265
  - the-phase-a-rank-collapse-is-caused-by-the-d5e8e6
key_numbers:
  cos_p50_v22: 0.1839
  cos_p95_v22: 0.7129
  eff_rank_v20_control: 7.4385
  eff_rank_v22: 5.9358
  rank_cost_of_supervision: -1.5
observed_effects:
- delta: -1.5027
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: 0.0361
  diagnostic_id: eiv_sensor_median_cosine
  direction: up
parent_experiment_id: exp-v20-stratified-batches-20260830
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[maximal-label-utilisation-on-the-repaired-9c2265]].
Branches from [[exp-v20-stratified-batches-20260830]].

## Measurements
- [[eiv_effective_rank]]: before 7.4385 → after 5.9358  (Δ -1.5027, down)
- [[eiv_sensor_median_cosine]]: before 0.1478 → after 0.1839  (Δ +0.0361, up)

## Gates
- `g0-supervision-keeps-geometry`: **FAIL** — FAIL, and the gate did its job: eff_rank 5.94 < 7.0 (v20strat identical-but-unsupervised: 7.44). The supervision block cost -1.50 rank -- the added heads pull the cloud back toward collapse territory. Runner aborted before B/C (exit 42), so the verdict cost 21 minutes of A instead of 2 hours of chain.
- `g1-corr-healthy`: **FAIL** — Not reached -- chain aborted at g0 by design.
- `g2-beats-v10-baseline-median`: **FAIL** — Not reached -- chain aborted at g0 by design.
- `g3-dense-advisory`: **FAIL** — Not reached.

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `recipe_conditional`

The compound supervision block {diary 0.3 + context 0.3 + affect 0.3 + statemask} costs -1.50 eff_rank on the strat-fixed Phase A (7.44 -> 5.94) and the geometry gate correctly refused to fund B/C -- a 21-minute verdict instead of a 2-hour failed chain. Label utilisation and geometry health are IN TENSION at these weights: the mechanism matches Jing et al.'s aux-dominated-variance route to dimensional collapse. Next moves, in order of cost: (1) per-head bisection (three geometry-gated A arms, 25 min each) to find which head(s) carry the rank cost; (2) if all cost rank, integrate labels as DETACHED probes (stop-gradient into the heads) so supervision reads the representation without shaping it; (3) or halve the weights. The v23 result stands independent: the current best full recipe is A(strat, no aux heads) -> C.

Transfers to: [[maximal-label-utilisation-on-the-repaired-9c2265]], [[the-phase-a-rank-collapse-is-caused-by-the-d5e8e6]]

## Key numbers
- `cos_p50_v22`: 0.1839
- `cos_p95_v22`: 0.7129
- `eff_rank_v20_control`: 7.4385
- `eff_rank_v22`: 5.9358
- `rank_cost_of_supervision`: -1.5

## Artifacts
- `outputs/checkpoints/v22max/phase_a`
- `logs/v22_driver.log`
- `logs/v22_phase_a.log`
- `logs/v22_geometry.json`
- `scratch/run_v22_max.sh`

## Do not retry
- Do NOT add the full supervision block {diary+context+affect+statemask} to Phase A in one shot: it costs -1.50 eff_rank (7.44 -> 5.94) on the identical strat-fixed profile and re-enters collapse territory. Bisect per-head with the geometry gate (~25 min per arm) before any chain.
- Do NOT skip the between-stage geometry gate on supervised-A chains: it converted a 2-hour failed chain into a 21-minute verdict here.

## Notes
The gate architecture paid for itself: the compound supervision block re-collapses the geometry the strat fix bought (-1.50 rank), and the runner refused to fund B/C on it. The per-head decomposition is now the open question: diary (0.3), context (0.3), affect (0.3) and statemask each carry different gradient geometries; the bisection is three 25-minute geometry-gated A arms. Note also the SIGN structure: supervision hurt rank while v20's strat helped -- consistent with the dimensional-collapse literature's 'strong per-dimension pressure from auxiliary objectives' mechanism (Jing et al.: augmentation/aux-dominated variance). A lighter-touch integration (lower weights, or heads on DETACHED features as pure probes) is the literature-suggested alternative if bisection shows every head costs rank.

#deployment-distribution #sensor-text #baseline #contrastive #collapse #epoch-resolution #matched-baseline #optihealth-eiv
