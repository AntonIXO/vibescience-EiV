---
ambition_class: null
created_at: '2026-08-18T22:42:11+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: The trained-encoder arm reduced to 8 components by train-fold-only
    PCA is scored on E29's exact rows/folds and reported with its OWN 24-permutation
    within-window shuffle null (mean +- sd), z, and permutation p. PASS = reported
    with all of raw AUC, null mean, null sd, z, p, n, n_subjects, n_windows, pos rate
    and feature dim, per the diagnostic's mandate. This gate is about REPORTING COMPLETENESS,
    not about the sign of the result.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g1-dim-matched-trained-arm-with-own-null
  mde: null
- blocking: true
  description: A RANDOM-INIT encoder arm at IDENTICAL patch geometry, identical avgmax
    pooling, identical PCA-8 dimension and identical rows/folds is run and reported
    with its own null. Without it, 'the learned weights destroy state' has no counterfactual
    and cannot be asserted. PASS = the arm ran and its AUC-over-null is recorded alongside
    the trained arm.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g2-random-init-counterfactual-exists
  mde: null
- blocking: true
  description: A PRODUCTION-STAGE arm (encoder -> channel_fusion -> projector query_proj/cross_attention/RMSNorm,
    i.e. the tensor that actually becomes the soft-token prefix) is measured epoch-locally
    over the same covering patches, at matched PCA-8 dimension, with its own null.
    Exists because ro-dual-avg-max-pooling-downstream-20260805 established the encoder
    stage is NOT the representation the downstream consumer receives. PASS = the arm
    ran and is reported; it may legitimately fail to beat its null, but it must be
    measured.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g3-production-stage-arm
  mde: null
- blocking: true
  description: The preregistered three-way decision rule (a/b/c in the statement)
    is resolved EXPLICITLY against the measured arms, and the JEPA GPU arm is declared
    justified or not justified on that basis -- not on enthusiasm for the method.
    PASS = the close notes name which branch fired and what it implies for spending
    GPU on a JEPA Phase-A replacement.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g4-jepa-decision-rule-stated-from-measured-arms
  mde: null
id: e29-s-headline-momentary-state-survives-the-58010a
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: dimension-matched (PCA-8) trained-encoder arm >= +0.05 AUC over
    its own within-window shuffle null, vs E29's raw 9216-feature enc_token at -0.0293
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- representation-learning
- subject-heldout
- data-contract-violation
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- jepa
- control
- baseline
- wearables
- n-of-1
- pooling
- epoch-resolution
---

## Statement
E29's headline -- 'momentary state survives the encoder's PATCH GEOMETRY (raw_patch_grid AUC 0.6309, z+2.66) and dies in its LEARNED WEIGHTS (enc_token 0.5202, z-0.84)' -- is not yet attributable to the learned weights, because the two arms differ in THREE variables at once, not one. (1) STAGE: enc_token was read at the raw TemporalEncoder output; the vault has already established via ro-dual-avg-max-pooling-downstream-20260805 that the encoder stage understates the production representation by 4.3x on hr_peak increment (+0.1694 encoder vs +0.7211 production, matched k), because production interposes channel_fusion + query_proj + cross_attention + RMSNorm. (2) DIMENSIONALITY: 8 features vs 9216, on n=3226 with GroupKFold and a RandomForest -- and the diagnostic eiv_momentary_state_auc_over_null's own description mandates reporting dim precisely because its null RISES with width (0.5189 at 8 feats -> 0.5495 at 9216). A RandomForest sampling sqrt(9216)=96 of 9216 mostly-uninformative columns per split is a readout handicap, not a statement about the representation. (3) TRAINING: no random-init arm at matched geometry+dim was ever run, so 'the learned weights destroyed it' has no counterfactual. THIS EXPERIMENT ADDS THE MISSING CONTROLS on E29's exact rows/folds/seed: a dimension-matched (train-fold-only PCA to 8 components) trained-encoder arm, a RANDOM-INIT encoder arm identical in geometry/dim/pool, and a PRODUCTION-STAGE arm carried through channel_fusion+projector. PRIMARY PREDICTION: the dimension-matched trained-encoder arm rises materially above E29's raw enc_token value of -0.0293 over its own null, i.e. a substantial part of E29's 'the weights destroy state' is a dimensionality/readout artifact rather than a property of the representation. THIS IS THE GO/NO-GO GATE FOR A JEPA ARM, and the decision rule is preregistered three ways: (a) if the dim-matched TRAINED arm scores BELOW the dim-matched RANDOM-INIT arm, training actively destroys state and the OBJECTIVE is the culprit -- JEPA (which replaces exactly that objective, latent prediction instead of InfoNCE-on-HR-captions) is justified and a GPU arm is warranted; (b) if trained ~= random-init and both fall short of raw_patch_grid, the loss is ARCHITECTURAL/readout and not the objective -- swapping InfoNCE for JEPA changes the wrong variable and the GPU arm is NOT justified as a state fix; (c) if the dim-matched trained arm recovers to ~raw_patch_grid, E29's finding was largely a measurement artifact, the encoder does carry state, and neither a supervised state head nor JEPA is motivated by THIS evidence. Magnitude note: 'materially above' is preregistered as >= +0.05 AUC-over-null, which is ~1.2x the 0.0422 null sd of the 8-feature arm and comfortably inside the +0.1120 that raw_patch_grid achieves; mechanism note: PCA is fit on the TRAIN FOLD ONLY inside each GroupKFold split, so no test information reaches the projection.

## Rationale


## Plan


## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g1-dim-matched-trained-arm-with-own-null` (blocking): The trained-encoder arm reduced to 8 components by train-fold-only PCA is scored on E29's exact rows/folds and reported with its OWN 24-permutation within-window shuffle null (mean +- sd), z, and permutation p. PASS = reported with all of raw AUC, null mean, null sd, z, p, n, n_subjects, n_windows, pos rate and feature dim, per the diagnostic's mandate. This gate is about REPORTING COMPLETENESS, not about the sign of the result.
Gate `g2-random-init-counterfactual-exists` (blocking): A RANDOM-INIT encoder arm at IDENTICAL patch geometry, identical avgmax pooling, identical PCA-8 dimension and identical rows/folds is run and reported with its own null. Without it, 'the learned weights destroy state' has no counterfactual and cannot be asserted. PASS = the arm ran and its AUC-over-null is recorded alongside the trained arm.
Gate `g3-production-stage-arm` (blocking): A PRODUCTION-STAGE arm (encoder -> channel_fusion -> projector query_proj/cross_attention/RMSNorm, i.e. the tensor that actually becomes the soft-token prefix) is measured epoch-locally over the same covering patches, at matched PCA-8 dimension, with its own null. Exists because ro-dual-avg-max-pooling-downstream-20260805 established the encoder stage is NOT the representation the downstream consumer receives. PASS = the arm ran and is reported; it may legitimately fail to beat its null, but it must be measured.
Gate `g4-jepa-decision-rule-stated-from-measured-arms` (blocking): The preregistered three-way decision rule (a/b/c in the statement) is resolved EXPLICITLY against the measured arms, and the JEPA GPU arm is declared justified or not justified on that basis -- not on enthusiasm for the method. PASS = the close notes name which branch fired and what it implies for spending GPU on a JEPA Phase-A replacement.

#jepa #control #baseline #wearables #n-of-1 #pooling #epoch-resolution #optihealth-eiv #matched-baseline #representation-learning #subject-heldout #data-contract-violation
