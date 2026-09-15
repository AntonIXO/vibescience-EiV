---
created_at: '2026-09-01T08:36:30+00:00'
former_ids: []
id: the-impossible-diagnostic-values-are-localised-to-two-multipositive-pilots
problem_id: vault-hygiene-and-record-readability
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:36:30+00:00'
topic_tags: []
---

## Claim
The impossible diagnostic values found today (eiv_peak_vram_gib at 9.5x card capacity, eiv_false_negative_pairs_per_batch spanning three orders of magnitude) are localised to the two multipositive pilot experiments of 2026-07-28, i.e. a bounded provenance failure in two runs.

## Method
Dumped every recorded diagnostic value from the two suspect experiments plus a control that recorded the same diagnostics, checked each against the physical bound of its declared unit, then tested whether a single consistent alternative unit reading (percent / normalised ratio / MiB) makes each field plausible across all three records. Scripts: scratch/_multipositive_pilot_blast_radius.py, scratch/_unit_convention_audit.py. No GPU.

## Evidence
REFUTED, AND THE REAL SCOPE IS BOTH WIDER AND NARROWER THAN THAT.

WIDER: the CONTROL experiment breaks the same bounds. exp-014-hyp-eiv-dcl-vs-hard-infonce records eiv_duplicate_recall5 = 35.0 against a declared unit of `fraction`, and eiv_effective_rank = 0.2079 against a diagnostic whose own description says 'collapse guard rejects rank below 5'. So at least three experiments are affected and the defect cannot be attributed to two bad runs.

NARROWER, AND THIS IS THE USEFUL PART: most of these are UNIT-DECLARATION defects, not wrong numbers. Tested by asking whether one consistent alternative reading makes each field plausible (scratch/_unit_convention_audit.py). Fields where 'declared? no / alternative? YES' holds consistently:
  eiv_duplicate_recall5     35.0 / 38.0 / 4.63   ordinary as PERCENT, impossible as a fraction
  eiv_effective_rank        0.2079 / 0.3688      ordinary as a NORMALISED RATIO, impossible as a rank the guard compares to 5
  eiv_crossmodal_median_rank 0.1346              ordinary NORMALISED, impossible as a rank >= 1
  eiv_peak_vram_gib         113.7 / 114.3        ordinary as MiB, impossible as GiB on an 11.94 GiB card
These records agree with EACH OTHER and disagree only with the declared unit. That is a metadata defect: the values are re-expressible, not retractable.

ONE FIELD SURVIVES NO READING AT ALL. eiv_heldout_hr_r2 = 5.2051 and 5.2109 in the two multipositive pilots. R2 is bounded above by 1.0 by construction, and no unit convention rescues 5.21 -- it is not a percentage of anything (an R2 percentage would be 521%), not a ratio, not a rescaling. The same field reads 0.9997 in the control. THIS is the genuine per-experiment provenance failure, and it is confined to the two pilots.

CONSEQUENCE. The two multipositive pilots' eiv_heldout_hr_r2 values must not be cited; the diagnostic is the vault's most-used unfloored metric (18 evaluations) and two of them are impossible. The remaining anomalies are a documentation job across several diagnostics, not evidence that any experiment measured the wrong thing.

METHOD NOTE WORTH KEEPING: the giveaway was that the CONTROL failed the same bounds. Had I audited only the suspects I would have concluded 'two corrupt experiments' and retracted results that are in fact fine under their true units.

## Links
Rules out for problem [[vault-hygiene-and-record-readability]].
