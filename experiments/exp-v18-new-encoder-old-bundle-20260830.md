---
artifacts:
- outputs/adapters/v18oldbundle_c
- logs/v18_driver.log
- logs/v18_phase_c.log
- logs/emission_v18oldbundle_supa_summary.json
- logs/emission_v18oldbundle_dense_summary.json
- scratch/run_v18_oldbundle.sh
- logs/token_geometry_probe.json
- scratch/_token_geometry_probe.py
closed: true
config_note: 'v18oldbundle: ONE variable vs exp-v16 -- training bundle swapped to
  data/canonical_1min_conv ([10344], the tensor jsonfit_s42 trained healthily on,
  corr 0.809). Same v11full/phase_b frozen encoder, same byte-untouched band04 profile,
  seed 42, scored on the same current eval_v3_1min (sha 03d62723). Complement 2x2
  cell to exp-v17. Runner scratch/run_v18_oldbundle.sh (queued behind v17 on GPU).
  RC=0, 0 tracebacks, 2600 steps.'
cost:
  gpu_hours: 1.0
  wall_hours: 0.98
crash_reason: ''
created_at: '2026-08-30T18:21:22+00:00'
diagnostics_measured:
- after: 7.847
  before: 10.058
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.194
  before: 0.231
  diagnostic_id: eiv_gen_peak_corr
- after: 30.255
  before: 24.519
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT fund any further C-stage arm as a collapse probe: the 2x2 is complete and
  the C stage is exonerated. The collapse travels with the ENCODER CHECKPOINT (v17:
  old enc on new data +0.352 corr; v18: new enc on old data still 0.194). C-stage
  variants can no longer produce information about this problem.'
- 'Do NOT trust linear-probe R2 or soft-token norms as encoder health checks for CLM
  trainability: v11full passes both (R2 0.759, norm 50.59) and carries the collapse.
  The discriminating metrics are effective rank (12.21 healthy vs 8.71 carrier, -29%)
  and inter-sample cosine (p50 0.499 vs 0.579, p95 0.974 vs 0.994) -- measured logs/token_geometry_probe.json.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL, decisively: supabase hr_raw corr 0.194 (bar 0.60), top1 0.540,
    distinct 8. The new v11full encoder COLLAPSES EVEN ON THE OLD HEALTHY BUNDLE --
    the same tensor jsonfit_s42 trained to corr 0.809 with an old encoder. Combined
    with v17 (+0.352 corr from swapping to the old encoder on new data), the double
    dissociation is complete: the collapse travels WITH THE ENCODER CHECKPOINT, not
    with the C training data.'
  gate_id: g1-new-encoder-healthy-on-old-bundle
  passed: false
- evidence: 'FAIL: MAE 7.847 vs trivial 3.954, skill -3.893. hr_peak 20.530, skill
    -3.120.'
  gate_id: g2-beats-trivial
  passed: false
- evidence: 'Advisory FAIL: dense-128 MAE 30.255 (worst dense of the series), corr
    0.193, parse_ok 108/128.'
  gate_id: g3-dense-advisory
  passed: false
git_ref: ''
hypothesis_id: complement-cell-of-the-v17-2x2-the-new-v11full-838f07
id: exp-v18-new-encoder-old-bundle-20260830
interpretation:
  at: '2026-08-30T18:22:01+00:00'
  failure_mode: claim_false
  lesson: 'The ''healthy on old data'' claim is refuted and that CLOSES the 2x2 in
    the most informative way: the new v11full encoder collapses even on the exact
    tensor an old encoder trained healthily on (0.194 vs jsonfit''s 0.809). With v17''s
    +0.352 from the reverse swap, the double dissociation convicts the ENCODER CHECKPOINT
    as the carrier in both directions and fully exonerates the C-stage data path.
    The mechanism has a measured signature: token-cloud effective rank 8.71 (carrier)
    vs 12.21 (healthy), inter-day cosine p50 0.579 vs 0.499 at pinned norms -- reduced
    day distinguishability that linear R2 probes cannot see. The problem moves one
    level up: locate WHERE the rank collapse enters (Phase A InfoNCE vs Phase B triplet,
    and why only on post-[13518] corpora). Free next probe: run the geometry probe
    on baseline_sync/phase_b ([13518]-trained) and on v11full/phase_a (pre-Phase-B)
    to bracket the phase and the corpus era in one CPU pass.'
  transfers_to:
  - complement-cell-of-the-v17-2x2-the-new-v11full-838f07
  - the-collapse-travels-with-the-new-corpus-66b5f8
key_numbers:
  dense_mae: 30.255
  eff_rank_new: 8.714
  eff_rank_old: 12.207
  inter_cos_p50_new: 0.579
  inter_cos_p50_old: 0.499
  supa_corr: 0.194
  supa_mae: 7.847
  supa_skill: -3.893
  supa_top1: 0.54
