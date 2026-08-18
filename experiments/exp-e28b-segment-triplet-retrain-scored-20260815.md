---
artifacts: []
closed: true
config_note: "Runner: `eiv train phase-b` on both profiles, then scratch/e28_score.py
  -> logs/e28_score.json. RTX 5070. Corpus git_sha e849eb7, encoder init outputs/checkpoints/12ch_v3_wl/phase_a,
  seed 42.\n\nUnblocked by the 4-bit precompute fix (optiHealth-EiV 877e709) after
  exp-e28-segment-triplet-retrain-20260815 crashed on OOM. BOTH arms use precompute_text_in_4bit,
  so that setting is not a variable between them.\n\nARMS (single variable, verified
  by loading and diffing both profiles — only segment_triplets and segment_minutes
  differ):\n  CONTROL      phase_b_12ch_winctrl_s42  window mining, batch 8 / accum
  6\n  INTERVENTION phase_b_12ch_segtrip_s42  identical + segment_triplets=true, W=120\n\nBoth
  exit 0, 1000 steps, 8000 triplets, 0 skipped triplet batches.\n\nSCORING on exp-e26's
  exact rows: flow-vs-stress within-window transitions, n=3226 (vs E26's 3248; the
  epoch>=120 full-history rule drops 22), 87 subjects, 660 windows, pos 0.481, GroupKFold
  by subject, LOCAL avgmax read over tokens covering [epoch-120, epoch). Each arm
  scored against its OWN 24-permutation within-window shuffle null. hr_mean guard
  on 2972 windows / 186 subjects."
crash_reason: ''
created_at: '2026-08-15T09:34:59+00:00'
diagnostics_measured:
- after: 0.5325
  before: 0.5237
  diagnostic_id: eiv_affect_multichannel_increment
- after: 0.9399
  before: 0.9398
  diagnostic_id: eiv_heldout_hr_r2
do_not_retry:
- 'Reading momentary STATE off Phase-B encoder tokens on this corpus, under any pooling/locality
  variant, without first showing the readout clears its own within-window shuffle
  null. Measured three times now: E27 frozen encoder 0.5876 real vs 0.5830 shuffled;
  E28b control 0.5237 vs null mean 0.5548 (z -0.98); E28b segment-mined 0.5325 vs
  null mean 0.5482 (z -0.48). Encoder tokens carry NO state signal on this corpus
  — the apparent numbers are high-dimensional readout artefacts.'
- Segment-level / hard-negative triplet mining at W=120 as a route to state in Phase
  B, at this scale (1000 steps, seed 42, 12ch_v3_wl init). The mining demonstrably
  worked (triplet loss 2.6x, margin pinned at ceiling, 0.41 hard-negative rate) and
  produced +0.0089, with both arms below their own shuffle nulls. Do not re-run this
  exact arm hoping for a different draw; only a materially different objective (supervised
  state head) or materially more state supply would change the answer.
external_run: logs/e28_score.json + logs/e28_segtrip_report.json + logs/e28_winctrl_report.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_hard_negatives_actually_mined
  passed: true
- evidence: ''
  gate_id: g2_state_readout_beats_control
  passed: false
- evidence: ''
  gate_id: g3_gain_survives_shuffle_null
  passed: false
- evidence: ''
  gate_id: g4_hr_axis_not_damaged
  passed: true
- evidence: ''
  gate_id: g5_triplet_engages
  passed: true
git_ref: ''
hypothesis_id: hyp-e28-segment-triplet-retrain-20260815
id: exp-e28b-segment-triplet-retrain-scored-20260815
key_numbers:
  control_hr_r2: 0.9398
  control_null_mean: 0.5548
  control_null_std: 0.0319
  control_state_auc: 0.5237
  control_z: -0.98
  e26_shallow_auc: 0.6329
  e26_shallow_null_mean: 0.5492
  e26_shallow_z: 3.41
  gap_hr_r2: 0.0
  gap_state_auc: 0.0089
  interv_easy_triplet_ratio: 0.625
  interv_final_margin: 0.8
  interv_hr_r2: 0.9399
  interv_null_mean: 0.5482
  interv_null_std: 0.0325
  interv_state_auc: 0.5325
  interv_triplet_loss: 0.4776
  interv_z: -0.48
  n: 3226.0
  n_subjects: 87.0
  n_windows: 660.0
observed_effects:
- delta: 0.0088
  diagnostic_id: eiv_affect_multichannel_increment
  direction: up
