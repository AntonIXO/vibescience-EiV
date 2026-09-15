---
ambition_class: mechanism
created_at: '2026-09-01T08:24:33+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'g1 BLOCKING: natively-closed JSON >= 128/128 on the dense draw, i.e.
    no worse than v36labels (128/128) under the repeat-key stop criterion. A REGRESSION
    here is the informative outcome: it would mean the truncated tail was load-bearing
    for termination in a way opposite to the prediction.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g1-termination-holds
  mde: 1.0
- blocking: true
  description: 'g2 BLOCKING: more than 2 distinct state values emitted across the
    dense 128 draw, against the near-constant prior this problem documents. This is
    the gate the skew makes testable for the first time -- the state enum has never
    been in a training prompt.'
  diagnostic_id: eiv_gen_pred_bucket_entropy
  id: g2-state-diversity
  mde: 0.0722
- blocking: true
  description: 'g3 BLOCKING: dense hr_raw MAE within k=2 of the incumbent''s 2.851
    at the MEASURED seed floor sigma 0.7566, i.e. [1.34, 4.36]. Note this band is
    measured today, not the asserted [2.23, 3.83] the campaign had been using. Failing
    g3 while passing g1/g2 is a REAL result: it would mean the whole prompt costs
    numeric grounding, a trade the product surface needs to know about.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-no-harm-hr-raw
  mde: 0.7566
id: closing-the-train-serve-prompt-skew-repairs-the-f13da1
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
  magnitude_note: 'Predicted UP but weakly. The registered seed floor is 0.0722 against
    calibrated arms sitting at 0.21-0.40, so a k=2 band of +/-0.144 spans most of
    the observed range: this diagnostic can currently only separate a hard collapse
    from a non-collapse. The state-diversity gate is therefore stated as a COUNT of
    distinct values rather than as an entropy delta, and entropy is recorded as context,
    not as the decisive number.'
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: none
  magnitude_note: Predicted NO CHANGE -- this is a no-harm gate, not a win condition.
    The mechanism under test is schema compliance and vocabulary, not numeric reading;
    a large move in EITHER direction on hr_raw would be evidence the prompt change
    did something other than what is claimed. Judged against the measured seed floor
    0.7566 (k=2 band [1.34, 4.36] around the incumbent's 2.851), not the asserted
    [2.23, 3.83].
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- conditioning-collapse
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: proposed
supersedes: null
topic_tags:
- target-style
- optihealth
- medgemma
- deployment-distribution
- readout
---

## Statement
CLOSING THE TRAIN/SERVE PROMPT SKEW REPAIRS THE TWO DEFECTS THAT LIVE IN THE TRUNCATED TAIL. Training Phase C with EIV_RESOLVE_NESTED_CONTEXT=1 (train prompt budget 256 -> 456, so the model is trained on the same 317-token json_slots prompt it is already tested on) will hold JSON termination at 128/128 dense and raise emitted state diversity above 2 distinct values, without harming dense hr_raw MAE beyond the measured seed floor.

## Rationale
MEASURED TODAY, NOT INFERRED (scratch/_train_serve_prompt_skew.py). The json_slots prompt is 317 tokens. The TRAIN budget resolves to 256 because resolve_model_context_tokens read only the config top level and medgemma-1.5-4b-it is a Gemma3Config whose context lives under text_config, so the resolver returned None and resolve_prompt_token_budget took its 256 auto-fallback. The SERVE budget is a hardcoded 512 in scratch/emission_diversity_audit.py:237. 512 > 317, so the scorer has always fed the whole prompt. Every arm v28..v37 was therefore TRAINED on a decapitated prompt and TESTED on the whole one -- a 61-token skew nobody had noticed, because the vault recorded the truncation as symmetric ('silently truncated at training and inference').

WHY THOSE 61 TOKENS AND NOT ANY OTHER 61. They are, verbatim, the tail of the schema: the `temp` enum, the ENTIRE `state` enum (stress/flow/neutral/burnout), and 'Do NOT include any markdown formatting, code blocks, or explanatory text. Output ONLY the JSON object.' Two open defects of this project sit exactly there:
  (1) the state slot is documented as a near-constant prior with arm-dependent polarity (v5c stress 139/200, v4w neutral 198/200, v5d flow 65/200) -- and its vocabulary was never in the training prompt;
  (2) the JSON-termination defect that blocked v33's promotion loops on '"temp":"normal","state":"flow"' -- the two keys whose definitions are cut -- while the 'output ONLY the JSON object' instruction is cut alongside them.
Neither is proof. Both become testable for the first time.

ONE VARIABLE: EIV_RESOLVE_NESTED_CONTEXT=1, off the incumbent's Phase B checkpoint, same C profile, same seed, same scorer. The serve side is deliberately untouched -- passing the flag to the scorer would be a no-op since it never calls the resolver, and leaving it alone keeps the scoring path byte-identical to every previous arm's.

PRIOR ~45%. Against: v36labels already reached 128/128 termination through captions alone, so the ceiling on g1 may already be reached and the arm can only fail to regress it. For: the state defect has survived every intervention tried so far (v29 all-heads, v30 state_patch on a healthy substrate, v32 detached probes), and none of those addressed the possibility that the model was never told what the state vocabulary IS. Calibration 0.408; the gates decide, not this number.

## Plan


## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_gen_pred_bucket_entropy]] (up).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (none).
Gate `g1-termination-holds` (blocking): g1 BLOCKING: natively-closed JSON >= 128/128 on the dense draw, i.e. no worse than v36labels (128/128) under the repeat-key stop criterion. A REGRESSION here is the informative outcome: it would mean the truncated tail was load-bearing for termination in a way opposite to the prediction.
Gate `g2-state-diversity` (blocking): g2 BLOCKING: more than 2 distinct state values emitted across the dense 128 draw, against the near-constant prior this problem documents. This is the gate the skew makes testable for the first time -- the state enum has never been in a training prompt.
Gate `g3-no-harm-hr-raw` (blocking): g3 BLOCKING: dense hr_raw MAE within k=2 of the incumbent's 2.851 at the MEASURED seed floor sigma 0.7566, i.e. [1.34, 4.36]. Note this band is measured today, not the asserted [2.23, 3.83] the campaign had been using. Failing g3 while passing g1/g2 is a REAL result: it would mean the whole prompt costs numeric grounding, a trade the product surface needs to know about.

#target-style #optihealth #medgemma #deployment-distribution #readout #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
