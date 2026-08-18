---
artifacts: []
closed: true
config_note: 'Runner: scratch/e27_encoder_transitions.py -> logs/e27_encoder_transitions.json.
  GPU RTX 5070, minutes. Corpus git_sha e849eb7, seed 0.


  SAME ROWS AS exp-e26: same 1274 transition windows, same flow-vs-stress pair, same
  W=120 alignment, same GroupKFold-by-subject split, n=3248 epochs / 87 subjects /
  662 windows / pos 0.480. The ONLY variable is the feature source.


  Encoder: outputs/checkpoints/v5d/phase_b temporal_encoder.pt (patch_len 120, stride
  60, input_dim 24, d_model 192), strict weight load, 662 unique windows encoded once
  to [662, 24, 23, 192] and indexed per epoch.'
cost: null
crash_reason: ''
created_at: '2026-08-15T08:32:59+00:00'
diagnostics_measured:
- after: 0.5876
  before: 0.6329
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry:
- Reading affect/state off the FROZEN v5d Phase-B encoder tokens with a high-dimensional
  RF/ridge readout, without a label-shuffle control on the identical features. The
  shuffle scores 0.5830 against a real-label 0.5876 on the transition task — essentially
  all apparent skill is capacity, not state. Any future encoder-token affect probe
  MUST report the shuffle on the same features or it is uninterpretable.
- 'Spending the ~4 GPU-hour Phase B retrain on REPRESENTATIONAL grounds. Measured
  NO-GO: every encoder arm is below the shallow percentile baseline on the transition
  task (-0.045 to -0.130), and the best one does not survive its shuffle control.
  The retrain retains only the SUPPLY justification (E25 non-degenerate triplets,
  E26 real contrast), which is a weaker and separate argument.'
external_run: logs/e27_encoder_transitions.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_encoder_beats_shallow_on_transitions
  passed: false
- evidence: ''
  gate_id: g2_shuffle_control_dead
  passed: false
- evidence: ''
  gate_id: g3_locality_at_transition_scale
  passed: false
git_ref: ''
hypothesis_id: hyp-e27-encoder-on-transitions-20260815
id: exp-e27-encoder-transitions-20260815
interpretation:
  at: '2026-08-18T21:08:42+00:00'
  failure_mode: claim_false
  lesson: 'The frozen v5d Phase-B encoder does NOT carry momentary state, and its
    apparent skill is a high-dimensional readout artefact: on exp-e26''s identical
    rows (n=3248, 87 subjects, 662 windows, same GroupKFold split) the best encoder
    arm reaches AUC 0.5876 vs shallow percentile features 0.6329 (gap -0.0453) and
    every encoder arm is below shallow (-0.045, -0.060, -0.108, -0.130), while the
    within-window label SHUFFLE on the same encoder features scores 0.5830 — a real-minus-shuffle
    gap of just 0.0046 against the shallow arm''s 0.1178. Locality reversed the prediction
    diagnostically: local arms are worse than global (enc_local_avg 0.5026 = chance,
    enc_local_avgmax 0.5254), consistent with fewer tokens meaning less capacity to
    overfit rather than with a real local signal. Actionable: any encoder-token affect/state
    probe in this vault is uninterpretable without a label-shuffle control on the
    identical features (recorded as do-not-retry), exp-e22''s g3 ''locality confirmed
    at the representation level'' (+0.054 to +0.066) was measured WITHOUT that control
    and must be treated as unverified, and a ~4 GPU-hour Phase B retrain is a measured
    NO-GO on representational grounds — only the separate, weaker supply argument
    survives.'
  transfers_to:
  - hyp-e22-encoder-representation-affect-20260815
  - hyp-e28-segment-triplet-retrain-20260815
  - hyp-e19-encoder-pooling-transfer-20260815
  - hyp-e19b-pooling-recipe-level-20260815
key_numbers:
  auc_clock: 0.5129
  auc_enc_global_avg: 0.5728
  auc_enc_global_avgmax: 0.5876
  auc_enc_local_avg: 0.5026
  auc_enc_local_avgmax: 0.5254
  auc_shallow: 0.6329
  auc_shuffle_on_best_encoder: 0.583
  e26_shallow_shuffle_gap: 0.1178
  encoder_real_minus_shuffle_gap: 0.0046
  gap_encoder_minus_shallow: -0.0453
  n: 3248.0
  n_subjects: 87.0
  n_windows: 662.0
observed_effects:
- delta: -0.0453
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: exp-e26-within-window-transitions-20260815
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e27-encoder-on-transitions-20260815]].
Branches from [[exp-e26-within-window-transitions-20260815]].
External run: logs/e27_encoder_transitions.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.6329 → after 0.5876  (Δ -0.0453, down ✗ predicted up)

