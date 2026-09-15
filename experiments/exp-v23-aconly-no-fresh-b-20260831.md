---
artifacts:
- outputs/adapters/v23aconly_c
- logs/v23_driver.log
- logs/v23_phase_c.log
- logs/emission_v23aconly_supa_summary.json
- logs/emission_v23aconly_dense_summary.json
- scratch/run_v23_aconly.sh
closed: true
config_note: 'v23aconly: band04 C-only, seed 42, encoder = outputs/checkpoints/v20strat/phase_a
  (strat-fixed A, eff_rank 7.44), NO fresh Phase B. Single difference vs exp-v21''s
  C stage: the encoder checkpoint. Runner scratch/run_v23_aconly.sh, RC=0, 0 tracebacks.
  Scored on the same instrument.'
cost:
  gpu_hours: 1.0
  wall_hours: 1.0
crash_reason: ''
created_at: '2026-08-30T23:41:56+00:00'
diagnostics_measured:
- after: 0.567
  before: 0.327
  diagnostic_id: eiv_gen_peak_corr
- after: 5.414
  before: 5.775
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 15.908
  before: 17.534
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT insert a fresh new-corpus Phase B into any chain until the B stage is repaired:
  with identical A and C, the fresh B cost -0.240 corr (0.567 -> 0.327) and pushed
  top1 0.555 -> 0.900. Skipping B entirely is currently the better recipe.'
- 'Do NOT read the C-only 0.80 bpm band as covering these arms'' corr axis: the band
  prices MAE between healthy arms; the 0.24 corr swing from removing B is 3x any within-band
  motion.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS: supabase hr_raw corr 0.567 -- into the v17 near-healthy class (bar
    >= 0.55), +0.240 over the identical C off the fresh B (0.327), with top1 0.555
    vs 0.900 and distinct 16 vs 9. Removing the fresh Phase B reproduced the v17 result
    on a completely different A lineage (strat-fixed new-corpus A vs old-corpus ab_ctrl).
    THE FRESH PHASE B IS THE REMAINING DEGRADER. Note dense parse_ok dropped to 83/128
    -- the de-collapsed adapter emits more varied text; consistent with v17''s parse
    drop (92/128).'
  gate_id: g1-discriminate-b-vs-a-residual
  passed: true
- evidence: 'Advisory FAIL: MAE 5.414, skill -1.460 -- near-healthy class, not fully
    healthy (2.69-2.84). Same residual gap class as v17 (4.28/-0.33). The remaining
    deficit is shared by both B-free arms and is the next target after the B repair.'
  gate_id: g2-fully-healthy
  passed: false
git_ref: ''
hypothesis_id: the-fresh-phase-b-is-the-remaining-degrader-07c28f
id: exp-v23-aconly-no-fresh-b-20260831
interpretation:
  at: '2026-08-30T23:42:36+00:00'
  failure_mode: null
  lesson: 'SECOND DEFECT SITE CONFIRMED: the fresh new-corpus Phase B degrades C-trainability
    (-0.240 corr with identical A and C) while INFLATING eff_rank -- so B damage is
    invisible to the geometry probe that catches A damage. Both B-free arms (v17 old-B,
    v23 no-B) land in the same near-healthy class (corr 0.57-0.58, MAE 4.3-5.4). Working
    recipe until B is repaired: A(strat) -> C directly, skip B. Prime suspect for
    the B defect: phase_b_12ch_v11full does NOT carry mask_unlabelled_state, so its
    window-state triplet term still trains on 40.6% fabricated neutral -- one-variable
    arm queued conceptually (B with the mask on). The residual gap to fully-healthy
    (5.4 vs 2.8 MAE, shared by both B-free arms) is the third, smallest target and
    may be the A-side rank deficit (7.44 vs old-corpus 13.7 at v4w).'
  transfers_to:
  - the-fresh-phase-b-is-the-remaining-degrader-07c28f
  - restoring-stratified-batches-at-phase-a-heals-d6697a
  - the-window-level-ce-state-term-trains-on-a35561
key_numbers:
  dense_corr: 0.583
  dense_mae: 15.908
  dense_parse_ok: 83.0
  supa_corr: 0.567
  supa_delta_corr_vs_freshB: 0.24
  supa_mae: 5.414
  supa_skill: -1.46
  supa_top1: 0.555
observed_effects:
- delta: 0.24
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: -0.361
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: -1.626
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-v21-fixed-chain-20260831
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-fresh-phase-b-is-the-remaining-degrader-07c28f]].
Branches from [[exp-v21-fixed-chain-20260831]].

