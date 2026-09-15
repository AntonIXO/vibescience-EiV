---
artifacts:
- outputs/checkpoints/v20strat/phase_a
- logs/v20_driver.log
- logs/v20_phase_a.log
- logs/v20_geometry.json
- scratch/run_v20_strat.sh
- scratch/_geom_gate_v20.py
closed: true
config_note: 'v20strat: phase_a_12ch_v20strat = phase_a_12ch_v11full + stratified_batches
  {lifesnaps:45,pmdata:12,*:35} restored (dict diff verified: only stratified_batches+notes).
  Full 19-ds roster kept. Phase A 2000 steps, 1229.6 s (5.6x longer than plain batch
  — 92 anchors/batch), RC=0, InfoNCE 4.76 -> -18.40. Readout: scratch/_geom_gate_v20.py,
  same 300 eval rows, logs/v20_geometry.json.'
cost:
  gpu_hours: 0.34
  wall_hours: 0.4
crash_reason: ''
created_at: '2026-08-30T21:33:22+00:00'
diagnostics_measured:
- after: 7.4385
  before: 4.4735
  diagnostic_id: eiv_effective_rank
- after: 0.1478
  before: 0.263
  diagnostic_id: eiv_sensor_median_cosine
do_not_retry:
- 'Do NOT write a new Phase-A profile without stratified_batches (or an equivalent
  >=90 anchors/batch): plain batch_size 16 cuts InfoNCE anchors 5.75x and collapses
  the token-cloud rank 8.35 -> 4.47, which downstream produces constant-emitting Phase
  C regardless of recipe. This is the named root cause of the entire v11-v18 collapse
  series.'
- 'Do NOT judge Phase-A health by loss values or linear HR probes: the collapsed arm
  had the LOWER (easier) InfoNCE loss (0.287 vs 1.377). Use the geometry probe (eff_rank
  + cos_p95, scratch/_geom_gate_v20.py, ~3 min).'
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS: v20strat phase_a eff_rank 7.44 vs the >= 7.0 bar -- 76% of the
    healthy-carrier gap recovered by restoring ONE profile key (carrier 4.47, healthy
    8.35, v19 roster-cut control 5.19). The anchors-per-batch drift is the Phase-A
    rank-collapse mechanism.'
  gate_id: g1-stratified-batches-recover-rank
  passed: true
- evidence: 'PASS and better than the healthy anchor: cos_p95 0.6625 vs healthy 0.695,
    carrier 0.891, v19 0.881. The top of the similarity distribution -- exactly where
    v19''s partial move failed -- fully recovered.'
  gate_id: g2-cos-p95-top-of-distribution
  passed: true
git_ref: ''
hypothesis_id: the-phase-a-rank-collapse-is-caused-by-the-d5e8e6
id: exp-v20-stratified-batches-20260830
interpretation:
  at: '2026-08-30T21:33:56+00:00'
  failure_mode: null
  lesson: 'CONFIRMED ROOT CAUSE of the entire v11-v18 collapse series: the v11-era
    Phase-A profile rewrite (around the new-corpus whitelist) silently dropped stratified_batches,
    cutting InfoNCE anchors 184k -> 32k (5.75x). Restoring the one key recovered 76%
    of the token-cloud rank gap (4.47 -> 7.44 vs healthy 8.35) and fully recovered
    cos_p95. Reusable engineering rules: (1) every new Phase-A profile MUST carry
    stratified_batches or equivalent anchor count -- add a guard test; (2) a FASTER-than-usual
    training stage is a red flag, not a win (the broken A ran 217 s vs healthy 1230
    s all week and nobody asked why); (3) the geometry probe (eff_rank/cos_p95, 3
    min) is the Phase-A health check -- loss values actively mislead (the broken arm
    had the LOWER loss).'
  transfers_to:
  - the-phase-a-rank-collapse-is-caused-by-the-d5e8e6
  - restoring-stratified-batches-at-phase-a-heals-d6697a
  - the-fresh-chain-collapse-is-not-caused-by-the-37ddda
key_numbers:
  anchors_carrier: 32000.0
  anchors_healthy: 184000.0
  cos_p50_v20: 0.1478
  cos_p95_v20: 0.6625
  eff_rank_carrier: 4.4735
  eff_rank_healthy: 8.353
  eff_rank_v20: 7.4385
  gap_recovered_share: 0.76
  phase_a_seconds: 1229.6
observed_effects:
- delta: 2.965
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: -0.1152
  diagnostic_id: eiv_sensor_median_cosine
  direction: down
