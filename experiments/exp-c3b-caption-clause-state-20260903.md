---
artifacts:
- logs/c3_caption_deltas.json
- logs/g5_enc_v36labels.json
- logs/g5_enc_v28affect01.json
- logs/c3_driver.log
- scratch/run_c3.sh
closed: true
config_note: 'scratch/run_c3.sh: scratch/g5_pool_ceiling.py --only enc on outputs/checkpoints/v36labels/phase_a
  and outputs/checkpoints/v28affect01/phase_a, CPU only, CLEAN pool (2,136 rows),
  identical folds and permutation seeds, each checkpoint''s random twin rebuilt in
  the same invocation. The two Phase A configs were diffed before launch and differ
  ONLY in caption_style (hr_dispersion_labels vs hr_only) plus detached_probes False/None;
  patch_len 120, stride 60, d_model 192, input_dim 24, state_patch_weight 0.0 on both.
  Because the geometry is identical and the twin is seeded with torch.manual_seed(0),
  BOTH checkpoints draw the SAME twin (+0.0430 / +0.0415 / +0.0555 / +0.0403), so
  the caption effect reduces exactly to the difference of the trained cells.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.27
crash_reason: ''
created_at: '2026-09-02T22:54:06+00:00'
diagnostics_measured:
- after: 0.0182
  before: 0.0022
  diagnostic_id: eiv_state_auc_trained_minus_random
- after: 0.0585
  before: 0.0425
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- Do not compare v36labels against v41rebased to test a CAPTION claim. They carry
  the SAME caption_style (hr_dispersion_labels) and differ in geometry (PL60/ST30
  vs PL120/ST60) and state supervision (state_patch_weight 0.5 vs 0.0). The original
  C-3 hypothesis made exactly this error and was superseded before it ran; v28affect01
  (hr_only, otherwise identical to v36labels) is the control.
- Do not read this result as 'the caption adds physiology'. The provenance-shortcut
  gate was not run, and v36labels' clause is a MEASURED corpus indicator (bal-acc
  lift +0.294 valence / +0.379 stress). It licenses 'the clause changes the representation'
  only.
- Do not fund a minute-stamped semantic-caption Phase A arm on the strength of these
  numbers alone. Even the best captioned encoder reads +0.0585 against raw_multiscale/rf's
  +0.1515 on the same rows, and exp-c1 showed clean-pool encoder gains do not survive
  the subject split.
external_run: logs/c3_caption_deltas.json
former_ids: []
gate_results:
- evidence: 'The gate demanded >= +0.02 under MULTI-SPAN GB and positive under multi-span
    ridge. Multi-span gb reads +0.0160 -- short by 0.0040, which is itself inside
    the +/-0.0141 band, so this is a near-miss and not a clean null. Multi-span ridge
    reads +0.0522, comfortably positive. The other two cells: single/ridge +0.0466,
    single/gb +0.0239. All four effects point the same way and three of four clear
    the band, but the gate was written on the cell that misses, and it is recorded
    as failed rather than re-read on a cell chosen after the fact.'
  gate_id: g1
  passed: false
- evidence: 'Both checkpoints are PL120/ST60/d_model 192/input_dim 24, and the twin
    is rebuilt in-invocation under torch.manual_seed(0), so BOTH draw the identical
    twin: +0.0430 (single/ridge), +0.0415 (single/gb), +0.0555 (multi/ridge), +0.0403
    (multi/gb). The caption effect therefore reduces exactly to the difference of
    the trained cells -- v36labels 0.0592/0.0622/0.0574/0.0585 against v28affect01
    0.0127/0.0383/0.0051/0.0425 -- with no twin-mismatch term to explain it away.'
  gate_id: g2
  passed: true
