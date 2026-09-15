---
artifacts:
- logs/n3_narrator_label.json
- logs/n3_narrator_label_rescore.json
- scratch/n2_narrator_state.py
- scratch/run_n3_gpu.sh
closed: true
config_note: scratch/n2_narrator_state.py --prompt-mode label; everything else byte-identical
  to N2 attempt 2 (prefill, 320 tokens, greedy, adapter disabled, union_ms/gb OOF
  probabilities from logs/g5_heldout_preds.json).
cost:
  gpu_hours: 1.2
  wall_hours: 2.3
crash_reason: ''
created_at: '2026-09-03T10:05:13+00:00'
diagnostics_measured:
- after: 1.0
  before: 0.952
  diagnostic_id: eiv_narration_state_fidelity_agreement
- after: 0.362
  before: 0.308
  diagnostic_id: eiv_state_auc_over_null_lab
- after: -0.016
  before: -0.012
  diagnostic_id: eiv_state_auc_over_null_freeliving
- after: 0.0252
  before: 0.0252
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: agreement 1.000 on 666/666 rows (WESAD 148/148, LifeSnaps 442/442), unparseable
    0. Zero disagreements against N2's 32.
  gate_id: g1
  passed: true
- evidence: 'Narrated verdict AUC equals the thresholded classifier exactly on both
    devices: WESAD 0.8657 vs 0.8657, LifeSnaps 0.4842 vs 0.4842 (agreement is 1.0
    so the two are the same vector). Narrated-probability AUC WESAD 0.9396 vs the
    classifier''s continuous 0.9728 -- the residual is percent rounding of the repeated
    number, not disagreement.'
  gate_id: g2
  passed: true
- evidence: Probability repeated on 666/666 rows (as decimal or percent), MAE 0.0026,
    within 0.01 on 100%; 30-min mean HR MAE 0.0252 bpm.
  gate_id: g3
  passed: true
- evidence: Identical script and settings to N2 attempt 2; the only change is --prompt-mode
    label. One relaunch after a network error at model load (HF hub SSL EOF); HF_HUB_OFFLINE=1
    added to the driver, no code change.
  gate_id: g4
  passed: true
git_ref: fix/soft-token-norm-scale@7b816cc+dirty
hypothesis_id: n3-narrator-handed-the-classifier-s-decision-label-carries-it-at-or-above-0-99
id: exp-n3-narrator-label-fidelity-20260903
interpretation:
  at: '2026-09-03T12:20:20+00:00'
  failure_mode: null
  lesson: 'Handed the head''s DECISION as a label (threshold applied upstream) plus
    the probability as a number to repeat, base MedGemma-1.5-4b-it with the adapter
    disabled carries state with zero loss: 666/666 agreement, 0 unparseable, probability
    MAE 0.0026, HR 0.025 bpm, narrated verdict AUC identical to the thresholded classifier
    on both devices (WESAD 0.866, LifeSnaps 0.484). Against N2''s 95.2% with a probability-plus-rule
    prompt, the entire failure was the rule. The narrator architecture is therefore
    settled at the fidelity level: HR head (0.812 bpm) + state classifier + narrator
    handed labels and numbers = the product path, with no training and no soft tokens;
    what the narrator says clinically is a separate, rubric-scored question that has
    not been asked.'
  transfers_to:
  - n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device
  - n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path
key_numbers:
  agreement: 1.0
  hr_fidelity_mae_bpm: 0.0252
  lifesnaps_clf_thresholded_auc: 0.4842
  lifesnaps_narrated_verdict_auc: 0.4842
  n: 666.0
  n2_agreement: 0.952
  prob_fidelity_mae: 0.0026
  unparseable_rate: 0.0
  wesad_clf_prob_auc: 0.9728
  wesad_clf_thresholded_auc: 0.8657
  wesad_narrated_prob_auc: 0.9396
  wesad_narrated_verdict_auc: 0.8657
observed_effects:
- delta: 0.048
  diagnostic_id: eiv_narration_state_fidelity_agreement
  direction: up
- delta: 0.054
  diagnostic_id: eiv_state_auc_over_null_lab
  direction: up
- delta: -0.004
  diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: down
