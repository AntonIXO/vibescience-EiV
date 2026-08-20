---
created_at: '2026-08-18T23:22:29+00:00'
former_ids: []
id: the-function-class-account-of-exp-046-that-8-511840
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-18T23:22:29+00:00'
topic_tags:
- control
- baseline
- pooling
- wearables
- n-of-1
- jepa
---

## Claim
The FUNCTION-CLASS account of exp-046: that 8 handcrafted features beat the trained encoder on momentary within-window state because the encoder's function class (per-timestep FlexMLP + a single Linear(patch_len=120 -> d_model=192) per patch) cannot expose within-patch order statistics (min/max/std), so the fix lies at patch construction. ELIMINATED on both of its testable halves. Also eliminated: the stronger reading of exp-046/E29 that the encoder's tokens 'do not carry' momentary state -- that conclusion is READOUT-DEPENDENT and does not survive a matched linear readout. NOT ELIMINATED: that the encoder's state signal is WEAK (z+2.83 under a linear readout is modest), and that a RandomForest on epoch-local encoder tokens is a bad instrument for this task.

## Method
scratch/e34_function_class_step0.py -> logs/e34_function_class_step0.json, commit b95c64d. The script (with its predictions P1/P2/P3 and their falsifiers) was COMMITTED BEFORE THE DATA EXISTED, per the preregistered-probe pattern, so the checks could not be written to fit the outcome.

Step 0 of runbook `the-function-class-residual-8-handcrafted-21b62f`, executed 2026-08-18 on CPU/GPU in ~9 minutes, zero training.

Design: exp-046 / E29's exact rows re-derived via src.training.datasets.state_series (n=3226 epochs, 87 subjects, 660 windows, flow vs stress, pos 0.481, W=120 -- reproduced exactly). Checkpoint outputs/checkpoints/v5c/phase_b.
T1: identical rows/folds/PCA/nulls as exp-046, sweeping ONLY the readout (RandomForest(300, leaf3, seed0) vs standardised LogisticRegression(max_iter=2000, C=1.0)); each arm scored against its OWN 24-permutation within-window shuffle null recomputed per readout, since the null is readout-dependent.
T2: RidgeCV (16 alphas) from the epoch-local pooled tokens to each of the 8 raw features, GroupKFold(5) by subject, out-of-fold R2, with a random-init encoder run through the identical pipeline as the control.

## Evidence
T1 MATCHED READOUT -- the runbook's own BLOCKING gate, and it FAILED, meaning the runbook self-abandons:
  arm                        readout     AUC     null            over_null    z
  raw_patch_grid_8           rf       0.6309  0.5189+-0.0422     +0.1120   +2.66
  raw_patch_grid_8           linear   0.5855  0.5711+-0.0130     +0.0143   +1.10
  enc_pca8                   rf       0.4950  0.5442+-0.0293     -0.0492   -1.68
  enc_pca8                   linear   0.5715  0.5310+-0.0143     +0.0405   +2.83
Gate: raw_linear - enc_linear = -0.0262 against a required > +0.05. FAIL => ABANDON.
Under a MATCHED LINEAR readout the ordering REVERSES: the trained encoder CLEARS its own within-window null at z +2.83 while the raw features fall to z +1.10. The entire '8 handcrafted numbers beat the whole encoder' effect is carried by the RandomForest, not by the representations. RF suits 8 dense handcrafted features and suits 8 PCA components of a distributed code badly; the comparison was confounded by the estimator.

T2 RECONSTRUCTION -- can the tokens linearly reconstruct their OWN input-patch statistics? (OOF R2, GroupKFold by subject, RidgeCV)
  feature      trained   random
  avg_mean      0.8232   0.8310
  avg_std       0.4591   0.5419
  avg_min       0.6491   0.5929
  avg_max       0.5203   0.6123
  max_mean      0.8074   0.7749
  max_std       0.5122   0.5975
  max_min       0.4858   0.4175
  max_max       0.4825   0.5734
Preregistered checks, all committed pre-data and ALL PASSED:
  P1 order-statistic mean R2 >= 0.50 : 0.5182 PASS  => the order statistics ARE linearly present in the tokens; the function-class account is refuted on its own falsifier.
  P2 mean-feats reconstruct better than order-feats : 0.8153 vs 0.5182 PASS => the architecture is indeed better at means than at order statistics, exactly as argued -- but the residual is a matter of DEGREE, not of absence.
  P3 random-init ~= trained (<= 0.10) : trained 0.5924 vs random 0.6177 PASS => reconstruction quality is a property of the OVER-COMPLETE LINEAR PROJECTION (120 -> 192 is information-preserving by construction), NOT of training. Random init is marginally BETTER. This control is what stops P1 being misread as evidence about the training objective.

SCOPE / WHAT THIS DOES NOT CLAIM: enc_pca8's +0.0405 (z+2.83) is a MODEST effect on one checkpoint (v5c/phase_b), one readout family, one task (flow-vs-stress transitions, W=120), single seed. It is not a claim that the state slot works. It IS sufficient to refute 'the tokens do not carry state' and to make patch-construction surgery unjustified on this evidence.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#control #baseline #pooling #wearables #n-of-1 #jepa
