---
ambition_class: mechanism
created_at: '2026-09-01T12:41:36+00:00'
former_ids: []
gap_closure_estimate: 0.4
gates:
- blocking: true
  description: 'g1 BLOCKING: held-out dense hr_raw MAE <= 2.602 (the incumbent''s
    3-seed held-out mean). Scored with --split heldout on the 1,485-row pool, NOT
    the 23-row subset. mde 0.7 bpm: below that the arms are indistinguishable at this
    pool size and the verdict is a powered null.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g1-heldout-mae
  mde: 0.7
- blocking: true
  description: 'g2 BLOCKING: emitted/true scale ratio inside [0.85, 1.35] on the HELD-OUT
    pool. This is where v36labels failed (1.59, 2.08) and v31detached failed (2.00)
    while looking fine in-sample. Read this BEFORE the MAE -- outside the band the
    floor is not valid and the MAE is not comparable.'
  diagnostic_id: eiv_gen_between_sample_std
  id: g2-heldout-calibration
  mde: 0.15
- blocking: false
  description: 'g3 ADVISORY: momentary state over_null > +0.02 on the clean transition
    pool (nurse_stress EXCLUDED per exp-047''s do_not_retry -- 43% of the full pool
    is its shift-schedule labels and even zero-parameter arms lose significance there).
    This is the state-detection question itself. A null here with g1/g2 passing means
    the chain is a good HR reader that does not read state, and the state slot should
    be dropped from the schema.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g3-state-advisory
  mde: 0.02
- blocking: true
  description: 'g0 BLOCKING, CHEAP, MID-CHAIN: Phase A eff_rank >= 7.0 before Phase
    C is funded. Anchors: healthy 8.35, carrier-collapse 4.47, v29-attached 6.783.
    NOTE this is a VETO not a direction -- v31detached scored the campaign record
    10.106 and then failed deployment, so a high value buys nothing.'
  diagnostic_id: eiv_effective_rank
  id: g0-geometry-veto
  mde: 0.153
id: a-held-out-scored-chain-combining-every-079038
interventions: []
override_reason: ''
paper_refs:
- timeperceiver-2025-2512-22550
predicted_effects:
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
  magnitude_note: held-out dense MAE below the incumbent's 2.602 3-seed mean. The
    incumbent's held-out seeds are 2.593/1.906/3.306, so beating the MEAN is a weaker
    bar than beating its best seed and is the honest one at n=1.
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: over_null > +0.02 on the clean transition pool. E29 measured +0.0309
    at the raw patch grid with zero learned parameters; A4/s43 reached +0.0189 z+3.23
    at PL60. Anything at or above +0.02 from a TRAINED chain would be the first time
    the signal survives the learned weights.
- diagnostic_id: eiv_effective_rank
  direction: none
  magnitude_note: no prediction -- rank is a veto at 7.0, not a target. v31detached's
    record 10.106 preceded a deployment failure, so a rise here is not evidence of
    anything.
problem_id: the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all
problem_tags:
- optihealth-eiv
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- deployment-distribution
- optihealth
- readout
---

## Statement
A HELD-OUT-SCORED CHAIN COMBINING EVERY CONFIRMED LEVER BEATS THE INCUMBENT ON GENERALISATION. Training a full A->B->C chain with patch_len 60 / stride 30, state_patch_weight 0.5, state_weight 0.5 and state_target: soft, then scoring on the 1,485-row held-out pool the new --split flag unlocks, will reach held-out dense hr_raw MAE at or below the incumbent's 2.602 three-seed mean while holding the emitted/true scale ratio inside [0.85, 1.35].

## Rationale
EVERY COMPONENT IS SEPARATELY CONFIRMED IN THIS VAULT AND THE COMBINATION HAS NEVER BEEN RUN. Full trace of 22 state-detection hypotheses: 13 refuted, 7 confirmed, 2 inconclusive.

REFUTED, MUST NOT BE REPEATED: E16 peri-EMA windowing, E17 unified readout, E18 within-subject N-of-1 head, E22 encoder-beats-summary, E25 triplet degeneracy fixed, E27, E28 segment triplet retrain, W2. Cross-subject DAILY state detection is dead -- lifesnaps hr_only AUC 0.4816, multichannel 0.4538, within-subject perm p=0.84. And state-detection-was-substrate-limited (v30state) was refuted 2026-08-31: v30state reads held-out dense 19.935 bpm against a 5.961 constant, skill -13.975, scale 2.77.

ALSO REFUTED, AND IT CONSTRAINS THIS ARM: state_target soft/ordinal ALONE. exp-047 closed 'supports' but DIRECTION-ONLY with the magnitude failed, and its do_not_retry records that soft/ordinal do not separate from the HARD control (s43 z+2.39 against old-corpus v5d z+1.84, same band). So soft targets are necessary-but-not-sufficient here and are combined, never tested alone.