## Measurements
- [[eiv_gen_peak_corr]]: before 0.327 → after 0.567  (Δ +0.24, up ✓ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 5.775 → after 5.414  (Δ -0.361, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 17.534 → after 15.908  (Δ -1.626, down)

## Gates
- `g1-discriminate-b-vs-a-residual`: PASS — PASS: supabase hr_raw corr 0.567 -- into the v17 near-healthy class (bar >= 0.55), +0.240 over the identical C off the fresh B (0.327), with top1 0.555 vs 0.900 and distinct 16 vs 9. Removing the fresh Phase B reproduced the v17 result on a completely different A lineage (strat-fixed new-corpus A vs old-corpus ab_ctrl). THE FRESH PHASE B IS THE REMAINING DEGRADER. Note dense parse_ok dropped to 83/128 -- the de-collapsed adapter emits more varied text; consistent with v17's parse drop (92/128).
- `g2-fully-healthy`: **FAIL** — Advisory FAIL: MAE 5.414, skill -1.460 -- near-healthy class, not fully healthy (2.69-2.84). Same residual gap class as v17 (4.28/-0.33). The remaining deficit is shared by both B-free arms and is the next target after the B repair.

**Verdict:** supports

## Interpretation
SECOND DEFECT SITE CONFIRMED: the fresh new-corpus Phase B degrades C-trainability (-0.240 corr with identical A and C) while INFLATING eff_rank -- so B damage is invisible to the geometry probe that catches A damage. Both B-free arms (v17 old-B, v23 no-B) land in the same near-healthy class (corr 0.57-0.58, MAE 4.3-5.4). Working recipe until B is repaired: A(strat) -> C directly, skip B. Prime suspect for the B defect: phase_b_12ch_v11full does NOT carry mask_unlabelled_state, so its window-state triplet term still trains on 40.6% fabricated neutral -- one-variable arm queued conceptually (B with the mask on). The residual gap to fully-healthy (5.4 vs 2.8 MAE, shared by both B-free arms) is the third, smallest target and may be the A-side rank deficit (7.44 vs old-corpus 13.7 at v4w).

Transfers to: [[the-fresh-phase-b-is-the-remaining-degrader-07c28f]], [[restoring-stratified-batches-at-phase-a-heals-d6697a]], [[the-window-level-ce-state-term-trains-on-a35561]]

## Key numbers
- `dense_corr`: 0.583
- `dense_mae`: 15.908
- `dense_parse_ok`: 83
- `supa_corr`: 0.567
- `supa_delta_corr_vs_freshB`: 0.24
- `supa_mae`: 5.414
- `supa_skill`: -1.46
- `supa_top1`: 0.555

## Artifacts
- `outputs/adapters/v23aconly_c`
- `logs/v23_driver.log`
- `logs/v23_phase_c.log`
- `logs/emission_v23aconly_supa_summary.json`
- `logs/emission_v23aconly_dense_summary.json`
- `scratch/run_v23_aconly.sh`

## Do not retry
- Do NOT insert a fresh new-corpus Phase B into any chain until the B stage is repaired: with identical A and C, the fresh B cost -0.240 corr (0.567 -> 0.327) and pushed top1 0.555 -> 0.900. Skipping B entirely is currently the better recipe.
- Do NOT read the C-only 0.80 bpm band as covering these arms' corr axis: the band prices MAE between healthy arms; the 0.24 corr swing from removing B is 3x any within-band motion.

## Notes
DISCRIMINATOR LANDED ON THE B SIDE: identical A (v20strat) and identical C (band04) with the ONLY difference being fresh-B-vs-no-B moved corr 0.327 -> 0.567 (+0.240), top1 0.900 -> 0.555, distinct 9 -> 16. Reproduces the v17 class on a different A lineage, which triangulates: BOTH B-free arms (old-corpus A via v17's old B, strat-fixed new A here) land near-healthy, while EVERY arm that passes through a fresh new-corpus Phase B collapses. The Phase-B triplet stage trained on post-rebuild corpora is now the second confirmed defect site (first: the strat drop at A). Two open questions for tomorrow: (1) WHAT in fresh-B training degrades C-trainability while INFLATING eff_rank (the sweep shows fresh Bs at 8.7-12.4) -- suspects: the triplet objective's state buckets on the new label distribution (40.6% fabricated-neutral was fixed only in profiles carrying mask_unlabelled_state; phase_b_12ch_v11full does NOT), projector drift during B; (2) the residual near-healthy-to-healthy gap (5.4 vs 2.8 MAE) shared by both B-free arms. NEXT CONCRETE ARMS: (a) B with mask_unlabelled_state on / state-term off -- one variable against v21's B; (b) v22max A-stage geometry readout (running) extends the label-head question independently.

#collapse #deployment-distribution #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
