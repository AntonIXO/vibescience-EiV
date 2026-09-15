---
ambition_class: reframe
created_at: '2026-09-02T21:06:37+00:00'
former_ids: []
gap_closure_estimate: 0.6
gates:
- blocking: true
  description: narration-vs-prompt hr_raw MAE <= 0.5 bpm on the 128 rows; unparseable
    <= 3%
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  id: g1
  mde: 0.3
- blocking: true
  description: end-to-end narration hr_raw vs truth <= head MAE + 0.5 (i.e. <= 1.3
    bpm) on the same rows, always-median 6.876 beside it
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g2
  mde: 0.5
- blocking: false
  description: 'state clause agreement with label at z >= 3 against a label-permutation
    null and within 0.03 AUC of the GBM''s own AUC on those rows (advisory: the state
    slot is the GBM''s, not the LLM''s)'
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  id: g3
  mde: null
id: n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path
interventions: []
override_reason: ''
paper_refs:
- sensorfm-2026-2605-22759
- feli-2025-cha-ppghr-agent-2502-12836
- phllm-2024-personal-health-llm-2406-06474
- tan-2024-llm-useful-timeseries-2406-16964
predicted_effects:
- diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  direction: down
  magnitude_note: <= 0.5 bpm between the number in the narration and the number in
    the prompt; unparseable rate <= 3%.
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
  magnitude_note: the product-path number becomes the head's 0.812 (+ fidelity error),
    vs 2.843 for the soft-token path on the same rows.
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- matched-baseline
- conditioning-collapse
- subject-heldout
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- optihealth
- sensor-text
- medgemma
- target-style
- deployment-distribution
- readout
---

## Statement
NARRATOR ARCHITECTURE (no training; prompt-level prototype). MedGemma-1.5-4b-it given, in its prompt, the attention head's hr_raw / hr_peak (0.812 / 11.17 bpm MAE on the 128 split-honest rows) and the multi-scale GBM's state probability, and asked for a short clinical description of the day, (1) repeats the numbers it was given with eiv_narration_numeric_fidelity_mae_bpm <= 0.5 bpm and an unparseable-narration rate <= 3%, so the product's shipped hr_raw error is the HEAD's 0.812 rather than the soft-token path's 2.843; and (2) its state clause agrees with the label above a label-permutation null (z >= 3) at the GBM's own AUC, i.e. the LLM neither adds nor destroys state information. This is SensorFM condition A (arXiv:2605.22759, FM predictions in context, indistinguishable from ground truth in 1,860 clinician ratings) and CHA-PPGHR (arXiv:2502.12836, agent 2.8 vs LLM-direct 8.9 bpm) instantiated on EiV's own numbers. NOT predicted: that the narration is clinically good -- that needs a rubric, not a scorer.

## Rationale
Every published wearable-health system in OVERVIEW.md that reports a NUMERIC target puts the LLM downstream of a head or tool (SensorFM, PH-LLM, PHIA, CHA-PPGHR); none reports soft-token injection beating a head. EiV's own tower says the same: head 0.812 vs LLM 2.843 on identical tokens (t1b). The narrator is therefore the product architecture the record supports today; what nobody measures is whether the narrator FAITHFULLY carries the numbers it is given, which is the one thing the product must guarantee. Costs one afternoon and no GPU training (inference on 128 rows). If fidelity fails (LLM rewrites numbers), the fix is constrained decoding of the numeric span, not more training.

## Plan
scratch/narrator_prototype.py: for each of the 128 rows, build a prompt with demographics-free context (head hr_raw, hr_peak, GBM state prob, daily coverage) and ask base MedGemma (no LoRA) for a 3-sentence clinical description; extract numbers with the existing scorer regex; compute fidelity vs prompt and MAE vs truth; state-clause agreement vs label with 200 permutations. GPU inference only, ~20 min. No new training. Register the measured fidelity sigma from 3 decode seeds.

## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_narration_numeric_fidelity_mae_bpm]] (down).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (down).
Gate `g1` (blocking): narration-vs-prompt hr_raw MAE <= 0.5 bpm on the 128 rows; unparseable <= 3%
Gate `g2` (blocking): end-to-end narration hr_raw vs truth <= head MAE + 0.5 (i.e. <= 1.3 bpm) on the same rows, always-median 6.876 beside it
Gate `g3` (advisory): state clause agreement with label at z >= 3 against a label-permutation null and within 0.03 AUC of the GBM's own AUC on those rows (advisory: the state slot is the GBM's, not the LLM's)
Cites [[sensorfm-2026-2605-22759]], [[feli-2025-cha-ppghr-agent-2502-12836]], [[phllm-2024-personal-health-llm-2406-06474]], [[tan-2024-llm-useful-timeseries-2406-16964]].

#optihealth #sensor-text #medgemma #target-style #deployment-distribution #readout #optihealth-eiv #matched-baseline #conditioning-collapse #subject-heldout
