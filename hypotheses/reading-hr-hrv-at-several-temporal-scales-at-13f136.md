---
ambition_class: mechanism
created_at: '2026-08-29T21:35:42+00:00'
former_ids: []
gap_closure_estimate: 0.15
gates:
- blocking: true
  description: 'g1 BLOCKING (existence): the 4-scale concat''s over_null must EXCEED
    the best single scale (raw_shallow W120 = +0.0735) by >= +0.02, i.e. reach >=
    +0.0935. MDE: the run is powered to ~+0.013 (null_std ~0.010-0.015 at these n);
    if the gate fails and |delta| < 0.013 this is a POWERED NULL against multi-scale
    fusion, not a near-miss.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g1-multiscale-beats-best-single-scale
  mde: 0.013
- blocking: true
  description: 'g2 BLOCKING (the control that decides it): a DIMENSION-MATCHED control
    that tiles the single best scale (W120) four times to the same 60-feature width
    must NOT reach the concat''s over_null. If tiling matches the concat, the effect
    is estimator capacity/feature count, not multi-scale information, and g1 is void
    regardless of its value.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g2-dimension-matched-tiling-control
  mde: 0.013
- blocking: false
  description: 'g3 (mechanism, non-blocking): scale-ablation. Dropping W30 from the
    concat should cost more than dropping W60 if the short scale carries the complementary
    information exp-049''s monotone-decreasing trained-head response implies. Diagnostic
    only; does not gate funding.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g3-scale-ablation-attribution
  mde: 0.013
id: reading-hr-hrv-at-several-temporal-scales-at-13f136
interventions: []
override_reason: ''
paper_refs:
- arXiv:2608.09088
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: 'Primary. Best single scale is raw_shallow W120 at +0.0735 (z+7.14,
    n=2272). I predict the 4-scale concat lands +0.02 to +0.04 above it, i.e. roughly
    +0.09 to +0.11 over null. Stated honestly: my calibration on this diagnostic is
    2/7 (0.286) and 0.411 overall, so this prediction deserves little weight on its
    own -- which is exactly why the dimension-matched control below is blocking.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- matched-baseline
- optihealth-eiv
- data-contract-violation
- subject-heldout
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- state-supervision
- pooling
- epoch-resolution
- wearables
- control
- baseline
---

## Statement
Reading HR/HRV at SEVERAL temporal scales at once carries more momentary-state information than reading it at any single scale, on the same rows and the same labels. Concretely: a 4-scale concatenation (W=30/60/120/240) of the recorded raw_shallow feature set beats the best single scale (W120, over_null +0.0735) by >= +0.02 AUC-over-null, AND that gain is NOT reproduced by a dimension-matched control that tiles the single best scale to the same feature width.

## Rationale
THE VAULT ALREADY NAMED THIS AND NEVER RAN IT. exp-049's own interpretation says: 'raw_shallow's W-response is U-shaped (best at W120) while the trained head's is monotone-decreasing -- they read DIFFERENT temporal structure, suggesting a shallow+head ensemble as a cheap follow-up.' That is a multi-scale-fusion proposal written by this project's own record, left unqueued.

MEASURED, from logs/wsweep_raw_shallow.json (the g4 honesty gate of exp-049): raw_shallow over_null by window is W30 +0.0666 (z+5.02) / W60 +0.0446 (z+4.39) / W120 +0.0735 (z+7.14) / W240 +0.0669 (z+4.35). NON-MONOTONE with a dip at W60 and a recovery at W120. The trained head over the same rows is monotone-decreasing and INVERTS by W240 (+0.0218 / +0.015 / +0.008 / -0.009). Two readouts over one signal disagree in SHAPE across scales -- which is the precondition a fusion argument needs, and it is already on the record rather than assumed.

WHY THIS IS THE RIGHT STAGE-0 FOR THE WHOLE MULTI-SCALE FAMILY. Gkikas et al. (arXiv:2608.09088, verified) fuse EEG windows of several durations through a shared encoder with sample-specific weights, beating the full-signal baseline. Their dynamic fusion beats plain CONCATENATION only marginally. So concatenation is the honest floor of that family: if fixed-weight concat of scales cannot beat the best single scale here, then sample-specific weighting -- strictly more parameters and a GPU arm -- is not funded. One CPU run prices the entire family.

WHAT THIS IS NOT. It does not re-test window SHORTENING as a route to cross-subject state (ruled out, shortening-the-analysis-window-to-align-with-0f6d56) -- every arm here keeps the same rows and the same labels, and the manipulation is how many scales are read at once, not which single window replaces the day. It does not re-test the patch grid (that-the-encoder-s-23-token-patch-grid-is-under-bc9aef ruled out finer grids at fixed 24h on RAW PEAK CONTENT, a different target and a different stage).

## Plan
CPU-ONLY STAGE 0, no GPU, no training, ~15-30 min. Reuse scratch/wsweep_raw_shallow.py's exact harness so the comparison is anchored to a recorded number: same clean pool (nurse_stress EXCLUDED per ro that-the-full-corpus-12-547-window-flow-vs-d55a19), same flow-vs-stress pair, same subject-grouped GroupKFold(5) OOF, same RF-300, same 24 within-window permutation nulls, same SEED.

ARMS, all on IDENTICAL rows (a window is admitted only if EVERY scale yields features for it, so the four arms are paired):
  A single_W30 / single_W60 / single_W120 / single_W240   (15 features each; re-derives the anchor)
  B concat_all4                                            (60 features)
  C tile_W120_x4                                           (60 features, dimension-matched control)
  D drop-one ablations (concat minus W30, minus W60)       (45 features, g3 only)
Each arm gets its OWN 24-permutation within-window null, because nulls move with the readout and the feature count (registered rule, eiv_momentary_state_auc_over_null 2026-08-18 amendment).

WRITE logs/w11_multiscale_fusion.json + scratch/w11_multiscale_fusion.py. If g1 or g2 fails, record the rule-out and DO NOT propose a dynamic-fusion variant.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g1-multiscale-beats-best-single-scale` (blocking): g1 BLOCKING (existence): the 4-scale concat's over_null must EXCEED the best single scale (raw_shallow W120 = +0.0735) by >= +0.02, i.e. reach >= +0.0935. MDE: the run is powered to ~+0.013 (null_std ~0.010-0.015 at these n); if the gate fails and |delta| < 0.013 this is a POWERED NULL against multi-scale fusion, not a near-miss.
Gate `g2-dimension-matched-tiling-control` (blocking): g2 BLOCKING (the control that decides it): a DIMENSION-MATCHED control that tiles the single best scale (W120) four times to the same 60-feature width must NOT reach the concat's over_null. If tiling matches the concat, the effect is estimator capacity/feature count, not multi-scale information, and g1 is void regardless of its value.
Gate `g3-scale-ablation-attribution` (advisory): g3 (mechanism, non-blocking): scale-ablation. Dropping W30 from the concat should cost more than dropping W60 if the short scale carries the complementary information exp-049's monotone-decreasing trained-head response implies. Diagnostic only; does not gate funding.
Cites [[arXiv:2608.09088]].

#state-supervision #pooling #epoch-resolution #wearables #control #baseline #matched-baseline #optihealth-eiv #data-contract-violation #subject-heldout
