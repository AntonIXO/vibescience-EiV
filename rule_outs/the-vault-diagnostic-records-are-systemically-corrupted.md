---
created_at: '2026-09-01T08:40:28+00:00'
former_ids: []
id: the-vault-diagnostic-records-are-systemically-corrupted
problem_id: vault-hygiene-and-record-readability
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:40:28+00:00'
topic_tags: []
---

## Claim
The vault's diagnostic records are systemically corrupted: a sweep of all 46 diagnostics found 12 carrying out-of-bound values, including 44 impossible correlations on eiv_gen_peak_corr, so recorded numbers cannot be trusted as a group.

## Method
Ran a bounds sweep twice: first with a regex over the markdown, then with yaml.safe_load over the `diagnostics_measured` list as structured records. Compared the two result sets and traced the discrepancy to YAML key ordering. Bounds derived from each diagnostic's declared unit string rather than hand-listed per metric. No GPU.

## Evidence
REFUTED -- BY MY OWN SCANNER BEING WRONG. The 12-diagnostic figure came from a regex that matched `diagnostic_id: X` and then read the `before:`/`after:` lines FOLLOWING it. In the vault's YAML the keys are alphabetical, so `diagnostic_id` is the LAST key of its list item and the values after it belong to the NEXT item. Every attribution was shifted by one. The 44 'impossible correlations' on eiv_gen_peak_corr (20.758, 14.3, 4.52) are real numbers belonging to eiv_gen_between_sample_std -- a bpm spread, where 20.758 is ordinary.

RE-RUN WITH yaml.safe_load OVER `diagnostics_measured` AS STRUCTURED RECORDS (scratch/_diagnostic_admissibility_sweep.py, 0 experiments unparseable):
  610 recorded values
  603 inside the bound implied by their declared unit
    0 rescuable
    7 out of bound, across 3 diagnostics

AND 6 OF THOSE 7 ARE MY BOUNDS BEING WRONG, NOT THE DATA:
  eiv_momentary_state_auc_over_null (5 values, -0.029 to -0.049) -- the name ends in OVER_NULL. It is an AUC MINUS its shuffle null, so it is signed and negative means below-null. Bounding it to [0,1] was my error.
  eiv_text_cache_batch_invariance_cosine = 1.0000 exactly -- a cosine of exactly 1.0 is inside [-1,1] and is precisely the intended result for a cache-invariance check; a floating-point equality tripped the bound.

ONE GENUINE ANOMALY SURVIVES: eiv_nonhr_channel_grouped_r2 = 1.3220 in exp-e13-nof1-calibration. R2 is bounded above by 1.0, and this diagnostic's own description says both non-HR channels score NEGATIVE (respiratory_rate -0.0757, sleep_prob_deep -0.1847). 1.322 contradicts the diagnostic's own documented range and needs provenance.

WHAT THIS OVERTURNS. The earlier finding of impossible eiv_heldout_hr_r2 values (902.32, 2822.0, 5.21) is NOT overturned -- those were read from the same records and confirmed by hand against a control before being recorded. But the claim of SYSTEMIC corruption across 12 diagnostics was an artefact, and the diagnostic descriptions I annotated for eiv_false_negative_pairs_per_batch and eiv_peak_vram_gib were based on hand-checked values from specific experiments, so they stand on their own evidence rather than on this sweep.

METHOD RULE EARNED: a scanner that finds a defect in nearly everything it examines is more likely broken than the corpus. The tell was a 44-value hit on a diagnostic whose own registered noise floor had just been measured from clean captures an hour earlier -- the two findings could not both be true.

## Links
Rules out for problem [[vault-hygiene-and-record-readability]].
