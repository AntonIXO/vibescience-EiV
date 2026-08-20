---
artifacts: []
closed: true
config_note: 'CPU/GPU probe, no training. Repo /root/optiHealth-EiV, commit ff14a20
  (script committed BEFORE the data existed, with predicted values + falsifier band
  in its header). Script scratch/e33_state_stage_and_dim_controls.py -> logs/e33_state_stage_and_dim_controls.json.
  Checkpoint outputs/checkpoints/v5c/phase_b, same as E29; encoder/fusion/projector
  loaded with refusal-to-proceed on missing keys. Rows rebuilt by E29''s exact construction
  (state_series, W=120, >=3 present HR steps in [epoch-120, epoch)); E29 reference
  n=3226 / 87 subjects / 660 windows / pos 0.481. GroupKFold(5) by subject, RandomForestClassifier(300,
  min_samples_leaf=3, random_state=0), 24-permutation within-window shuffle null per
  arm — all identical to E29 so the arms are comparable. FIVE arms: raw_patch_grid_8
  (E29 positive control), enc_pca8 (trained encoder, train-fold-only PCA to 8 comps),
  rand_pca8 (random-init encoder, identical geometry/pool/dim), prod_pca8 (encoder->channel_fusion->projector
  query_proj+cross_attention+RMSNorm, epoch-local avgmax, random projection d_llm=2560->48/channel
  then PCA-8), enc_full (E29 replication at 9216). PCA is fit INSIDE each train fold
  only. BASELINES FROZEN PRE-RUN from logs/e29_state_localisation_and_supply.json:
  raw_patch_grid AUC 0.6309 null 0.5189+-0.0422 z+2.66; enc_token AUC 0.5202 null
  0.5495+-0.0351 z-0.84; raw_shallow AUC 0.6430. P2/P3 reproduction bands (+-0.03)
  are harness-validity gates: if they fail, no other number in this run may be compared
  to E29.'
cost: null
crash_reason: ''
created_at: '2026-08-18T22:43:52+00:00'
diagnostics_measured:
- after: -0.0492
  before: -0.0293
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS (reporting completeness). enc_pca8 measured on E29''s exact rows
    and folds: n=3226 epochs, 87 subjects, 660 windows, pos rate 0.481, feature dim
    8 (train-fold-only PCA from 9216). AUC 0.4950, own 24-perm within-window shuffle
    null 0.5442 +- 0.0293, over_null -0.0492, z -1.68, perm p 0.8750. Harness validity
    confirmed by the two reproduction gates in the same run: raw_patch_grid_8 reproduced
    E29''s 0.6309 EXACTLY (null 0.5189+-0.0422, z+2.66, p 0.0000) and enc_full reproduced
    E29''s enc_token 0.5202 EXACTLY (null 0.5495+-0.0351, z-0.84, p 0.7917), both
    to 4 decimal places, so every arm here is directly comparable to E29.'
  gate_id: g1-dim-matched-trained-arm-with-own-null
  passed: true
- evidence: 'PASS. rand_pca8 ran: random-init TemporalEncoder at identical patch geometry
    (patch_len=120, stride=60), identical epoch-local avgmax pooling over covering
    patches, identical train-fold-only PCA to 8 components, identical rows/folds/seed/classifier.
    AUC 0.5442, own null 0.5464 +- 0.0290, over_null -0.0022, z -0.08, perm p 0.5000.
    The counterfactual E29 lacked now exists: an UNTRAINED encoder of this architecture
    sits EXACTLY ON its own null (inert, carries nothing), while the TRAINED encoder
    sits below it (-0.0492, z-1.68).'
  gate_id: g2-random-init-counterfactual-exists
  passed: true