CONFIRMED AND NEVER COMBINED:
- E24: sub-daily windows DO recover state on lab-protocol cohorts that every 24h arm silently excluded (zero usable windows at W=1440).
- E26: within-window transitions are the cleanest design in the corpus -- 1,274 windows carry 2-3 states, same subject/day/device, flow-vs-stress OOF AUC 0.6329 at W=120 against a 0.5129 clock control.
- E29: the information is present at the encoder's OWN patch grid (raw_patch_grid AUC 0.6309, z+2.66, ZERO learned parameters) and dies in the learned weights (enc_token 0.5202, z-0.84).
- W-SWEEP: PL60 supervision > PL120 at matched W, and the trained head reads recent minutes rather than the day.
- A4: PL60 A->B chain at s43 cleared A +0.0163 z+2.80, B +0.0189 z+3.23, clean pool, 200-perm null, zero fitted parameters.

SO THE UNTESTED RESIDUAL IS EXACT: patch_len 60 (the geometry W-SWEEP confirmed) + state_patch_weight (the axis E29 showed carries the signal) + state_target soft (which makes a 41-minute induction visible against its own baseline instead of losing to the window majority) in ONE chain. Every previous state arm moved one of these on a 120-minute patch.

AND THE SCORING SIDE NOW EXISTS. The --split flag landed today: heldout pool 1,485 rows -- 65x the 23 rows every held-out number in this campaign currently rests on. Verified: heldout 1485, test 738, train 5027, and --dataset optihealth_supabase --split heldout correctly RAISES rather than returning an empty draw, because supabase is one subject in TRAIN.

WHY IT COULD FAIL, STATED UP FRONT: E29's refutation says momentary state dies in the learned weights and no arm has yet made it survive training. If PL60 + patch supervision + soft targets still lands at chance on g3, the honest conclusion is that the state slot should be DROPPED from the emitted schema rather than shipped as a noise generator -- the decision E18 was built to force, which this arm inherits.

## Plan
ONE CHAIN, THREE VARIABLES AGAINST THE INCUMBENT, deliberately not one -- this is a mechanism arm combining levers each separately confirmed, not a knob test. Named v41pl60state.

PHASE A: phase_a_12ch_v36labels as the base, with patch_len 120->60, stride 60->30, state_patch_weight 0->0.5, state_weight 0->0.5. state_target already reads 'soft' on that profile. caption_style stays hr_dispersion_labels (the v36 recipe) so the caption is NOT a fourth variable.
PHASE B: phase_b_12ch_v25bigbatch at matched patch geometry.
PHASE C: lora_proxy_12ch_wl_band04 unchanged, off the new Phase B.

SCORING, and this is the part that is new: every capture taken TWICE -- once with --split heldout (1,485-row pool, the generalisation number the gates read) and once unrestricted (comparable to the existing arm table). The incumbent must be re-scored with --split heldout on the same pool, because no existing capture used the flag.

SEEDS: single seed 42 for the gate decision. If g1 and g2 both pass, a 3-seed family follows -- today's family established that a single-seed promotion is not readable against a retrain floor.

## Links
Tests problem [[the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all]].
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (down).
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Predicts [[eiv_effective_rank]] (none).
Gate `g1-heldout-mae` (blocking): g1 BLOCKING: held-out dense hr_raw MAE <= 2.602 (the incumbent's 3-seed held-out mean). Scored with --split heldout on the 1,485-row pool, NOT the 23-row subset. mde 0.7 bpm: below that the arms are indistinguishable at this pool size and the verdict is a powered null.
Gate `g2-heldout-calibration` (blocking): g2 BLOCKING: emitted/true scale ratio inside [0.85, 1.35] on the HELD-OUT pool. This is where v36labels failed (1.59, 2.08) and v31detached failed (2.00) while looking fine in-sample. Read this BEFORE the MAE -- outside the band the floor is not valid and the MAE is not comparable.
Gate `g3-state-advisory` (advisory): g3 ADVISORY: momentary state over_null > +0.02 on the clean transition pool (nurse_stress EXCLUDED per exp-047's do_not_retry -- 43% of the full pool is its shift-schedule labels and even zero-parameter arms lose significance there). This is the state-detection question itself. A null here with g1/g2 passing means the chain is a good HR reader that does not read state, and the state slot should be dropped from the schema.
Gate `g0-geometry-veto` (blocking): g0 BLOCKING, CHEAP, MID-CHAIN: Phase A eff_rank >= 7.0 before Phase C is funded. Anchors: healthy 8.35, carrier-collapse 4.47, v29-attached 6.783. NOTE this is a VETO not a direction -- v31detached scored the campaign record 10.106 and then failed deployment, so a high value buys nothing.
Cites [[timeperceiver-2025-2512-22550]].

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline

## Resolution (2026-09-03)

SUPERSEDED 2026-09-03 by v41-pl60-state-chain-rebased-and-split-honest (confirmed, exp-v41rebased-pl60-splithonest-20260902). This hypothesis was written against the 2.602 / 2.969 'held-out' bars that rule-out that-the-scorer-s-split-heldout-flag-makes-the-d88faa showed to be in-sample (125/128 rows from training subjects), so its g1 could never be scored honestly as written. The successor re-based the whole chain on split: train and passed all four gates (hr_raw 2.843 vs trivial 6.876, scale 0.992). Closed to match, verdict carried from the successor.
