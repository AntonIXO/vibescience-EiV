---
artifacts:
- outputs/adapters/v27affect_c
- outputs/checkpoints/v27affect/phase_b
- logs/v27_driver.log
- logs/emission_v27affect_supa_summary.json
- logs/emission_v27affect_dense_summary.json
- scratch/run_v27_affectchain.sh
closed: true
config_note: 'v27affect: A = outputs/checkpoints/v26affect/phase_a (affect 0.3, eff_rank
  8.60, valence/arousal/stress over 6 corpora) -> B = phase_b_12ch_v25bigbatch batch
  30 -> C = band04 untouched. ONE variable vs exp-v25r30: the A checkpoint. Runner
  scratch/run_v27_affectchain.sh.'
cost:
  gpu_hours: 1.3
  wall_hours: 1.35
crash_reason: ''
created_at: '2026-08-31T03:46:36+00:00'
diagnostics_measured:
- after: 4.688
  before: 3.203
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.659
  before: 0.717
  diagnostic_id: eiv_gen_peak_corr
- after: 8.587
  before: 7.388
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT equate Phase-A eff_rank gains with deployment gains: the affect-A (rank
  8.60, best fresh-A on record) chains to MAE 4.69 vs the no-head A''s 3.20 (rank
  7.44). Rank is a NECESSARY-floor diagnostic (below ~6 predicts collapse), not a
  monotone quality score above the floor.'
- Do NOT stack the affect head into the baseline chain at 0.3 for deployment purposes;
  if affect supervision is wanted for its own probes, it costs ~1.5 bpm supabase MAE
  at this weight.
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL: MAE 4.688 vs the 2.836 bar -- and WORSE than the no-head comparator
    v25r30 (3.203) by 1.49 bpm, outside the 0.80 C-only band. top1_mass 0.835 despite
    corr 0.659: the adapter concentrates most rows on one value while tracking with
    the rest. The affect head''s Phase-A rank gain (8.60) did NOT survive the chain
    into deployment accuracy.'
  gate_id: g1-beats-baseline-median
  passed: false
- evidence: 'PASS on corr (0.659 >= 0.60) but MAE 4.688 sits outside the 2.40-4.00
    no-harm band. Dense mirrors it: corr 0.730 (BEST dense corr of any chain) with
    MAE 8.587 (worse than v25r30''s 7.388). The affect-A chain reads days BETTER (corr
    up on dense) but emits values further from truth -- a calibration/offset degradation,
    not a collapse.'
  gate_id: g2-no-harm-vs-v25
  passed: true
git_ref: ''
hypothesis_id: affect-supervised-chain-beats-the-baseline-v27-030fea
id: exp-v27-affect-chain-20260831
interpretation:
  at: '2026-08-31T04:57:34+00:00'
  failure_mode: claim_false
  lesson: 'Phase-A rank gains do not chain into deployment gains: the affect-A (rank
    8.60, best fresh A ever) produced MAE 4.69 vs the plain strat-A''s 3.20 -- while
    KEEPING corr 0.66-0.73 (best dense corr on record). The damage signature is calibration/offset,
    not collapse: days are ordered correctly but values shift. Three laws consolidate:
    (1) eff_rank is a FLOOR diagnostic -- below ~6 predicts collapse, above it is
    not monotone with quality; (2) aux supervision at 0.3 perturbs C''s value calibration
    even when geometry improves; (3) the baseline chain stays v25r30 (strat-A -> B@30
    -> band04: 3.203/0.717). Label utilisation continues via lower weights (0.1) or
    detached probes, tested against the deployment gate, not the rank gate.'
  transfers_to:
  - affect-supervised-chain-beats-the-baseline-v27-030fea
  - maximal-label-utilisation-on-the-repaired-9c2265
  - per-head-rank-cost-bisection-adding-each-dfd199
key_numbers:
  dense_corr: 0.73
  dense_mae: 8.587
  supa_corr: 0.659
  supa_mae: 4.688
  supa_skill: -0.734
  supa_top1: 0.835
  v25_comparator_mae: 3.203
