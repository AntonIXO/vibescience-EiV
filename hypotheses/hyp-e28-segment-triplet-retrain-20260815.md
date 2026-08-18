---
created_at: '2026-08-15T08:55:34+00:00'
former_ids: []
gates:
- blocking: true
  description: MINING ACTUALLY CHANGED. The intervention run must log a hard-negative
    rate >= 0.25 (measured 0.41 offline on 300 draws). This is the run's own proof
    that anchors and negatives came from the same window; a run that silently collapses
    to all-easy cross-window negatives has reverted to the control objective and its
    comparison is void regardless of downstream numbers.
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  id: g1_hard_negatives_actually_mined
  mde: 0.25
- blocking: true
  description: PRIMARY. Probe the resulting Phase-B encoder tokens on exp-e26's EXACT
    rows (flow-vs-stress transitions, W=120, GroupKFold by subject, n=3248/87 subjects/662
    windows) and compare against the CONTROL checkpoint's tokens on the identical
    rows. Intervention minus control must be >= +0.05 AUC. This is the only gate that
    speaks to whether state detection improved.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_state_readout_beats_control
  mde: 0.05
- blocking: true
  description: SHUFFLE CONTROL, non-negotiable after exp-e27. Any encoder-token readout
    must be scored against a WITHIN-WINDOW label-shuffle null of >= 20 permutations,
    reporting mean/std, and the real score must exceed the null mean by >= 2 SD. E27's
    encoder arm scored 0.5876 real vs 0.5830 shuffled — a 0.0046 gap that a single-draw
    control would have mistaken for signal. A gain that does not clear its own null
    is a capacity artefact, not state.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_gain_survives_shuffle_null
  mde: 2.0
- blocking: true
  description: DO-NO-HARM on the axis that actually ships. hr_mean subject-grouped
    readout R2 must stay within 0.03 of the control checkpoint. Phase B is a step
    on the path to the deployment model, and buying state skill by degrading the one
    channel that reads back at R2 0.97 is a net loss.
  diagnostic_id: eiv_heldout_hr_r2
  id: g4_hr_axis_not_damaged
  mde: 0.03
- blocking: false
  description: 'Mechanism check: does the margin actually engage? Report the easy-triplet
    ratio and realised dynamic margin over training for both arms. Hard negatives
    should produce a LOWER easy-triplet ratio than the control. Informational — it
    explains a null rather than gating it.'
  diagnostic_id: eiv_effective_rank
  id: g5_triplet_engages
  mde: null
id: hyp-e28-segment-triplet-retrain-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'Phase-B encoder trained with segment-level hard negatives beats
    the window-mined control by >= +0.05 AUC when probed on exp-e26''s exact transition
    rows, and the gain survives a >=20-permutation within-window shuffle null by >=
    2 SD. HONEST PRIOR: genuinely uncertain, leaning negative. E27 measured the frozen
    (window-mined) encoder as WORSE than percentile features on this very task and
    mostly shuffle artefact, so the bet is specifically that the training SIGNAL was
    the defect — that an objective which asked the encoder to separate a window from
    itself taught it nothing about state. If the gain fails to appear, the honest
    conclusion is that this corpus''s state signal is shallow-accessible only, and
    the soft-token path is not the state pathway.'
problem_id: phase-b-triplet-orthogonalization-is-f6d3c5
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
status: refuted
supersedes: null
topic_tags:
- contrastive
- collapse
- epoch-resolution
- baseline
- control
- dataset-whitelist
---

## Statement
H-E28 (segment-level triplet retrain). Phase B's triplet objective has never been trained on a signal capable of separating states: it mines WHOLE WINDOWS bucketed by infer_state_label, so for the 1274 windows carrying a state transition the anchor and the negative are literally the same tensor and the margin term carries no gradient direction.

Replacing the triplet UNIT with a peri-epoch SEGMENT (W=120) makes an anchor and a hard negative distinct tensors drawn from the SAME window — same subject, same day, same device, same circadian phase — so the only axis left to separate is state.

PREDICTION: a Phase B encoder trained with segment-level hard-negative mining beats the identically-configured window-mined control by >= +0.05 AUC when probed on exp-e26's exact within-window transition rows, and the gain survives a >=20-permutation within-window shuffle null by >= 2 SD, without degrading hr_mean readout by more than 0.03 R2.

## Rationale
Four measurements converge on this arm being the right next step, and one warns against expecting too much:

E25: the label degeneracy that parked this problem is GONE. Single-state share 0.991 -> 0.8194; states span 6-8 datasets at top-dataset purity 0.475-0.545, so same-state positives are ~half cross-dataset and the margin is no longer forced to encode provenance.
E26: the within-window contrast is REAL. flow-vs-stress at W=120 reads 0.6329 subject-grouped vs clock-only 0.5129, and 3.41 SD above a 40-permutation within-window shuffle null (mean 0.5492), p=0.0000. This is the ONLY design in the project where physiology beats every available baseline, because trait is held constant by construction.
RULE-OUT one-line-label-swap-fixes-the-triplet-miner: the obvious repair is structurally void — with a window-level index, anchor and negative for a transition window are the SAME tensor, so the margin term has no gradient direction. The unit must change, not the label.
E27: the WARNING. The frozen window-mined encoder is worse than percentile features on this task (-0.0453) and its apparent skill is 99% shuffle artefact (0.5830 vs 0.5876). So this arm is explicitly a bet that the training signal was the binding defect, not the architecture — and it must be scored against its own shuffle null or it will reproduce exactly that artefact.

