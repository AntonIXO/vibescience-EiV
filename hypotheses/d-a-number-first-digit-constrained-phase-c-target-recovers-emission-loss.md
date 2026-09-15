---
ambition_class: mechanism
created_at: '2026-09-02T20:55:18+00:00'
former_ids: []
gap_closure_estimate: 0.45
gates:
- blocking: true
  description: 'PRECONDITION: Track A''s eiv_lm_hidden_hr_raw_ridge_mae_bpm on v41rebased_c
    <= 1.6; otherwise this arm is not launched'
  diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
  id: g0
  mde: null
- blocking: true
  description: split-honest hr_raw <= 1.9 bpm on the 128 rows, paired CI95 vs 2.843
    entirely below zero
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g1
  mde: 0.5
- blocking: true
  description: scale (std ratio) in [0.85,1.35] -- read BEFORE g1
  diagnostic_id: eiv_gen_between_sample_std
  id: g2
  mde: null
- blocking: false
  description: hr_peak MAE not worse than 14.288 by the family band
  diagnostic_id: eiv_gen_peak_skill_bpm
  id: g3
  mde: null
- blocking: true
  description: 'one variable at the recipe level: Phase B checkpoint byte-identical
    to v41rebased/phase_b; the target format + prompt-budget fix + estimator are declared
    together as THE emission-side variable and no encoder/LoRA hyperparameter moves'
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g4
  mde: null
id: d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss
interventions: []
override_reason: ''
paper_refs:
- song-2025-decoding-based-regression-2501-19383
- chen-2025-rl-decoding-regression-2512-06533
- tan-2024-llm-useful-timeseries-2406-16964
predicted_effects:
- diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: down
  magnitude_note: 2.843 -> <= 1.9 bpm on the same 128 rows (paired), i.e. at least
    ~45% of the 2.03 bpm gap to the token head closed. Gate band = 2 x seed-family
    sd once s43/s44 land.
- diagnostic_id: eiv_gen_between_sample_std
  direction: none
  magnitude_note: scale stays in [0.85,1.35]; a number-first target must not collapse
    dispersion.
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: none
  magnitude_note: hr_peak not worse than v41's 14.288 MAE by the band.
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- matched-baseline
- conditioning-collapse
- subject-heldout
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- optihealth
- target-style
- readout
- medgemma
- deployment-distribution
---

## Statement
TRACK D-a (GPU 2-3 h, CONDITIONAL on Track A reading the LLM's hidden-state hr_raw at <= 1.6 bpm, i.e. the information survives the forward pass). Retraining ONLY Phase C off v41's frozen Phase B with an emission-side target change -- the numeric slots first, each digit-by-digit under constrained decoding, the JSON scaffold and enum keys AFTER the numbers, train and serve prompt budgets fixed to the same value (EIV_RESOLVE_NESTED_CONTEXT=1 on both sides, closing the 61-token train/serve skew) -- and scoring with the MAE-matched estimator (median of K=8 samples) brings split-honest hr_raw on the 128 rows from 2.843 to <= 1.9 bpm (below the token-head's 0.812 is NOT predicted), with scale in [0.85,1.35] and hr_peak not worse than 14.288 by the band. If A reads the hidden state at >= 2.3 bpm this hypothesis is NOT run: the loss is upstream of emission and no target format recovers it.

## Rationale
Song & Bahri 2025 (arXiv:2501.19383): a decoder head over digit tokens matches a pointwise head GIVEN digit-by-digit tokens, constrained decoding, no interleaved language tokens and the loss-matched point estimate; their Sec. 5 names exactly our departures (LLM vocabulary, top-p, interleaved template) as the expected causes of loss. This project already has one measurement in that direction: exp-037 (2026-08) decoded the full conditional pmf instead of the argmax on the old adapter and took hr_peak corr 0.55 -> 0.795, skill -1.91 -> +1.86 bpm, with NO retrain -- so the decode rule alone is worth ~4 bpm on hr_peak, and the vault's own lesson was 'separate decode rule from representation before concluding the signal is absent'. exp-e26 showed the digit function class carries coarse magnitude, not accurate values, which is what a scaffold-first target with token-CE would produce. The train/serve prompt skew (256 vs 512 tokens, the state enum never seen in training) is a second known emission-side defect this arm closes, stated as part of the variable. Chen 2025 (arXiv:2512.06533) is the escalation if a gap remains after this: sequence-level numeric reward, not more format changes.

## Plan
Code, default-off: target_style json_slots_numfirst in _build_json_slots_target (numeric slots first as bare digit strings, scaffold after); constrained digit decoding in emission_diversity_audit.py (--decode constrained, --estimator median8); EIV_RESOLVE_NESTED_CONTEXT=1 exported for both train and serve. Profile lora_proxy_12ch_v41pl60_numfirst differing from lora_proxy_12ch_v41pl60 by target_style only (asserted). Score on the same 128 rows as v41/A0/t1b. Launch only after Track A's g0 precondition is on the record.

## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_splithonest_heldout_hr_raw_mae_bpm]] (down).
Predicts [[eiv_gen_between_sample_std]] (none).
Predicts [[eiv_gen_peak_skill_bpm]] (none).
Gate `g0` (blocking): PRECONDITION: Track A's eiv_lm_hidden_hr_raw_ridge_mae_bpm on v41rebased_c <= 1.6; otherwise this arm is not launched
Gate `g1` (blocking): split-honest hr_raw <= 1.9 bpm on the 128 rows, paired CI95 vs 2.843 entirely below zero
Gate `g2` (blocking): scale (std ratio) in [0.85,1.35] -- read BEFORE g1
Gate `g3` (advisory): hr_peak MAE not worse than 14.288 by the family band
Gate `g4` (blocking): one variable at the recipe level: Phase B checkpoint byte-identical to v41rebased/phase_b; the target format + prompt-budget fix + estimator are declared together as THE emission-side variable and no encoder/LoRA hyperparameter moves
Cites [[song-2025-decoding-based-regression-2501-19383]], [[chen-2025-rl-decoding-regression-2512-06533]], [[tan-2024-llm-useful-timeseries-2406-16964]].

#optihealth #target-style #readout #medgemma #deployment-distribution #optihealth-eiv #matched-baseline #conditioning-collapse #subject-heldout

## Resolution (2026-09-03)

WITHDRAWN 2026-09-03, never run. Its own blocking precondition g0 required Track A's eiv_lm_hidden_hr_raw_ridge_mae_bpm <= 1.6 on v41rebased_c; exp-a-hr-tower-localisation-20260903 measured 2.331 (75% of the LLM's 2.031 bpm loss is in the forward pass, 0.512 at emission). A perfect emission fix therefore has <= 0.5 bpm to win against a measured seed floor of 0.208 (k=2 -> 0.416): unpowered by its own g1 mde. The one Phase C that auto-started off a stale probe file (exp-d-a-numfirst-not-launched-20260903, `crashed`) was killed three minutes in. Code landed inert: target_style json_slots_numfirst + prompts/json_slots_numfirst_prompt.md, --decode medianK|constrained on the scorer. Re-open only if something raises the residual-stream readout above 0.812 + band.
