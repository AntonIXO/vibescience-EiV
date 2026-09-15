---
ambition_class: tuning
created_at: '2026-09-03T10:05:08+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: agreement >= 0.99 AND unparseable <= 1%
  diagnostic_id: eiv_narration_state_fidelity_agreement
  id: g1
  mde: 0.02
- blocking: true
  description: per-device narrated-verdict AUC within +/-0.02 of the classifier THRESHOLDED
    at 0.5 on the same rows (WESAD 0.8657, LifeSnaps 0.4842)
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g2
  mde: 0.02
- blocking: false
  description: probability repeated within 0.01 on >= 95% of rows; HR within 0.5 bpm
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  id: g3
  mde: null
- blocking: true
  description: Design identical to N2 attempt 2 (adapter disabled, greedy, prefilled
    answer, terminal Verdict line parsed); the ONLY change is --prompt-mode label
  diagnostic_id: eiv_narration_state_fidelity_agreement
  id: g4
  mde: null
id: n3-narrator-handed-the-classifier-s-decision-label-carries-it-at-or-above-0-99
interventions: []
override_reason: ''
paper_refs:
- sensorfm-2026-2605-22759
predicted_effects:
- diagnostic_id: eiv_narration_state_fidelity_agreement
  direction: up
  magnitude_note: 0.952 (N2) -> >= 0.99; before = N2's 0.952
- diagnostic_id: eiv_state_auc_over_null_lab
  direction: up
  magnitude_note: narrated verdict over null on WESAD 0.308 (N2) -> ~0.362 (the thresholded
    classifier's own)
- diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: none
  magnitude_note: stays at the classifier's own chance level, -0.016 +/- 0.02
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- conditioning-collapse
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device
topic_tags:
- optihealth
- readout
- target-style
- medgemma
- deployment-distribution
---

## Statement
Same narrator, same 666 held-out rows, same classifier, one change: the prompt states the classifier's DECISION as a word (FLOW or STRESS, the 0.50 threshold applied upstream) and gives the probability only as a number to repeat -- SensorFM's head-prediction shape. Prediction: agreement with the classifier's decision >= 0.99 (N2 read 0.952 with every miss at p in [0.30, 0.50)), so the narrated verdict's per-device AUC equals the thresholded classifier's within 0.02 (WESAD 0.866, LifeSnaps 0.484), numeric fidelity unchanged.

## Rationale
exp-n2-narrator-state-fidelity-20260903 (directional_only, recipe_conditional): the narrator carried numbers at 0.012 / 0.025 bpm but applied the 0.50 rule wrongly on 32/666 rows, all with p(FLOW) in [0.30, 0.50), siding with the state word it had just written. The failure is in rule application, not in carrying content, so removing the rule from the narrator's job should remove the failure. This is exactly the condition SensorFM's clinician study ran (LLM given head predictions ~ LLM given ground truth, p=0.396).

## Plan
scratch/n2_narrator_state.py --prompt-mode label --out logs/n3_narrator_label.json on the 5070, ~1 GPU-hour.

## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_narration_state_fidelity_agreement]] (up).
Predicts [[eiv_state_auc_over_null_lab]] (up).
Predicts [[eiv_state_auc_over_null_freeliving]] (none).
Gate `g1` (blocking): agreement >= 0.99 AND unparseable <= 1%
Gate `g2` (blocking): per-device narrated-verdict AUC within +/-0.02 of the classifier THRESHOLDED at 0.5 on the same rows (WESAD 0.8657, LifeSnaps 0.4842)
Gate `g3` (advisory): probability repeated within 0.01 on >= 95% of rows; HR within 0.5 bpm
Gate `g4` (blocking): Design identical to N2 attempt 2 (adapter disabled, greedy, prefilled answer, terminal Verdict line parsed); the ONLY change is --prompt-mode label
Cites [[sensorfm-2026-2605-22759]].
Supersedes [[n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device]].

#optihealth #readout #target-style #medgemma #deployment-distribution #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