observed_effects:
- delta: -2.211
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: -0.037
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: 5.736
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v17-old-encoder-new-corpus-20260830
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[complement-cell-of-the-v17-2x2-the-new-v11full-838f07]].
Branches from [[exp-v17-old-encoder-new-corpus-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 10.058 → after 7.847  (Δ -2.211, down)
- [[eiv_gen_peak_corr]]: before 0.231 → after 0.194  (Δ -0.037, down ✗ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 24.519 → after 30.255  (Δ +5.736, up)

## Gates
- `g1-new-encoder-healthy-on-old-bundle`: **FAIL** — FAIL, decisively: supabase hr_raw corr 0.194 (bar 0.60), top1 0.540, distinct 8. The new v11full encoder COLLAPSES EVEN ON THE OLD HEALTHY BUNDLE -- the same tensor jsonfit_s42 trained to corr 0.809 with an old encoder. Combined with v17 (+0.352 corr from swapping to the old encoder on new data), the double dissociation is complete: the collapse travels WITH THE ENCODER CHECKPOINT, not with the C training data.
- `g2-beats-trivial`: **FAIL** — FAIL: MAE 7.847 vs trivial 3.954, skill -3.893. hr_peak 20.530, skill -3.120.
- `g3-dense-advisory`: **FAIL** — Advisory FAIL: dense-128 MAE 30.255 (worst dense of the series), corr 0.193, parse_ok 108/128.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The 'healthy on old data' claim is refuted and that CLOSES the 2x2 in the most informative way: the new v11full encoder collapses even on the exact tensor an old encoder trained healthily on (0.194 vs jsonfit's 0.809). With v17's +0.352 from the reverse swap, the double dissociation convicts the ENCODER CHECKPOINT as the carrier in both directions and fully exonerates the C-stage data path. The mechanism has a measured signature: token-cloud effective rank 8.71 (carrier) vs 12.21 (healthy), inter-day cosine p50 0.579 vs 0.499 at pinned norms -- reduced day distinguishability that linear R2 probes cannot see. The problem moves one level up: locate WHERE the rank collapse enters (Phase A InfoNCE vs Phase B triplet, and why only on post-[13518] corpora). Free next probe: run the geometry probe on baseline_sync/phase_b ([13518]-trained) and on v11full/phase_a (pre-Phase-B) to bracket the phase and the corpus era in one CPU pass.

Transfers to: [[complement-cell-of-the-v17-2x2-the-new-v11full-838f07]], [[the-collapse-travels-with-the-new-corpus-66b5f8]]

## Key numbers
- `dense_mae`: 30.255
- `eff_rank_new`: 8.714
- `eff_rank_old`: 12.207
- `inter_cos_p50_new`: 0.579
- `inter_cos_p50_old`: 0.499
- `supa_corr`: 0.194
- `supa_mae`: 7.847
- `supa_skill`: -3.893
- `supa_top1`: 0.54

## Artifacts
- `outputs/adapters/v18oldbundle_c`
- `logs/v18_driver.log`
- `logs/v18_phase_c.log`
- `logs/emission_v18oldbundle_supa_summary.json`
- `logs/emission_v18oldbundle_dense_summary.json`
- `scratch/run_v18_oldbundle.sh`
- `logs/token_geometry_probe.json`
- `scratch/_token_geometry_probe.py`

## Do not retry
- Do NOT fund any further C-stage arm as a collapse probe: the 2x2 is complete and the C stage is exonerated. The collapse travels with the ENCODER CHECKPOINT (v17: old enc on new data +0.352 corr; v18: new enc on old data still 0.194). C-stage variants can no longer produce information about this problem.
- Do NOT trust linear-probe R2 or soft-token norms as encoder health checks for CLM trainability: v11full passes both (R2 0.759, norm 50.59) and carries the collapse. The discriminating metrics are effective rank (12.21 healthy vs 8.71 carrier, -29%) and inter-sample cosine (p50 0.499 vs 0.579, p95 0.974 vs 0.994) -- measured logs/token_geometry_probe.json.

## Notes
DOUBLE DISSOCIATION COMPLETE. 2x2 readout: old-enc x old-data healthy (recorded, 0.74-0.81) | old-enc x new-data NEAR-healthy (v17: 0.583, constant gone) | new-enc x new-data collapse (v15/v16) | new-enc x old-data COLLAPSE (this arm: 0.194). The collapse travels with the encoder checkpoint in BOTH directions. Mechanism measured (token_geometry_probe, same 300 eval rows, same production path): the v11full encoder's token cloud has effective rank 8.71 vs ab_ctrl's 12.21 (-29%) and inter-day cosine p50 0.579 vs 0.499 (p95 0.994 vs 0.974) at identical norms (50.59/50.61, RMSNorm-pinned) -- days are less DISTINGUISHABLE, so prefix-reading buys less loss than constant-emission, while linear HR probes (R2 0.76) stay blind to it because extractability is not distinguishability. ROOT CAUSE now one level up: WHAT in A/B training on post-[13518] corpora produces the rank collapse. Suspects for the next arm: InfoNCE pool composition after rebuild (short lab cohorts with near-empty prefixes as contrastive positives/negatives), the Phase-B triplet stage, or the 2026-08-24 refresh's data content itself. Cheap first probe: measure eff_rank/cosine on baseline_sync (trained on [13518]) and on an A-only checkpoint of v11 (before Phase B) to localise WHICH PHASE collapses the rank.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
