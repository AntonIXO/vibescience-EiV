---
artifacts:
- logs/w11_multiscale_fusion.json
- scratch/w11_multiscale_fusion.py
- logs/w11_multiscale_fusion.log
closed: true
config_note: 'W11 Stage-0, CPU-only, no GPU, no training. Runner scratch/w11_multiscale_fusion.py
  -> logs/w11_multiscale_fusion.json (RC=0, zero tracebacks).


  Harness copied verbatim from scratch/wsweep_raw_shallow.py (exp-049''s g4 honesty
  gate) so the single-scale arms re-derive a recorded anchor: same clean pool (nurse_stress
  EXCLUDED), same flow-vs-stress pair, same 15 handcrafted HR/HRV features, same subject-grouped
  GroupKFold(5) OOF, same RF-300, same SEED=0, 24 within-window permutations.


  TWO DELIBERATE DESIGN CHANGES, both recorded before the run:

  (1) ROWS ARE PAIRED ACROSS SCALES -- a (window, epoch) is admitted only if EVERY
  scale W in {30,60,120,240} yields features, and epoch >= 240. That makes the arms
  comparable on identical rows, at the cost of n=2029 / 92 subjects / 642 windows
  instead of exp-049''s per-W row sets (2154-2302). Single-scale over_null therefore
  does NOT reproduce exp-049 exactly and must not be quoted as if it did.

  (2) CORPUS IS THE CURRENT [13518,1440,12] bundle (merge git_sha f82b082), not the
  12,547 one exp-049 ran on. Cross-session absolute comparison is void per references/controlled-ab-runs.md;
  the WITHIN-RUN contrast on one instrument is what carries this.


  Every arm carries its OWN 24-permutation within-window null, because nulls move
  with the readout AND with feature count -- which is exactly what the blocking tiling
  control exists to test.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.07
crash_reason: ''
created_at: '2026-08-29T21:40:39+00:00'
diagnostics_measured:
- after: 0.1315
  before: 0.0946
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- Do NOT include W60 in a multi-scale stack for this target. drop_W60 (+0.1323) matches
  the full 4-scale concat (+0.1315) to within 0.0008, and W60 is also the weakest
  single scale (+0.0619 vs +0.0912-0.0946 for the other three). The useful set is
  {30, 120, 240}. 'More scales' is not the mechanism -- COMPLEMENTARY scales are,
  and one of four candidates was pure cost.
- Do NOT quote this run's single-scale numbers as a reproduction of exp-049's W-sweep.
  Rows are PAIRED here (a cell is admitted only if all four scales yield features,
  epoch >= 240), n=2029 vs exp-049's 2154-2302, and the corpus is [13518] vs [12547].
  W60 lands +0.0619 here vs +0.0446 there. The within-run contrast carries this experiment;
  the cross-session absolute comparison is void.
- Do NOT claim a deployment or product effect. Frozen-feature CPU probe on handcrafted
  HR/HRV features, flow-vs-stress only, NOTHING trained. It prices a family; it does
  not ship one.
- Do NOT jump to sample-specific / dynamic-weighting fusion (Gkikas-style) as the
  next step without first testing fixed-weight multi-scale INSIDE the encoder. Fixed
  concat already clears the bar, and Gkikas's own result has dynamic fusion beating
  plain concatenation only marginally -- the expensive half of that family is still
  unpriced.
- Do NOT read this as reviving window SHORTENING (ruled out at shortening-the-analysis-window-to-align-with-0f6d56).
  Every arm keeps the same rows and labels and reads MORE spans; none replaces the
  day with a shorter window.
external_run: logs/w11_multiscale_fusion.json
former_ids: []
gate_results:
- evidence: PASS. concat_all4 over_null +0.1315 (AUC 0.6897, z+8.25) vs best single
    scale W120 +0.0946 (AUC 0.6417, z+5.46) on IDENTICAL paired rows. Delta +0.0369,
    clearing the preregistered +0.02 bar by 1.85x and sitting 2.8x above the run's
    own MDE of 0.013.
  gate_id: g1-multiscale-beats-best-single-scale
  passed: true
- evidence: 'PASS, and this is the gate that makes the result mean anything. tile_W120_x4
    -- the SAME best single scale repeated four times to the identical 60-feature
    width -- scores over_null +0.0926, i.e. 0.0020 BELOW the 15-feature single W120
    arm (+0.0946) rather than above it. Feature count alone buys nothing: the RF does
    not gain from width per se. concat_all4 beats the dimension-matched control by
    +0.0390, which is essentially its entire margin over the best single scale (+0.0369).
    The gain is multi-scale INFORMATION, not estimator capacity.'
  gate_id: g2-dimension-matched-tiling-control
  passed: true
