---
created_at: '2026-08-05T18:32:38+00:00'
gates:
- blocking: true
  description: 'PRIMARY. eiv_readout_linear_gap > +0.15: best linear readout first-digit
    accuracy minus frozen greedy emission accuracy, at the hr_peak emission position
    under neutral context.'
  id: gate-readout-linear-gap
- blocking: true
  description: 'The linear readout must beat the trivial predictor at the VALUE level:
    B_linear_mae < always_predict_median_mae on the held-out split. Guards against
    an accuracy gap that does not correspond to usable numeric skill.'
  id: gate-beats-median-mae
- blocking: true
  description: 'Donor-prefix null: the same linear readout fitted on a DONOR sample''s
    prefix must score at or below the majority-class baseline on first digit (C_acc
    <= majority + 0.05). Guards against the probe reading a prefix-correlated nuisance
    rather than per-sample peak information.'
  id: gate-donor-null
- blocking: false
  description: The linear readout must beat the majority-class baseline on first digit
    by more than +0.05, so a degenerate constant predictor cannot pass the primary
    gate.
  id: gate-above-majority
id: the-hr-peak-readout-failure-is-recoverable-by-a-linear-correction-at-lm-head-the
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_readout_linear_gap
  direction: up
  magnitude_note: PRIMARY. Predict > +0.15 first-digit accuracy gap (linear readout
    minus frozen greedy emission), with the linear arm also beating the majority-class
    baseline and surviving the donor-prefix null. Verdict keys off this.
- diagnostic_id: eiv_lm_hidden_peak_r2
  direction: none
  magnitude_note: Expected unchanged near the E24c value 0.685 -- this experiment
    re-uses the same position/context/population and changes only the READOUT operator,
    so a large shift here would indicate an apparatus difference rather than a result.
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak readout failure is RECOVERABLE BY A LINEAR CORRECTION AT lm_head. The best linear readout of the frozen final hidden state, at the exact hr_peak emission position under neutral context, beats what the frozen head greedily emits by more than +0.15 first-digit accuracy AND achieves a lower MAE than always-predict-median. Committed to the HIGH branch before running. This is an UPPER BOUND on rb-e4, not an analogy: a rank-32 LoRA on lm_head is full-rank over the 10 digit rows (rank <= min(32,10) = 10), so it can realise ANY linear-in-h correction to the digit logits, and the best linear readout IS its capacity ceiling. If the gap is small, presence does not convert to usability under a linear map and E4 as specified cannot fix the defect regardless of how long it trains.

## Rationale
E24 closed with the peak PRESENT at the emission position (R2 0.6850 neutral context, donor null -0.0014) while the emitted value has NEGATIVE skill vs always-predict-median (-5.88 bpm). I recorded 'presence is not usability' as a scope limit -- Ridge has a free scale and an unconstrained linear map, a frozen head has neither -- but left it untested, which makes rb-e4 a bet rather than a targeted arm. Apparatus verified first in scratch/probe_e25_smoke.py: (1) lm_head(hidden_states[-1]) == logits EXACTLY (max abs diff 0.0e+00), so the tensor E24 probed IS what the readout consumes and no final norm intervenes; (2) lm_head.weight IS TIED to the input embeddings (identical data_ptr) -- this was the open blocking question flagged against E4 and it is now answered, with the consequence that a LoRA on lm_head also perturbs the input embedding matrix unless the adapter is applied to an untied copy; (3) hr_peak values tokenize one digit per token ('97.1' -> ['9','7','.','1']) with digit mass >= 0.995 at those positions, so the model already emits DIGITS and merely selects the wrong ones -- the failure is digit SELECTION, which is exactly what a linear correction on the digit rows addresses.

## Plan
scratch/probe_e25_readout_ceiling.py, adapter medgemma_12ch_e23v2 (BASELINE, same as E24), 4-bit, no training, n=400, strict-clean filter and SEED 0 matching E24b/E24c. At the first-digit emission position under the E24c NEUTRAL context: arm A greedily decodes what the frozen head emits (parsed to a value); arm B fits multinomial logistic regression on the frozen final hidden state for the first digit plus RidgeCV for the value; arm C repeats arm B on a DONOR sample's prefix as the null. Also scores the >=100 bpm decision the bucket rule keys on, and reports the majority-class baseline and always-predict-median MAE so no number is quoted bare.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_readout_linear_gap]] (up).
Predicts [[eiv_lm_hidden_peak_r2]] (none).
Gate `gate-readout-linear-gap` (blocking): PRIMARY. eiv_readout_linear_gap > +0.15: best linear readout first-digit accuracy minus frozen greedy emission accuracy, at the hr_peak emission position under neutral context.
Gate `gate-beats-median-mae` (blocking): The linear readout must beat the trivial predictor at the VALUE level: B_linear_mae < always_predict_median_mae on the held-out split. Guards against an accuracy gap that does not correspond to usable numeric skill.
Gate `gate-donor-null` (blocking): Donor-prefix null: the same linear readout fitted on a DONOR sample's prefix must score at or below the majority-class baseline on first digit (C_acc <= majority + 0.05). Guards against the probe reading a prefix-correlated nuisance rather than per-sample peak information.
Gate `gate-above-majority` (advisory): The linear readout must beat the majority-class baseline on first digit by more than +0.05, so a degenerate constant predictor cannot pass the primary gate.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
