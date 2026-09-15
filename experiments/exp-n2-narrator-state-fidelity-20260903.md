---
artifacts:
- logs/n2_narrator_state.json
- logs/n2_narrator_state.attempt1.json
- logs/n2_narrator_state_rescore.json
- logs/g5_heldout_preds.json
- scratch/n2_narrator_state.py
closed: true
config_note: scratch/n2_narrator_state.py over the 666 held-out g5 rows; classifier
  = union_ms/gb out-of-fold probability from g5 --dump-preds; base MedGemma-1.5-4b-it
  via outputs/adapters/v41rebased_c with the LoRA disabled, 4-bit, greedy, max 160
  new tokens; prompt carries the 30-min mean/max HR (tensor-derived stand-in) and
  the probability; parsed on a terminal 'Verdict:' line.
cost:
  gpu_hours: 1.8
  wall_hours: 3.2
crash_reason: ''
created_at: '2026-09-03T06:48:12+00:00'
diagnostics_measured:
- after: 0.952
  before: 0.5
  diagnostic_id: eiv_narration_state_fidelity_agreement
- after: 0.308
  before: 0.362
  diagnostic_id: eiv_state_auc_over_null_lab
- after: -0.012
  before: -0.016
  diagnostic_id: eiv_state_auc_over_null_freeliving
- after: 0.0252
  before: 0.0245
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
do_not_retry:
- 'Do not hand a narrator a probability and a decision rule and expect it to apply
  the rule: at p(FLOW) in [0.30, 0.50) base MedGemma says FLOW on 32/666 rows. Hand
  it the DECISION (the class label) and, optionally, the probability as a number to
  repeat -- SensorFM''s condition feeds head predictions, not thresholds to compute.'
- Do not score a binary narrated verdict against the classifier's continuous-probability
  AUC (0.973); the fair comparator is the classifier thresholded at 0.5 (0.866).
external_run: ''
former_ids: []
gate_results:
- evidence: 'Attempt 2 (corrected prompt, prefilled answer, 320-token budget): agreement
    0.9520 over 666 rows (WESAD 0.959, LifeSnaps 0.950), unparseable 0/666. Passes
    the 0.95 bar by 0.002 -- at the bar, not clear of it. Attempt 1 (logs/n2_narrator_state.attempt1.json)
    read 0.6725 with 382/666 unparseable: the model opened with a planning preamble
    and hit the 160-token cap; recorded as an instrument failure, not evidence.'
  gate_id: g1
  passed: true
- evidence: 'WESAD (n=148): classifier probability AUC 0.9728, classifier THRESHOLDED
    at 0.5 (the fair comparator for a binary verdict) 0.8657, narrated verdict 0.8121,
    narrated probability 0.8218 -- the narrator loses 0.054 against the thresholded
    classifier, outside the 0.02 band. LifeSnaps (n=442): thresholded 0.4842 vs narrated
    0.4878, inside the band (faithfully at chance). The loss is one systematic error:
    all 32 disagreements have the FLOW probability in [0.30, 0.50) and read ''a 39%
    probability of FLOW ... more likely in FLOW. Verdict: FLOW'' -- the narrator applies
    the 0.50 rule wrongly when the stated probability is below half but the state
    word is present.'
  gate_id: g2
  passed: false
- evidence: Probability repeated (as a decimal or a percent) on 666/666 rows, MAE
    0.0117, within 0.01 on 96.8% of rows (max error 0.295 on one row); 30-min mean
    HR repeated at 0.0252 bpm MAE. Numbers are carried; the decision rule is what
    slips.
  gate_id: g3
  passed: true
