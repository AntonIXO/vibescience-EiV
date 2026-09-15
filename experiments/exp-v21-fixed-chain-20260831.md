---
artifacts:
- outputs/adapters/v21chain_c
- outputs/checkpoints/v21chain/phase_b
- logs/v21_driver.log
- logs/emission_v21chain_supa_summary.json
- logs/emission_v21chain_dense_summary.json
- logs/geometry_sweep.json
- scratch/run_v21_chain.sh
- scratch/_geometry_sweep_all.py
closed: true
config_note: 'v21chain: full A->B->C on the fixed [14041] bundle. A = v20strat (stratified
  restored, eff_rank 7.44 gate PASSED at launch); B = phase_b_12ch_v11full off that
  A (RC=0; post-hoc geometry: B rank 12.36, HIGHEST of any fresh-B on record); C =
  byte-untouched band04. Scoring same instrument. Runner scratch/run_v21_chain.sh,
  self-gated between A and B.'
cost:
  gpu_hours: 1.6
  wall_hours: 2.0
crash_reason: ''
created_at: '2026-08-30T22:41:58+00:00'
diagnostics_measured:
- after: 5.775
  before: 10.058
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.327
  before: 0.231
  diagnostic_id: eiv_gen_peak_corr
- after: 17.534
  before: 24.519
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: 7.4385
  before: 4.4735
  diagnostic_id: eiv_effective_rank
do_not_retry:
- 'Do NOT treat phase_b eff_rank as a C-health predictor: the 68-checkpoint sweep
  shows fresh-B ranks of 8.7-12.4 on COLLAPSING chains (v11full B 8.71, v21chain B
  12.36) while phase_A rank and corpus era are what separate healthy from broken.
  B systematically inflates rank without restoring C-trainability.'
- 'Do NOT treat stratified_batches as SUFFICIENT: baseline_sync/phase_a ran strat=True
  (184k anchors) on the [13518] corpus and still has rank 4.61 -- anchors are NECESSARY
  (all rank>7 A-stages have them) but a second corpus-linked factor operates on post-rebuild
  rosters. baseline_sync differs from healthy ab_ctrl in roster (10-ds incl. 4 short
  lab cohorts vs 6-ds long-only) and seed.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS (auto-enforced pre-B): v20strat phase_a eff_rank 7.44 >= 7.0, logs/v20_geometry.json.
    The A-stage geometry premise held.'
  gate_id: g0-geometry-gate-passed
  passed: true
- evidence: 'FAIL: supabase hr_raw corr 0.327 vs >= 0.60, top1_mass 0.90 (highest
    concentration of the series) -- still a constant emitter despite the recovered
    A rank AND the highest fresh-B rank on record (12.36, post-hoc sweep).'
  gate_id: g1-chain-corr-healthy
  passed: false
- evidence: 'FAIL: MAE 5.775 vs trivial 3.954 -> skill -1.820. Better than every v11-v16
    chain but far from healthy (2.69-2.84).'
  gate_id: g2-beats-trivial
  passed: false
- evidence: 'Advisory FAIL: dense 17.534 vs trivial 6.986 (best dense of any fresh
    chain, still 2.5x the trivial). corr 0.399, distinct 20, top1 0.328.'
  gate_id: g3-dense-advisory
  passed: false
git_ref: ''
hypothesis_id: restoring-stratified-batches-at-phase-a-heals-d6697a
id: exp-v21-fixed-chain-20260831
interpretation:
  at: '2026-08-30T22:42:36+00:00'
  failure_mode: recipe_conditional
  lesson: 'The strat fix is REAL but INSUFFICIENT end-to-end: it produced the best
    fresh-chain numbers of the series (MAE 10.06 -> 5.78, dense 24.5 -> 17.5) yet
    the chain still crystallised a constant (top1 0.90, corr 0.327). Combined with
    the 68-checkpoint sweep, the mechanism model must be revised: (a) anchors are
    necessary, not sufficient -- baseline_sync had strat AND rank 4.61 on [13518],
    differing from healthy in ROSTER (short lab cohorts) and seed; (b) phase_b rank
    inflates without restoring trainability (fresh Bs 8.7-12.4 all collapse), so the
    A-rank->C-health mapping that held on the 5-way probe does NOT extend through
    a fresh B; (c) v17''s near-healthy came off an OLD-corpus B. Two carrier candidates
    remain: the fresh PHASE B trained on the new corpus, vs a residual A property
    invisible to eff_rank. exp-v23 (C-only directly off v20strat/phase_a) discriminates;
    it is running.'
  transfers_to:
  - restoring-stratified-batches-at-phase-a-heals-d6697a
  - the-phase-a-rank-collapse-is-caused-by-the-d5e8e6
key_numbers:
  a_rank: 7.44
  b_rank: 12.36
  dense_corr: 0.399
  dense_mae: 17.534
  supa_corr: 0.327
  supa_mae: 5.775
  supa_skill: -1.82
  supa_top1: 0.9
  sweep_n_checkpoints: 68.0
observed_effects:
- delta: -4.283
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: 0.096
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: -6.985
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
- delta: 2.965
  diagnostic_id: eiv_effective_rank
  direction: up