- evidence: 'NOT EVALUATED, recorded as unmet rather than skipped. The dataset-provenance
    shortcut probe was not run on either encoder''s tokens, so the confound this gate
    exists to bound is unmeasured: v36labels'' clause is a MEASURED provenance shortcut
    (bal-acc lift +0.294 valence / +0.379 stress, from the 2026-09-01 clause-leakage
    audit), and a caption that lets the encoder infer which corpus a window came from
    can raise a flow-vs-stress readout on a pool whose positives and negatives are
    unevenly distributed across corpora. The positive direction here must therefore
    be read as ''the clause changes the representation'', not yet as ''the clause
    adds physiology''.'
  gate_id: g3
  passed: false
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: c3b-caption-label-clauses-are-what-put-state-in-the-encoder
id: exp-c3b-caption-clause-state-20260903
interpretation:
  at: '2026-09-02T22:55:04+00:00'
  failure_mode: recipe_conditional
  lesson: 'THE CONTROL ARM IS THE FINDING. v28affect01, the incumbent recipe with
    an `hr_only` caption, reads BELOW its own random-init twin on three of four cells
    (-0.0303 single/ridge, -0.0032 single/gb, -0.0504 multi/ridge). Training on HR-statistical
    captions does not merely fail to add momentary state -- it DESTROYS state the
    untrained architecture already carried. Adding window-level label clauses (`valence
    3.3`, `stress 2.0` on 35.1% of roster windows) recovers the encoder to roughly
    twin level (+0.0018 to +0.0207) and no further, so the caption effect is +0.0160
    to +0.0522 depending on cell and misses its gated bar of +0.02 on multi/gb by
    0.0040. Read the mechanism as: the caption decides how much state Phase A ERASES,
    not how much it adds, and the best captioned encoder measured here is still at
    the level of random weights and far under raw_multiscale/rf''s +0.1515 on identical
    rows. TWO THINGS THIS DOES NOT LICENSE. (1) It is not evidence that the clause
    adds PHYSIOLOGY: the provenance gate was not run and the clause is a measured
    corpus indicator (bal-acc lift +0.294 valence / +0.379 stress), so an encoder
    that learned "which corpus is this" would move these cells the same way on a pool
    whose classes are unevenly spread across corpora. (2) It is not a green light
    for a minute-stamped semantic-caption Phase A arm. SensorLM''s Table 4 jump (0.49-0.51
    -> 0.71-0.84) is between caption FAMILIES and a window-level number is the weakest
    possible proxy for it; combined with exp-c1 (clean-pool encoder gains do not survive
    the subject split, and the pooled state metric is a WESAD/LifeSnaps mixture),
    any such arm must be gated on HELD-OUT per-cohort numbers before it is funded.
    METHOD NOTE WORTH KEEPING: because both checkpoints share geometry and the twin
    is seeded, they drew the IDENTICAL twin, so the caption effect reduced exactly
    to the difference of the trained cells with no twin-mismatch term -- that is the
    cleanest form this comparison can take, and it is only available when the two
    arms differ in one non-geometric key.'
  transfers_to:
  - e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
  - h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale
key_numbers:
  band: 0.0141
  caption_effect_multi_gb: 0.016
  caption_effect_multi_ridge: 0.0522
  caption_effect_single_gb: 0.0239
  caption_effect_single_ridge: 0.0466
  raw_multiscale_rf_same_rows: 0.1515
  shared_twin_multi_gb: 0.0403
  v28affect01_multi_gb_over_null: 0.0425
  v28affect01_trained_minus_twin_multi_gb: 0.0022
  v36labels_multi_gb_over_null: 0.0585
  v36labels_trained_minus_twin_multi_gb: 0.0182
observed_effects:
- delta: 0.016
  diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
- delta: 0.016
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
parent_experiment_id: exp-c1-heldout-pool-ceiling-20260903
prediction_match:
  overall: true
  per_diagnostic:
    eiv_momentary_state_auc_over_null: true
    eiv_state_auc_trained_minus_random: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[c3b-caption-label-clauses-are-what-put-state-in-the-encoder]].
