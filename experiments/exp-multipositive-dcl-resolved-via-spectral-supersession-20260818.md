---
artifacts: []
closed: true
config_note: 'VERDICT TRANSFER — NO NEW MEASUREMENT WAS TAKEN. This experiment exists
  only to close a hypothesis that its own declared successor already answered. Every
  number below is COPIED VERBATIM from exp-eiv-multipositive-spectral-entropy-pilot-20260728,
  the pilot of the superseding hypothesis hyp-eiv-multipositive-spectral-entropy-20260728
  (status: confirmed) — a 500-step seed-17 validation-only pilot on the RTX 5070,
  protocol SHA 9884ac3df783c5c9. No GPU was used and nothing was re-run; this is bookkeeping,
  not independent evidence, and it should not count toward calibration.


  THE SUCCESSOR''S `confirmed` IS NOT INHERITED, AND THAT IS THE POINT. The successor
  was confirmed on ITS primary (effective rank / spectral entropy). This predecessor
  preregistered a different primary — eiv_duplicate_recall5 UP by >= +0.005 pilot
  / >= +0.010 confirmation — and in the very same run recall@5 was measured FLAT (0.10723192
  -> 0.10723192, delta exactly 0.0). Scoring the predecessor against its own preregistration
  rather than against its successor''s status is what keeps the closure honest: the
  multi-positive construction did move the geometry, and it did not move retrieval.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.0
crash_reason: ''
created_at: '2026-08-18T20:50:09+00:00'
diagnostics_measured:
- after: 0.10723192019950124
  before: 0.10723192019950124
  diagnostic_id: eiv_duplicate_recall5
- after: 4.517719665813096
  before: 4.380875185347812
  diagnostic_id: eiv_effective_rank
- after: 39.0
  before: 38.0
  diagnostic_id: eiv_crossmodal_median_rank
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_false_negative_pairs_per_batch
do_not_retry: []
external_run: COPIED FROM exp-eiv-multipositive-spectral-entropy-pilot-20260728 —
  no new run
former_ids: []
gate_results: []
git_ref: master@66544f1+dirty
hypothesis_id: hyp-eiv-multipositive-dcl-20260728
id: exp-multipositive-dcl-resolved-via-spectral-supersession-20260818
interpretation:
  at: '2026-08-18T20:50:09+00:00'
  failure_mode: mechanism_wrong
  lesson: 'The multi-positive DCL construction does exactly what its MECHANISM claim
    says and nothing its VALUE claim promised. In the same 500-step pilot: false-negative
    caption-equivalent pairs held at exactly 0.0 (the mechanism check passed by construction),
    effective rank rose +0.1368 (4.3809 -> 4.5177) and spectral entropy rose — but
    the preregistered primary, duplicate recall@5, moved by EXACTLY 0.0 against a
    >= +0.005 bar, and cross-modal median rank went the wrong way (38 -> 39, predicted
    down). Removing false negatives from the denominator is therefore not sufficient
    for retrieval gain at this scale. Lesson for future arms: an intervention whose
    mechanism gate is satisfied BY CONSTRUCTION (here: exact-caption equivalence sets)
    can never fail that gate, so it carries no evidential weight — the value claim
    must be carried by an independent primary, which is what happened and what failed.'
  transfers_to:
  - hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728
key_numbers: {}
observed_effects:
- delta: 0.0
  diagnostic_id: eiv_duplicate_recall5
  direction: none
- delta: 0.136844480465
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: 1.0
  diagnostic_id: eiv_crossmodal_median_rank
  direction: up
- delta: 0.0
  diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: none
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_crossmodal_median_rank: false
    eiv_duplicate_recall5: false
    eiv_effective_rank: true
    eiv_false_negative_pairs_per_batch: false
provenance: {}
recipe_fingerprint:
  git_ref: master@66544f1+dirty
  interventions: eiv-dcl-package,eiv-multi-positive-dcl
verdict: inconclusive
---

## Summary
Tests hypothesis [[hyp-eiv-multipositive-dcl-20260728]].
At commit `master@66544f1+dirty`.
External run: COPIED FROM exp-eiv-multipositive-spectral-entropy-pilot-20260728 — no new run

## Measurements
- [[eiv_duplicate_recall5]]: before 0.10723192019950124 → after 0.10723192019950124  (Δ +0, flat ✗ predicted up)
- [[eiv_effective_rank]]: before 4.380875185347812 → after 4.517719665813096  (Δ +0.136844, up ✓ predicted up)
- [[eiv_crossmodal_median_rank]]: before 38.0 → after 39.0  (Δ +1, up ✗ predicted down)
- [[eiv_false_negative_pairs_per_batch]]: before 0.0 → after 0.0  (Δ +0, flat ✗ predicted down)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `mechanism_wrong`

The multi-positive DCL construction does exactly what its MECHANISM claim says and nothing its VALUE claim promised. In the same 500-step pilot: false-negative caption-equivalent pairs held at exactly 0.0 (the mechanism check passed by construction), effective rank rose +0.1368 (4.3809 -> 4.5177) and spectral entropy rose — but the preregistered primary, duplicate recall@5, moved by EXACTLY 0.0 against a >= +0.005 bar, and cross-modal median rank went the wrong way (38 -> 39, predicted down). Removing false negatives from the denominator is therefore not sufficient for retrieval gain at this scale. Lesson for future arms: an intervention whose mechanism gate is satisfied BY CONSTRUCTION (here: exact-caption equivalence sets) can never fail that gate, so it carries no evidential weight — the value claim must be carried by an independent primary, which is what happened and what failed.

Transfers to: [[hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728]]

## Notes
Closed by VERDICT TRANSFER from the superseding hypothesis hyp-eiv-multipositive-spectral-entropy-20260728 (run exp-eiv-multipositive-spectral-entropy-pilot-20260728). Numbers copied, NOT re-measured — see config_note. This resolves a `hypothesis_superseded_but_open` doctor finding: the predecessor was still advertising an already-answered question to recall(). The verdict below is COMPUTED from the predecessor's own preregistered effects against the successor's measurements, not inherited from the successor's status.

#dcl #multi-positive #contrastive #sensor-text #false-negatives #cross-modal #false-negatives #representation-collapse #subject-heldout #optihealth-eiv
