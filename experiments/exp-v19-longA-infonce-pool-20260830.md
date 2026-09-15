---
artifacts:
- outputs/checkpoints/v19longA/phase_a
- logs/v19_driver.log
- logs/v19_phase_a.log
- scratch/run_v19_longA.sh
- logs/token_geometry_probe.json
- logs/token_geometry_5way.log
closed: true
config_note: 'v19longA: phase_a_12ch_v19longA = phase_a_12ch_v11full with dataset_filter
  cut to 7 long cohorts (dict diff verified: only dataset_filter+notes). Phase A 2000
  steps, 239 s, RC=0, loss 2.56 -> -17.38. Readout: token-geometry probe, same 300
  eval rows. Anchors re-measured in the same 3-way run (rng consumption differs slightly
  from the 5-way pass: ab_ctrl 8.35 vs 8.43, v11full 4.47 vs 4.58 -- ~0.1 jitter,
  verdict-irrelevant).'
cost:
  gpu_hours: 0.07
  wall_hours: 0.12
crash_reason: ''
created_at: '2026-08-30T18:40:24+00:00'
diagnostics_measured:
- after: 5.1868
  before: 4.4735
  diagnostic_id: eiv_effective_rank
- after: 0.1555
  before: 0.263
  diagnostic_id: eiv_sensor_median_cosine
do_not_retry:
- 'Do NOT retry pool-composition levers against the Phase-A rank collapse (short-cohort
  cuts, coverage gates on the A pool): cutting all 12 short cohorts moved eff_rank
  +0.71 of a 3.88 gap (18%). Pool composition is now refuted at BOTH stages (C: v14;
  A: this).'
- 'The remaining delta between the healthy ab_ctrl Phase-A run and every collapsed-lineage
  Phase-A run is NOT the roster: ab_ctrl trained on the [12547] corpus''s POOL with
  the OLD dataset_filter AND the old corpus content. v19 equalised roster-composition
  only. The next bracket is corpus CONTENT vs Phase-A recipe drift: diff the phase_a
  profiles (ab_ctrl''s phase_a_12ch_pmdata lineage vs phase_a_12ch_v11full) key by
  key, then if identical, train the v11full profile on data/canonical_1min_conv (old
  content) and probe geometry.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL: v19longA phase_a eff_rank 5.19 vs the >= 7.0 bar (anchors in the
    same run: carrier 4.47, healthy 8.35). The long pool moved rank by +0.71 of the
    3.88 gap -- 18%, nowhere near the 70% the mechanism demanded. Cutting the short
    cohorts from the InfoNCE pool does NOT restore the geometry.'
  gate_id: g1-long-pool-recovers-phase-a-rank
  passed: false
- evidence: Advisory PASS on the letter (cos_p50 0.1555 <= 0.16) but note cos_p95
    stayed carrier-like (0.881 vs healthy 0.695) -- the bulk median moved while the
    top of the similarity distribution did not, consistent with a cloud still low-rank
    in its dominant directions.
  gate_id: g2-cosine-signature-recorded
  passed: true
git_ref: ''
hypothesis_id: the-phase-a-rank-collapse-is-caused-by-short-145c4a
id: exp-v19-longA-infonce-pool-20260830
interpretation:
  at: '2026-08-30T18:41:02+00:00'
  failure_mode: claim_false
  lesson: 'Short near-empty windows in the InfoNCE pool are NOT what collapses the
    Phase-A token-cloud rank: removing all 12 short cohorts recovered only 18% of
    the healthy-carrier gap (eff_rank 4.47 -> 5.19 vs healthy 8.35). Pool composition
    is now refuted at both stages (C by v14, A by this). The Phase-A geometry damage
    comes from something else the post-rebuild A runs share: either recipe drift between
    the healthy lineage''s phase_a profile and phase_a_12ch_v11full (the earlier byte-check
    compared different objects -- recheck directly), or corpus CONTENT (bracket by
    training the v11full A profile on canonical_1min_conv, 4 min + CPU probe), or
    the caption side of the contrastive pair on new cohorts. The geometry probe is
    now a validated 5-minute instrument for every one of these brackets -- no C arms
    needed.'
  transfers_to:
  - the-phase-a-rank-collapse-is-caused-by-short-145c4a
  - the-c-stage-collapse-is-caused-by-short-window-5859e2
key_numbers:
  cos_p50_v19: 0.1555
  cos_p95_v19: 0.8814
  eff_rank_carrier: 4.4735
  eff_rank_healthy: 8.353
  eff_rank_v19: 5.1868
  gap_recovered_share: 0.18
  phase_a_seconds: 239.35
observed_effects:
- delta: 0.7133
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: -0.1075
  diagnostic_id: eiv_sensor_median_cosine
  direction: down
