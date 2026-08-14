---
created_at: '2026-08-05T20:55:27+00:00'
former_ids:
- the-hr-peak-readout-cap-is-an-information-limit-not-a-function-class-limit-widen
gates:
- blocking: true
  description: PRIMARY. Best non-linear arm (min MAE over MLP_SCALAR, MLP_BIN, GBT_SCALAR)
    vs best linear arm (min over LIN_SCALAR, LIN_BIN), paired on the same 300 test
    samples, bootstrap CI95 with 5000 resamples on the difference. PASS (function-class
    limit) requires the CI to lie strictly below zero. If it touches or crosses zero
    the diagnosis is INFORMATION LIMIT and the gate FAILS. All hyperparameters CV-selected
    on the TRAIN split only.
  diagnostic_id: null
  id: gate-nonlinear-beats-linear
  mde: null
- blocking: true
  description: Every arm must be refit on donor features Hd[:,0,:] with identical
    hyperparameters, and the real-prefix arm must beat its own donor. An arm whose
    donor matches or beats it is fitting the marginal target distribution rather than
    reading this sample's prefix. This exact failure mode produced false passes in
    E25 (majority-tying logistic) and E26 (positions 2 and 4). Report donor MAE for
    all five arms.
  diagnostic_id: null
  id: gate-donor-null-all-arms
  mde: null
- blocking: true
  description: 'Shuffled-target control on LIN_SCALAR and MLP_SCALAR: refit on a permuted
    training target, predict test. Both must score NO BETTER than always-predict-median
    (18.492 bpm). d=2560 with n_train=700 is 3.7x underdetermined, and the non-linear
    arms add capacity on top, so an overfitting check is mandatory before any capacity
    claim is credited.'
  diagnostic_id: null
  id: gate-shuffled-target-null
  mde: null
- blocking: false
  description: 'HONESTY GUARD / SELF-CORRECTION. The E27 quantization floor (5.694
    bpm at K=4) must NOT be quoted as an achievability bound. It is an ENCODING limit:
    reaching it requires perfectly assigning every sample to its true bin, which presupposes
    information the frozen features may not carry. My prior summary said ''the cap
    is the linear readout itself'', which conflated function-class weakness with information
    absence. E28 exists to separate them. Whatever the outcome, record explicitly
    that the 53.4%-lost-to-readout-error decomposition from E27 does not by itself
    license a readout-capacity diagnosis.'
  diagnostic_id: null
  id: gate-floor-not-quoted-as-achievability
  mde: null
- blocking: false
  description: Report GBT_SCALAR alongside the MLP arms. Gradient boosting has a very
    different inductive bias from a dense MLP, so agreement between them guards against
    reading 'the MLP failed to fit' as 'the information is absent'. If MLP and GBT
    disagree materially, the capacity question is unresolved and must be reported
    as such rather than decided by the better of the two.
  diagnostic_id: null
  id: gate-two-nonlinear-families
  mde: null
id: the-hr-peak-readout-cap-is-an-a61025
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'PRIMARY, committed to the NULL branch before running. Predicting
    the best non-linear readout will NOT materially beat the best linear readout:
    the paired bootstrap CI95 of (best non-linear - best linear) decoded MAE will
    TOUCH OR CROSS ZERO. Reference points on this identical test split: LIN_BIN 12.529
    bpm (E27), DIGIT free-running 14.263 (E26), always-median 18.492, K=4 quantization
    floor 5.694. If instead the CI lies strictly below zero, the diagnosis flips to
    a function-class limit, a non-linear numeric head becomes the lever, and my prior
    session summary claim that ''the cap is the linear readout itself'' is vindicated
    rather than corrected. I expect the null because E26 and E27 agree the features
    resolve only coarse quartile-level magnitude, which is a statement about information
    content rather than about the shape of the map.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak readout cap is an INFORMATION limit, not a function-class limit. Widening the readout's function class beyond linear -- a non-linear MLP head and a gradient-boosted head, in both scalar-regression and binned-classification form, fit on the SAME frozen final hidden state at the SAME hr_peak emission position with the SAME train/test split -- will NOT materially beat the best LINEAR readout (E27's LIN_BIN at 12.529 bpm decoded MAE). The paired bootstrap CI95 on (best non-linear minus best linear) will touch or cross zero.

COMMITTED TO THE NULL BRANCH BEFORE RUNNING. I expect no material non-linear gain because E26 and E27 independently converge on the readout resolving only coarse, roughly quartile-level magnitude: E26 found zero non-leading digit positions clearing both their majority baseline and their own donor null, and E27 found bin accuracy collapsing as K rises (0.5800 at K=4 to 0.0800 at K=32) even as the quantization floor falls. That pattern describes how much information is present, not the shape of the map that reads it.

THIS HYPOTHESIS ALSO CORRECTS MY OWN PRIOR REPORTING. After E27 I stated 'the cap is the linear readout itself'. That claim was not licensed by the evidence: it read the quantization floor as an achievability bound when it is only an encoding limit. E28 is the measurement that decides it either way, and I am on record predicting my earlier phrasing was WRONG.

CONSEQUENCE IF CONFIRMED (null holds): head redesign is refuted as a lever for hr_peak. Record a rule_out so no future session spends a training run on a non-linear numeric head. The surviving candidates are the ones that change what ENTERS the stack -- prefix basis alignment (E20: cos(prefix HR direction, LM native numeric direction) = -0.015) or zero-init gated cross-attention -- and the problem should be re-framed from 'the readout is weak' to 'the prefix delivers only coarse magnitude through this interface'.

