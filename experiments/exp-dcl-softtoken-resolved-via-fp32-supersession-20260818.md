---
artifacts: []
closed: true
config_note: 'VERDICT TRANSFER — NO NEW MEASUREMENT WAS TAKEN. This experiment exists
  only to close a hypothesis that its own declared successor already answered, so
  that recall() stops offering an answered question. Every number below is COPIED
  VERBATIM from exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728, the single run of
  the superseding hypothesis hyp-eiv-dcl-vs-hard-infonce-fp32-20260728 (status: inconclusive).
  No GPU was used and nothing was re-run; treat this record as bookkeeping, not as
  independent evidence, and do not count it toward calibration.


  WHY THE SUCCESSOR IS INCONCLUSIVE AND NOT A RESULT. Both arms (hard InfoNCE and
  DCL) failed symmetrically BEFORE initialization or step 1: each tried to precompute
  4,962 missing corrected-mask caption embeddings by moving fp32 MedGemma onto the
  11.51 GiB RTX 5070 and OOMed at 10.314 GiB reserved. No retrieval or training diagnostic
  was ever produced. The predecessor''s own predicted effects (eiv_duplicate_recall5
  up, eiv_crossmodal_median_rank down, eiv_effective_rank none, eiv_sensor_median_cosine
  none) are therefore ALL UNMEASURED — the DCL-vs-hard-InfoNCE question is UNTESTED,
  not settled. Closing this record removes a duplicate open question; it does not
  close the scientific question.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.0
crash_reason: ''
created_at: '2026-08-18T20:50:08+00:00'
diagnostics_measured:
- after: 10.314453125
  before: 10.314453125
  diagnostic_id: eiv_peak_vram_gib
do_not_retry: []
external_run: COPIED FROM exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728 — no new
  run
former_ids: []
gate_results: []
git_ref: master@66544f1+dirty
hypothesis_id: hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728
id: exp-dcl-softtoken-resolved-via-fp32-supersession-20260818
interpretation:
  at: '2026-08-18T20:50:09+00:00'
  failure_mode: test_invalid
  lesson: 'UNTESTED, not refuted: the DCL-vs-hard-InfoNCE soft-token comparison never
    ran. Both arms OOMed identically at 10.314 GiB on the 11.51 GiB 5070 while materialising
    4,962 missing caption embeddings with fp32 MedGemma resident — a capacity blockage
    upstream of step 1, not a result about either objective. Anyone reviving this
    must first materialise the exact text-cache corpus under a NEW campaign root (compatible
    cached embeddings already exist under other cache fingerprints) so the embedding
    pass never coincides with a resident fp32 backbone. Recorded so the flat verdict
    is never mistaken for evidence that decoupled contrastive loss does not help.'
  transfers_to:
  - hyp-eiv-dcl-vs-hard-infonce-fp32-20260728
key_numbers: {}
observed_effects:
- delta: 0.0
  diagnostic_id: eiv_peak_vram_gib
  direction: none
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_crossmodal_median_rank: false
    eiv_duplicate_recall5: false
    eiv_effective_rank: false
    eiv_sensor_median_cosine: false
provenance: {}
recipe_fingerprint:
  git_ref: master@66544f1+dirty
  interventions: eiv-dcl-denominator-only
verdict: inconclusive
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728]].
At commit `master@66544f1+dirty`.
External run: COPIED FROM exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728 — no new run

## Measurements
- [[eiv_peak_vram_gib]]: before 10.314453125 → after 10.314453125  (Δ +0, flat)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `test_invalid`

UNTESTED, not refuted: the DCL-vs-hard-InfoNCE soft-token comparison never ran. Both arms OOMed identically at 10.314 GiB on the 11.51 GiB 5070 while materialising 4,962 missing caption embeddings with fp32 MedGemma resident — a capacity blockage upstream of step 1, not a result about either objective. Anyone reviving this must first materialise the exact text-cache corpus under a NEW campaign root (compatible cached embeddings already exist under other cache fingerprints) so the embedding pass never coincides with a resident fp32 backbone. Recorded so the flat verdict is never mistaken for evidence that decoupled contrastive loss does not help.

Transfers to: [[hyp-eiv-dcl-vs-hard-infonce-fp32-20260728]]

## Notes
Closed by VERDICT TRANSFER from the superseding hypothesis hyp-eiv-dcl-vs-hard-infonce-fp32-20260728 (run exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728). Numbers copied, NOT re-measured — see config_note. This resolves a `hypothesis_superseded_but_open` doctor finding: the predecessor was still advertising an already-answered question to recall(). The verdict below is COMPUTED from the predecessor's own preregistered effects against the successor's measurements, not inherited from the successor's status.

#infonce #dcl #denominator #contrastive #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #matched-baseline #optihealth-eiv #contrastive-loss #subject-heldout #representation-learning