- evidence: 'PASS (measured; result is negative and that is legitimate under this
    gate''s own wording). prod_pca8 carried the tensor all the way to the production
    soft-token stage: encoder -> channel_fusion (channel_mask built as pipeline does)
    -> projector query_proj + cross_attention over the 128 learned prototypes + output_norm
    + rmsnorm_scale_v3 unit-RMS tail with log_scale forced to 1.0 (Phase C''s projector_output_scale_override),
    then epoch-local avgmax over the same covering patches, random projection d_llm
    2560->48 per channel, then train-fold-only PCA-8. AUC 0.4747, own null 0.5554
    +- 0.0262, over_null -0.0807, z -3.08, perm p 1.0000. This is the WORST arm in
    the run and the only one significantly below its own null. The stage confound
    is refuted in the OPPOSITE direction to my prediction: I expected production to
    be BETTER than the encoder stage (the 4.3x hr_peak result of ro-dual-avg-max-pooling-downstream-20260805);
    for momentary state it is WORSE (-0.0807 vs -0.0492).'
  gate_id: g3-production-stage-arm
  passed: true
- evidence: 'PASS (rule resolved explicitly from the measured arms, not from enthusiasm).
    Measured: enc_pca8 - rand_pca8 = -0.0470 against a preregistered branch-(a) bar
    of <= -0.05; enc_pca8 - raw_patch_grid_8 = -0.1613. BRANCH (b) FIRED, BY 0.0030.
    Under the committed rule this reads: the loss is architectural/readout rather
    than objective-owned, so swapping InfoNCE for a JEPA latent-prediction objective
    changes the wrong variable and a JEPA GPU arm is NOT JUSTIFIED AS A STATE FIX
    on this evidence. HONESTY ON THE MARGIN, stated rather than resolved in my favour:
    -0.0470 misses branch (a) by 0.0030, which is a tenth of the arms'' own null sd
    (~0.029), so this run genuinely CANNOT separate (a) from (b) at the resolution
    it preregistered. The qualitative pattern leans (a) -- random-init sits exactly
    on its null (z-0.08, inert) while trained sits below it (z-1.68) -- but leaning
    is not clearing a committed bar, and the bar was committed precisely so this could
    not be argued after the fact. The load-bearing conclusion does NOT depend on the
    (a)/(b) split: BOTH trained and random-init encoders fail to expose state (over_null
    -0.0492 and -0.0022) where 8 handcrafted raw features on the SAME patch geometry
    succeed at +0.1120 / z+2.66. A new objective on an architecture whose RANDOM INIT
    already shows no headroom under this readout has no demonstrated room to win here.
    JEPA is therefore not funded by THIS evidence as a momentary-state fix; it remains
    untested, and separately arguable, for the text-pair-dependency and hr_peak axes
    it was actually designed to address.'
  gate_id: g4-jepa-decision-rule-stated-from-measured-arms
  passed: true