- evidence: 'Adapter disabled via model.disable_adapter(), greedy, no soft tokens,
    terminal ''Verdict:'' line parsed, per-device permutation null of the classifier
    reported (WESAD 0.504+/-0.055, LifeSnaps 0.500+/-0.028). Deviation from the registered
    plan: the prompt wording was corrected after attempt 1 (''under FLOW rather than
    in STRESS'' -> both probabilities stated and the rule spelled out) and the assistant
    turn is prefilled with ''Based on the data, ''. Both attempts are on disk.'
  gate_id: g4
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device
id: exp-n2-narrator-state-fidelity-20260903
interpretation:
  at: '2026-09-03T10:04:27+00:00'
  failure_mode: recipe_conditional
  lesson: 'Base MedGemma carries NUMBERS almost perfectly (probability MAE 0.012,
    HR 0.025 bpm, 0/666 unparseable once the answer is prefilled) but carries a DECISION
    RULE only 95.2% of the time, and the 4.8% it drops is one systematic error: every
    one of the 32 disagreements has p(FLOW) in [0.30, 0.50) and reads ''a 39% probability
    of FLOW ... more likely in FLOW'' -- the narrator sides with the state word it
    just wrote rather than the threshold. On WESAD that costs 0.054 AUC against the
    thresholded classifier (0.866 -> 0.812); on LifeSnaps the narrator is faithfully
    at chance (0.484 vs 0.488). Product consequence, and it is SensorFM''s own design:
    hand the narrator the head''s DECISION as a label, plus the probability as a number
    to repeat, never a rule to apply. That variant is expected to read ~1.0 and is
    the one to ship; this experiment does not licence the probability-plus-rule prompt.
    Instrument lessons carried to the record: prefill the assistant turn (MedGemma
    plans for 160 tokens otherwise), and compare a binary verdict to the thresholded
    classifier, not to its continuous AUC.'
  transfers_to:
  - n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path
key_numbers:
  agreement: 0.952
  attempt1_agreement: 0.6725
  attempt1_unparseable_rate: 0.574
  disagree_p_band_hi: 0.5
  disagree_p_band_lo: 0.3
  hr_fidelity_mae_bpm: 0.0252
  lifesnaps_clf_thresholded_auc: 0.4842
  lifesnaps_narrated_verdict_auc: 0.4878
  n: 666.0
  n_disagree: 32.0
  prob_fidelity_mae: 0.0117
  unparseable_rate: 0.0
  wesad_clf_prob_auc: 0.9728
  wesad_clf_thresholded_auc: 0.8657
  wesad_narrated_verdict_auc: 0.8121
observed_effects:
- delta: 0.452
  diagnostic_id: eiv_narration_state_fidelity_agreement
  direction: up
- delta: -0.054
  diagnostic_id: eiv_state_auc_over_null_lab
  direction: down
- delta: 0.004
  diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: up
