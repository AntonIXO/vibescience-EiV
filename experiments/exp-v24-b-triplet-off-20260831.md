---
artifacts:
- outputs/adapters/v24btripoff_c
- outputs/checkpoints/v24btripoff/phase_b
- logs/v24_driver.log
- logs/emission_v24btripoff_supa_summary.json
- scratch/run_v24_btripoff.sh
closed: true
config_note: 'v24btripoff: B = phase_b_12ch_v24tripoff (= phase_b_12ch_v11full with
  triplet_weight 0.05->0.0, dict-diff verified single variable) off v20strat A; C
  = byte-untouched band04; same scoring. Runner scratch/run_v24_btripoff.sh.'
cost:
  gpu_hours: 1.1
  wall_hours: 1.15
crash_reason: ''
created_at: '2026-08-31T00:05:42+00:00'
diagnostics_measured:
- after: 0.24
  before: 0.327
  diagnostic_id: eiv_gen_peak_corr
- after: 5.282
  before: 5.775
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 18.652
  before: 17.534
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT spend further arms on B-internal single-objective ablations (triplet on/off
  is done: 0.240 vs 0.327 -- both collapse classes): the B toxin is not the triplet
  term. The remaining B suspects are the B-stage small-anchor InfoNCE itself (batch
  25, no stratified key exists for B) and projector drift; a B with stratified-equivalent
  anchors is the one remaining B-side arm worth funding.'
- 'Do NOT read v24''s MAE 5.28 as improvement -- top1 0.73, corr 0.24: constant location
  again.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL: corr 0.240 -- BELOW even the triplet-carrying B (0.327), nowhere
    near the B-free class (0.567). Removing the triplet did not restore B; the toxin
    is in what remains: the B-stage InfoNCE re-alignment itself (25,000 samples at
    batch 25 = small-anchor contrastive on top of a strat-trained A -- the SAME anchor-starved
    geometry mechanism as the A-stage defect, now operating during B) or projector
    drift during B.'
  gate_id: g1-triplet-off-restores-b
  passed: false
- evidence: 'Advisory PASS on the letter (MAE 5.282 < v23''s 5.414) but meaningless
    under a failed g1: with corr 0.240 and top1 0.730 the MAE measures the constant''s
    location. Not evidence that B adds value.'
  gate_id: g2-b-adds-value-over-no-b
  passed: true
git_ref: ''
hypothesis_id: the-triplet-term-is-the-b-toxin-phase-b-12ch-34ca35
id: exp-v24-b-triplet-off-20260831
interpretation:
  at: '2026-08-31T01:15:28+00:00'
  failure_mode: claim_false
  lesson: 'The triplet term is NOT the B toxin -- removing it made C slightly worse
    (0.327 -> 0.240), so the degradation lives in what remains: B''s own InfoNCE re-alignment
    at batch 25 (no stratified mechanism exists for B; anchors ~25/step vs the 92
    that healed A) and/or projector drift. This unifies the two defect sites into
    ONE mechanism candidate: SMALL-ANCHOR CONTRASTIVE TRAINING ON POST-REBUILD CORPORA
    collapses geometry wherever it runs -- at A it was fixed by restoring stratified_batches;
    at B the same starvation operates with no key to restore. The one remaining B-side
    arm: B with stratified-equivalent anchor count. If that heals B, the entire collapse
    saga reduces to a single law: contrastive stages on this corpus need >=90 anchors/step.'
  transfers_to:
  - the-triplet-term-is-the-b-toxin-phase-b-12ch-34ca35
  - the-fresh-phase-b-is-the-remaining-degrader-07c28f
  - the-phase-a-rank-collapse-is-caused-by-the-d5e8e6
key_numbers:
  dense_corr: 0.421
  dense_mae: 18.652
  supa_corr: 0.24
  supa_mae: 5.282
  supa_top1: 0.73
observed_effects:
- delta: -0.087
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: -0.493
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: 1.118
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v23-aconly-no-fresh-b-20260831
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-triplet-term-is-the-b-toxin-phase-b-12ch-34ca35]].
Branches from [[exp-v23-aconly-no-fresh-b-20260831]].

