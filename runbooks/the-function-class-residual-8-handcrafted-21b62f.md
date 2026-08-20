---
created_at: '2026-08-18T23:08:26+00:00'
former_ids: []
gates:
- 'BLOCKING STEP 0 (CPU, minutes): the 8 raw features must be shown to beat the encoder
  because of FUNCTION CLASS, not because of the classifier. Re-fit BOTH the 8 raw
  features and enc_pca8 with a LINEAR readout (logistic) as well as the RF, on identical
  rows/folds, each against its own 24-perm within-window null. If the raw arm''s advantage
  disappears under a matched linear readout, the ''function class'' framing is wrong
  and this runbook is ABANDONED.'
- Any arm claiming to fix this must be scored on eiv_momentary_state_auc_over_null
  against the FULL exp-046 triple (raw_patch_grid_8 +0.1120 / enc_pca8 -0.0492 / rand_pca8
  -0.0022), not against enc_pca8 alone. Beating a broken arm is not evidence.
- The random-init arm must be RE-RUN for any architecture change, not inherited. rand_pca8's
  -0.0022 is a property of the CURRENT architecture; a new one has its own inert baseline
  and it must be measured.
- No claim of success may rest on a within-subject or cross-day null. Only the WITHIN-WINDOW
  shuffle null is admissible on this task (it holds subject AND day constant); ro-encoder-affect-is-time-of-day-20260815
  shows what happens otherwise -- a time-of-day confound read as affect.
hypothesis_id: null
id: the-function-class-residual-8-handcrafted-21b62f
notes:
- at: '2026-08-18T23:22:48+00:00'
  text: "[2026-08-18] ABANDONED BY ITS OWN BLOCKING GATE, ~9 min after being queued.
    Step 0 ran (scratch/e34_function_class_step0.py -> logs/e34_function_class_step0.json,
    commit b95c64d, preregistered before the data existed). Rule-out `the-function-class-account-of-exp-046-that-8-511840`.\n\nTHE
    GATE: 'if the raw arm's advantage disappears under a matched linear readout, the
    function-class framing is wrong and this runbook is ABANDONED.' It disappeared,
    and then some -- it INVERTED.\n\n  arm                readout     AUC     null
    \           over_null    z\n  raw_patch_grid_8   rf       0.6309  0.5189+-0.0422
    \    +0.1120   +2.66\n  raw_patch_grid_8   linear   0.5855  0.5711+-0.0130     +0.0143
    \  +1.10\n  enc_pca8           rf       0.4950  0.5442+-0.0293     -0.0492   -1.68\n
    \ enc_pca8           linear   0.5715  0.5310+-0.0143     +0.0405   +2.83\nGate
    arithmetic: raw_linear - enc_linear = -0.0262, needed > +0.05. FAIL.\n\nUnder
    a MATCHED LINEAR readout the trained encoder CLEARS its own within-window null
    (z +2.83) and the raw features do NOT (z +1.10). The '8 handcrafted numbers beat
    the whole encoder' effect was carried by the RANDOMFOREST, not by the representations:
    RF suits 8 dense handcrafted features and suits 8 PCA components of a distributed
    code badly. The estimator was the confound.\n\nT2 killed the other half. Fitting
    RidgeCV from the epoch-local tokens to the 8 raw features (OOF, GroupKFold by
    subject): order-statistic mean R2 0.5182 (preregistered falsifier was < 0.50 =>
    function class), so min/max/std ARE linearly present in the tokens. As predicted,
    means reconstruct better than order statistics (0.8153 vs 0.5182) -- so the architecture
    IS biased toward means, but that is a matter of degree, not of absence, and it
    does not justify patch-construction surgery. The random-init CONTROL reconstructs
    equally well (0.6177 vs trained 0.5924), confirming reconstruction is a property
    of the over-complete 120->192 linear projection rather than of training.\n\nCONSEQUENCE
    BEYOND THIS RUNBOOK: exp-046's and E29's shared headline -- 'the encoder does
    not carry momentary state' -- is READOUT-DEPENDENT and does not survive a matched
    linear readout. It should not be quoted unqualified again. The honest version
    is: under an RF on epoch-local tokens the encoder scores below its null; under
    a linear readout it clears it at z+2.83 while handcrafted features do not. The
    signal is WEAK either way (single checkpoint, single seed, one task), so this
    is not a claim that the state slot works.\n\nMETHOD LESSON worth carrying: on
    this task the shuffle null is not only feature-count-dependent (already documented
    on the diagnostic) but READOUT-dependent -- RF nulls 0.5189/0.5442, linear nulls
    0.5711/0.5310. Any future arm must recompute its null per readout, and two arms
    compared under one estimator are comparing (representation x estimator), never
    the representation alone."