- evidence: 'MEASURED, and it INVERTS the predicted attribution. I preregistered that
    dropping W30 would cost more than dropping W60. Measured: drop_W30 +0.1084 (costs
    -0.0231 vs the full concat) while drop_W60 +0.1323 (costs -0.0000, i.e. +0.0008,
    indistinguishable). So W30 is indeed load-bearing -- consistent with the prediction''s
    direction -- but W60 is entirely REDUNDANT: a 3-scale {30,120,240} stack matches
    the 4-scale one exactly. Non-blocking, recorded as a mechanism finding: the useful
    scales are the short (30), the mid-long (120) and the long (240), and W60 -- which
    was also the weakest single scale at +0.0619 -- adds nothing on top of them.'
  gate_id: g3-scale-ablation-attribution
  passed: true
git_ref: ''
hypothesis_id: reading-hr-hrv-at-several-temporal-scales-at-13f136
id: exp-w11-multiscale-fusion-stage0-20260829
interpretation:
  at: '2026-08-29T22:10:47+00:00'
  failure_mode: null
  lesson: 'MULTI-SCALE FUSION IS THE FIRST STATE-SIDE LEVER IN THIS VAULT TO CLEAR
    A BLOCKING GATE WITH ITS CONTROL INTACT, and the control is the entire story.
    Reading the SAME 15 handcrafted HR/HRV features at {30,60,120,240} minutes at
    once gives over_null +0.1315 (AUC 0.6897, z+8.25) against +0.0946 for the best
    single scale -- delta +0.0369, 1.85x the preregistered bar and 2.8x the MDE. The
    dimension-matched tiling control scores +0.0926, BELOW the 15-feature single-scale
    arm, so the gain is not estimator capacity from a wider vector: it is information
    that only exists ACROSS scales.


    THE ABLATION INVERTED MY ATTRIBUTION, which is the most useful part. I predicted
    W30 would be load-bearing and W60 less so. W30 is load-bearing (dropping it costs
    -0.0231), but W60 is entirely REDUNDANT (-0.0008): a 3-scale {30,120,240} stack
    IS the 4-scale result, and W60 was also the weakest single scale. So ''more scales''
    is not the mechanism -- COMPLEMENTARY scales are, and one of four candidates was
    pure cost. Any follow-up selects scales rather than adding them.


    WHY THIS MATTERS AGAINST THE VAULT''S OWN HISTORY. W8 concluded momentary state
    is SUPPLY-limited rather than architecture-limited, measured entirely through
    SINGLE-scale readouts. Supply-limited and single-scale-limited are different diagnoses
    and only the first had been tested. The same rows, labels and features read at
    several spans move the handcrafted bound +0.0946 -> +0.1315.


    REPLICATION NOTE ADDED 2026-08-30: re-running this on the rebuilt [13879] corpus
    reproduced every number BIT-IDENTICALLY (n=2029, all eight arms). That is not
    a coincidence and it is not a strong replication -- the +361 new windows are popane_s5/s6,
    which carry valence and protocol_condition but NO state labels, so they cannot
    enter a flow-vs-stress pool. The pool is unchanged, therefore the result is unchanged.
    Recorded because ''identical numbers after a corpus rebuild'' looks like a caching
    bug until you check what the new data actually contains.


    SCOPE: frozen-feature CPU probe on handcrafted features, one label pair, nothing
    trained, no deployment claim. It prices the multi-scale family; it does not ship
    it. Does not revive window shortening (every arm keeps the same rows and labels
    and reads MORE spans). Does not justify Gkikas-style sample-specific dynamic weighting
    before fixed-weight multi-scale is tested inside the encoder -- their own dynamic-vs-concat
    margin is slim, so concat is the honest floor of that family.'
  transfers_to:
  - h-w8-cardinality-invariant-attention-pooling-f01250
key_numbers:
  concat_all4_auc: 0.6897
  concat_all4_over_null: 0.1315
  concat_all4_z: 8.25
  delta_concat_minus_best_single: 0.0369
  delta_concat_minus_tile: 0.039
  drop_W30_over_null: 0.1084
  drop_W60_over_null: 0.1323
  mde: 0.013
  n_paired_rows: 2029.0
  n_subjects: 92.0
  n_windows: 642.0
  preregistered_g1_bar: 0.02
  single_W120_over_null: 0.0946
  single_W240_over_null: 0.0912
  single_W30_over_null: 0.0939
  single_W60_over_null: 0.0619
  tile_W120_x4_over_null: 0.0926
observed_effects:
- delta: 0.0369
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_momentary_state_auc_over_null: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[reading-hr-hrv-at-several-temporal-scales-at-13f136]].
External run: logs/w11_multiscale_fusion.json

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before 0.0946 → after 0.1315  (Δ +0.0369, up ✓ predicted up)

