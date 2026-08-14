---
created_at: '2026-08-05T19:08:34+00:00'
gates:
- blocking: true
  description: PRIMARY. Free-running decoded MAE (eiv_digit_readout_decoded_mae) <
    always-predict-median MAE, with a bootstrap CI95 (5000 resamples) on the difference
    EXCLUDING zero. Free-running means the classifier's own predicted digit is fed
    back through the model via KV cache -- no oracle prefix.
  id: gate-fr-beats-median
- blocking: true
  description: At least one NON-LEADING digit position must beat its OWN majority-class
    baseline by > +0.05. The leading digit is ~89% '1' on this population, so a readout
    can look accurate there while carrying zero numeric information. If this fails,
    the readout can only reproduce the population mode.
  id: gate-non-leading-digit
- blocking: false
  description: The teacher-forced arm is recorded SEPARATELY and labelled OPTIMISTIC.
    It uses an oracle prefix (true preceding digits) that E4 never has at generation
    time. If TF passes while FR fails, that is recorded as a REFUTATION of the capacity
    argument, not a partial success.
  id: gate-tf-vs-fr-honesty
- blocking: false
  description: 'Donor-prefix null: free-running decode from a donor sample''s prefix
    must be no better than always-predict-median. Guards against the per-position
    classifiers having learned a prefix-independent digit prior.'
  id: gate-donor-null-fr
id: measured-in-lm-head-s-actual-function-class-a-per-digit-position-linear-classifi
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'PRIMARY. Committed to the NULL branch before running: I predict
    the free-running decoded MAE does NOT beat always-predict-median with a CI excluding
    zero, i.e. the digit-classification ceiling is at or near the trivial predictor
    even though the scalar-regression readout reached MAE 11.815. Rationale for this
    direction: E25''s own logistic arm -- the only arm in the correct function class
    -- tied the majority baseline exactly. If this prediction holds, rb-e4''s capacity
    argument is refuted and the runbook should be abandoned rather than launched.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: the-hr-peak-readout-failure-is-recoverable-by-a-linear-correction-at-lm-head-the
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
Measured in lm_head's ACTUAL function class -- a per-digit-position linear classifier over {'0'..'9','.',END} with argmax and free-running feedback -- the best linear readout of the frozen final hidden state does NOT beat always-predict-median on decoded hr_peak MAE. Committed to the NULL branch before running. If this holds, the information that RidgeCV extracts at R2 +0.5107 is NOT extractable by the digit-emitting readout lm_head actually implements, rb-e4's capacity argument is refuted, and the runbook must be abandoned rather than launched -- no rank or training length fixes a function-class mismatch. This supersedes the capacity-ceiling claim of the E25 hypothesis, which measured a scalar-regression readout that lm_head cannot implement.

## Rationale
SELF-CORRECTION of exp-e25-readout-ceiling-20260805. I closed E25 reporting 'best linear readout MAE 11.815 vs always-median 20.094, increment +0.5283' and used it to conclude rb-e4 is capacity-sufficient. That conclusion was measured in the WRONG FUNCTION CLASS and is withdrawn. E25's arm B had two sub-parts I conflated: B-ridge (h -> SCALAR, R2 +0.5107) and B-logistic (h -> digit logits -> argmax, acc 0.8917). lm_head CANNOT emit a scalar; it is Linear(d_model->vocab) followed by argmax over token ids, emitting ONE DIGIT AT A TIME. So the function class a LoRA on lm_head can realise is B-logistic -- and B-logistic tied the majority-class baseline EXACTLY (0.8917 vs 0.8917), i.e. a constant predictor. RidgeCV gets to output 118.6 directly; lm_head must independently select '1','1','8','.','6'. A regression R2 says nothing about whether the per-position digit-classification problem is linearly separable. Two further defects in E25: hidden states were captured at ONE position (the leading digit) so the positions carrying most numeric precision were never measured; and n_train=280 against d_model=2560 is 9x underdetermined, making the logistic fit regularisation-dominated. E26 re-measures the ceiling in the correct class, at every digit position, with free-running decode and a larger n. NOTE: the E24/E25 localisation chain (peak information IS present in the frozen hidden state, increment +0.5283 over the true-mean control, donor null -0.0784) is UNAFFECTED -- that is a claim about information content, which regression legitimately measures. Only the capacity-ceiling inference is withdrawn.

## Plan
scratch/probe_e26_digit_ceiling.py, adapter medgemma_12ch_e23v2, 4-bit, no training, n=1000 (raised from E25's 400 because n_train=280 vs d_model=2560 was 9x underdetermined), SEED 0, strict-clean population, E24c neutral context. Teacher-force the TRUE hr_peak string and capture the frozen final hidden state at EVERY emission position (E25 captured only the leading digit). Fit a per-position multinomial classifier over {'0'..'9','.',END} with C chosen by cross-validation on the TRAIN split only. Then decode two ways: ARM TF (oracle prefix, optimistic) and ARM FR (free-running, classifier's own digit fed back via KV cache -- the decision-relevant arm), plus ARM DON (donor-prefix free-running null). Report per-position accuracy against each position's own majority baseline. Apparatus already smoke-tested at n=60: all arms parse at 1.000. Features saved to .npz so follow-ups are CPU-only.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-fr-beats-median` (blocking): PRIMARY. Free-running decoded MAE (eiv_digit_readout_decoded_mae) < always-predict-median MAE, with a bootstrap CI95 (5000 resamples) on the difference EXCLUDING zero. Free-running means the classifier's own predicted digit is fed back through the model via KV cache -- no oracle prefix.
Gate `gate-non-leading-digit` (blocking): At least one NON-LEADING digit position must beat its OWN majority-class baseline by > +0.05. The leading digit is ~89% '1' on this population, so a readout can look accurate there while carrying zero numeric information. If this fails, the readout can only reproduce the population mode.
Gate `gate-tf-vs-fr-honesty` (advisory): The teacher-forced arm is recorded SEPARATELY and labelled OPTIMISTIC. It uses an oracle prefix (true preceding digits) that E4 never has at generation time. If TF passes while FR fails, that is recorded as a REFUTATION of the capacity argument, not a partial success.
Gate `gate-donor-null-fr` (advisory): Donor-prefix null: free-running decode from a donor sample's prefix must be no better than always-predict-median. Guards against the per-position classifiers having learned a prefix-independent digit prior.
Supersedes [[the-hr-peak-readout-failure-is-recoverable-by-a-linear-correction-at-lm-head-the]].

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