git_ref: ''
hypothesis_id: e29-s-headline-momentary-state-survives-the-58010a
id: exp-046-e29-s-headline-momentary-state-0d3751
interpretation:
  at: '2026-08-18T23:02:09+00:00'
  failure_mode: claim_false
  lesson: 'I challenged E29 on two confounds and BOTH challenges died, which promoted
    E29''s finding from ''suggestive but confounded'' to ''survived an adversarial
    re-test''. Dimension-matching the trained encoder (9216 -> PCA-8, train-fold only)
    moved it from -0.0293 to -0.0492 over its own null: DOWN, where I predicted >=
    +0.05. The dimensionality story was a real mechanism (the null does rise with
    width, 0.5442 at 8 vs 0.5495 at 9216, exactly as the diagnostic''s description
    warns) that explained none of the arm''s deficit.


    THE REUSABLE MISTAKE: I transported a stage result across TARGETS. ro-dual-avg-max-pooling-downstream-20260805
    measured the production stage reading 4.3x better than the encoder stage on hr_peak
    increment (+0.7211 vs +0.1694), and I assumed momentary state would follow the
    same gradient. It inverted: prod_pca8 is the worst arm in the run (-0.0807, z-3.08,
    p=1.0000), significantly BELOW its own null, versus enc_pca8 at -0.0492. The projector''s
    cross-attention over 128 learned prototypes plus a unit-RMS tail concentrates
    DAILY-AGGREGATE structure -- which is what hr_peak is -- and that is precisely
    the operation that erases a momentary 2-hour contrast. ''Evidence is bound to
    its substrate'' has a second half nobody had written down here: it is bound to
    its TARGET too. A stage/pooling/geometry result measured on a daily aggregate
    is not evidence about a within-window momentary label, and vice versa.


    THE MOST VALUABLE NUMBER IS THE ONE E29 NEVER RAN. The random-init counterfactual
    sits EXACTLY on its own null (-0.0022, z-0.08): an untrained encoder of this architecture
    is inert on this task. The trained one sits below it (-0.0492, z-1.68). So the
    honest reframing of E29 is not ''training destroyed the state signal'' but ''this
    architecture never surfaced momentary state at all -- untrained it carries nothing,
    trained it carries slightly less than nothing -- while 8 handcrafted raw features
    on its OWN patch grid read the same rows at +0.1120, z+2.66, with zero learned
    parameters''. The information is in the input and the function class never exposes
    it.


    JEPA DECISION, and the discipline that produced it: the preregistered branch-(a)
    bar was enc-minus-rand <= -0.05; measured -0.0470. Branch (b) fired BY 0.0030,
    a tenth of the null sd -- so this run genuinely cannot separate ''the objective
    destroys state'' from ''the architecture never had it''. I am recording that as
    unresolved rather than rounding toward the branch I preferred going in. The JEPA
    verdict does not rest on that split: an architecture whose RANDOM INIT already
    shows zero headroom under this readout gives a new pretraining objective nothing
    to win. A JEPA Phase-A replacement is NOT funded as a momentary-state fix.


    SCOPE THIS DOES NOT COVER (so it cannot be over-cited later): nothing here tests
    JEPA on the axes JEPA.md actually proposes it for -- eliminating the sensor-text
    caption dependency in Phase A, hr_peak/excursion representation quality, or robustness
    under NMAR missingness. Nothing here trains on the state label, so a supervised
    state head is untouched (supply remains 77081 epochs / 281 subjects). And the
    whole run is encoder-side READOUT on the flow-vs-stress transition population
    at W=120.'
  transfers_to:
  - e29-state-geometry-innocent-weights-guilty-20260815
key_numbers: {}
observed_effects:
- delta: -0.0199
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_momentary_state_auc_over_null: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[e29-s-headline-momentary-state-survives-the-58010a]].

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before -0.0293 → after -0.0492  (Δ -0.0199, down ✗ predicted up)