## Gates
- `g1_encoder_beats_shallow_on_transitions`: **FAIL**
- `g2_shuffle_control_dead`: **FAIL**
- `g3_locality_at_transition_scale`: **FAIL**

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The frozen v5d Phase-B encoder does NOT carry momentary state, and its apparent skill is a high-dimensional readout artefact: on exp-e26's identical rows (n=3248, 87 subjects, 662 windows, same GroupKFold split) the best encoder arm reaches AUC 0.5876 vs shallow percentile features 0.6329 (gap -0.0453) and every encoder arm is below shallow (-0.045, -0.060, -0.108, -0.130), while the within-window label SHUFFLE on the same encoder features scores 0.5830 — a real-minus-shuffle gap of just 0.0046 against the shallow arm's 0.1178. Locality reversed the prediction diagnostically: local arms are worse than global (enc_local_avg 0.5026 = chance, enc_local_avgmax 0.5254), consistent with fewer tokens meaning less capacity to overfit rather than with a real local signal. Actionable: any encoder-token affect/state probe in this vault is uninterpretable without a label-shuffle control on the identical features (recorded as do-not-retry), exp-e22's g3 'locality confirmed at the representation level' (+0.054 to +0.066) was measured WITHOUT that control and must be treated as unverified, and a ~4 GPU-hour Phase B retrain is a measured NO-GO on representational grounds — only the separate, weaker supply argument survives.

Transfers to: [[hyp-e22-encoder-representation-affect-20260815]], [[hyp-e28-segment-triplet-retrain-20260815]], [[hyp-e19-encoder-pooling-transfer-20260815]], [[hyp-e19b-pooling-recipe-level-20260815]]

## Key numbers
- `auc_clock`: 0.5129
- `auc_enc_global_avg`: 0.5728
- `auc_enc_global_avgmax`: 0.5876
- `auc_enc_local_avg`: 0.5026
- `auc_enc_local_avgmax`: 0.5254
- `auc_shallow`: 0.6329
- `auc_shuffle_on_best_encoder`: 0.583
- `e26_shallow_shuffle_gap`: 0.1178
- `encoder_real_minus_shuffle_gap`: 0.0046
- `gap_encoder_minus_shallow`: -0.0453
- `n`: 3248
- `n_subjects`: 87
- `n_windows`: 662

## Do not retry
- Reading affect/state off the FROZEN v5d Phase-B encoder tokens with a high-dimensional RF/ridge readout, without a label-shuffle control on the identical features. The shuffle scores 0.5830 against a real-label 0.5876 on the transition task — essentially all apparent skill is capacity, not state. Any future encoder-token affect probe MUST report the shuffle on the same features or it is uninterpretable.
- Spending the ~4 GPU-hour Phase B retrain on REPRESENTATIONAL grounds. Measured NO-GO: every encoder arm is below the shallow percentile baseline on the transition task (-0.045 to -0.130), and the best one does not survive its shuffle control. The retrain retains only the SUPPLY justification (E25 non-degenerate triplets, E26 real contrast), which is a weaker and separate argument.

## Notes
NO-GO ON THE PHASE B RETRAIN, decided by the rule committed before the run. And the shuffle control turned this from a simple null into a warning about the whole encoder-probe family.

THE GATE: best encoder arm 0.5876 vs shallow 0.6329 = -0.0453. The encoder is BELOW percentile features, and every encoder arm is below shallow (-0.045, -0.060, -0.108, -0.130).

THE FINDING THAT MATTERS MORE: enc_global_avgmax on WITHIN-WINDOW SHUFFLED labels scores 0.5830 against its real-label 0.5876. Gap 0.0046. Destroying the state contrast costs it essentially nothing, so its apparent +0.0747 over clock is a high-dimensional readout artefact — 4608-9216 features against 3248 rows. E26's shallow arm passed the identical control cleanly (0.6329 real vs 0.5151 shuffled, gap 0.1178). Same rows, same split, same control: shallow is real, encoder is not.

Had I omitted the shuffle, enc_global_avgmax at 0.5876 / +0.0747 over clock would have read as a modest positive and could have been cited to justify the retrain. This is the fifth time today a control or premise check changed a conclusion.

LOCALITY PREDICTION REVERSED, DIAGNOSTICALLY. I expected locality to help MORE here (the two epochs share a day, differing only locally). Observed local WORSE than global: -0.0702 (avg) and -0.0622 (avgmax), with enc_local_avg at 0.5026 = chance. The shuffle explains it: fewer tokens means less capacity to overfit, so the artefact shrinks and the arm falls to chance. CONSEQUENCE FOR E22 — its g3 'locality confirmed at the representation level' (+0.054 to +0.066, four of four arms on daily labels) was measured WITHOUT a shuffle control on the same features. Given what this run shows, that result should be treated as UNVERIFIED until re-run with the shuffle, and should not be cited as evidence for time-indexed readouts.

WHAT SURVIVES. E26 stands: the shallow detector reads state at 0.6329 vs clock 0.5129 with a dead shuffle, in the design where trait is held constant by construction. That is the deployable artefact, and per Anton's direction the next step is option 3 — ship it as a per-window state head.

WHAT THE RETRAIN WOULD STILL NEED. The supply argument (E25: triplets now non-degenerate, states span 6-8 datasets at purity 0.475-0.545; E26: 1274 windows carry real contrast the miner currently discards) is untouched by this result. But it is a claim that BETTER TRAINING might create representational value, not that the value already exists — and this run shows it does not exist in the current encoder. Any future proposal must say which of those two it is betting on.

#wearables #cross-modal #pooling #baseline #control #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
