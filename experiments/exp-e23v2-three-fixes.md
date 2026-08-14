---
artifacts: []
closed: true
config_note: 'profile lora_proxy_12ch_e23v2, 2600 steps, frozen encoder, MedGemma-1.5-4b-it
  4bit QLoRA, RTX 5070. THREE stacked fixes: (1) label-consistent sampler at strength
  0.5, (2) zero-HR contamination filter min_valid_hr_bpm=30.0 (drops 1830/8774), (3)
  hr_augment re-anchored on the PEAK instead of the MEAN. Final loss 1.720, elapsed
  2247s. Adapter outputs/adapters/medgemma_12ch_e23v2. Evidence: outputs/probes/e23v2_gates.json,
  e23g_peak_decodability.json, e23j_teacher_forced.json.'
crash_reason: ''
created_at: '2026-08-05T11:05:13+00:00'
diagnostics_measured:
- after: 0.309
  before: 0.5
  diagnostic_id: eiv_train_label_marginal_max
- after: 0.0
  before: 0.257
  diagnostic_id: eiv_gen_pred_bucket_entropy
- after: 0.159
  before: 0.68
  diagnostic_id: eiv_gen_peak_corr
external_run: local:outputs/adapters/medgemma_12ch_e23v2
gate_results:
- evidence: ''
  gate_id: gate-marginal-not-saturated
  passed: true
- evidence: ''
  gate_id: gate-prediction-entropy
  passed: false
- evidence: ''
  gate_id: gate-peak-corr-positive
  passed: false
- evidence: ''
  gate_id: gate-no-hr-raw-regression
  passed: true
- evidence: ''
  gate_id: gate-p2-conditioning-preserved
  passed: false
git_ref: ''
hypothesis_id: hyp-eiv-label-consistent-sampler-20260805
id: exp-e23v2-three-fixes
observed_effects:
- delta: -0.191
  diagnostic_id: eiv_train_label_marginal_max
  direction: down
- delta: -0.257
  diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: down
- delta: -0.521
  diagnostic_id: eiv_gen_peak_corr
  direction: down
parent_experiment_id: exp-e23-label-consistent-sampler-run1
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_gen_pred_bucket_entropy: false
    eiv_train_label_marginal_max: true
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-eiv-label-consistent-sampler-20260805]].
Branches from [[exp-e23-label-consistent-sampler-run1]].
External run: local:outputs/adapters/medgemma_12ch_e23v2

## Measurements
- [[eiv_train_label_marginal_max]]: before 0.5 → after 0.309  (Δ -0.191, down ✓ predicted down)
- [[eiv_gen_pred_bucket_entropy]]: before 0.257 → after 0.0  (Δ -0.257, down ✗ predicted up)
- [[eiv_gen_peak_corr]]: before 0.68 → after 0.159  (Δ -0.521, down ✗ predicted up)

## Gates
- `gate-marginal-not-saturated`: PASS
- `gate-prediction-entropy`: **FAIL**
- `gate-peak-corr-positive`: **FAIL**
- `gate-no-hr-raw-regression`: PASS
- `gate-p2-conditioning-preserved`: **FAIL**

**Verdict:** refutes

## Notes
VERDICT: the marginal hypothesis is REFUTED. Balancing the training distribution did not fix the collapse and made the primary metric worse (entropy 0.257 -> 0.000, 91/91 tachycardic).

What is now RULED OUT for the hr_peak/bucket collapse, each with direct measurement:

1. TRAINING MARGINAL (refuted). 400 real targets dumped from InstructionDataset._load_raw with augmentation live: elevated 30.75 / tachycardic 25.50 / resting 22.00 / bradycardic 21.75, hr_peak values p25=90 p50=103 p75=120. The LM is shown a balanced distribution and still emits one label. 'Always tachycardic' is NOT loss-optimal here, so the shortcut story cannot hold. (scratch/diag_e23h_actual_targets.py)

2. REPRESENTATION LIMIT (refuted). Subject-held-out GroupKFold Ridge on the soft-token prefix of THIS adapter: hr_mean R2=0.992 MAE 0.7, hr_peak R2=0.753 MAE 5.5, hr_std R2=0.969 MAE 1.2 (n=600). The peak is present and linearly decodable. My mean-pooling-destroys-the-max hypothesis was wrong. (scratch/diag_e23g_peak_decodability.py)

3. TRAIN/EVAL PROMPT SKEW (refuted). The training prompt from the dataset and prompts/json_slots_prompt.md are byte-identical. (scratch/diag_e23i_prompt_skew.py)

4. ZERO-HR CONTAMINATION (real, fixed, but not the cause). 1830 samples removed; the collapse persists without them. Its main effect was to FAKE a passing peak-corr gate in run 1 (+0.680 -> +0.159 once removed).

WHAT THE EVIDENCE NOW POINTS AT - a slot-level conditioning failure, not a global one:

Teacher-forced scoring of the four candidate bucket strings (scratch/diag_e23j_teacher_forced.py, n=32 label-stratified): argmax accuracy 0.250 with the MATCHED prefix and 0.250 with a MISMATCHED prefix - both exactly chance, and the argmax is 'bradycardic' for all 32 samples regardless of true label OR of which sample's prefix is supplied. The bucket slot's logits have ZERO measurable dependence on the sensor input.

Meanwhile the SAME prefix in the SAME forward pass drives hr_raw generation to corr +0.604 (full-coverage) and +0.909 (sparse). So conditioning is alive; it just never reached the categorical slot.

Note the interesting inversion: teacher-forced argmax is always 'bradycardic' while free-running generation is always 'tachycardic'. The slot is being decided by position-dependent template priors rather than by content - consistent with the ICLR 2024 additive-prefix result already on file (an additive prefix adds a fixed bias and cannot change relative attention over content). A continuous @@-value slot can absorb a fixed additive bias into its magnitude; a 4-way categorical choice over discrete token strings cannot.

NEXT LEVER (do not run another sampler/marginal variant): the bucket slot needs a path where the prefix can change RELATIVE attention - zero-init gated cross-attention (LLaMA-Adapter, arXiv:2303.16199), already flagged in the skill as the E20 follow-up - or simply derive heart_rate_bucket deterministically from the generated hr_peak value at decode time, since hr_peak is decodable at R2=0.753 and hr_raw already generates well. The second option is cheap and testable immediately.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #training-instability