- delta: 0.0
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  direction: none
parent_experiment_id: exp-n2-narrator-state-fidelity-20260903
prediction_match:
  overall: true
  per_diagnostic:
    eiv_narration_state_fidelity_agreement: true
    eiv_state_auc_over_null_freeliving: false
    eiv_state_auc_over_null_lab: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@7b816cc+dirty
verdict: supports
---

## Summary
Tests hypothesis [[n3-narrator-handed-the-classifier-s-decision-label-carries-it-at-or-above-0-99]].
Branches from [[exp-n2-narrator-state-fidelity-20260903]].
At commit `fix/soft-token-norm-scale@7b816cc+dirty`.

## Measurements
- [[eiv_narration_state_fidelity_agreement]]: before 0.952 → after 1.0  (Δ +0.048, up ✓ predicted up)
- [[eiv_state_auc_over_null_lab]]: before 0.308 → after 0.362  (Δ +0.054, up ✓ predicted up)
- [[eiv_state_auc_over_null_freeliving]]: before -0.012 → after -0.016  (Δ -0.004, down ✗ predicted none)
- [[eiv_narration_numeric_fidelity_mae_bpm]]: before 0.0252 → after 0.0252  (Δ +0, flat)

## Gates
- `g1`: PASS — agreement 1.000 on 666/666 rows (WESAD 148/148, LifeSnaps 442/442), unparseable 0. Zero disagreements against N2's 32.
- `g2`: PASS — Narrated verdict AUC equals the thresholded classifier exactly on both devices: WESAD 0.8657 vs 0.8657, LifeSnaps 0.4842 vs 0.4842 (agreement is 1.0 so the two are the same vector). Narrated-probability AUC WESAD 0.9396 vs the classifier's continuous 0.9728 -- the residual is percent rounding of the repeated number, not disagreement.
- `g3`: PASS — Probability repeated on 666/666 rows (as decimal or percent), MAE 0.0026, within 0.01 on 100%; 30-min mean HR MAE 0.0252 bpm.
- `g4`: PASS — Identical script and settings to N2 attempt 2; the only change is --prompt-mode label. One relaunch after a network error at model load (HF hub SSL EOF); HF_HUB_OFFLINE=1 added to the driver, no code change.

**Verdict:** supports

## Interpretation
Handed the head's DECISION as a label (threshold applied upstream) plus the probability as a number to repeat, base MedGemma-1.5-4b-it with the adapter disabled carries state with zero loss: 666/666 agreement, 0 unparseable, probability MAE 0.0026, HR 0.025 bpm, narrated verdict AUC identical to the thresholded classifier on both devices (WESAD 0.866, LifeSnaps 0.484). Against N2's 95.2% with a probability-plus-rule prompt, the entire failure was the rule. The narrator architecture is therefore settled at the fidelity level: HR head (0.812 bpm) + state classifier + narrator handed labels and numbers = the product path, with no training and no soft tokens; what the narrator says clinically is a separate, rubric-scored question that has not been asked.

Transfers to: [[n2-narrator-carries-the-classifier-s-state-decision-faithfully-per-device]], [[n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path]]

## Key numbers
- `agreement`: 1
- `hr_fidelity_mae_bpm`: 0.0252
- `lifesnaps_clf_thresholded_auc`: 0.4842
- `lifesnaps_narrated_verdict_auc`: 0.4842
- `n`: 666
- `n2_agreement`: 0.952
- `prob_fidelity_mae`: 0.0026
- `unparseable_rate`: 0
- `wesad_clf_prob_auc`: 0.9728
- `wesad_clf_thresholded_auc`: 0.8657
- `wesad_narrated_prob_auc`: 0.9396
- `wesad_narrated_verdict_auc`: 0.8657

## Artifacts
- `logs/n3_narrator_label.json`
- `logs/n3_narrator_label_rescore.json`
- `scratch/n2_narrator_state.py`
- `scratch/run_n3_gpu.sh`

## Notes
The eiv_dense128_hr_raw_mae_bpm control does not apply: the HR in the prompt is a tensor-derived 30-minute mean checked only for repetition. Together with exp-n (numbers, 0.0245 bpm) this closes the narrator's fidelity question on both halves: base MedGemma, adapter disabled, carries a head's numbers and its decision without loss when the decision is handed to it as a label. What it does not measure: clinical quality of the prose (needs SensorFM's five-axis rubric) and anything about a narrator that has to decide what to say.

#optihealth #readout #target-style #medgemma #deployment-distribution #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
