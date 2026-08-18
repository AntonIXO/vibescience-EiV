---
artifacts: []
closed: true
config_note: 'BOOKKEEPING CLOSURE, no new computation. H4 (proposed 2026-08-08) has
  read `status: proposed` ever since, despite being explicitly superseded by h4b-supersedes-h4-sharpened-from-add-82c23a,
  which was RUN and REFUTED. This experiment exists to transfer the successor''s verdict
  onto the predecessor so recall() stops offering an already-answered question.


  H4 claimed: the hr_peak deficit is a SUPERVISION deficit — captions describe LEVEL
  not SPREAD, so putting within-day dispersion into the caption raises hr_peak readout
  with no architectural change.


  h4b sharpened that to ONE named scalar (hr_std in the caption) and was measured:
  REFUTED, eiv_peak_excursion_increment_r2 delta -0.0956, i.e. the predicted rise
  did not occur and the increment fell. The 3-seed follow-up recorded increment +0.313
  vs a control +0.409 against a 0.56 bar, with hr_mean also dropping 0.971 -> 0.938
  — the reallocation signature H4b''s own falsification clause named as disqualifying.


  No new evidence is generated here; the diagnostic value is copied from the h4b record
  so the closure is auditable rather than asserted.


  Surfaced by the doctor check added in vibescience-mcp 5c042a6 (hypothesis_superseded_but_open),
  which was written after finding this exact record.'
cost: null
crash_reason: ''
created_at: '2026-08-15T07:01:22+00:00'
diagnostics_measured:
- after: 0.3349
  before: 0.4305
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: see h4b-supersedes-h4-sharpened-from-add-82c23a
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: h4-caption-content-not-architecture-the-3306b2
id: exp-h4-resolved-via-h4b-supersession-20260815
interpretation:
  at: '2026-08-18T21:08:45+00:00'
  failure_mode: mechanism_wrong
  lesson: 'H4''s DIAGNOSIS survives and its PRESCRIPTION is dead: within-day dispersion
    really is missing downstream (the measured ladder level-only 0.0226 -> caption
    scalars 0.1242 -> deep stack 0.4209 -> hand dispersion 0.7075 -> patch maxes 0.9706
    is not in dispute), but writing those scalars into the caption does not transfer
    that ceiling. The successor h4b tested the sharpest version (hr_std in the caption)
    and moved the peak-excursion increment DOWN, 0.4305 -> 0.3349 (delta -0.0956),
    with a 3-seed arm at +0.313 against a same-code control of +0.409 and a 0.56 bar,
    while hr_mean fell 0.971 -> 0.938 — the capacity-reallocation signature h4b pre-named
    as disqualifying. This record is a BOOKKEEPING CLOSURE with no new computation
    (diagnostic copied from h4b), so cite h4b for evidence. Do not re-open caption-enrichment
    for hr_peak; the measured working route is architectural — swapping the cross-patch
    pooling operator (E19/E19b: avg +0.2807 -> avgmax +0.6849 on real encoder tokens,
    reproducing on 5 checkpoints with a 0.0457 band and a dead shuffle control), the
    exact opposite of H4''s ''no further architectural change is needed''.'
  transfers_to:
  - h4b-supersedes-h4-sharpened-from-add-82c23a
  - hyp-e19-encoder-pooling-transfer-20260815
  - hyp-e19b-pooling-recipe-level-20260815
key_numbers:
  h4b_3seed_control_increment: 0.409
  h4b_3seed_increment: 0.313
  h4b_bar: 0.56
  h4b_delta: -0.0956
  hr_mean_after: 0.938
  hr_mean_before: 0.971
observed_effects:
- delta: -0.0956
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_heldout_hr_r2: false
    eiv_peak_excursion_increment_r2: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h4-caption-content-not-architecture-the-3306b2]].
External run: see h4b-supersedes-h4-sharpened-from-add-82c23a

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4305 → after 0.3349  (Δ -0.0956, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `mechanism_wrong`

H4's DIAGNOSIS survives and its PRESCRIPTION is dead: within-day dispersion really is missing downstream (the measured ladder level-only 0.0226 -> caption scalars 0.1242 -> deep stack 0.4209 -> hand dispersion 0.7075 -> patch maxes 0.9706 is not in dispute), but writing those scalars into the caption does not transfer that ceiling. The successor h4b tested the sharpest version (hr_std in the caption) and moved the peak-excursion increment DOWN, 0.4305 -> 0.3349 (delta -0.0956), with a 3-seed arm at +0.313 against a same-code control of +0.409 and a 0.56 bar, while hr_mean fell 0.971 -> 0.938 — the capacity-reallocation signature h4b pre-named as disqualifying. This record is a BOOKKEEPING CLOSURE with no new computation (diagnostic copied from h4b), so cite h4b for evidence. Do not re-open caption-enrichment for hr_peak; the measured working route is architectural — swapping the cross-patch pooling operator (E19/E19b: avg +0.2807 -> avgmax +0.6849 on real encoder tokens, reproducing on 5 checkpoints with a 0.0457 band and a dead shuffle control), the exact opposite of H4's 'no further architectural change is needed'.

Transfers to: [[h4b-supersedes-h4-sharpened-from-add-82c23a]], [[hyp-e19-encoder-pooling-transfer-20260815]], [[hyp-e19b-pooling-recipe-level-20260815]]

## Key numbers
- `h4b_3seed_control_increment`: 0.409
- `h4b_3seed_increment`: 0.313
- `h4b_bar`: 0.56
- `h4b_delta`: -0.0956
- `hr_mean_after`: 0.938
- `hr_mean_before`: 0.971

## Notes
RESOLVED BY SUPERSESSION — no new computation, verdict transferred from the successor.

H4 asserted the hr_peak deficit is a SUPERVISION deficit curable by putting dispersion into the caption. h4b tested exactly that with one named scalar (hr_std) and was REFUTED: increment 0.4305 -> 0.3349, delta -0.0956, moving DOWN where H4 predicted UP. The 3-seed arm gave +0.313 against a same-code control of +0.409 and a 0.56 bar, while hr_mean fell 0.971 -> 0.938 — the capacity-reallocation signature h4b's own falsification clause pre-named as disqualifying.

WHAT SURVIVES FROM H4, AND IT MATTERS. Its measured ladder is not in dispute: level-only 0.0226 -> caption scalars 0.1242 -> deep stack 0.4209 -> hand dispersion 0.7075 -> patch maxes 0.9706. The shallow ceiling really does sit far above the deep stack, so the information IS there. What is refuted is the specific route — writing those scalars into the caption does not transfer the ceiling to the model.

AND THE RIGHT ROUTE IS NOW MEASURED. E19/E19b (2026-08-15) reached the same headroom from the other side: leave the caption alone and change the cross-patch POOLING operator. avg +0.2807 -> avgmax +0.6849 on real encoder tokens, reproducing on 5 checkpoints with a band of 0.0457 and a dead shuffle control. So H4's diagnosis (dispersion information is missing downstream) was right, its prescription (put it in the text view) was wrong, and the operative fix is architectural after all — the exact opposite of H4's 'no further architectural change is needed'.

This closure was surfaced by the doctor check added at vibescience-mcp 5c042a6, written specifically because this record sat `proposed` for 7 days while its successor was already refuted and the age-gated staleness check could not see it.

#cross-modal #sensor-text #contrastive #infonce #baseline #epoch-resolution #dataset-whitelist #wearables