## Gates
- `g1-dim-matched-trained-arm-with-own-null`: PASS — PASS (reporting completeness). enc_pca8 measured on E29's exact rows and folds: n=3226 epochs, 87 subjects, 660 windows, pos rate 0.481, feature dim 8 (train-fold-only PCA from 9216). AUC 0.4950, own 24-perm within-window shuffle null 0.5442 +- 0.0293, over_null -0.0492, z -1.68, perm p 0.8750. Harness validity confirmed by the two reproduction gates in the same run: raw_patch_grid_8 reproduced E29's 0.6309 EXACTLY (null 0.5189+-0.0422, z+2.66, p 0.0000) and enc_full reproduced E29's enc_token 0.5202 EXACTLY (null 0.5495+-0.0351, z-0.84, p 0.7917), both to 4 decimal places, so every arm here is directly comparable to E29.
- `g2-random-init-counterfactual-exists`: PASS — PASS. rand_pca8 ran: random-init TemporalEncoder at identical patch geometry (patch_len=120, stride=60), identical epoch-local avgmax pooling over covering patches, identical train-fold-only PCA to 8 components, identical rows/folds/seed/classifier. AUC 0.5442, own null 0.5464 +- 0.0290, over_null -0.0022, z -0.08, perm p 0.5000. The counterfactual E29 lacked now exists: an UNTRAINED encoder of this architecture sits EXACTLY ON its own null (inert, carries nothing), while the TRAINED encoder sits below it (-0.0492, z-1.68).
- `g3-production-stage-arm`: PASS — PASS (measured; result is negative and that is legitimate under this gate's own wording). prod_pca8 carried the tensor all the way to the production soft-token stage: encoder -> channel_fusion (channel_mask built as pipeline does) -> projector query_proj + cross_attention over the 128 learned prototypes + output_norm + rmsnorm_scale_v3 unit-RMS tail with log_scale forced to 1.0 (Phase C's projector_output_scale_override), then epoch-local avgmax over the same covering patches, random projection d_llm 2560->48 per channel, then train-fold-only PCA-8. AUC 0.4747, own null 0.5554 +- 0.0262, over_null -0.0807, z -3.08, perm p 1.0000. This is the WORST arm in the run and the only one significantly below its own null. The stage confound is refuted in the OPPOSITE direction to my prediction: I expected production to be BETTER than the encoder stage (the 4.3x hr_peak result of ro-dual-avg-max-pooling-downstream-20260805); for momentary state it is WORSE (-0.0807 vs -0.0492).
- `g4-jepa-decision-rule-stated-from-measured-arms`: PASS — PASS (rule resolved explicitly from the measured arms, not from enthusiasm). Measured: enc_pca8 - rand_pca8 = -0.0470 against a preregistered branch-(a) bar of <= -0.05; enc_pca8 - raw_patch_grid_8 = -0.1613. BRANCH (b) FIRED, BY 0.0030. Under the committed rule this reads: the loss is architectural/readout rather than objective-owned, so swapping InfoNCE for a JEPA latent-prediction objective changes the wrong variable and a JEPA GPU arm is NOT JUSTIFIED AS A STATE FIX on this evidence. HONESTY ON THE MARGIN, stated rather than resolved in my favour: -0.0470 misses branch (a) by 0.0030, which is a tenth of the arms' own null sd (~0.029), so this run genuinely CANNOT separate (a) from (b) at the resolution it preregistered. The qualitative pattern leans (a) -- random-init sits exactly on its null (z-0.08, inert) while trained sits below it (z-1.68) -- but leaning is not clearing a committed bar, and the bar was committed precisely so this could not be argued after the fact. The load-bearing conclusion does NOT depend on the (a)/(b) split: BOTH trained and random-init encoders fail to expose state (over_null -0.0492 and -0.0022) where 8 handcrafted raw features on the SAME patch geometry succeed at +0.1120 / z+2.66. A new objective on an architecture whose RANDOM INIT already shows no headroom under this readout has no demonstrated room to win here. JEPA is therefore not funded by THIS evidence as a momentary-state fix; it remains untested, and separately arguable, for the text-pair-dependency and hr_peak axes it was actually designed to address.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

I challenged E29 on two confounds and BOTH challenges died, which promoted E29's finding from 'suggestive but confounded' to 'survived an adversarial re-test'. Dimension-matching the trained encoder (9216 -> PCA-8, train-fold only) moved it from -0.0293 to -0.0492 over its own null: DOWN, where I predicted >= +0.05. The dimensionality story was a real mechanism (the null does rise with width, 0.5442 at 8 vs 0.5495 at 9216, exactly as the diagnostic's description warns) that explained none of the arm's deficit.

THE REUSABLE MISTAKE: I transported a stage result across TARGETS. ro-dual-avg-max-pooling-downstream-20260805 measured the production stage reading 4.3x better than the encoder stage on hr_peak increment (+0.7211 vs +0.1694), and I assumed momentary state would follow the same gradient. It inverted: prod_pca8 is the worst arm in the run (-0.0807, z-3.08, p=1.0000), significantly BELOW its own null, versus enc_pca8 at -0.0492. The projector's cross-attention over 128 learned prototypes plus a unit-RMS tail concentrates DAILY-AGGREGATE structure -- which is what hr_peak is -- and that is precisely the operation that erases a momentary 2-hour contrast. 'Evidence is bound to its substrate' has a second half nobody had written down here: it is bound to its TARGET too. A stage/pooling/geometry result measured on a daily aggregate is not evidence about a within-window momentary label, and vice versa.