observed_effects:
- delta: 1.485
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: -0.058
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: 1.199
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v26-head-bisection-20260831
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[affect-supervised-chain-beats-the-baseline-v27-030fea]].
Branches from [[exp-v26-head-bisection-20260831]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 3.203 → after 4.688  (Δ +1.485, up ✗ predicted down)
- [[eiv_gen_peak_corr]]: before 0.717 → after 0.659  (Δ -0.058, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 7.388 → after 8.587  (Δ +1.199, up)

## Gates
- `g1-beats-baseline-median`: **FAIL** — FAIL: MAE 4.688 vs the 2.836 bar -- and WORSE than the no-head comparator v25r30 (3.203) by 1.49 bpm, outside the 0.80 C-only band. top1_mass 0.835 despite corr 0.659: the adapter concentrates most rows on one value while tracking with the rest. The affect head's Phase-A rank gain (8.60) did NOT survive the chain into deployment accuracy.
- `g2-no-harm-vs-v25`: PASS — PASS on corr (0.659 >= 0.60) but MAE 4.688 sits outside the 2.40-4.00 no-harm band. Dense mirrors it: corr 0.730 (BEST dense corr of any chain) with MAE 8.587 (worse than v25r30's 7.388). The affect-A chain reads days BETTER (corr up on dense) but emits values further from truth -- a calibration/offset degradation, not a collapse.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

Phase-A rank gains do not chain into deployment gains: the affect-A (rank 8.60, best fresh A ever) produced MAE 4.69 vs the plain strat-A's 3.20 -- while KEEPING corr 0.66-0.73 (best dense corr on record). The damage signature is calibration/offset, not collapse: days are ordered correctly but values shift. Three laws consolidate: (1) eff_rank is a FLOOR diagnostic -- below ~6 predicts collapse, above it is not monotone with quality; (2) aux supervision at 0.3 perturbs C's value calibration even when geometry improves; (3) the baseline chain stays v25r30 (strat-A -> B@30 -> band04: 3.203/0.717). Label utilisation continues via lower weights (0.1) or detached probes, tested against the deployment gate, not the rank gate.

Transfers to: [[affect-supervised-chain-beats-the-baseline-v27-030fea]], [[maximal-label-utilisation-on-the-repaired-9c2265]], [[per-head-rank-cost-bisection-adding-each-dfd199]]

## Key numbers
- `dense_corr`: 0.73
- `dense_mae`: 8.587
- `supa_corr`: 0.659
- `supa_mae`: 4.688
- `supa_skill`: -0.734
- `supa_top1`: 0.835
- `v25_comparator_mae`: 3.203

## Artifacts
- `outputs/adapters/v27affect_c`
- `outputs/checkpoints/v27affect/phase_b`
- `logs/v27_driver.log`
- `logs/emission_v27affect_supa_summary.json`
- `logs/emission_v27affect_dense_summary.json`
- `scratch/run_v27_affectchain.sh`

## Do not retry
- Do NOT equate Phase-A eff_rank gains with deployment gains: the affect-A (rank 8.60, best fresh-A on record) chains to MAE 4.69 vs the no-head A's 3.20 (rank 7.44). Rank is a NECESSARY-floor diagnostic (below ~6 predicts collapse), not a monotone quality score above the floor.
- Do NOT stack the affect head into the baseline chain at 0.3 for deployment purposes; if affect supervision is wanted for its own probes, it costs ~1.5 bpm supabase MAE at this weight.

## Notes
REFUTED and the lesson reshapes the rank diagnostic: the affect head's +1.16 Phase-A rank did NOT buy deployment -- MAE degraded 3.20 -> 4.69 (outside the band) while corr stayed healthy-ish (0.659 supa, 0.730 dense -- the best dense corr recorded). The signature (high corr + high top1 + inflated MAE) is calibration/offset damage: the model orders days correctly but emits shifted values. So eff_rank is a FLOOR diagnostic (rank < ~6 predicts collapse) not a quality score, and label supervision at 0.3 perturbs the value-emission calibration even when geometry improves. Utilisation path forward: (a) v25r30 stays the baseline candidate; (b) supervision integration must protect C-stage calibration -- lower weight (0.1) or detached probes; (c) the affect ENCODER remains valuable for affect-side questions (its own probes) independent of HR deployment.

#deployment-distribution #sensor-text #baseline #contrastive #wearables #epoch-resolution #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