- delta: 0.0007
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  direction: up
parent_experiment_id: exp-n-narrator-fidelity-20260903
prediction_match:
  overall: true
  per_diagnostic:
    eiv_narration_state_fidelity_agreement: true
    eiv_state_auc_over_null_freeliving: false
    eiv_state_auc_over_null_lab: false
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device]].
Branches from [[exp-n-narrator-fidelity-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.

## Measurements
- [[eiv_narration_state_fidelity_agreement]]: before 0.5 → after 0.952  (Δ +0.452, up ✓ predicted up)
- [[eiv_state_auc_over_null_lab]]: before 0.362 → after 0.308  (Δ -0.054, down ✗ predicted up)
- [[eiv_state_auc_over_null_freeliving]]: before -0.016 → after -0.012  (Δ +0.004, up ✗ predicted none)
- [[eiv_narration_numeric_fidelity_mae_bpm]]: before 0.0245 → after 0.0252  (Δ +0.0007, up)

## Gates
- `g1`: PASS — Attempt 2 (corrected prompt, prefilled answer, 320-token budget): agreement 0.9520 over 666 rows (WESAD 0.959, LifeSnaps 0.950), unparseable 0/666. Passes the 0.95 bar by 0.002 -- at the bar, not clear of it. Attempt 1 (logs/n2_narrator_state.attempt1.json) read 0.6725 with 382/666 unparseable: the model opened with a planning preamble and hit the 160-token cap; recorded as an instrument failure, not evidence.
- `g2`: **FAIL** — WESAD (n=148): classifier probability AUC 0.9728, classifier THRESHOLDED at 0.5 (the fair comparator for a binary verdict) 0.8657, narrated verdict 0.8121, narrated probability 0.8218 -- the narrator loses 0.054 against the thresholded classifier, outside the 0.02 band. LifeSnaps (n=442): thresholded 0.4842 vs narrated 0.4878, inside the band (faithfully at chance). The loss is one systematic error: all 32 disagreements have the FLOW probability in [0.30, 0.50) and read 'a 39% probability of FLOW ... more likely in FLOW. Verdict: FLOW' -- the narrator applies the 0.50 rule wrongly when the stated probability is below half but the state word is present.
- `g3`: PASS — Probability repeated (as a decimal or a percent) on 666/666 rows, MAE 0.0117, within 0.01 on 96.8% of rows (max error 0.295 on one row); 30-min mean HR repeated at 0.0252 bpm MAE. Numbers are carried; the decision rule is what slips.
- `g4`: PASS — Adapter disabled via model.disable_adapter(), greedy, no soft tokens, terminal 'Verdict:' line parsed, per-device permutation null of the classifier reported (WESAD 0.504+/-0.055, LifeSnaps 0.500+/-0.028). Deviation from the registered plan: the prompt wording was corrected after attempt 1 ('under FLOW rather than in STRESS' -> both probabilities stated and the rule spelled out) and the assistant turn is prefilled with 'Based on the data, '. Both attempts are on disk.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

Base MedGemma carries NUMBERS almost perfectly (probability MAE 0.012, HR 0.025 bpm, 0/666 unparseable once the answer is prefilled) but carries a DECISION RULE only 95.2% of the time, and the 4.8% it drops is one systematic error: every one of the 32 disagreements has p(FLOW) in [0.30, 0.50) and reads 'a 39% probability of FLOW ... more likely in FLOW' -- the narrator sides with the state word it just wrote rather than the threshold. On WESAD that costs 0.054 AUC against the thresholded classifier (0.866 -> 0.812); on LifeSnaps the narrator is faithfully at chance (0.484 vs 0.488). Product consequence, and it is SensorFM's own design: hand the narrator the head's DECISION as a label, plus the probability as a number to repeat, never a rule to apply. That variant is expected to read ~1.0 and is the one to ship; this experiment does not licence the probability-plus-rule prompt. Instrument lessons carried to the record: prefill the assistant turn (MedGemma plans for 160 tokens otherwise), and compare a binary verdict to the thresholded classifier, not to its continuous AUC.

Transfers to: [[n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path]]

## Key numbers
- `agreement`: 0.952
- `attempt1_agreement`: 0.6725
- `attempt1_unparseable_rate`: 0.574
- `disagree_p_band_hi`: 0.5
- `disagree_p_band_lo`: 0.3
- `hr_fidelity_mae_bpm`: 0.0252
- `lifesnaps_clf_thresholded_auc`: 0.4842
- `lifesnaps_narrated_verdict_auc`: 0.4878
- `n`: 666
- `n_disagree`: 32
- `prob_fidelity_mae`: 0.0117
- `unparseable_rate`: 0
- `wesad_clf_prob_auc`: 0.9728
- `wesad_clf_thresholded_auc`: 0.8657
- `wesad_narrated_verdict_auc`: 0.8121

## Artifacts
- `logs/n2_narrator_state.json`
- `logs/n2_narrator_state.attempt1.json`
- `logs/n2_narrator_state_rescore.json`
- `logs/g5_heldout_preds.json`
- `scratch/n2_narrator_state.py`

## Do not retry
- Do not hand a narrator a probability and a decision rule and expect it to apply the rule: at p(FLOW) in [0.30, 0.50) base MedGemma says FLOW on 32/666 rows. Hand it the DECISION (the class label) and, optionally, the probability as a number to repeat -- SensorFM's condition feeds head predictions, not thresholds to compute.
- Do not score a binary narrated verdict against the classifier's continuous-probability AUC (0.973); the fair comparator is the classifier thresholded at 0.5 (0.866).

## Notes
The control 'eiv_dense128_hr_raw_mae_bpm' declared on the numeric-fidelity diagnostic does not apply here: the HR number in the prompt is a tensor-derived 30-minute mean used only as a repetition check, not a prediction against truth. Two instrument lessons: (1) MedGemma-1.5-4b-it opens with a numbered plan under a 160-token budget and never reaches the answer -- prefill the assistant turn; (2) never keyword-spot the state word in prose; the terminal Verdict line is the only thing parsed.

#optihealth #readout #target-style #medgemma #deployment-distribution #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