parent_experiment_id: exp-v20-stratified-batches-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_supabase_hr_raw_mae_bpm: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[restoring-stratified-batches-at-phase-a-heals-d6697a]].
Branches from [[exp-v20-stratified-batches-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 10.058 → after 5.775  (Δ -4.283, down ✓ predicted down)
- [[eiv_gen_peak_corr]]: before 0.231 → after 0.327  (Δ +0.096, up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 24.519 → after 17.534  (Δ -6.985, down)
- [[eiv_effective_rank]]: before 4.4735 → after 7.4385  (Δ +2.965, up)

## Gates
- `g0-geometry-gate-passed`: PASS — PASS (auto-enforced pre-B): v20strat phase_a eff_rank 7.44 >= 7.0, logs/v20_geometry.json. The A-stage geometry premise held.
- `g1-chain-corr-healthy`: **FAIL** — FAIL: supabase hr_raw corr 0.327 vs >= 0.60, top1_mass 0.90 (highest concentration of the series) -- still a constant emitter despite the recovered A rank AND the highest fresh-B rank on record (12.36, post-hoc sweep).
- `g2-beats-trivial`: **FAIL** — FAIL: MAE 5.775 vs trivial 3.954 -> skill -1.820. Better than every v11-v16 chain but far from healthy (2.69-2.84).
- `g3-dense-advisory`: **FAIL** — Advisory FAIL: dense 17.534 vs trivial 6.986 (best dense of any fresh chain, still 2.5x the trivial). corr 0.399, distinct 20, top1 0.328.

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `recipe_conditional`

The strat fix is REAL but INSUFFICIENT end-to-end: it produced the best fresh-chain numbers of the series (MAE 10.06 -> 5.78, dense 24.5 -> 17.5) yet the chain still crystallised a constant (top1 0.90, corr 0.327). Combined with the 68-checkpoint sweep, the mechanism model must be revised: (a) anchors are necessary, not sufficient -- baseline_sync had strat AND rank 4.61 on [13518], differing from healthy in ROSTER (short lab cohorts) and seed; (b) phase_b rank inflates without restoring trainability (fresh Bs 8.7-12.4 all collapse), so the A-rank->C-health mapping that held on the 5-way probe does NOT extend through a fresh B; (c) v17's near-healthy came off an OLD-corpus B. Two carrier candidates remain: the fresh PHASE B trained on the new corpus, vs a residual A property invisible to eff_rank. exp-v23 (C-only directly off v20strat/phase_a) discriminates; it is running.

Transfers to: [[restoring-stratified-batches-at-phase-a-heals-d6697a]], [[the-phase-a-rank-collapse-is-caused-by-the-d5e8e6]]

## Key numbers
- `a_rank`: 7.44
- `b_rank`: 12.36
- `dense_corr`: 0.399
- `dense_mae`: 17.534
- `supa_corr`: 0.327
- `supa_mae`: 5.775
- `supa_skill`: -1.82
- `supa_top1`: 0.9
- `sweep_n_checkpoints`: 68

## Artifacts
- `outputs/adapters/v21chain_c`
- `outputs/checkpoints/v21chain/phase_b`
- `logs/v21_driver.log`
- `logs/emission_v21chain_supa_summary.json`
- `logs/emission_v21chain_dense_summary.json`
- `logs/geometry_sweep.json`
- `scratch/run_v21_chain.sh`
- `scratch/_geometry_sweep_all.py`

## Do not retry
- Do NOT treat phase_b eff_rank as a C-health predictor: the 68-checkpoint sweep shows fresh-B ranks of 8.7-12.4 on COLLAPSING chains (v11full B 8.71, v21chain B 12.36) while phase_A rank and corpus era are what separate healthy from broken. B systematically inflates rank without restoring C-trainability.
- Do NOT treat stratified_batches as SUFFICIENT: baseline_sync/phase_a ran strat=True (184k anchors) on the [13518] corpus and still has rank 4.61 -- anchors are NECESSARY (all rank>7 A-stages have them) but a second corpus-linked factor operates on post-rebuild rosters. baseline_sync differs from healthy ab_ctrl in roster (10-ds incl. 4 short lab cohorts vs 6-ds long-only) and seed.

## Notes
PARTIAL: the strat fix moved every axis in the right direction (best fresh-chain numbers of the series: MAE 5.78 vs 10.06, dense 17.5 vs 24.5) but the chain still emits a near-constant (top1 0.90). The 68-checkpoint geometry sweep (logs/geometry_sweep.json, CPU) reframes the mechanism: (1) anchors NECESSARY-not-sufficient -- baseline_sync had strat AND rank 4.61 on [13518]; its delta vs healthy is the ROSTER (10-ds with short lab cohorts vs 6-ds long-only) and seed; (2) phase_b rank does not predict C-health (fresh Bs reach 12.4 and still collapse); the predictive signal was A-stage rank x corpus era; (3) v17's healthy-ish result came off an OLD-corpus phase_b -- so the remaining carrier candidates are the fresh PHASE B (trained on the new corpus) vs a residual A-stage property invisible to rank. exp-v23 (C-only directly off v20strat/phase_a, skipping fresh B) is running as the discriminator; v22max queued behind it.

#collapse #contrastive #deployment-distribution #sensor-text #baseline #epoch-resolution #matched-baseline #optihealth-eiv