THE MOST VALUABLE NUMBER IS THE ONE E29 NEVER RAN. The random-init counterfactual sits EXACTLY on its own null (-0.0022, z-0.08): an untrained encoder of this architecture is inert on this task. The trained one sits below it (-0.0492, z-1.68). So the honest reframing of E29 is not 'training destroyed the state signal' but 'this architecture never surfaced momentary state at all -- untrained it carries nothing, trained it carries slightly less than nothing -- while 8 handcrafted raw features on its OWN patch grid read the same rows at +0.1120, z+2.66, with zero learned parameters'. The information is in the input and the function class never exposes it.

JEPA DECISION, and the discipline that produced it: the preregistered branch-(a) bar was enc-minus-rand <= -0.05; measured -0.0470. Branch (b) fired BY 0.0030, a tenth of the null sd -- so this run genuinely cannot separate 'the objective destroys state' from 'the architecture never had it'. I am recording that as unresolved rather than rounding toward the branch I preferred going in. The JEPA verdict does not rest on that split: an architecture whose RANDOM INIT already shows zero headroom under this readout gives a new pretraining objective nothing to win. A JEPA Phase-A replacement is NOT funded as a momentary-state fix.

SCOPE THIS DOES NOT COVER (so it cannot be over-cited later): nothing here tests JEPA on the axes JEPA.md actually proposes it for -- eliminating the sensor-text caption dependency in Phase A, hr_peak/excursion representation quality, or robustness under NMAR missingness. Nothing here trains on the state label, so a supervised state head is untouched (supply remains 77081 epochs / 281 subjects). And the whole run is encoder-side READOUT on the flow-vs-stress transition population at W=120.

Transfers to: [[e29-state-geometry-innocent-weights-guilty-20260815]]

## Notes
ARTIFACTS: scratch/e33_state_stage_and_dim_controls.py, logs/e33_state_stage_and_dim_controls.json, logs/e33_run.log, commit ff14a20 (script committed BEFORE the data existed, predictions + falsifier in its header).

MY PRIMARY PREDICTION FAILED, AND THE FAILURE IS THE RESULT. I predicted the dimension-matched trained encoder would recover to >= +0.05 over its own null, i.e. that a substantial part of E29's 'the learned weights destroy state' was a dimensionality/readout artifact. It measured -0.0492. The artifact account is REFUTED and E29's reading SURVIVES the two confounds I raised against it.