## Measurements
- [[eiv_gen_peak_corr]]: before 0.327 → after 0.24  (Δ -0.087, down ✗ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 5.775 → after 5.282  (Δ -0.493, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 17.534 → after 18.652  (Δ +1.118, up)

## Gates
- `g1-triplet-off-restores-b`: **FAIL** — FAIL: corr 0.240 -- BELOW even the triplet-carrying B (0.327), nowhere near the B-free class (0.567). Removing the triplet did not restore B; the toxin is in what remains: the B-stage InfoNCE re-alignment itself (25,000 samples at batch 25 = small-anchor contrastive on top of a strat-trained A -- the SAME anchor-starved geometry mechanism as the A-stage defect, now operating during B) or projector drift during B.
- `g2-b-adds-value-over-no-b`: PASS — Advisory PASS on the letter (MAE 5.282 < v23's 5.414) but meaningless under a failed g1: with corr 0.240 and top1 0.730 the MAE measures the constant's location. Not evidence that B adds value.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The triplet term is NOT the B toxin -- removing it made C slightly worse (0.327 -> 0.240), so the degradation lives in what remains: B's own InfoNCE re-alignment at batch 25 (no stratified mechanism exists for B; anchors ~25/step vs the 92 that healed A) and/or projector drift. This unifies the two defect sites into ONE mechanism candidate: SMALL-ANCHOR CONTRASTIVE TRAINING ON POST-REBUILD CORPORA collapses geometry wherever it runs -- at A it was fixed by restoring stratified_batches; at B the same starvation operates with no key to restore. The one remaining B-side arm: B with stratified-equivalent anchor count. If that heals B, the entire collapse saga reduces to a single law: contrastive stages on this corpus need >=90 anchors/step.

Transfers to: [[the-triplet-term-is-the-b-toxin-phase-b-12ch-34ca35]], [[the-fresh-phase-b-is-the-remaining-degrader-07c28f]], [[the-phase-a-rank-collapse-is-caused-by-the-d5e8e6]]

## Key numbers
- `dense_corr`: 0.421
- `dense_mae`: 18.652
- `supa_corr`: 0.24
- `supa_mae`: 5.282
- `supa_top1`: 0.73

## Artifacts
- `outputs/adapters/v24btripoff_c`
- `outputs/checkpoints/v24btripoff/phase_b`
- `logs/v24_driver.log`
- `logs/emission_v24btripoff_supa_summary.json`
- `scratch/run_v24_btripoff.sh`

## Do not retry
- Do NOT spend further arms on B-internal single-objective ablations (triplet on/off is done: 0.240 vs 0.327 -- both collapse classes): the B toxin is not the triplet term. The remaining B suspects are the B-stage small-anchor InfoNCE itself (batch 25, no stratified key exists for B) and projector drift; a B with stratified-equivalent anchors is the one remaining B-side arm worth funding.
- Do NOT read v24's MAE 5.28 as improvement -- top1 0.73, corr 0.24: constant location again.

## Notes
Triplet exonerated as the B toxin: removing it made corr WORSE (0.327 -> 0.240). What remains in B is its own InfoNCE re-alignment at batch 25 with NO stratified_batches mechanism (the B profile family never had the key -- the healthy old-corpus Bs ran the same 25 and were fine, but they consumed an A trained on the old corpus). Best current full recipe remains A(strat) -> C (v23: corr 0.567, MAE 5.41). Overnight ledger: 9 experiments closed today; two confirmed mechanisms (strat drop at A, fresh-B degradation); label-head supervision costs rank at 0.3 weights (v22). Next session's preregistered queue: (1) B with high-anchor InfoNCE (stratified for B) -- the last B-side lever; (2) per-head bisection of the supervision block at halved weights; (3) the residual near-healthy gap (5.4 vs 2.8) -- suspects A-rank deficit vs old corpus (7.44 vs 13.7).

#collapse #deployment-distribution #sensor-text #contrastive #epoch-resolution #matched-baseline #optihealth-eiv
