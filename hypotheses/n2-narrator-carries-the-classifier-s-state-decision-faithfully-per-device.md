---
ambition_class: mechanism
created_at: '2026-09-03T06:46:46+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: agreement between narrated verdict and classifier decision >= 0.95
    AND unparseable rows <= 3%
  diagnostic_id: eiv_narration_state_fidelity_agreement
  id: g1
  mde: 0.05
- blocking: true
  description: per-device narrated-state AUC vs truth within +/-0.02 of the classifier's
    own per-device AUC on the same out-of-fold predictions, for every device with
    n >= 30 and both classes present
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g2
  mde: 0.02
- blocking: false
  description: the stress probability and the 30-minute mean HR placed in the prompt
    are repeated with numeric fidelity <= 0.02 (probability) and <= 0.5 bpm
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  id: g3
  mde: null
- blocking: true
  description: 'Design: adapter disabled, greedy decode, the narrator is asked to
    end with an explicit ''Verdict: STRESS|FLOW'' line which is what is parsed; a
    permutation null of the classifier probability against the labels is reported
    per device. No clinical-quality claim.'
  diagnostic_id: eiv_narration_state_fidelity_agreement
  id: g4
  mde: null
id: n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device
interventions: []
override_reason: ''
paper_refs:
- sensorfm-2026-2605-22759
- feli-2025-cha-ppghr-agent-2502-12836
predicted_effects:
- diagnostic_id: eiv_narration_state_fidelity_agreement
  direction: up
  magnitude_note: predicted >= 0.95 agreement with the classifier's 0.5 decision over
    666 held-out rows (before = 0.5, a narrator ignoring the prompt)
- diagnostic_id: eiv_state_auc_over_null_lab
  direction: up
  magnitude_note: narrated-state AUC on WESAD rows within 0.02 of the classifier's
    own 0.9728
- diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: none
  magnitude_note: narrated-state AUC on LifeSnaps rows within 0.02 of the classifier's
    own 0.4744 — i.e. faithfully at chance; a narrator that reads ABOVE the head here
    is overriding it, which is a failure of fidelity, not a win
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- conditioning-collapse
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- optihealth
- readout
- target-style
- medgemma
- deployment-distribution
---

## Statement
Base MedGemma-1.5-4b-it with the LoRA adapter DISABLED and no soft tokens, handed in its prompt the union_ms/gb classifier's out-of-fold stress probability for a moment of the day plus that moment's 30-minute mean and peak HR, narrates the state the classifier decided (stress vs flow at 0.5) on at least 95% of rows, and the narrated state's AUC against the true label, scored PER DEVICE (WESAD lab, LifeSnaps free-living), matches the classifier's own per-device AUC on the identical out-of-fold predictions within 0.02 in both directions — the narrator neither loses nor silently overrides the head. This is the state half of exp-n-narrator-fidelity-20260903's g3, which was unevaluated.

## Rationale
exp-n (supports) showed 0.0245 bpm numeric fidelity; SensorFM's clinician study (arXiv:2605.22759) found LLM-given-head-predictions indistinguishable from LLM-given-ground-truth, which is exactly a fidelity claim. exp-c1 gives the classifier's per-device AUC (WESAD 0.9728, LifeSnaps 0.4744 for union_ms/gb) on the held-out pool, so the narrator can be scored against the head it is repeating. HR numbers in the prompt are the window's tensor-derived 30-minute mean/max (denormalised), a stand-in for the attention head's numbers because the head has no predictions on the canonical-bundle rows the state labels live on; the claim under test is state fidelity, and the HR clause is only checked for verbatim repetition.

## Plan
1) scratch/g5_pool_ceiling.py --pool heldout --only union_ms/gb --dump-preds logs/g5_heldout_preds.json (CPU/GPU-light). 2) scratch/n2_narrator_state.py over those 666 rows on the 5070 (~15-30 min inference). 3) score agreement, per-device AUC of narrated score vs truth beside the classifier's, numeric fidelity.

## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_narration_state_fidelity_agreement]] (up).
Predicts [[eiv_state_auc_over_null_lab]] (up).
Predicts [[eiv_state_auc_over_null_freeliving]] (none).
Gate `g1` (blocking): agreement between narrated verdict and classifier decision >= 0.95 AND unparseable rows <= 3%
Gate `g2` (blocking): per-device narrated-state AUC vs truth within +/-0.02 of the classifier's own per-device AUC on the same out-of-fold predictions, for every device with n >= 30 and both classes present
Gate `g3` (advisory): the stress probability and the 30-minute mean HR placed in the prompt are repeated with numeric fidelity <= 0.02 (probability) and <= 0.5 bpm
Gate `g4` (blocking): Design: adapter disabled, greedy decode, the narrator is asked to end with an explicit 'Verdict: STRESS|FLOW' line which is what is parsed; a permutation null of the classifier probability against the labels is reported per device. No clinical-quality claim.
Cites [[sensorfm-2026-2605-22759]], [[feli-2025-cha-ppghr-agent-2502-12836]].

#optihealth #readout #target-style #medgemma #deployment-distribution #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