priority: 10
problem_id: peri-ema-window-destroys-momentary-labels-20260815
status: abandoned
topic_tags: []
updated_at: '2026-08-18T23:22:48+00:00'
---

## Prompt
THE FUNCTION-CLASS RESIDUAL: 8 handcrafted numbers beat the whole encoder on momentary state, and an UNTRAINED encoder is inert. Work out whether the lever is the encoder's function class / epoch-local readout rather than its objective. SELF-CONTAINED prompt; a fresh session has no context from the one that queued it.

=== THE MEASURED FACT THAT MOTIVATES THIS (exp-046, 2026-08-18, commit ff14a20) ===
On E29's within-window transition rows (n=3226 epochs, 87 subjects, 660 windows, flow vs stress, pos 0.481, W=120), GroupKFold(5) by subject, RF(300, leaf3, seed0), each arm vs its OWN 24-permutation WITHIN-WINDOW shuffle null:
    raw_patch_grid_8    8 feats   AUC 0.6309  null 0.5189+-0.0422  over_null +0.1120  z +2.66  p 0.0000
    enc_pca8            8 feats   AUC 0.4950  null 0.5442+-0.0293  over_null -0.0492  z -1.68  p 0.8750
    rand_pca8           8 feats   AUC 0.5442  null 0.5464+-0.0290  over_null -0.0022  z -0.08  p 0.5000
    prod_pca8           8 feats   AUC 0.4747  null 0.5554+-0.0262  over_null -0.0807  z -3.08  p 1.0000
    enc_full         9216 feats   AUC 0.5202  null 0.5495+-0.0351  over_null -0.0293  z -0.84  p 0.7917
Harness validity: raw_patch_grid_8 and enc_full reproduced E29's 0.6309 and 0.5202 to FOUR DECIMALS against preregistered +-0.03 bands.

READ THE TRIPLE CAREFULLY. The 8 raw features are avgmax(mean,std,min,max) over patches covering [epoch-120, epoch) on the encoder's OWN grid (patch_len=120, stride=60) -- the encoder's exact temporal geometry with ZERO learned parameters. They score z+2.66. The same geometry passed through the trained encoder scores z-1.68, and through a RANDOMLY INITIALISED encoder scores z-0.08, i.e. exactly its own null.

=== WHAT THAT RULES IN AND OUT ===
OUT (do not re-run): the objective. rule_out `that-a-jepa-latent-prediction-pretraining-7061c5` eliminated a JEPA/latent-prediction Phase-A replacement as a fix FOR THIS TARGET, because an architecture whose random init is already inert gives a new pretraining objective no headroom. Also out: the dimensionality account (9216->PCA-8 moved the trained arm DOWN, -0.0293 -> -0.0492) and the production-stage account (prod_pca8 is the WORST arm, inverting the 4.3x hr_peak stage gradient of ro-dual-avg-max-pooling-downstream-20260805 -- that result does NOT transport to a momentary target).
IN (this runbook): the FUNCTION CLASS and the epoch-local readout. Candidate mechanisms to separate, cheapest first:
  (1) FlexMLP (src/models/flexmlp.py) is Linear(C->64) -> GELU -> Linear(64->C), applied PER TIMESTEP across channels. It mixes channels and cannot express a within-patch order statistic at all. TemporalPatching (src/models/patching.py) is then a single Linear(patch_len=120 -> d_model=192) per patch. The 8 winning features are min/max/std WITHIN a patch -- exactly the statistics a per-timestep MLP plus one linear patch projection is poorly placed to construct, even though the linear map 120->192 is over-complete and information-preserving in principle.
  (2) The epoch-local pooling used to READ the encoder (avgmax over covering patches) may be the wrong readout for a representation trained to serve a daily aggregate.
The distinguishing question: is the information absent from the encoder's OUTPUT, or present but not linearly accessible under this readout? Answer it with a probe, not a retrain.

=== STEP 0 (CPU, minutes, BLOCKING) ===
The cheap decisive test is a MATCHED-READOUT comparison. Re-fit both raw_patch_grid_8 and enc_pca8 with a LINEAR (logistic) readout in addition to the RF, identical rows/folds/nulls. Then, separately, fit a linear map from the encoder's per-patch tokens to the 8 raw features themselves (R2 per feature): if the encoder's tokens cannot linearly reconstruct min/max/std of their OWN input patch, that is the function-class defect named concretely and it points at patch construction. If they CAN, the information is present and the defect is in the epoch-local readout, which is a much cheaper fix.
Cost: minutes. Copy the harness from scratch/e33_state_stage_and_dim_controls.py -- it is already validated against E29.