- delta: 0.0001
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
parent_experiment_id: exp-e28-segment-triplet-retrain-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: true
provenance: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[hyp-e28-segment-triplet-retrain-20260815]].
Branches from [[exp-e28-segment-triplet-retrain-20260815]].
External run: logs/e28_score.json + logs/e28_segtrip_report.json + logs/e28_winctrl_report.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.5237 → after 0.5325  (Δ +0.0088, up ✓ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.9398 → after 0.9399  (Δ +0.0001, up)

## Gates
- `g1_hard_negatives_actually_mined`: PASS
- `g2_state_readout_beats_control`: **FAIL**
- `g3_gain_survives_shuffle_null`: **FAIL**
- `g4_hr_axis_not_damaged`: PASS
- `g5_triplet_engages`: PASS

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Key numbers
- `control_hr_r2`: 0.9398
- `control_null_mean`: 0.5548
- `control_null_std`: 0.0319
- `control_state_auc`: 0.5237
- `control_z`: -0.98
- `e26_shallow_auc`: 0.6329
- `e26_shallow_null_mean`: 0.5492
- `e26_shallow_z`: 3.41
- `gap_hr_r2`: 0
- `gap_state_auc`: 0.0089
- `interv_easy_triplet_ratio`: 0.625
- `interv_final_margin`: 0.8
- `interv_hr_r2`: 0.9399
- `interv_null_mean`: 0.5482
- `interv_null_std`: 0.0325
- `interv_state_auc`: 0.5325
- `interv_triplet_loss`: 0.4776
- `interv_z`: -0.48
- `n`: 3226
- `n_subjects`: 87
- `n_windows`: 660

## Do not retry
- Reading momentary STATE off Phase-B encoder tokens on this corpus, under any pooling/locality variant, without first showing the readout clears its own within-window shuffle null. Measured three times now: E27 frozen encoder 0.5876 real vs 0.5830 shuffled; E28b control 0.5237 vs null mean 0.5548 (z -0.98); E28b segment-mined 0.5325 vs null mean 0.5482 (z -0.48). Encoder tokens carry NO state signal on this corpus — the apparent numbers are high-dimensional readout artefacts.
- Segment-level / hard-negative triplet mining at W=120 as a route to state in Phase B, at this scale (1000 steps, seed 42, 12ch_v3_wl init). The mining demonstrably worked (triplet loss 2.6x, margin pinned at ceiling, 0.41 hard-negative rate) and produced +0.0089, with both arms below their own shuffle nulls. Do not re-run this exact arm hoping for a different draw; only a materially different objective (supervised state head) or materially more state supply would change the answer.

## Notes
REFUTED, CLEANLY, AND THE SHUFFLE NULL IS WHY IT IS CLEAN.

The headline is not that the gain was small (+0.0089 vs a +0.05 bar). It is that BOTH ARMS SCORE BELOW THEIR OWN WITHIN-WINDOW SHUFFLE NULL:
  control      real 0.5237  null 0.5548 ± 0.0319  z -0.98  perm p 0.7917
  intervention real 0.5325  null 0.5482 ± 0.0325  z -0.48  perm p 0.5833
Permuted labels fit BETTER than real ones on identical features. There is no state signal in the encoder tokens for the intervention to improve, so the +0.0089 is a difference between two noise draws and must not be reported as a small positive.

THE INTERVENTION WAS NOT A NO-OP — THAT IS WHAT MAKES THIS INFORMATIVE. Training counters show the objective genuinely changed: triplet loss 0.4776 vs 0.1831 (2.6x), easy-triplet ratio 0.625 vs 0.375, dynamic margin driven to its 0.80 ceiling vs 0.12, 0 skipped batches over 8000 triplets, 49449 segments / 1004 transition windows logged inside the real entrypoint. Same-window hard negatives are substantially harder to satisfy, exactly as designed. The mining worked and the representation still learned nothing about state.

AND THE SIGNAL IS DEFINITELY THERE IN THE DATA. On the SAME 3226 rows, shallow percentile features read 0.6329 against a null mean of 0.5492 — +3.41 SD, p=0.0000 (exp-e26). So this is not 'no signal exists'; it is 'the encoder does not carry it', before or after the retrain.

DO-NO-HARM PASSES: hr_mean R2 0.9398 -> 0.9399, delta +0.0000. The intervention is harmless, which rules out 'the retrain broke the encoder' as an explanation.

MY G5 PREDICTION WAS WRONG AND THE REASON IS MECHANICAL. I predicted hard negatives would LOWER the easy-triplet ratio; it rose (0.625 vs 0.375). The dynamic-margin scheduler raises the margin when the easy ratio is high, so a harder regime pushes the margin up, which re-classifies more triplets as easy relative to the enlarged margin. Both signals agree that the negatives got harder; my stated direction was simply the wrong reading of the instrument.

WHAT THIS SETTLES, AND WHAT IT DOES NOT.
SETTLED: the InfoNCE-plus-auxiliary-triplet objective does not put momentary state into the encoder on this corpus, and the triplet miner was NOT the binding defect. The E25/E26 supply argument was necessary but not sufficient — fixing supply does not create representation.
NOT SETTLED: whether a SUPERVISED state head (cross-entropy on the momentary label, not a 0.05-weighted auxiliary margin) could learn it. Every arm tried so far treats state as an auxiliary regulariser at weight 0.05 while InfoNCE-on-captions dominates the gradient; nothing has yet optimised state directly. That is the one untried direction, and E26 shows the target is learnable from these very rows by a shallow model.

PRODUCT CONSEQUENCE, UNCHANGED: the shipped detector (src/annotation/momentary_state.py, AUC 0.6329) remains the right artefact for the state slot. It reads state from percentile features, which this experiment reconfirms is the only route on this corpus that clears its own null.

#contrastive #collapse #epoch-resolution #baseline #control #dataset-whitelist #optihealth-eiv #matched-baseline #subject-heldout
