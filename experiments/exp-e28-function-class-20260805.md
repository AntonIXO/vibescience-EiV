---
artifacts: []
closed: true
config_note: 'CPU-only, zero GPU. SAME frozen H[:,0,:] at the hr_peak emission position,
  SAME train/test split (700/300), SAME adapter medgemma_12ch_e23v2 and strict-clean
  population as E26/E27. Only the READOUT FUNCTION CLASS varies. Five arms: LIN_SCALAR
  (RidgeCV h->scalar, full 2560-dim), LIN_BIN (E27''s K=4 bin logistic), MLP_SCALAR,
  MLP_BIN, GBT_SCALAR. Non-linear arms on PCA fit on TRAIN ONLY (d=2560 vs n_train=700
  is 3.7x underdetermined); all hyperparameters CV-selected on TRAIN ONLY (mlp_scalar
  n_comp=128 hid=(128,64) alpha=10.0 cv 12.030; mlp_bin n_comp=64 hid=(128,64) alpha=10.0
  cv 13.950; gbt_scalar n_comp=32 cv 13.004). Donor arms for all five with identical
  hyperparameters; shuffled-target control on two. Smoke-tested at n_train=200 before
  the run. Scripts scratch/probe_e28_function_class.py and scratch/probe_e28b_e27_recheck.py,
  decision rules in docstrings, written before running. Committed to the NULL branch.'
crash_reason: ''
created_at: '2026-08-05T21:13:42+00:00'
diagnostics_measured:
- after: 10.27
  before: 12.529
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAILED, which CONFIRMS the preregistered null and is the intended informative
    outcome. Best linear = LIN_SCALAR 10.270 bpm; best non-linear = GBT_SCALAR 11.776
    bpm. Paired bootstrap CI95 (5000 resamples) of (non-linear minus linear) = [+0.574,
    +2.441], lying ENTIRELY ABOVE zero. The gate required the CI strictly below zero
    to declare a function-class limit; it is strictly above, so widening the function
    class does not merely fail to help, it measurably HURTS. Full ordering: LIN_SCALAR
    10.270 < GBT_SCALAR 11.776 < MLP_SCALAR 11.902 < MLP_BIN 12.398 < LIN_BIN 12.529.
    DIAGNOSIS: (B) INFORMATION LIMIT. The extra capacity fits noise the features do
    not contain signal for.'
  gate_id: gate-nonlinear-beats-linear
  passed: false
- evidence: 'PASS for all five arms, with wide margins. Real-prefix vs donor-prefix
    MAE (bpm): LIN_SCALAR 10.270 vs 19.380 (+9.111); LIN_BIN 12.529 vs 25.018 (+12.489);
    MLP_SCALAR 11.902 vs 21.373 (+9.471); MLP_BIN 12.398 vs 22.390 (+9.992); GBT_SCALAR
    11.776 vs 19.651 (+7.875). Every donor arm is also WORSE than always-predict-median
    (18.492), so no arm is fitting the marginal target distribution. The per-sample
    conditioning is real in every function class -- which is what makes the null result
    interpretable as an information ceiling rather than an absence of signal.'
  gate_id: gate-donor-null-all-arms
  passed: true
- evidence: 'PASS. Shuffled-target refits: LIN_SCALAR 19.339 bpm and MLP_SCALAR 20.870
    bpm, both WORSE than always-predict-median 18.492. No leakage or overfitting despite
    d=2560 vs n_train=700 (3.7x underdetermined) and the added non-linear capacity.
    This matters specifically for the null: it establishes that the non-linear arms''
    failure is not masked by them having overfit the training split.'
  gate_id: gate-shuffled-target-null
  passed: true
- evidence: 'HONOURED, and it changed the reported conclusion. The K=4 quantization
    floor of 5.694 bpm is recorded here explicitly as an ENCODING limit and NOT an
    achievability bound: reaching it presupposes perfect true-bin assignment, i.e.
    information the frozen features may not carry. E28 is the measurement that separates
    function-class weakness from information absence, and it lands on INFORMATION.
    Accordingly my post-E27 verbal claim that ''the cap is the linear readout itself''
    is WITHDRAWN as unlicensed -- E27''s 53.4%-lost-to-readout-error decomposition
    never distinguished the two diagnoses, and the honest statement is that the features
    carry roughly quartile-level magnitude and no readout of any tested class extracts
    more.'
  gate_id: gate-floor-not-quoted-as-achievability
  passed: true
