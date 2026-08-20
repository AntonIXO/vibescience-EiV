---
created_at: '2026-08-18T23:02:38+00:00'
former_ids: []
id: that-a-jepa-latent-prediction-pretraining-7061c5
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-18T23:02:38+00:00'
topic_tags:
- jepa
- control
- baseline
- pooling
- wearables
- n-of-1
---

## Claim
That a JEPA / latent-prediction pretraining objective replacing Phase A's InfoNCE-on-HR-captions is justified AS A FIX FOR MOMENTARY WITHIN-WINDOW STATE DETECTION on the encoder side. ELIMINATED for this specific motivation. Also eliminated as motivations for that same arm: (i) the claim that E29's enc_token deficit is a DIMENSIONALITY/readout artifact of comparing 8 features against 9216, and (ii) the claim that measuring at the PRODUCTION stage (channel_fusion + projector) instead of the raw encoder output will recover momentary state, by analogy with the 4.3x production-over-encoder gap that ro-dual-avg-max-pooling-downstream-20260805 measured for hr_peak. NOT ELIMINATED and explicitly still open: JEPA for removing the sensor-text caption-pair dependency in Phase A, JEPA for hr_peak/excursion representation, JEPA for robustness under NMAR missingness, and a SUPERVISED state head (nothing here trains on the state label; supply is untouched at 77081 state-labelled epochs over 281 subjects).

## Method
exp-046-e29-s-headline-momentary-state-0d3751 (hypothesis e29-s-headline-momentary-state-survives-the-58010a, verdict `refutes` -- the refuted hypothesis was MY OWN artifact-account challenge to E29, so this rule-out records that E29 SURVIVED an adversarial re-test).

scratch/e33_state_stage_and_dim_controls.py -> logs/e33_state_stage_and_dim_controls.json, commit ff14a20. The script was committed BEFORE the data existed with its predicted values and falsifier band written into the header comment (the preregistered-probe pattern), so the run could not be scored by eyeball afterwards.

Design: E29's exact within-window transition rows rebuilt via src.training.datasets.state_series (n=3226 epochs, 87 subjects, 660 windows, flow vs stress, pos 0.481, W=120), GroupKFold(5) by subject, RandomForestClassifier(300, min_samples_leaf=3, random_state=0), 24-permutation WITHIN-WINDOW shuffle null per arm (the only null that holds subject AND day constant). Checkpoint outputs/checkpoints/v5c/phase_b. PCA fit on the TRAIN FOLD ONLY inside each split, so no test information reaches the projection. prod_pca8 replicates the production path: encoder -> channel_fusion(channel_mask as pipeline builds it) -> projector query_proj + cross_attention over 128 prototypes + output_norm + rmsnorm_scale_v3 unit-RMS tail with log_scale forced to 1.0 (Phase C's projector_output_scale_override), then epoch-local avgmax, random projection d_llm 2560->48/channel, then PCA-8.

## Evidence
All five arms on identical rows/folds/seed, each vs its OWN 24-perm within-window shuffle null:
  arm               dim     AUC     null            over_null    z       p
  raw_patch_grid_8     8  0.6309  0.5189+-0.0422    +0.1120   +2.66  0.0000
  enc_pca8             8  0.4950  0.5442+-0.0293    -0.0492   -1.68  0.8750
  rand_pca8            8  0.5442  0.5464+-0.0290    -0.0022   -0.08  0.5000
  prod_pca8            8  0.4747  0.5554+-0.0262    -0.0807   -3.08  1.0000
  enc_full          9216  0.5202  0.5495+-0.0351    -0.0293   -0.84  0.7917

1. HARNESS VALIDITY (preregistered blocking, +-0.03 bands): raw_patch_grid_8 reproduced E29's 0.6309 and enc_full reproduced E29's 0.5202, both to FOUR DECIMALS. Comparisons to E29 are therefore licensed.

2. DIMENSIONALITY ACCOUNT REFUTED: 9216 -> PCA-8 moved the trained encoder -0.0293 -> -0.0492 (DOWN), against a preregistered recovery bar of >= +0.05. The width effect is real in the NULL (0.5495 -> 0.5442) and explains none of the arm deficit.

3. STAGE ACCOUNT REFUTED, AND INVERTED: prod_pca8 (-0.0807, z-3.08, p=1.0000) is the WORST arm and the only one significantly below its own null, versus enc_pca8 -0.0492. The projector's prototype cross-attention + unit-RMS tail concentrates daily-aggregate structure (what hr_peak is) and erases the momentary 2-hour contrast. The hr_peak stage gradient does NOT transport to this target.

4. THE DECISIVE COUNTERFACTUAL (which E29 never ran): a RANDOM-INIT encoder at identical geometry/pool/dim sits EXACTLY on its own null (-0.0022, z-0.08) -- inert. The trained encoder sits below it (-0.0492, z-1.68). An architecture whose random init already shows zero headroom under this readout offers a new pretraining objective nothing to win, so the objective is not the indicated lever for THIS target.

5. MARGIN DECLARED, NOT ROUNDED: the preregistered branch-(a) bar was enc-minus-rand <= -0.05; measured -0.0470, missing by 0.0030 (~a tenth of the arms' null sd ~0.029). This run therefore does NOT separate 'training destroys state' from 'the architecture never carried it'. The rule-out rests on point 4, which holds under either reading, not on the branch split.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#jepa #control #baseline #pooling #wearables #n-of-1
