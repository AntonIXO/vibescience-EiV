---
created_at: '2026-08-05T21:15:24+00:00'
former_ids:
- a-non-linear-readout-head-on-the-frozen-lm-s-final-hidden-state-a-small-mlp-nume
id: a-non-linear-readout-head-on-the-frozen-ee304d
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T21:15:24+00:00'
topic_tags:
- cross-modal
- medgemma
- collapse
---

## Claim
A NON-LINEAR READOUT HEAD on the frozen LM's final hidden state (a small MLP numeric head, or any higher-capacity readout replacing the linear one) will recover hr_peak accuracy. ELIMINATED. Also eliminated: (a) the framing I stated after E27 that 'the cap is the linear readout itself' -- the cap is INFORMATION, not readout capacity; (b) the E27 framing that TARGET ENCODING (digits vs bins) is the interesting axis -- the axis that mattered was scalar-vs-token output, and E27's chosen winner was not even the best readout in its own linear class; (c) 'use more bins' as a follow-up to E27.

## Method
Five readout function classes fit on the IDENTICAL frozen final hidden state H[:,0,:] at the hr_peak emission position, with the IDENTICAL train/test split (700/300), adapter (medgemma_12ck_e23v2), strict-clean population and E24c neutral context used by exp-e26 and exp-e27 -- so every number is directly comparable and only the readout varies. Arms: LIN_SCALAR (RidgeCV h->scalar, full 2560-dim), LIN_BIN (E27's K=4 quantile-bin logistic), MLP_SCALAR, MLP_BIN, GBT_SCALAR (HistGradientBoosting). All scored as decoded MAE in bpm. Two non-linear FAMILIES deliberately included so that a single family failing to fit could not be mistaken for absent information. Controls: donor-prefix refit for all five arms with identical hyperparameters, and shuffled-target refits for two. Follow-up scratch/probe_e28b_e27_recheck.py computed the paired bootstrap CIs E27 never ran because LIN_SCALAR was not in E27's arm set. CPU only, zero GPU, on cached features from exp-e26. Scripts scratch/probe_e28_function_class.py (smoke-tested at n_train=200 first) and scratch/probe_e28b_e27_recheck.py, decision rules in docstrings, written before running; hypothesis committed to the NULL branch before the run.

## Evidence
=== 1. FIVE READOUT CLASSES, SAME FROZEN FEATURES, SAME SPLIT (n_test=300) ===
arm            MAE(bpm)   donor    margin
LIN_SCALAR      10.270    19.380   +9.111   <- BEST, and it is plain RidgeCV
GBT_SCALAR      11.776    19.651   +7.875
MLP_SCALAR      11.902    21.373   +9.471
MLP_BIN         12.398    22.390   +9.992
LIN_BIN         12.529    25.018  +12.489   <- E27's 'winner'
DIGIT (E26)     14.263 ; always-median 18.492 ; K=4 quantization floor 5.694

PRIMARY GATE: CI95 (best non-linear GBT_SCALAR minus best linear LIN_SCALAR) = [+0.574, +2.441], ENTIRELY ABOVE ZERO. Extra capacity does not merely fail to help, it measurably HURTS. Preregistered bar was CI strictly below zero to declare a function-class limit.

=== 2. WHY THIS IS NOT 'THE OPTIMISER FAILED TO FIT' ===
Two non-linear families with very different inductive biases (dense MLP, gradient boosting) land within 0.13 bpm of each other and BOTH lose to Ridge. MLP_BIN also roughly ties LIN_BIN, so non-linearity does not rescue the bin parameterization either. All hyperparameters CV-selected on the TRAIN split only, selections interior to their grids (mlp_scalar n_comp=128 hid=(128,64) alpha=10.0 cv 12.030; mlp_bin n_comp=64 hid=(128,64) alpha=10.0 cv 13.950; gbt n_comp=32 cv 13.004). Non-linear arms ran on PCA fit on TRAIN ONLY because d=2560 vs n_train=700 is 3.7x underdetermined.

=== 3. CONTROLS ===
DONOR: all five arms refit on donor-prefix features with identical hyperparameters. Every real arm beats its own donor by +7.9 to +12.5 bpm, and every donor arm is WORSE than always-predict-median. So per-sample conditioning is real in every function class -- the null is an information ceiling, not absent signal.
SHUFFLED TARGET: LIN_SCALAR 19.339, MLP_SCALAR 20.870, both worse than always-median 18.492. No overfitting, which is what makes the non-linear failure interpretable.

=== 4. THE FLOOR WAS BEING MISREAD (this is what motivated E28) ===
E27's 5.694 bpm K=4 quantization floor is an ENCODING limit, NOT an achievability bound: reaching it presupposes perfectly assigning every sample to its TRUE bin, i.e. information the features may not carry. E27's decomposition ('readout realises 46.6%, loses 53.4%') therefore conflated function-class weakness with information absence. E28 separates them and lands on INFORMATION.

=== 5. E27'S HEADLINE SUPERSEDED (scratch/probe_e28b_e27_recheck.py) ===
LIN_SCALAR was never tested in E27. It beats LIN_BIN with CI95 [-3.310, -1.185] and DIGIT with CI95 [-5.364, -2.657]. The 1.734 bpm E27 attributed to 'the encoding' is really 3.993 bpm available from plain scalar regression over DIGIT. Re-derived budget over the 12.799 bpm headroom: LIN_BIN realises 5.964 (46.6%), LIN_SCALAR realises 8.222 (64.2%). E27 compared two weak parameterizations while the strongest readout in the same linear class went untested.

=== 6. WHAT SURVIVES AND WHERE TO GO ===
The prefix carries hr_peak at R2 0.8084 / increment +0.7211 (strict-clean n=1641), and at the emission position it still supports R2 0.6850 with donor null -0.0014. But only quartile-level magnitude survives to a decodable value (~10.3 bpm MAE floor across every readout class tested). Four levers are now measured and none is the cap: pooling operator, bin granularity, digit-vs-bin encoding, readout function class. The loss is in the INTERFACE -- what crosses from prefix into the residual stream -- not in how it is read out afterwards. Surviving candidates both change what ENTERS the stack: prefix basis alignment (E20: cos(prefix HR direction, LM native numeric direction) = -0.015) and zero-init gated cross-attention (arXiv:2303.16199).

CAVEAT: fitted readouts on FROZEN features. Bounds what the features permit per function class; does not prove end-to-end training would find the same solution. LIN_SCALAR's 10.270 is also NOT emittable by the current architecture (lm_head cannot output a scalar) -- it is the ceiling a numeric-head architecture would target, and E28 shows that ceiling is set by information, not head design.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#cross-modal #medgemma #collapse