parent_experiment_id: exp-v18-new-encoder-old-bundle-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_effective_rank: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[the-phase-a-rank-collapse-is-caused-by-short-145c4a]].
Branches from [[exp-v18-new-encoder-old-bundle-20260830]].

## Measurements
- [[eiv_effective_rank]]: before 4.4735 → after 5.1868  (Δ +0.7133, up ✓ predicted up)
- [[eiv_sensor_median_cosine]]: before 0.263 → after 0.1555  (Δ -0.1075, down)

## Gates
- `g1-long-pool-recovers-phase-a-rank`: **FAIL** — FAIL: v19longA phase_a eff_rank 5.19 vs the >= 7.0 bar (anchors in the same run: carrier 4.47, healthy 8.35). The long pool moved rank by +0.71 of the 3.88 gap -- 18%, nowhere near the 70% the mechanism demanded. Cutting the short cohorts from the InfoNCE pool does NOT restore the geometry.
- `g2-cosine-signature-recorded`: PASS — Advisory PASS on the letter (cos_p50 0.1555 <= 0.16) but note cos_p95 stayed carrier-like (0.881 vs healthy 0.695) -- the bulk median moved while the top of the similarity distribution did not, consistent with a cloud still low-rank in its dominant directions.

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `claim_false`

Short near-empty windows in the InfoNCE pool are NOT what collapses the Phase-A token-cloud rank: removing all 12 short cohorts recovered only 18% of the healthy-carrier gap (eff_rank 4.47 -> 5.19 vs healthy 8.35). Pool composition is now refuted at both stages (C by v14, A by this). The Phase-A geometry damage comes from something else the post-rebuild A runs share: either recipe drift between the healthy lineage's phase_a profile and phase_a_12ch_v11full (the earlier byte-check compared different objects -- recheck directly), or corpus CONTENT (bracket by training the v11full A profile on canonical_1min_conv, 4 min + CPU probe), or the caption side of the contrastive pair on new cohorts. The geometry probe is now a validated 5-minute instrument for every one of these brackets -- no C arms needed.

Transfers to: [[the-phase-a-rank-collapse-is-caused-by-short-145c4a]], [[the-c-stage-collapse-is-caused-by-short-window-5859e2]]

## Key numbers
- `cos_p50_v19`: 0.1555
- `cos_p95_v19`: 0.8814
- `eff_rank_carrier`: 4.4735
- `eff_rank_healthy`: 8.353
- `eff_rank_v19`: 5.1868
- `gap_recovered_share`: 0.18
- `phase_a_seconds`: 239.35

## Artifacts
- `outputs/checkpoints/v19longA/phase_a`
- `logs/v19_driver.log`
- `logs/v19_phase_a.log`
- `scratch/run_v19_longA.sh`
- `logs/token_geometry_probe.json`
- `logs/token_geometry_5way.log`

## Do not retry
- Do NOT retry pool-composition levers against the Phase-A rank collapse (short-cohort cuts, coverage gates on the A pool): cutting all 12 short cohorts moved eff_rank +0.71 of a 3.88 gap (18%). Pool composition is now refuted at BOTH stages (C: v14; A: this).
- The remaining delta between the healthy ab_ctrl Phase-A run and every collapsed-lineage Phase-A run is NOT the roster: ab_ctrl trained on the [12547] corpus's POOL with the OLD dataset_filter AND the old corpus content. v19 equalised roster-composition only. The next bracket is corpus CONTENT vs Phase-A recipe drift: diff the phase_a profiles (ab_ctrl's phase_a_12ch_pmdata lineage vs phase_a_12ch_v11full) key by key, then if identical, train the v11full profile on data/canonical_1min_conv (old content) and probe geometry.

## Notes
Cheap and decisive: 4 GPU-minutes + a CPU probe killed the pool-composition account at the A stage. The rank collapse is NOT caused by short near-empty windows in the InfoNCE pool. Suspects remaining for the Phase-A geometry damage: (a) Phase-A recipe drift between the healthy lineage's profile and v11full (hparams were byte-checked for A-stage keys earlier, but the check compared healthy-vs-collapsed CHAINS on the old corpus era -- recheck ab_ctrl's actual phase_a profile vs phase_a_12ch_v11full including state_target/soft additions, hr_coverage_denominator, caption_style); (b) corpus CONTENT (train the v11full A profile on canonical_1min_conv and probe -- completes an A-stage 2x2); (c) the caption/text side of InfoNCE on new cohorts (hash captions of near-identical lab days creating false negatives pressure toward a shared direction). Note v19 was 239s not 217s: the pool shrank but steps are fixed at 2000.

#collapse #contrastive #sensor-text #deployment-distribution #epoch-resolution #matched-baseline #optihealth-eiv
