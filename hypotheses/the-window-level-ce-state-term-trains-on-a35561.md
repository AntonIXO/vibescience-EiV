---
ambition_class: mechanism
created_at: '2026-08-30T07:59:32+00:00'
former_ids: []
gap_closure_estimate: 0.25
gates:
- blocking: true
  description: 'g1 BLOCKING (the fabricated supervision is actually removed): with
    the presence mask live, the number of windows contributing to the window-level
    ce_state term must equal the number carrying a real state label -- 5,628 of 9,479
    on the v11 A/B roster -- and NOT 9,479. Asserted directly on the batch by a unit
    test that counts scored rows, not inferred from a loss value. If a batch annotates
    no window at all, the term must return a differentiable zero rather than a neutral-shaped
    gradient (the same contract patch_state_loss already honours).'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g1-fabricated-neutral-no-longer-trained
  mde: 0.02
- blocking: true
  description: 'g2 BLOCKING (nothing else moves): with every window labelled, the
    masked loss must equal the unmasked loss to within 1e-6 -- i.e. the change is
    a no-op on a fully-labelled batch and cannot be silently altering the objective
    for arms that were never affected. Also: the 856-test suite must stay green, and
    `state_target: hard` must keep working (this is not a soft-only fix).'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g2-no-op-where-every-window-is-labelled
  mde: 0.02
- blocking: false
  description: 'g3 (non-blocking, mechanism): report which datasets stop contributing
    fabricated neutral. Expected from the pre-fix census: big_ideas_glycemic (145
    windows), optihealth_supabase (302), mmash (42), brain_wearable_monitoring (21)
    carry NO state label in any window and are currently 100% fabricated; lifesnaps
    contributes 2,749 unlabelled and pmdata 519. Diagnostic only -- it does not gate.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g3-which-corpora-stop-fabricating
  mde: 0.02
id: the-window-level-ce-state-term-trains-on-a35561
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: 'Primary. 40.6% of the window-level state gradient currently points
    at a label nobody recorded, so removing it should help -- but I am NOT predicting
    a large effect and the vault''s base rate says so. exp-w8 measured that a random-init
    encoder matches or beats the trained one on this diagnostic in all four cells,
    i.e. Phase A/B training puts almost nothing state-shaped into the tokens to begin
    with. Cleaning a gradient that was not creating signal may well change nothing
    measurable. I predict +0.00 to +0.03 over null, and the honest primary claim of
    this arm is the MECHANISM (fabricated supervision removed, asserted by test),
    not the metric. Calibration: 2/7 on this diagnostic, 0.411 overall.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- data-contract-violation
- matched-baseline
- optihealth-eiv
- subject-heldout
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- state-supervision
- control
- baseline
- wearables
- epoch-resolution
---

## Statement
The window-level `ce_state` term trains on FABRICATED supervision: 3,851 of 9,479 admitted windows (40.6% of the v11 A/B roster) carry no state annotation, and `pipeline.py:2108` maps each of them to a `neutral` one-hot with no presence mask -- so 40.6% of that gradient points at a label nobody recorded. Masking the term to annotated windows only, exactly as `patch_state_loss` already does on the patch axis, removes that fabricated supervision without changing the objective for any window that IS labelled.

## Rationale
MEASURED, NOT INFERRED. On the v11 A/B roster, 3,851 of 9,479 admitted windows (40.6%) carry NO state annotation at all, and pipeline.py:2108 maps every one of them to STATE_TO_INDEX['neutral'] with no presence mask. `state_distribution` does the same thing on the soft path, returning a one-hot neutral for an unlabelled window. So under both `hard` and `soft`, 40.6% of the window-level ce_state gradient trains toward a label nobody recorded.

Four corpora are 100% fabricated on this axis -- big_ideas_glycemic (145 windows), optihealth_supabase (302), mmash (42), brain_wearable_monitoring (21) carry no state label in any window -- and lifesnaps alone contributes 2,749 unlabelled windows.

THE PATCH-LEVEL TERM ALREADY DOES THIS CORRECTLY. `patch_state_loss` takes a `presence` argument and selects annotated rows BEFORE the loss, with a docstring explaining why zero-weighting afterwards would be wrong. The window-level term simply never got the same treatment. This is not a design decision being revisited; it is one axis of the same objective missing the guard the other axis has.

WHY IT IS NOT SELF-CORRECTING: a fabricated `neutral` is byte-identical to a real one in both the hard label and the one-hot distribution. Nothing downstream can tell them apart, which is why this survived a state campaign that measured this diagnostic eight times. It is the fifth instance this week of the vault's standing pattern -- an absent declaration being indistinguishable from real data (rule-out that-a-declaration-based-registry-in-this-repo-bd79da).

## Plan
CODE FIX + UNIT TESTS FIRST, GPU ARM ONLY IF THE FIX IS CLEAN.

Step 1 (no GPU): carry an explicit `state_present` flag from the dataset. `state_series(metadata)` already answers it -- an empty series means no annotated epoch -- so this is a new key alongside `state_label`/`state_dist` in SensorTextPairDataset.__getitem__, not a new derivation. Do NOT infer presence from the value: a real `neutral` and a fabricated one are identical in both the hard label and the one-hot distribution, which is exactly why this bug survived.

Step 2 (no GPU): mask the window-level term. Select the present rows BEFORE the loss, mirroring patch_state_loss, which documents why zero-weighting after the fact is wrong (a zero row is a valid CORN distribution and would train toward the lowest rank). Return a differentiable zero when a batch has no labelled window.

Step 3 (no GPU): unit tests -- scored-row count equals labelled-window count; masked == unmasked to 1e-6 when all windows are labelled; empty-batch returns a finite zero; works under hard AND soft.

Step 4 (GPU, ~4 min Phase A): a single-variable A-stage arm against phase_a_12ch_v11full, changing ONLY the mask. Phase A is the right stage because that is where the state head lives and where 217 s buys a full run.

Do NOT bundle this with any other change. The v11 chain currently running is the comparison anchor and must stay untouched.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g1-fabricated-neutral-no-longer-trained` (blocking): g1 BLOCKING (the fabricated supervision is actually removed): with the presence mask live, the number of windows contributing to the window-level ce_state term must equal the number carrying a real state label -- 5,628 of 9,479 on the v11 A/B roster -- and NOT 9,479. Asserted directly on the batch by a unit test that counts scored rows, not inferred from a loss value. If a batch annotates no window at all, the term must return a differentiable zero rather than a neutral-shaped gradient (the same contract patch_state_loss already honours).
Gate `g2-no-op-where-every-window-is-labelled` (blocking): g2 BLOCKING (nothing else moves): with every window labelled, the masked loss must equal the unmasked loss to within 1e-6 -- i.e. the change is a no-op on a fully-labelled batch and cannot be silently altering the objective for arms that were never affected. Also: the 856-test suite must stay green, and `state_target: hard` must keep working (this is not a soft-only fix).
Gate `g3-which-corpora-stop-fabricating` (advisory): g3 (non-blocking, mechanism): report which datasets stop contributing fabricated neutral. Expected from the pre-fix census: big_ideas_glycemic (145 windows), optihealth_supabase (302), mmash (42), brain_wearable_monitoring (21) carry NO state label in any window and are currently 100% fabricated; lifesnaps contributes 2,749 unlabelled and pmdata 519. Diagnostic only -- it does not gate.

#state-supervision #control #baseline #wearables #epoch-resolution #data-contract-violation #matched-baseline #optihealth-eiv #subject-heldout