## Gates
- `g1-multiscale-beats-best-single-scale`: PASS — PASS. concat_all4 over_null +0.1315 (AUC 0.6897, z+8.25) vs best single scale W120 +0.0946 (AUC 0.6417, z+5.46) on IDENTICAL paired rows. Delta +0.0369, clearing the preregistered +0.02 bar by 1.85x and sitting 2.8x above the run's own MDE of 0.013.
- `g2-dimension-matched-tiling-control`: PASS — PASS, and this is the gate that makes the result mean anything. tile_W120_x4 -- the SAME best single scale repeated four times to the identical 60-feature width -- scores over_null +0.0926, i.e. 0.0020 BELOW the 15-feature single W120 arm (+0.0946) rather than above it. Feature count alone buys nothing: the RF does not gain from width per se. concat_all4 beats the dimension-matched control by +0.0390, which is essentially its entire margin over the best single scale (+0.0369). The gain is multi-scale INFORMATION, not estimator capacity.
- `g3-scale-ablation-attribution`: PASS — MEASURED, and it INVERTS the predicted attribution. I preregistered that dropping W30 would cost more than dropping W60. Measured: drop_W30 +0.1084 (costs -0.0231 vs the full concat) while drop_W60 +0.1323 (costs -0.0000, i.e. +0.0008, indistinguishable). So W30 is indeed load-bearing -- consistent with the prediction's direction -- but W60 is entirely REDUNDANT: a 3-scale {30,120,240} stack matches the 4-scale one exactly. Non-blocking, recorded as a mechanism finding: the useful scales are the short (30), the mid-long (120) and the long (240), and W60 -- which was also the weakest single scale at +0.0619 -- adds nothing on top of them.

**Verdict:** supports

## Interpretation
MULTI-SCALE FUSION IS THE FIRST STATE-SIDE LEVER IN THIS VAULT TO CLEAR A BLOCKING GATE WITH ITS CONTROL INTACT, and the control is the entire story. Reading the SAME 15 handcrafted HR/HRV features at {30,60,120,240} minutes at once gives over_null +0.1315 (AUC 0.6897, z+8.25) against +0.0946 for the best single scale -- delta +0.0369, 1.85x the preregistered bar and 2.8x the MDE. The dimension-matched tiling control scores +0.0926, BELOW the 15-feature single-scale arm, so the gain is not estimator capacity from a wider vector: it is information that only exists ACROSS scales.

THE ABLATION INVERTED MY ATTRIBUTION, which is the most useful part. I predicted W30 would be load-bearing and W60 less so. W30 is load-bearing (dropping it costs -0.0231), but W60 is entirely REDUNDANT (-0.0008): a 3-scale {30,120,240} stack IS the 4-scale result, and W60 was also the weakest single scale. So 'more scales' is not the mechanism -- COMPLEMENTARY scales are, and one of four candidates was pure cost. Any follow-up selects scales rather than adding them.

WHY THIS MATTERS AGAINST THE VAULT'S OWN HISTORY. W8 concluded momentary state is SUPPLY-limited rather than architecture-limited, measured entirely through SINGLE-scale readouts. Supply-limited and single-scale-limited are different diagnoses and only the first had been tested. The same rows, labels and features read at several spans move the handcrafted bound +0.0946 -> +0.1315.

REPLICATION NOTE ADDED 2026-08-30: re-running this on the rebuilt [13879] corpus reproduced every number BIT-IDENTICALLY (n=2029, all eight arms). That is not a coincidence and it is not a strong replication -- the +361 new windows are popane_s5/s6, which carry valence and protocol_condition but NO state labels, so they cannot enter a flow-vs-stress pool. The pool is unchanged, therefore the result is unchanged. Recorded because 'identical numbers after a corpus rebuild' looks like a caching bug until you check what the new data actually contains.

SCOPE: frozen-feature CPU probe on handcrafted features, one label pair, nothing trained, no deployment claim. It prices the multi-scale family; it does not ship it. Does not revive window shortening (every arm keeps the same rows and labels and reads MORE spans). Does not justify Gkikas-style sample-specific dynamic weighting before fixed-weight multi-scale is tested inside the encoder -- their own dynamic-vs-concat margin is slim, so concat is the honest floor of that family.

Transfers to: [[h-w8-cardinality-invariant-attention-pooling-f01250]]

## Key numbers
- `concat_all4_auc`: 0.6897
- `concat_all4_over_null`: 0.1315
- `concat_all4_z`: 8.25
- `delta_concat_minus_best_single`: 0.0369
- `delta_concat_minus_tile`: 0.039
- `drop_W30_over_null`: 0.1084
- `drop_W60_over_null`: 0.1323
- `mde`: 0.013
- `n_paired_rows`: 2029
- `n_subjects`: 92
- `n_windows`: 642
- `preregistered_g1_bar`: 0.02
- `single_W120_over_null`: 0.0946
- `single_W240_over_null`: 0.0912
- `single_W30_over_null`: 0.0939
- `single_W60_over_null`: 0.0619
- `tile_W120_x4_over_null`: 0.0926