- evidence: 'PASS, and the two families AGREE, which is what makes the null credible.
    Dense MLP and gradient boosting have very different inductive biases yet land
    within 0.13 bpm of each other (MLP_SCALAR 11.902, GBT_SCALAR 11.776) and BOTH
    lose to plain Ridge at 10.270. A single family underperforming would have left
    ''the optimiser failed to fit'' open; agreement across families closes it. Additionally
    MLP_BIN (12.398) roughly ties LIN_BIN (12.529), so non-linearity does not rescue
    the bin parameterization either. CV selections were interior to the grids, not
    at a boundary that would suggest under-search: mlp_scalar n_comp=128 hid=(128,64)
    alpha=10.0 (cv 12.030), mlp_bin n_comp=64 hid=(128,64) alpha=10.0 (cv 13.950),
    gbt_scalar n_comp=32 (cv 13.004).'
  gate_id: gate-two-nonlinear-families
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-readout-cap-is-an-a61025
id: exp-e28-function-class-20260805
key_numbers: {}
observed_effects:
- delta: -2.259
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
parent_experiment_id: exp-e27-bin-encoding-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-readout-cap-is-an-a61025]].
Branches from [[exp-e27-bin-encoding-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 12.529 → after 10.27  (Δ -2.259, down ✗ predicted none)

## Gates
- `gate-nonlinear-beats-linear`: **FAIL** — FAILED, which CONFIRMS the preregistered null and is the intended informative outcome. Best linear = LIN_SCALAR 10.270 bpm; best non-linear = GBT_SCALAR 11.776 bpm. Paired bootstrap CI95 (5000 resamples) of (non-linear minus linear) = [+0.574, +2.441], lying ENTIRELY ABOVE zero. The gate required the CI strictly below zero to declare a function-class limit; it is strictly above, so widening the function class does not merely fail to help, it measurably HURTS. Full ordering: LIN_SCALAR 10.270 < GBT_SCALAR 11.776 < MLP_SCALAR 11.902 < MLP_BIN 12.398 < LIN_BIN 12.529. DIAGNOSIS: (B) INFORMATION LIMIT. The extra capacity fits noise the features do not contain signal for.
- `gate-donor-null-all-arms`: PASS — PASS for all five arms, with wide margins. Real-prefix vs donor-prefix MAE (bpm): LIN_SCALAR 10.270 vs 19.380 (+9.111); LIN_BIN 12.529 vs 25.018 (+12.489); MLP_SCALAR 11.902 vs 21.373 (+9.471); MLP_BIN 12.398 vs 22.390 (+9.992); GBT_SCALAR 11.776 vs 19.651 (+7.875). Every donor arm is also WORSE than always-predict-median (18.492), so no arm is fitting the marginal target distribution. The per-sample conditioning is real in every function class -- which is what makes the null result interpretable as an information ceiling rather than an absence of signal.
- `gate-shuffled-target-null`: PASS — PASS. Shuffled-target refits: LIN_SCALAR 19.339 bpm and MLP_SCALAR 20.870 bpm, both WORSE than always-predict-median 18.492. No leakage or overfitting despite d=2560 vs n_train=700 (3.7x underdetermined) and the added non-linear capacity. This matters specifically for the null: it establishes that the non-linear arms' failure is not masked by them having overfit the training split.
- `gate-floor-not-quoted-as-achievability`: PASS — HONOURED, and it changed the reported conclusion. The K=4 quantization floor of 5.694 bpm is recorded here explicitly as an ENCODING limit and NOT an achievability bound: reaching it presupposes perfect true-bin assignment, i.e. information the frozen features may not carry. E28 is the measurement that separates function-class weakness from information absence, and it lands on INFORMATION. Accordingly my post-E27 verbal claim that 'the cap is the linear readout itself' is WITHDRAWN as unlicensed -- E27's 53.4%-lost-to-readout-error decomposition never distinguished the two diagnoses, and the honest statement is that the features carry roughly quartile-level magnitude and no readout of any tested class extracts more.
- `gate-two-nonlinear-families`: PASS — PASS, and the two families AGREE, which is what makes the null credible. Dense MLP and gradient boosting have very different inductive biases yet land within 0.13 bpm of each other (MLP_SCALAR 11.902, GBT_SCALAR 11.776) and BOTH lose to plain Ridge at 10.270. A single family underperforming would have left 'the optimiser failed to fit' open; agreement across families closes it. Additionally MLP_BIN (12.398) roughly ties LIN_BIN (12.529), so non-linearity does not rescue the bin parameterization either. CV selections were interior to the grids, not at a boundary that would suggest under-search: mlp_scalar n_comp=128 hid=(128,64) alpha=10.0 (cv 12.030), mlp_bin n_comp=64 hid=(128,64) alpha=10.0 (cv 13.950), gbt_scalar n_comp=32 (cv 13.004).

**Verdict:** refutes

## Notes
NULL PREDICTION HELD, AND IT ALSO OVERTURNED THE EXPERIMENT ONE STEP BACK. Two results, both recorded.

=== 1. THE PRIMARY QUESTION: FUNCTION CLASS OR INFORMATION? ===
ANSWER: INFORMATION. Every non-linear arm is WORSE than plain Ridge.
  LIN_SCALAR  10.270 | donor 19.380
  GBT_SCALAR  11.776 | donor 19.651
  MLP_SCALAR  11.902 | donor 21.373
  MLP_BIN     12.398 | donor 22.390
  LIN_BIN     12.529 | donor 25.018
  DIGIT (E26) 14.263 ; always-median 18.492 ; K=4 floor 5.694 (ENCODING limit, not achievable)
CI95 (best non-linear GBT minus best linear LIN_SCALAR) = [+0.574, +2.441], entirely ABOVE zero. I predicted 'no material gain'; the observed result is a significant non-linear LOSS. Dense MLP and gradient boosting -- very different inductive biases -- agree within 0.13 bpm, so this is not one optimiser failing to fit. Shuffled-target controls sit worse than always-median (19.339 / 20.870), so nothing is overfitting at d=2560 vs n_train=700.
CONSEQUENCE: head redesign is REFUTED as a lever for hr_peak. A non-linear numeric head on the final hidden state should NOT be built, and no future session should spend a training run on one. This must be recorded as a rule_out.

=== 2. UNPLANNED: E27'S HEADLINE IS SUPERSEDED (scratch/probe_e28b_e27_recheck.py) ===
LIN_SCALAR (10.270) was NEVER TESTED IN E27, and it beats E27's winner LIN_BIN (12.529) with CI95 [-3.310, -1.185], and beats E26's DIGIT (14.263) with CI95 [-5.364, -2.657]. E27 concluded 'a single-token bin target beats the multi-token digit string' -- true as stated, but it compared two WEAK parameterizations to each other while the strongest readout in the same linear class went untested. The 1.734 bpm E27 attributed to 'the encoding' is really 3.993 bpm available from plain scalar regression over DIGIT. Re-derived error budget over the 12.799 bpm headroom: LIN_BIN realises 5.964 (46.6%), LIN_SCALAR realises 8.222 (64.2%).
E27's verdict stands as recorded (its directional prediction was correct and its gates were honestly scored) but its FRAMING -- that target encoding is the interesting axis -- is withdrawn here. The axis that mattered was scalar-vs-token output, not digits-vs-bins.

=== 3. WHAT I GOT WRONG AND AM CORRECTING ===
After E27 I told the user 'the cap is the linear readout itself'. WITHDRAWN. That read the K=4 quantization floor as an achievability bound when it is only an encoding limit -- reaching it presupposes perfect true-bin assignment, i.e. information that may not be present. E27's '53.4% lost to readout error' decomposition never separated function-class weakness from information absence. E28 separates them and lands on information. The honest statement: the frozen features at the emission position carry roughly quartile-level magnitude, and no readout of any tested class (linear scalar, linear bin, MLP scalar, MLP bin, GBT) extracts materially more than ~10.3 bpm MAE.

=== 4. WHERE THIS LEAVES THE PROBLEM ===
Four levers are now measured and none is the cap: pooling operator (ro-dual-avg-max-pooling), bin granularity (E27 per-K), digit-vs-bin encoding (E27/E28b), readout function class (E28). Meanwhile the prefix ITSELF carries hr_peak at R2 0.8084 / increment +0.7211, and at the emission position it still supports R2 0.6850 with donor null -0.0014. So the information IS in the prefix and only quartile-level magnitude survives to the emission position. The loss is in the INTERFACE -- what crosses from prefix into the residual stream -- not in how it is read out afterwards.
Surviving candidates, both of which change what ENTERS the stack: prefix basis alignment (E20 measured cos(prefix HR direction, LM native numeric direction) = -0.015, i.e. the prefix writes into a direction the LM's numeric machinery does not read) and zero-init gated cross-attention (arXiv:2303.16199). These should now be ranked ahead of rb-e4, whose expected prize was already only ~4 bpm and which remains blocked on lm_head embedding-tying.

=== 5. CAVEAT, UNCHANGED ===
Fitted readouts on FROZEN features. This bounds what the features permit under each function class; it does not prove end-to-end cross-entropy training would find the same solution. Also: LIN_SCALAR is outside lm_head's function class, so its 10.270 is NOT a number the current architecture can emit -- it is the ceiling a numeric-head architecture would target, and E28 shows that ceiling is set by information, not by head design.

Artifacts: outputs/probes/e28_function_class.json, outputs/probes/e28b_e27_recheck.json, logs/e28_fclass.log, logs/e28b_recheck.log. Scripts scratch/probe_e28_function_class.py, scratch/probe_e28b_e27_recheck.py, scratch/probe_e28_smoke.py. Zero GPU.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