CONSEQUENCE IF REFUTED (non-linear wins with CI excluding zero): the cap IS the function class, a small non-linear head on the final hidden state becomes a precisely-targeted new arm that is outside both lm_head's function class and rb-e4's reach, and it should be preregistered as an intervention and ranked against rb-e4 -- whose expected prize is only ~4 bpm.

SCOPE LIMIT, unchanged from E25/E26/E27 and inherited by any downstream arm: this measures FITTED readouts on FROZEN features. It bounds what the features permit under each function class; it does not prove that end-to-end cross-entropy training would find the same solution.

## Rationale
SELF-CORRECTION OF MY OWN REPORTING. After closing E27 I summarised the result as 'the cap is the linear readout itself'. That over-reads the evidence. The E27 error-budget decomposition (headroom 12.799 bpm from always-median 18.492 to the K=4 quantization floor 5.694; readout realises 5.964 = 46.6%, loses 6.835 = 53.4%) labels everything not realised as 'readout error'. But the floor is an ENCODING limit, not an ACHIEVABILITY bound -- reaching it requires perfectly assigning each sample to its TRUE bin, which presupposes information the frozen features may not contain. So '53.4% lost to readout error' silently conflates two different diagnoses:
  (A) FUNCTION-CLASS limit -- information is present, a linear map cannot reach it
  (B) INFORMATION limit -- no readout of any class does better from these features
They are separable by widening the function class and seeing whether anything moves. Nothing in the vault distinguishes them: E24/E24c, E25, E26 and E27 all fitted LINEAR readouts (Ridge, per-position logistic, bin logistic). The capacity axis has never been varied.

This matters for what gets built. If (A), the lever is a small non-linear numeric head on the final hidden state -- which is outside lm_head's function class AND outside rb-e4's reach, i.e. a genuinely new arm. If (B), no head redesign helps and the remaining levers are the ones that change what ENTERS the stack (prefix basis alignment, where E20 measured cos(prefix HR direction, LM native numeric direction) = -0.015; or zero-init gated cross-attention, arXiv:2303.16199), and head redesign should be ruled out so no future session spends a training run on it.

Design notes. Five arms on the SAME frozen H[:,0,:], SAME split as E26/E27, all scored as decoded MAE in bpm so every number is directly comparable to 12.529 / 14.263 / 18.492. LIN_SCALAR is RidgeCV h->scalar: outside lm_head's class, but it is exactly the 'add a numeric regression head' architecture candidate, so it is deployable rather than merely diagnostic. Two non-linear FAMILIES (MLP and gradient boosting) because a single family failing to fit is not evidence of absent information. MLP/GBT arms run on PCA fit on TRAIN ONLY since d=2560 vs n_train=700 is 3.7x underdetermined; Ridge runs at full dimension because it regularises. Donor arms for all five and a shuffled-target control on two, because marginal-distribution fitting and overfitting have each already produced a false pass in this problem (E25, E26).

Smoke-tested at n_train=200: all five arms execute, return finite bpm-scale predictions, and HistGradientBoostingRegressor(early_stopping=True) is valid (the Pyright flag was stub noise). Script scratch/probe_e28_function_class.py with the decision rule in its docstring, written before the run. Zero GPU -- runs entirely on E26's cached features.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-nonlinear-beats-linear` (blocking): PRIMARY. Best non-linear arm (min MAE over MLP_SCALAR, MLP_BIN, GBT_SCALAR) vs best linear arm (min over LIN_SCALAR, LIN_BIN), paired on the same 300 test samples, bootstrap CI95 with 5000 resamples on the difference. PASS (function-class limit) requires the CI to lie strictly below zero. If it touches or crosses zero the diagnosis is INFORMATION LIMIT and the gate FAILS. All hyperparameters CV-selected on the TRAIN split only.
Gate `gate-donor-null-all-arms` (blocking): Every arm must be refit on donor features Hd[:,0,:] with identical hyperparameters, and the real-prefix arm must beat its own donor. An arm whose donor matches or beats it is fitting the marginal target distribution rather than reading this sample's prefix. This exact failure mode produced false passes in E25 (majority-tying logistic) and E26 (positions 2 and 4). Report donor MAE for all five arms.
Gate `gate-shuffled-target-null` (blocking): Shuffled-target control on LIN_SCALAR and MLP_SCALAR: refit on a permuted training target, predict test. Both must score NO BETTER than always-predict-median (18.492 bpm). d=2560 with n_train=700 is 3.7x underdetermined, and the non-linear arms add capacity on top, so an overfitting check is mandatory before any capacity claim is credited.
Gate `gate-floor-not-quoted-as-achievability` (advisory): HONESTY GUARD / SELF-CORRECTION. The E27 quantization floor (5.694 bpm at K=4) must NOT be quoted as an achievability bound. It is an ENCODING limit: reaching it requires perfectly assigning every sample to its true bin, which presupposes information the frozen features may not carry. My prior summary said 'the cap is the linear readout itself', which conflated function-class weakness with information absence. E28 exists to separate them. Whatever the outcome, record explicitly that the 53.4%-lost-to-readout-error decomposition from E27 does not by itself license a readout-capacity diagnosis.
Gate `gate-two-nonlinear-families` (advisory): Report GBT_SCALAR alongside the MLP arms. Gradient boosting has a very different inductive bias from a dense MLP, so agreement between them guards against reading 'the MLP failed to fit' as 'the information is absent'. If MLP and GBT disagree materially, the capacity question is unresolved and must be reported as such rather than decided by the better of the two.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