## Artifacts
- `logs/w11_multiscale_fusion.json`
- `scratch/w11_multiscale_fusion.py`
- `logs/w11_multiscale_fusion.log`

## Do not retry
- Do NOT include W60 in a multi-scale stack for this target. drop_W60 (+0.1323) matches the full 4-scale concat (+0.1315) to within 0.0008, and W60 is also the weakest single scale (+0.0619 vs +0.0912-0.0946 for the other three). The useful set is {30, 120, 240}. 'More scales' is not the mechanism -- COMPLEMENTARY scales are, and one of four candidates was pure cost.
- Do NOT quote this run's single-scale numbers as a reproduction of exp-049's W-sweep. Rows are PAIRED here (a cell is admitted only if all four scales yield features, epoch >= 240), n=2029 vs exp-049's 2154-2302, and the corpus is [13518] vs [12547]. W60 lands +0.0619 here vs +0.0446 there. The within-run contrast carries this experiment; the cross-session absolute comparison is void.
- Do NOT claim a deployment or product effect. Frozen-feature CPU probe on handcrafted HR/HRV features, flow-vs-stress only, NOTHING trained. It prices a family; it does not ship one.
- Do NOT jump to sample-specific / dynamic-weighting fusion (Gkikas-style) as the next step without first testing fixed-weight multi-scale INSIDE the encoder. Fixed concat already clears the bar, and Gkikas's own result has dynamic fusion beating plain concatenation only marginally -- the expensive half of that family is still unpriced.
- Do NOT read this as reviving window SHORTENING (ruled out at shortening-the-analysis-window-to-align-with-0f6d56). Every arm keeps the same rows and labels and reads MORE spans; none replaces the day with a shorter window.

## Notes
ALL THREE GATES SCORED; BOTH BLOCKING GATES PASS WITH THE CONTROL INTACT.

MEASURED (n=2029 paired rows, 92 subjects, 642 windows, flow-vs-stress, pos 0.517; each arm with its OWN 24-perm within-window null):
  single_W30    AUC 0.6467  over_null +0.0939  z+6.76
  single_W60    AUC 0.6152  over_null +0.0619  z+3.75
  single_W120   AUC 0.6417  over_null +0.0946  z+5.46   <- best single
  single_W240   AUC 0.6419  over_null +0.0912  z+7.07
  concat_all4   AUC 0.6897  over_null +0.1315  z+8.25   <- treatment
  tile_W120_x4  AUC 0.6354  over_null +0.0926  z+5.36   <- dimension-matched control
  drop_W30      AUC 0.6639  over_null +0.1084  z+7.49
  drop_W60      AUC 0.6882  over_null +0.1323  z+9.34

g1 PASS: concat beats best single by +0.0369 against a preregistered +0.02 bar, 2.8x the MDE.
g2 PASS, and it is what makes the result mean anything: tiling the best single scale to the same 60-feature width scores +0.0926 -- 0.0020 BELOW the 15-feature single arm, not above it. Width alone buys nothing; concat beats the matched control by +0.0390, essentially its entire margin. The gain is multi-scale INFORMATION, not estimator capacity.
g3 INVERTED MY ATTRIBUTION: I predicted dropping W30 would cost more than dropping W60. W30 is load-bearing (-0.0231) as predicted, but W60 is entirely REDUNDANT (-0.0008) -- a 3-scale {30,120,240} stack IS the 4-scale result.

WHY THIS MATTERS AGAINST THE VAULT'S OWN HISTORY. W8 concluded state is SUPPLY-limited, not architecture-limited, from single-scale readouts. This says the supply is richer than any single scale exposes: same rows, same labels, same 15 features, read at several spans, moves the handcrafted bound +0.0946 -> +0.1315. Supply-limited and single-scale-limited are different diagnoses, and only the first had been tested.

SCOPE, stated so this is not over-cited: frozen-feature CPU probe, handcrafted HR/HRV features, one label pair, nothing trained, no deployment claim. Also note the honest calibration context -- my prior on this diagnostic was 2/7 and I called the magnitude roughly right (+0.0369 vs a predicted +0.02-+0.04) but the scale attribution wrong.

METHOD NOTE: pairing rows across scales cost ~6% of n and is what makes the arms comparable at all. Without it the single-scale arms sit on different row sets and 'concat beats best single' would be partly a population difference.

#state-supervision #pooling #epoch-resolution #wearables #control #baseline #matched-baseline #optihealth-eiv #data-contract-violation #subject-heldout