ARMS (E29's exact rows n=3226 / 87 subjects / 660 windows / pos 0.481; GroupKFold by subject; RF(300, leaf3, seed0); 24-perm within-window shuffle null per arm; PCA fit train-fold-only):
  arm               dim     AUC     null            over_null    z       p
  raw_patch_grid_8     8  0.6309  0.5189+-0.0422    +0.1120   +2.66  0.0000
  enc_pca8             8  0.4950  0.5442+-0.0293    -0.0492   -1.68  0.8750
  rand_pca8            8  0.5442  0.5464+-0.0290    -0.0022   -0.08  0.5000
  prod_pca8            8  0.4747  0.5554+-0.0262    -0.0807   -3.08  1.0000
  enc_full          9216  0.5202  0.5495+-0.0351    -0.0293   -0.84  0.7917

HARNESS VALIDITY FIRST: raw_patch_grid_8 reproduced E29's 0.6309 and enc_full reproduced E29's 0.5202, both to FOUR DECIMAL PLACES, against bands of +-0.03 committed before the run. These were preregistered as blocking validity checks precisely so that a failure to reproduce would forbid comparing anything else to E29. They passed, so every number here is comparable.

WHAT DIED (three of my own claims, all filed against me):
1. THE DIMENSIONALITY CONFOUND IS NOT LOAD-BEARING. Compressing 9216 -> 8 components moved the trained encoder from -0.0293 to -0.0492, i.e. slightly DOWN, not up. The RF-width handicap argument (sqrt(9216)=96 columns sampled per split) was real as a mechanism and irrelevant as an explanation. Note the null does behave exactly as the diagnostic's description predicts (0.5495 at 9216 -> 0.5442 at 8), so the width effect on the NULL is confirmed while its effect on the ARM is not.
2. THE STAGE CONFOUND IS REFUTED IN THE OPPOSITE DIRECTION. I imported ro-dual-avg-max-pooling-downstream-20260805's finding that the production stage reads 4.3x better than the encoder stage (hr_peak increment +0.7211 vs +0.1694) and predicted state would follow. It does not: prod_pca8 is the WORST arm (-0.0807, z-3.08, p=1.0000), the only one significantly BELOW its own null. Evidence is bound to its substrate and its TARGET: a stage that concentrates daily-aggregate HR level demonstrably does not preserve a momentary 2-hour contrast. Transporting a stage result across targets was my error.
3. ONE CONFOUND SURVIVED AND CHANGED THE PICTURE, JUST NOT IN MY FAVOUR. The random-init counterfactual E29 never ran now exists, and it is the most informative number here: an UNTRAINED encoder at identical geometry/pool/dim sits EXACTLY ON its own null (-0.0022, z-0.08) -- inert, carrying nothing -- while the TRAINED encoder sits BELOW it (-0.0492, z-1.68). So E29's phrase 'the learned weights destroy state' is defensible in sign, but the sharper and better-supported statement is that this architecture never CARRIED momentary state to begin with: its random init has no state to destroy.

DECISION RULE, RESOLVED HONESTLY ON A MARGIN I WILL NOT ROUND: enc_pca8 - rand_pca8 = -0.0470 against a branch-(a) bar of <= -0.05. BRANCH (b) FIRED, BY 0.0030 -- about a tenth of the arms' own null sd (~0.029). This run therefore cannot separate (a) 'training actively destroys state, objective is the culprit, JEPA justified' from (b) 'architectural/readout, JEPA changes the wrong variable' at the resolution it committed to. I am recording that as an ambiguity rather than resolving it toward the answer I preferred going in.

WHY THE JEPA CONCLUSION SURVIVES THAT AMBIGUITY ANYWAY: the load-bearing fact is not the (a)/(b) split. It is that BOTH the trained and the randomly-initialised encoder fail to expose momentary state (-0.0492, -0.0022) on the exact geometry where 8 handcrafted raw features score +0.1120 at z+2.66 with zero learned parameters. The information is sitting in the input, 8 numbers are enough to read it, and this architecture surfaces none of it TRAINED OR UNTRAINED. Replacing the training objective is not indicated by that pattern; it is an argument about the encoder's function class and its epoch-local readout.

SCOPE -- WHAT THIS DOES *NOT* CLOSE (deliberately narrow, per rule-out hygiene): this is a probe of ENCODER-SIDE READOUT of MOMENTARY WITHIN-WINDOW STATE on the flow-vs-stress transition population at W=120. It says NOTHING about JEPA for the axes it was actually proposed for in JEPA.md -- removing the sensor-text pair dependency in Phase A, hr_peak/excursion representation quality, or robustness under missingness. Those remain untested and separately arguable. It also does not close a SUPERVISED state head: E29's supply count (77081 state-labelled epochs, 281 subjects) is untouched, and nothing here was trained on the state label at all.

BUDGET: ~13 minutes CPU/GPU probe, zero training. It replaced a GPU arm that would have been justified by a headline I have now shown rests on a partly-wrong mechanism.

#jepa #control #baseline #wearables #n-of-1 #pooling #epoch-resolution #optihealth-eiv #matched-baseline #representation-learning #subject-heldout #data-contract-violation
