---
artifacts: []
closed: true
config_note: 'profile lora_proxy_12ch_e23, 2600 steps, frozen encoder, MedGemma-1.5-4b-it
  4bit QLoRA, RTX 5070. Label-consistent sampler ONLY (sampler_balance_strength=0.5,
  sampler_weight_cap=4.0). E22-corrected channel_validity_mask at all inference sites
  from step 0. Final loss 1.268, elapsed 2253s. Adapter: outputs/adapters/medgemma_12ch_e23.
  Eval: scratch/eval_e23_gates.py, label-stratified, coverage-split at 0.5, n=96 ->
  outputs/probes/e23_gates.json. Required PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True
  to avoid OOM.'
crash_reason: ''
created_at: '2026-08-05T10:20:27+00:00'
diagnostics_measured:
- after: 0.2078
  before: 0.5
  diagnostic_id: eiv_train_label_marginal_max
- after: 0.257
  before: 0.0
  diagnostic_id: eiv_gen_pred_bucket_entropy
- after: 0.68
  before: -0.013
  diagnostic_id: eiv_gen_peak_corr
do_not_retry: []
external_run: local:outputs/adapters/medgemma_12ch_e23
former_ids: []
gate_results:
- evidence: ''
  gate_id: gate-marginal-not-saturated
  passed: true
- evidence: ''
  gate_id: gate-prediction-entropy
  passed: false
- evidence: ''
  gate_id: gate-peak-corr-positive
  passed: true
- evidence: ''
  gate_id: gate-no-hr-raw-regression
  passed: false
git_ref: ''
hypothesis_id: hyp-eiv-label-consistent-sampler-20260805
id: exp-e23-label-consistent-sampler-run1
key_numbers: {}
observed_effects:
- delta: -0.2922
  diagnostic_id: eiv_train_label_marginal_max
  direction: down
- delta: 0.257
  diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
- delta: 0.693
  diagnostic_id: eiv_gen_peak_corr
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
    eiv_gen_pred_bucket_entropy: true
    eiv_train_label_marginal_max: true
provenance: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-eiv-label-consistent-sampler-20260805]].
External run: local:outputs/adapters/medgemma_12ch_e23

## Measurements
- [[eiv_train_label_marginal_max]]: before 0.5 → after 0.2078  (Δ -0.2922, down ✓ predicted down)
- [[eiv_gen_pred_bucket_entropy]]: before 0.0 → after 0.257  (Δ +0.257, up ✓ predicted up)
- [[eiv_gen_peak_corr]]: before -0.013 → after 0.68  (Δ +0.693, up ✓ predicted up)

## Gates
- `gate-marginal-not-saturated`: PASS
- `gate-prediction-entropy`: **FAIL**
- `gate-peak-corr-positive`: PASS
- `gate-no-hr-raw-regression`: **FAIL**

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
VERDICT: the sampler/target partition mismatch was REAL and is FIXED, but it was NOT SUFFICIENT. The primary preregistered gate (predicted-bucket entropy >= 0.50) FAILED at 0.257, and the full-coverage group is still 48/48 tachycardic. A 20.8% tachycardic training marginal cannot by itself produce a 100% saturated predictor, so I kept digging instead of declaring victory.

Two further independent mechanisms, both measured:

(2) AUGMENTER/LABEL STATISTIC MISMATCH (scratch/diag_e23b_augment_peak_bias.py). hr_augment shifted the daily MEAN to U(40,135), but both the hr_peak slot and heart_rate_bucket are defined on the PEAK. The peak rides along at mean + spread (mean spread 32.1 bpm, median 28.1), so the augmented 40% of steps carried hr_peak centred at 119.5 bpm / median 118.8 -- sitting exactly on the 120 tachycardic boundary, 48.8% tachycardic and 67.6% elevated-or-above. The augmenter was manufacturing the very prior it was added to destroy. Fixed by anchoring the augmenter on the PEAK, the same statistic the label uses.

(3) ZERO-HR CONTAMINATION (scratch/diag_e23d_zero_hr.py). 1830 lifesnaps samples -- 20.9% of the lifesnaps+pmdata filter, 27.6% of lifesnaps, 0% of pmdata -- have the HR channel marked PRESENT by M_mask while every present value is exactly 0.0 bpm (within-sample zero fraction 1.000). They pass min_coverage and were training the LM to emit hr_peak=0. Through hr_label_bucket(0,0) they all label 'bradycardic', so 72.0% of ALL bradycardic targets were garbage. Removing them changes the natural label marginal from 28.95/25.18/28.78/17.10 to 10.22/31.81/36.36/21.60 -- i.e. the corpus statistics every prior analysis relied on were themselves contaminated. Fixed by min_valid_hr_bpm=30.0 in InstructionDataset.

METHODOLOGICAL NOTE worth keeping: the peak-corr gate PASSED (+0.680) on a model that was demonstrably degenerate. That pass was manufactured by mechanism (3): a bimodal true distribution (0 bpm vs ~120 bpm) lets 'predict ~0 for zero samples, ~120 otherwise' score high correlation with zero real skill. Full-coverage-only corr was +0.086 with predictions pinned to [120,130]. The preregistered ENTROPY gate is what caught the truth. This is direct evidence for preferring distributional gates over correlation gates when the failure mode is a saturated prior -- and for the standing rule to split by HR coverage rather than trust a pooled number.

All three fixes verified together pre-run (scratch/diag_e23_v2_combined.py): impossible hr_peak values 11.6% -> 0.0%, tachycardic marginal 32.5% -> 20.6%, max label marginal 0.306. Successor run exp-e23v2 launched as profile lora_proxy_12ch_e23v2.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #training-instability