=== SEPARATELY STILL OPEN, DO NOT CONFLATE WITH THIS ===
A SUPERVISED state head is untouched by exp-046 (nothing there trained on the state label at all). E29's supply stands: 77081 state-labelled epochs over 281 subjects {neutral 62053, stress 10297, flow 4731} across 9 datasets. That is a GPU arm with its own justification and its own runbook; it is NOT this one, and this one's Step 0 should be done first because it is minutes and may reframe it.

## Preconditions
CPU only for Step 0. Corpus data/canonical at 1-minute epochs, checkpoint outputs/checkpoints/v5c/phase_b. Read exp-046 and rule_out that-a-jepa-latent-prediction-pretraining-7061c5 first. Harness to copy verbatim: scratch/e33_state_stage_and_dim_controls.py (it reproduces E29 to 4 decimals).

## Log
- **2026-08-18T23:22:48+00:00** — [2026-08-18] ABANDONED BY ITS OWN BLOCKING GATE, ~9 min after being queued. Step 0 ran (scratch/e34_function_class_step0.py -> logs/e34_function_class_step0.json, commit b95c64d, preregistered before the data existed). Rule-out `the-function-class-account-of-exp-046-that-8-511840`.

THE GATE: 'if the raw arm's advantage disappears under a matched linear readout, the function-class framing is wrong and this runbook is ABANDONED.' It disappeared, and then some -- it INVERTED.

  arm                readout     AUC     null            over_null    z
  raw_patch_grid_8   rf       0.6309  0.5189+-0.0422     +0.1120   +2.66
  raw_patch_grid_8   linear   0.5855  0.5711+-0.0130     +0.0143   +1.10
  enc_pca8           rf       0.4950  0.5442+-0.0293     -0.0492   -1.68
  enc_pca8           linear   0.5715  0.5310+-0.0143     +0.0405   +2.83
Gate arithmetic: raw_linear - enc_linear = -0.0262, needed > +0.05. FAIL.

Under a MATCHED LINEAR readout the trained encoder CLEARS its own within-window null (z +2.83) and the raw features do NOT (z +1.10). The '8 handcrafted numbers beat the whole encoder' effect was carried by the RANDOMFOREST, not by the representations: RF suits 8 dense handcrafted features and suits 8 PCA components of a distributed code badly. The estimator was the confound.

T2 killed the other half. Fitting RidgeCV from the epoch-local tokens to the 8 raw features (OOF, GroupKFold by subject): order-statistic mean R2 0.5182 (preregistered falsifier was < 0.50 => function class), so min/max/std ARE linearly present in the tokens. As predicted, means reconstruct better than order statistics (0.8153 vs 0.5182) -- so the architecture IS biased toward means, but that is a matter of degree, not of absence, and it does not justify patch-construction surgery. The random-init CONTROL reconstructs equally well (0.6177 vs trained 0.5924), confirming reconstruction is a property of the over-complete 120->192 linear projection rather than of training.

CONSEQUENCE BEYOND THIS RUNBOOK: exp-046's and E29's shared headline -- 'the encoder does not carry momentary state' -- is READOUT-DEPENDENT and does not survive a matched linear readout. It should not be quoted unqualified again. The honest version is: under an RF on epoch-local tokens the encoder scores below its null; under a linear readout it clears it at z+2.83 while handcrafted features do not. The signal is WEAK either way (single checkpoint, single seed, one task), so this is not a claim that the state slot works.

METHOD LESSON worth carrying: on this task the shuffle null is not only feature-count-dependent (already documented on the diagnostic) but READOUT-dependent -- RF nulls 0.5189/0.5442, linear nulls 0.5711/0.5310. Any future arm must recompute its null per readout, and two arms compared under one estimator are comparing (representation x estimator), never the representation alone.

## Links
Queued for problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Intended gates: `BLOCKING STEP 0 (CPU, minutes): the 8 raw features must be shown to beat the encoder because of FUNCTION CLASS, not because of the classifier. Re-fit BOTH the 8 raw features and enc_pca8 with a LINEAR readout (logistic) as well as the RF, on identical rows/folds, each against its own 24-perm within-window null. If the raw arm's advantage disappears under a matched linear readout, the 'function class' framing is wrong and this runbook is ABANDONED.`, `Any arm claiming to fix this must be scored on eiv_momentary_state_auc_over_null against the FULL exp-046 triple (raw_patch_grid_8 +0.1120 / enc_pca8 -0.0492 / rand_pca8 -0.0022), not against enc_pca8 alone. Beating a broken arm is not evidence.`, `The random-init arm must be RE-RUN for any architecture change, not inherited. rand_pca8's -0.0022 is a property of the CURRENT architecture; a new one has its own inert baseline and it must be measured.`, `No claim of success may rest on a within-subject or cross-day null. Only the WITHIN-WINDOW shuffle null is admissible on this task (it holds subject AND day constant); ro-encoder-affect-is-time-of-day-20260815 shows what happens otherwise -- a time-of-day confound read as affect.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
