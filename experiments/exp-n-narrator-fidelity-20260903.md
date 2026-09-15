---
artifacts:
- logs/n_narrator_v41.json
- logs/track_n.log
- scratch/n_narrator_prototype.py
closed: true
config_note: 'scratch/n_narrator_prototype.py. No training of any kind: the adapter
  bundle is loaded only because that is how the base MedGemma weights sit on disk
  (the HF repo is gated on this host), and generation runs inside `model.disable_adapter()`
  with NO soft tokens -- the prompt is text. Each of the 128 split-honest rows gets
  the attention head''s own per-row hr_raw and hr_peak (read from outputs/t1b_tokens_v41/heldout_predictions_trained.json,
  not recomputed) written into a three-sentence clinical-description request; the
  scorer''s number regex then reads the narration back. Numbers are attributed to
  slots by NEAREST VALUE rather than by position, so a narrator that reorders or rounds
  is not scored as a failure. Greedy decode, 140 new tokens.'
cost:
  gpu_hours: 0.25
  wall_hours: 0.3
crash_reason: ''
created_at: '2026-09-02T22:32:07+00:00'
diagnostics_measured:
- after: 0.0245
  before: 2.843
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
- after: 0.8145
  before: 2.843
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- Do not re-measure numeric fidelity of the narrator on HR. It is 0.0245 bpm with
  a worst row of 0.05 and zero rows above 0.5 -- the entire residual is one-decimal
  rounding, and there is no headroom left to study. What is untested is the STATE
  clause, which needs the labelled g5 pool and a prompt carrying the GBM's probability.
- 'Do not pass a BatchEncoding from apply_chat_template straight into model.generate:
  transformers raises a bare AttributeError from tokenization_utils_base.__getattr__
  with no message. Take enc[''input_ids''] when the return is not a tensor.'
external_run: logs/n_narrator_v41.json
former_ids: []
gate_results:
- evidence: 'narration-vs-prompt hr_raw MAE 0.0245 bpm against a <= 0.5 bar, worst
    single row 0.05 bpm, ZERO rows above 0.5, and 0/128 unparseable against a <= 3%
    bar. hr_peak fidelity 0.0230, worst 0.05. The whole residual is one-decimal rounding:
    handed 74.494, the narration says 74.5.'
  gate_id: g1
  passed: true
- evidence: 'End-to-end narrated hr_raw vs truth 0.8145 bpm against a <= 1.312 bar
    (head 0.8119 + 0.5), with the trivial always-predict-median at 6.876 on the same
    rows. The soft-token path emits 2.843 on those rows, so the product path is 2.03
    bpm better. hr_peak: narrated 11.167 vs head 11.169 vs soft-token 14.288 vs trivial
    17.979.'
  gate_id: g2
  passed: true
- evidence: 'NOT EVALUATED, and recorded as unmet rather than skipped. The prompt
    carried only the head''s two HR numbers -- no state probability -- and the 128
    rows come from data/eval_v3_1min, whose windows carry no momentary flow/stress
    labels, so there is nothing on these rows to score a state clause against. The
    state half of the narrator is therefore UNTESTED: this experiment licenses ''the
    narrator carries numbers faithfully'', not ''the narrator carries state''. Testing
    it needs the C-1 pool (which has the labels) and a prompt that includes the GBM''s
    probability.'
  gate_id: g3
  passed: false
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path
id: exp-n-narrator-fidelity-20260903
interpretation:
  at: '2026-09-03T06:43:59+00:00'
  failure_mode: null
  lesson: 'A base MedGemma with the LoRA DISABLED and no soft tokens, handed the attention
    head''s two numbers in the prompt, repeats them at 0.0245 bpm fidelity (worst
    row 0.05, 0/128 unparseable), so the head->narrator path reads 0.814 bpm vs truth
    against the trained soft-token path''s 2.843 and a 6.876 trivial — strictly better
    with zero training. Scope: this licenses "the narrator carries NUMBERS faithfully";
    the state clause (g3) was never evaluated because the 128 eval_v3 rows carry no
    momentary labels and the prompt carried no state probability, and no clinical-quality
    rubric was run. Product decision this supports: the LLM belongs DOWNSTREAM of
    heads (SensorFM / PH-LLM / CHA-PPGHR shape); Phase C''s JSON-slot objective is
    no longer what the product needs.'
  transfers_to:
  - d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss
  - a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission
key_numbers:
  fidelity_hr_peak_mae_bpm: 0.023
  fidelity_hr_raw_mae_bpm: 0.0245
  fidelity_worst_row_bpm: 0.05
  head_hr_raw_vs_truth: 0.8119
  n: 128.0
  narrated_hr_peak_vs_truth: 11.167
  narrated_hr_raw_vs_truth: 0.8145
  rows_above_half_bpm: 0.0
  soft_token_hr_peak_vs_truth: 14.288
  soft_token_hr_raw_vs_truth: 2.843
  trivial_hr_peak: 17.979
  trivial_hr_raw: 6.876
  unparseable_rate: 0.0
observed_effects:
- delta: -2.8185
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  direction: down
- delta: -2.0285
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-a-hr-tower-localisation-20260903
prediction_match:
  overall: true
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: true
    eiv_narration_numeric_fidelity_mae_bpm: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: supports
---