## Plan
CODE (landed, tested): src/training/segment_triplets.py (SegmentIndex, SegmentTripletMiner, segment_view, slice_segment) + TripletStateDataset(segment_triplets=True) + pipeline/cli wiring. 13 unit tests; window mode untouched by default.

SEGMENT VIEW, NOT SLICE: TemporalEncoder is shape-bound (patch_len=120/stride=60 over 1440 min = 23 tokens). A 120-row slice would yield ONE token and break every downstream contract. So a segment is a FULL-WIDTH window with visibility masked to [t-W, t) — which the model already understands because presence is supplied explicitly (with_presence_v1, input_dim=24) and 45.7% of hr_mean cells are absent in normal operation. Verified: anchor and negative are distinct tensors of shape (1440,12), mean visible fraction 0.0827 vs the 120/1440=0.0833 ideal.

SUPPLY VERIFIED ON THE REAL CORPUS (logs/segment_miner_verify.json, logs/segment_dataset_integration.json):
  64005 segments over 6389 windows, 3 states (neutral 49655, stress 10079, flow 4271)
  1129 transition windows after the epoch>=120 full-history rule (1274 before it)
  0/300 anchor-negative tensor collisions; 435/435 same-window negatives share the subject
  hard-negative rate 0.41 over 300 dataset draws

ARMS, single variable:
  CONTROL      phase_b_12ch_wl_dryad   (window mining, incumbent)
  INTERVENTION phase_b_12ch_segtrip    (identical except segment_triplets=true, segment_minutes=120)
Verified by loading both profiles and diffing: the ONLY differing keys are segment_triplets, segment_minutes and notes.

SCORING: probe both resulting encoders on exp-e26's exact transition rows, subject-grouped, with a >=20-permutation within-window shuffle null on the same features; carry hr_mean R2 as the do-no-harm guard; log hard-negative rate and easy-triplet ratio from the runs themselves.

HONEST PRIOR, RECORDED BEFORE RUNNING: E27 measured the frozen encoder as WORSE than percentile features on this exact task (best arm -0.0453 below shallow) and mostly artefact (0.5830 shuffled vs 0.5876 real). This arm bets that the DEFECT WAS THE TRAINING SIGNAL rather than the architecture — that an encoder never trained on a non-degenerate state objective cannot be expected to encode state. That is a real hypothesis, but it is not a safe one, and g3 exists so a capacity artefact cannot be mistaken for success.

## Links
Tests problem [[phase-b-triplet-orthogonalization-is-f6d3c5]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_hard_negatives_actually_mined` (blocking): MINING ACTUALLY CHANGED. The intervention run must log a hard-negative rate >= 0.25 (measured 0.41 offline on 300 draws). This is the run's own proof that anchors and negatives came from the same window; a run that silently collapses to all-easy cross-window negatives has reverted to the control objective and its comparison is void regardless of downstream numbers.
Gate `g2_state_readout_beats_control` (blocking): PRIMARY. Probe the resulting Phase-B encoder tokens on exp-e26's EXACT rows (flow-vs-stress transitions, W=120, GroupKFold by subject, n=3248/87 subjects/662 windows) and compare against the CONTROL checkpoint's tokens on the identical rows. Intervention minus control must be >= +0.05 AUC. This is the only gate that speaks to whether state detection improved.
Gate `g3_gain_survives_shuffle_null` (blocking): SHUFFLE CONTROL, non-negotiable after exp-e27. Any encoder-token readout must be scored against a WITHIN-WINDOW label-shuffle null of >= 20 permutations, reporting mean/std, and the real score must exceed the null mean by >= 2 SD. E27's encoder arm scored 0.5876 real vs 0.5830 shuffled — a 0.0046 gap that a single-draw control would have mistaken for signal. A gain that does not clear its own null is a capacity artefact, not state.
Gate `g4_hr_axis_not_damaged` (blocking): DO-NO-HARM on the axis that actually ships. hr_mean subject-grouped readout R2 must stay within 0.03 of the control checkpoint. Phase B is a step on the path to the deployment model, and buying state skill by degrading the one channel that reads back at R2 0.97 is a net loss.
Gate `g5_triplet_engages` (advisory): Mechanism check: does the margin actually engage? Report the easy-triplet ratio and realised dynamic margin over training for both arms. Hard negatives should produce a LOWER easy-triplet ratio than the control. Informational — it explains a null rather than gating it.

#contrastive #collapse #epoch-resolution #baseline #control #dataset-whitelist #optihealth-eiv #matched-baseline #subject-heldout
