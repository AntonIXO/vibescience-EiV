---
created_at: '2026-08-15T10:20:30+00:00'
former_ids: []
gates:
- blocking: true
  description: 'GEOMETRY GATE (the one that decides whether the supervised-head GPU
    arm is justified at all): raw features on the ENCODER''S OWN patch grid must land
    within 0.05 AUC of raw_shallow AND clear their own within-window shuffle null
    with perm p < 0.05. MEASURED: 0.6309 vs raw_shallow 0.6430 (gap 0.0121), null
    0.5189+-0.0422, z +2.66, perm p 0.0000. PASS.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g1_patch_geometry_preserves_state
  mde: 0.05
- blocking: false
  description: 'REPRODUCTION GATE: the trained encoder''s tokens must fail to beat
    their own within-window shuffle null, reproducing exp-e28''s finding with an independent
    harness on the same rows. MEASURED: 0.5202 vs null 0.5495+-0.0351, z -0.84, perm
    p 0.7917. CONFIRMED (fails to beat null, as predicted).'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g2_encoder_tokens_reproduce_e28_null
  mde: 0.05
id: e29-state-geometry-innocent-weights-guilty-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: 'PRIMARY. raw_patch_grid (the encoder''s temporal geometry, none
    of its learned weights) will RETAIN the shallow signal: predicted AUC within ~0.05
    of raw_shallow and clearing its own within-window shuffle null by >2 SD. The falsifying
    outcome was explicit and would have killed the GPU arm: if raw_patch_grid landed
    at or below its null, the 120/60 patch geometry itself would be the blocker, no
    objective on that geometry could recover momentary state, and the supervised head
    would be recorded as unjustified — exactly as rb-w4 was closed earlier the same
    day.'
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: none
  magnitude_note: 'SECONDARY, reproduction of exp-e28 with an independent harness
    on the same rows: the TRAINED encoder''s tokens will NOT beat their own within-window
    shuffle null.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
status: confirmed
supersedes: null
topic_tags:
- n-of-1
- control
- baseline
- wearables
- epoch-resolution
---

## Statement
E28 refuted the triplet miner and left one residual: nothing has ever optimised the momentary state label DIRECTLY (state has only been a 0.05-weighted auxiliary margin while InfoNCE-on-HR-captions dominated the gradient). That residual is a GPU retrain, so it needs a cheap CPU gate separating two explanations of E28's null: (a) the objective never asked for state, vs (b) this architecture/geometry cannot represent state. E29 inserts the missing middle term between E26 (shallow features, 0.6329) and E28 (trained encoder tokens, ~0.53): RAW canonical HR read on the ENCODER'S OWN patch grid (patch_len=120, stride=60, avgmax over covering patches) — the encoder's exact temporal geometry with NONE of its learned weights. CLAIM: the geometry is innocent and the learned weights are guilty, which eliminates (b) and justifies the supervised state head.

## Rationale
E22/E27 established that frozen encoder tokens are worse than shallow features on the state axis and artefact-prone under shuffle. E28 then showed that fixing the triplet miner does not create representation. But every one of those measurements compares SHALLOW FEATURES against ENCODER OUTPUT, which confounds two distinct things: the temporal patching (how the day is cut into tokens) and the learned InfoNCE weights (what those tokens are trained to encode). Nobody had isolated the patch geometry. If 120/60 patching itself averaged away a momentary event, no objective on that geometry could recover it and the supervised head would be dead on arrival — the same way rb-w4's lever turned out not to exist once measured at the production site. This arm is the cheap discriminator, and it was designed to be able to kill the GPU run.

## Plan
E26's exact rows (n=3226 epochs, 87 subjects, 660 transition windows, flow vs stress — two DIFFERENT states, SAME subject, SAME day, minutes apart, so trait is held constant by construction). GroupKFold(5) by subject, RandomForest(300, min_samples_leaf=3). Three feature sets on identical rows and folds: raw_shallow (E26's 15 percentile features, the known 0.6329 reference), raw_patch_grid (8 features: avgmax over the patches covering [epoch-120, epoch) on the encoder's own 120/60 grid, ZERO learned parameters), enc_token (the trained v5c Phase-B encoder's tokens, same covering patches, same avgmax pool, 9216 dims). Every arm scored against its OWN 24-permutation WITHIN-WINDOW shuffle null. Supply counted in the same run via src.training.datasets.state_series. Artefacts: scratch/e29_state_localisation_and_supply.py, logs/e29_state_localisation_and_supply.json, commit b34ed44.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Predicts [[eiv_momentary_state_auc_over_null]] (none).
Gate `g1_patch_geometry_preserves_state` (blocking): GEOMETRY GATE (the one that decides whether the supervised-head GPU arm is justified at all): raw features on the ENCODER'S OWN patch grid must land within 0.05 AUC of raw_shallow AND clear their own within-window shuffle null with perm p < 0.05. MEASURED: 0.6309 vs raw_shallow 0.6430 (gap 0.0121), null 0.5189+-0.0422, z +2.66, perm p 0.0000. PASS.
Gate `g2_encoder_tokens_reproduce_e28_null` (advisory): REPRODUCTION GATE: the trained encoder's tokens must fail to beat their own within-window shuffle null, reproducing exp-e28's finding with an independent harness on the same rows. MEASURED: 0.5202 vs null 0.5495+-0.0351, z -0.84, perm p 0.7917. CONFIRMED (fails to beat null, as predicted).

#n-of-1 #control #baseline #wearables #epoch-resolution #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