## Summary
Tests hypothesis [[n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path]].
Branches from [[exp-a-hr-tower-localisation-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/n_narrator_v41.json

## Measurements
- [[eiv_narration_numeric_fidelity_mae_bpm]]: before 2.843 → after 0.0245  (Δ -2.8185, down ✓ predicted down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 2.843 → after 0.8145  (Δ -2.0285, down ✓ predicted down)

## Gates
- `g1`: PASS — narration-vs-prompt hr_raw MAE 0.0245 bpm against a <= 0.5 bar, worst single row 0.05 bpm, ZERO rows above 0.5, and 0/128 unparseable against a <= 3% bar. hr_peak fidelity 0.0230, worst 0.05. The whole residual is one-decimal rounding: handed 74.494, the narration says 74.5.
- `g2`: PASS — End-to-end narrated hr_raw vs truth 0.8145 bpm against a <= 1.312 bar (head 0.8119 + 0.5), with the trivial always-predict-median at 6.876 on the same rows. The soft-token path emits 2.843 on those rows, so the product path is 2.03 bpm better. hr_peak: narrated 11.167 vs head 11.169 vs soft-token 14.288 vs trivial 17.979.
- `g3`: **FAIL** — NOT EVALUATED, and recorded as unmet rather than skipped. The prompt carried only the head's two HR numbers -- no state probability -- and the 128 rows come from data/eval_v3_1min, whose windows carry no momentary flow/stress labels, so there is nothing on these rows to score a state clause against. The state half of the narrator is therefore UNTESTED: this experiment licenses 'the narrator carries numbers faithfully', not 'the narrator carries state'. Testing it needs the C-1 pool (which has the labels) and a prompt that includes the GBM's probability.

**Verdict:** supports

## Interpretation
A base MedGemma with the LoRA DISABLED and no soft tokens, handed the attention head's two numbers in the prompt, repeats them at 0.0245 bpm fidelity (worst row 0.05, 0/128 unparseable), so the head->narrator path reads 0.814 bpm vs truth against the trained soft-token path's 2.843 and a 6.876 trivial — strictly better with zero training. Scope: this licenses "the narrator carries NUMBERS faithfully"; the state clause (g3) was never evaluated because the 128 eval_v3 rows carry no momentary labels and the prompt carried no state probability, and no clinical-quality rubric was run. Product decision this supports: the LLM belongs DOWNSTREAM of heads (SensorFM / PH-LLM / CHA-PPGHR shape); Phase C's JSON-slot objective is no longer what the product needs.

Transfers to: [[d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss]], [[a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission]]

## Key numbers
- `fidelity_hr_peak_mae_bpm`: 0.023
- `fidelity_hr_raw_mae_bpm`: 0.0245
- `fidelity_worst_row_bpm`: 0.05
- `head_hr_raw_vs_truth`: 0.8119
- `n`: 128
- `narrated_hr_peak_vs_truth`: 11.167
- `narrated_hr_raw_vs_truth`: 0.8145
- `rows_above_half_bpm`: 0
- `soft_token_hr_peak_vs_truth`: 14.288
- `soft_token_hr_raw_vs_truth`: 2.843
- `trivial_hr_peak`: 17.979
- `trivial_hr_raw`: 6.876
- `unparseable_rate`: 0

## Artifacts
- `logs/n_narrator_v41.json`
- `logs/track_n.log`
- `scratch/n_narrator_prototype.py`

## Do not retry
- Do not re-measure numeric fidelity of the narrator on HR. It is 0.0245 bpm with a worst row of 0.05 and zero rows above 0.5 -- the entire residual is one-decimal rounding, and there is no headroom left to study. What is untested is the STATE clause, which needs the labelled g5 pool and a prompt carrying the GBM's probability.
- Do not pass a BatchEncoding from apply_chat_template straight into model.generate: transformers raises a bare AttributeError from tokenization_utils_base.__getattr__ with no message. Take enc['input_ids'] when the return is not a tensor.

## Notes
THE NARRATOR CARRIES NUMBERS PERFECTLY, AND THE PRODUCT PATH IS 2.03 bpm BETTER THAN THE ONE WE SHIP. On the same 128 split-honest rows: head 0.8119 -> narration 0.8145 vs truth (the narrator ADDS 0.0026 bpm), against the soft-token path's 2.843 and a trivial 6.876. hr_peak 11.167 vs 14.288 vs 17.979. 0/128 narrations unparseable.

WHAT THIS IS EVIDENCE FOR, NARROWLY. That routing the number around the LM rather than through it costs nothing in transmission -- which is the one thing SensorFM (arXiv:2605.22759) and CHA-PPGHR (arXiv:2502.12836) both assume and neither measures. Combined with Track A (the LM's residual stream carries only 2.331 bpm of the 0.812 the tokens hold), it says the soft-token path's numeric loss is not recoverable at the decoder and is avoidable entirely at the interface.

WHAT IT IS NOT EVIDENCE FOR. (1) Clinical quality: no rubric was run, and SensorFM's five axes (Harm, Context, Personalization, Justifiability, Relevance) are the instrument for that. (2) State: gate g3 is recorded FAILED-because-unevaluated -- the prompt carried no state probability and the eval bundle's rows carry no momentary labels. (3) Anything about a narrator that must also decide WHAT to say: this one was handed two numbers and asked to repeat them in prose.

ONE FINDING WORTH KEEPING FOR ANY FUTURE PROMPT WORK: base MedGemma with the LoRA DISABLED does this. No sensor adapter, no fine-tune, no soft tokens.

#optihealth #sensor-text #medgemma #target-style #deployment-distribution #readout #optihealth-eiv #matched-baseline #conditioning-collapse #subject-heldout