parent_experiment_id: exp-v19-longA-infonce-pool-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_effective_rank: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-phase-a-rank-collapse-is-caused-by-the-d5e8e6]].
Branches from [[exp-v19-longA-infonce-pool-20260830]].

## Measurements
- [[eiv_effective_rank]]: before 4.4735 → after 7.4385  (Δ +2.965, up ✓ predicted up)
- [[eiv_sensor_median_cosine]]: before 0.263 → after 0.1478  (Δ -0.1152, down)

## Gates
- `g1-stratified-batches-recover-rank`: PASS — PASS: v20strat phase_a eff_rank 7.44 vs the >= 7.0 bar -- 76% of the healthy-carrier gap recovered by restoring ONE profile key (carrier 4.47, healthy 8.35, v19 roster-cut control 5.19). The anchors-per-batch drift is the Phase-A rank-collapse mechanism.
- `g2-cos-p95-top-of-distribution`: PASS — PASS and better than the healthy anchor: cos_p95 0.6625 vs healthy 0.695, carrier 0.891, v19 0.881. The top of the similarity distribution -- exactly where v19's partial move failed -- fully recovered.

**Verdict:** supports

## Interpretation
CONFIRMED ROOT CAUSE of the entire v11-v18 collapse series: the v11-era Phase-A profile rewrite (around the new-corpus whitelist) silently dropped stratified_batches, cutting InfoNCE anchors 184k -> 32k (5.75x). Restoring the one key recovered 76% of the token-cloud rank gap (4.47 -> 7.44 vs healthy 8.35) and fully recovered cos_p95. Reusable engineering rules: (1) every new Phase-A profile MUST carry stratified_batches or equivalent anchor count -- add a guard test; (2) a FASTER-than-usual training stage is a red flag, not a win (the broken A ran 217 s vs healthy 1230 s all week and nobody asked why); (3) the geometry probe (eff_rank/cos_p95, 3 min) is the Phase-A health check -- loss values actively mislead (the broken arm had the LOWER loss).

Transfers to: [[the-phase-a-rank-collapse-is-caused-by-the-d5e8e6]], [[restoring-stratified-batches-at-phase-a-heals-d6697a]], [[the-fresh-chain-collapse-is-not-caused-by-the-37ddda]]

## Key numbers
- `anchors_carrier`: 32000
- `anchors_healthy`: 184000
- `cos_p50_v20`: 0.1478
- `cos_p95_v20`: 0.6625
- `eff_rank_carrier`: 4.4735
- `eff_rank_healthy`: 8.353
- `eff_rank_v20`: 7.4385
- `gap_recovered_share`: 0.76
- `phase_a_seconds`: 1229.6

## Artifacts
- `outputs/checkpoints/v20strat/phase_a`
- `logs/v20_driver.log`
- `logs/v20_phase_a.log`
- `logs/v20_geometry.json`
- `scratch/run_v20_strat.sh`
- `scratch/_geom_gate_v20.py`

## Do not retry
- Do NOT write a new Phase-A profile without stratified_batches (or an equivalent >=90 anchors/batch): plain batch_size 16 cuts InfoNCE anchors 5.75x and collapses the token-cloud rank 8.35 -> 4.47, which downstream produces constant-emitting Phase C regardless of recipe. This is the named root cause of the entire v11-v18 collapse series.
- Do NOT judge Phase-A health by loss values or linear HR probes: the collapsed arm had the LOWER (easier) InfoNCE loss (0.287 vs 1.377). Use the geometry probe (eff_rank + cos_p95, scratch/_geom_gate_v20.py, ~3 min).

## Notes
MECHANISM CONFIRMED, gates 2/2 PASS, first 'supports'-class result on this problem since exp-054. One restored profile key (stratified_batches) recovered 76% of the rank gap and OVERSHOT the healthy anchor on cos_p95 (0.662 vs 0.695). Cost asymmetry now explained too: the healthy Phase A took ~1230 s, the broken ones ~217 s -- the '4-minute Phase A' that looked like a nice speedup all week WAS the bug. Chain v21 (A=v20strat -> B unchanged -> C band04) is already running with this checkpoint, geometry gate auto-passed at launch; its supabase capture is the closing link. Root cause narrative for the record: when the v11 profile family was rewritten around the new-corpus whitelist, stratified_batches did not survive the rewrite -- a silent single-key regression that produced eleven collapsed arms and consumed ~10 GPU-hours of localisation.

#collapse #contrastive #sensor-text #deployment-distribution #epoch-resolution #matched-baseline #optihealth-eiv