Branches from [[exp-c1-heldout-pool-ceiling-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/c3_caption_deltas.json

## Measurements
- [[eiv_state_auc_trained_minus_random]]: before 0.0022 → after 0.0182  (Δ +0.016, up ✓ predicted up)
- [[eiv_momentary_state_auc_over_null]]: before 0.0425 → after 0.0585  (Δ +0.016, up ✓ predicted up)

## Gates
- `g1`: **FAIL** — The gate demanded >= +0.02 under MULTI-SPAN GB and positive under multi-span ridge. Multi-span gb reads +0.0160 -- short by 0.0040, which is itself inside the +/-0.0141 band, so this is a near-miss and not a clean null. Multi-span ridge reads +0.0522, comfortably positive. The other two cells: single/ridge +0.0466, single/gb +0.0239. All four effects point the same way and three of four clear the band, but the gate was written on the cell that misses, and it is recorded as failed rather than re-read on a cell chosen after the fact.
- `g2`: PASS — Both checkpoints are PL120/ST60/d_model 192/input_dim 24, and the twin is rebuilt in-invocation under torch.manual_seed(0), so BOTH draw the identical twin: +0.0430 (single/ridge), +0.0415 (single/gb), +0.0555 (multi/ridge), +0.0403 (multi/gb). The caption effect therefore reduces exactly to the difference of the trained cells -- v36labels 0.0592/0.0622/0.0574/0.0585 against v28affect01 0.0127/0.0383/0.0051/0.0425 -- with no twin-mismatch term to explain it away.
- `g3`: **FAIL** — NOT EVALUATED, recorded as unmet rather than skipped. The dataset-provenance shortcut probe was not run on either encoder's tokens, so the confound this gate exists to bound is unmeasured: v36labels' clause is a MEASURED provenance shortcut (bal-acc lift +0.294 valence / +0.379 stress, from the 2026-09-01 clause-leakage audit), and a caption that lets the encoder infer which corpus a window came from can raise a flow-vs-stress readout on a pool whose positives and negatives are unevenly distributed across corpora. The positive direction here must therefore be read as 'the clause changes the representation', not yet as 'the clause adds physiology'.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

THE CONTROL ARM IS THE FINDING. v28affect01, the incumbent recipe with an `hr_only` caption, reads BELOW its own random-init twin on three of four cells (-0.0303 single/ridge, -0.0032 single/gb, -0.0504 multi/ridge). Training on HR-statistical captions does not merely fail to add momentary state -- it DESTROYS state the untrained architecture already carried. Adding window-level label clauses (`valence 3.3`, `stress 2.0` on 35.1% of roster windows) recovers the encoder to roughly twin level (+0.0018 to +0.0207) and no further, so the caption effect is +0.0160 to +0.0522 depending on cell and misses its gated bar of +0.02 on multi/gb by 0.0040. Read the mechanism as: the caption decides how much state Phase A ERASES, not how much it adds, and the best captioned encoder measured here is still at the level of random weights and far under raw_multiscale/rf's +0.1515 on identical rows. TWO THINGS THIS DOES NOT LICENSE. (1) It is not evidence that the clause adds PHYSIOLOGY: the provenance gate was not run and the clause is a measured corpus indicator (bal-acc lift +0.294 valence / +0.379 stress), so an encoder that learned "which corpus is this" would move these cells the same way on a pool whose classes are unevenly spread across corpora. (2) It is not a green light for a minute-stamped semantic-caption Phase A arm. SensorLM's Table 4 jump (0.49-0.51 -> 0.71-0.84) is between caption FAMILIES and a window-level number is the weakest possible proxy for it; combined with exp-c1 (clean-pool encoder gains do not survive the subject split, and the pooled state metric is a WESAD/LifeSnaps mixture), any such arm must be gated on HELD-OUT per-cohort numbers before it is funded. METHOD NOTE WORTH KEEPING: because both checkpoints share geometry and the twin is seeded, they drew the IDENTICAL twin, so the caption effect reduced exactly to the difference of the trained cells with no twin-mismatch term -- that is the cleanest form this comparison can take, and it is only available when the two arms differ in one non-geometric key.

Transfers to: [[e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling]], [[h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale]]

## Key numbers
- `band`: 0.0141
- `caption_effect_multi_gb`: 0.016
- `caption_effect_multi_ridge`: 0.0522
- `caption_effect_single_gb`: 0.0239
- `caption_effect_single_ridge`: 0.0466
- `raw_multiscale_rf_same_rows`: 0.1515
- `shared_twin_multi_gb`: 0.0403
- `v28affect01_multi_gb_over_null`: 0.0425
- `v28affect01_trained_minus_twin_multi_gb`: 0.0022
- `v36labels_multi_gb_over_null`: 0.0585
- `v36labels_trained_minus_twin_multi_gb`: 0.0182

## Artifacts
- `logs/c3_caption_deltas.json`
- `logs/g5_enc_v36labels.json`
- `logs/g5_enc_v28affect01.json`
- `logs/c3_driver.log`
- `scratch/run_c3.sh`

## Do not retry
- Do not compare v36labels against v41rebased to test a CAPTION claim. They carry the SAME caption_style (hr_dispersion_labels) and differ in geometry (PL60/ST30 vs PL120/ST60) and state supervision (state_patch_weight 0.5 vs 0.0). The original C-3 hypothesis made exactly this error and was superseded before it ran; v28affect01 (hr_only, otherwise identical to v36labels) is the control.
- Do not read this result as 'the caption adds physiology'. The provenance-shortcut gate was not run, and v36labels' clause is a MEASURED corpus indicator (bal-acc lift +0.294 valence / +0.379 stress). It licenses 'the clause changes the representation' only.
- Do not fund a minute-stamped semantic-caption Phase A arm on the strength of these numbers alone. Even the best captioned encoder reads +0.0585 against raw_multiscale/rf's +0.1515 on the same rows, and exp-c1 showed clean-pool encoder gains do not survive the subject split.

## Notes
THE CAPTION MOVES THE ENCODER, BY LESS THAN THE GATE ASKED FOR. One variable (caption_style hr_dispersion_labels vs hr_only) on two otherwise-identical PL120 checkpoints, clean pool, both families, shared twin:

  cell            v36labels   v28affect01   effect
  single/ridge     +0.0163      -0.0303     +0.0466
  single/gb        +0.0207      -0.0032     +0.0239
  multi/ridge      +0.0018      -0.0504     +0.0522
  multi/gb         +0.0182      +0.0022     +0.0160   <- the gated cell, bar +0.02

All four effects are positive and three of four clear the +/-0.0141 band, so the DIRECTION is consistent across estimator families and spans. The gated cell misses by 0.0040. The second predicted effect misses by more: v36labels' encoder reads +0.0585 over null against a committed >= +0.09.

THE MOST INFORMATIVE NUMBER IS THE CONTROL, NOT THE TREATMENT. v28affect01 -- the incumbent, `hr_only` caption -- reads BELOW its own random twin on three of four cells (-0.0303, -0.0032, -0.0504). Training on HR-only captions does not merely fail to add momentary state; it removes what the untrained architecture had. The label clause recovers roughly to twin level (+0.0018 to +0.0207) rather than climbing above it. So the honest reading of "the caption is the lever" is: the caption decides how much state the encoder DESTROYS, and the best captioned encoder here is still barely at the level of random weights.

WHAT THIS SAYS ABOUT SensorLM's TABLE 4, which motivated the arm. Its jump (0.49-0.51 statistical/structural -> 0.71-0.84 semantic) is between caption FAMILIES, and a window-level 'valence 3.3' clause is not the minute-stamped semantic caption it describes. This arm tests the weakest possible version of the claim and finds a small positive effect, which is consistent with the stronger version being worth testing -- but see the do-not-retry: not on this evidence alone, and not before the provenance confound is bounded.

#optihealth #sensor-text #control #state-supervision #readout #baseline #optihealth-eiv #representation-learning #matched-baseline
