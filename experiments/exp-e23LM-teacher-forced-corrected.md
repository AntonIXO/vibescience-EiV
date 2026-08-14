---
artifacts: []
closed: true
config_note: 'Re-analysis of adapter outputs/adapters/medgemma_12ch_e23v2 (no retraining).
  Corrected the E23j teacher-forced scoring bug and quantified effect size vs template
  prior. Evidence: outputs/probes/e23L_teacher_forced_fixed.json, outputs/probes/e23M_effect_vs_prior.json.
  Scripts: scratch/diag_e23L_teacher_forced_fixed.py, scratch/diag_e23M_effect_vs_prior.py.'
crash_reason: ''
created_at: '2026-08-05T11:17:42+00:00'
diagnostics_measured:
- after: 0.418
  before: 0.0
  diagnostic_id: eiv_gen_pred_bucket_entropy
do_not_retry: []
external_run: local:outputs/adapters/medgemma_12ch_e23v2
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
  passed: false
- evidence: ''
  gate_id: gate-no-hr-raw-regression
  passed: true
- evidence: ''
  gate_id: gate-p2-conditioning-preserved
  passed: true
git_ref: ''
hypothesis_id: hyp-eiv-label-consistent-sampler-20260805
id: exp-e23LM-teacher-forced-corrected
key_numbers: {}
observed_effects:
- delta: 0.418
  diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
parent_experiment_id: exp-e23v2-three-fixes
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_gen_pred_bucket_entropy: true
    eiv_train_label_marginal_max: false
provenance: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-eiv-label-consistent-sampler-20260805]].
Branches from [[exp-e23v2-three-fixes]].
External run: local:outputs/adapters/medgemma_12ch_e23v2

## Measurements
- [[eiv_gen_pred_bucket_entropy]]: before 0.0 → after 0.418  (Δ +0.418, up ✓ predicted up)

## Gates
- `gate-marginal-not-saturated`: PASS
- `gate-prediction-entropy`: **FAIL**
- `gate-peak-corr-positive`: **FAIL**
- `gate-no-hr-raw-regression`: PASS
- `gate-p2-conditioning-preserved`: PASS

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
SELF-CORRECTION. Overturns my own E23j conclusion. No retraining - re-analysis of the same adapter.

THE BUG IN E23j: it scored '{"heart_rate_bucket":"@@' + cand + '@@"' and returned tokl.mean() over ALL tokens, including the ~10 shared prefix tokens identical across candidates. The four labels tokenise to different lengths, so each candidate's mean was normalised by a different divisor and the argmax was partly decided by token count. The tell I should have caught immediately: 'bradycardic' won on all 32 samples AND on every mismatched prefix - too clean to be behavioural.

CORRECTED TEST (E23L): score ONLY the candidate-distinctive tokens, and make the primary test a PAIRED prefix swap holding the string fixed at the true label: delta = logP(true | matched prefix) - logP(true | mismatched prefix). Identical tokens, length and position, so length/unigram bias cancels exactly.
RESULT n=40 label-stratified: mean delta +0.4181 nats, median +0.5384, frac positive 0.725, Wilcoxon p = 4.51e-02.
=> THE BUCKET SLOT DOES READ THE PREFIX. My 'zero prefix dependence' claim was an artifact and is retracted.

EFFECT SIZE (E23M), same n=40, one scale:
  PRIOR SPREAD (nats needed to change the argmax): mean 5.080, median 6.428, max 9.523
  PREFIX EFFECT (matched vs mismatched, same string): mean +0.418, median +0.538
  NULL CONTROL (matched vs norm-matched random gaussian prefix): mean -1.750, median -1.770
  RATIO effect/prior = 0.0823  ->  required gain ~12.2x

INTERPRETATION - replaces the 'dead slot' story with a quantitative one: the sensor signal reaching the categorical slot is real, correctly signed and statistically significant, but ~12x too small to overcome the fixed template/positional prior that decides the emitted token. That is why generation looks constant while the paired test shows genuine dependence. Gate-prediction-entropy therefore still FAILS at 0.000 - the behaviour did not change, only the explanation.

The NULL control is independently informative: a norm-matched RANDOM prefix costs 1.750 nats, 4.2x MORE than swapping in another real sample's prefix (0.418). The model is strongly sensitive to the prefix being well-formed sensor-manifold input and only weakly sensitive to WHICH sample it encodes - the learned prefix subspace carries mostly sample-invariant 'this is valid sensor data' mass with a small per-sample component. Consistent with E19/E20 geometry.

WHAT THIS DOES AND DOES NOT LICENCE:
- Does NOT support 'the architecture cannot work'. I overstated that.
- DOES set a concrete engineering target: ~an order of magnitude more per-sample logit influence at the categorical slot.
- Cheaper untested levers that could plausibly deliver it: value_loss_weight concentrated on the bucket token, LoRA on the LM head / final layers, unfreezing the projector (arm lora_proxy_12ch_attnfix already exists in configs/training.yaml), longer training, or reducing the template prior by shortening/reordering the JSON schema so the bucket is not slot 1. Gated cross-attention is one option among several, not the only route.

METHOD LESSON: never compare candidate strings of differing token length by mean logprob. Use a paired swap that holds the string fixed and changes only the conditioning, and always run a norm-matched random control.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #training-instability
