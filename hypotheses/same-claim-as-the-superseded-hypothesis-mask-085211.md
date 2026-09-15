---
ambition_class: mechanism
created_at: '2026-08-29T22:04:14+00:00'
former_ids: []
gap_closure_estimate: 0.2
gates:
- blocking: true
  description: 'g1 BLOCKING (out-of-sample generalisation, UNCHANGED from the superseded
    hypothesis): on popane_s5 + popane_s6, masked_avg''s mean variance-compression
    ratio must exceed avg''s by >= +0.05. Both are non-degenerate by the filter (r
    0.87/0.87 in the avg arm), so the mean is safe HERE -- two homogeneous cohorts,
    not eight heterogeneous ones.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g1-repair-generalises-to-unseen-cohorts
  mde: 0.05
- blocking: true
  description: 'g2r BLOCKING, RESTATED AS PER-COHORT WORST CASE: among LONG cohorts
    passing the degeneracy filter, NO SINGLE cohort may lose more than 0.05 of ratio
    under masked_avg. This is a strictly HARDER test than the median and cannot be
    rescued by averaging. Degeneracy filter (declared before re-scoring): a cohort
    is excluded iff ratio > 2.0 in either arm AND |r| < 0.15 in the avg arm -- a readout
    whose predictions vary more than twice the truth while carrying no correlation
    is not measuring compression. Cohorts excluded by this filter must be REPORTED,
    never silently dropped.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g2r-long-cohorts-no-harm-worst-case
  mde: 0.05
- blocking: true
  description: 'g3r BLOCKING (the filter must not be doing the work): the degeneracy
    filter must exclude AT MOST ONE cohort, and that cohort''s exclusion must be justified
    by its avg-arm |r| alone. If the filter has to remove several cohorts to let g2r
    pass, the filter is the result and the gate fails.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g3r-filter-excludes-at-most-one-cohort
  mde: 0.05
id: same-claim-as-the-superseded-hypothesis-mask-085211
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: 'Primary unchanged: held-out short-cohort ratio rises by >= +0.05.
    Already measured at +0.2412 in the parent run, so the substantive question is
    not g1 but whether the no-harm claim survives an instrument that a single degenerate
    cohort cannot flip.'
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- matched-baseline
- subject-heldout
- optihealth-eiv
- data-contract-violation
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: the-mask-weighted-pooling-repair-w6-corrects-a-6a722b
topic_tags:
- extent-heterogeneity
- pooling
- wearables
- control
- baseline
- denominator
- state-supervision
- deployment-distribution
---

## Statement
Same claim as the superseded hypothesis -- mask-weighted pooling corrects a GEOMETRIC property of short recordings and generalises to unseen short cohorts -- but with the no-harm gate restated on a statistic that a single degenerate cohort cannot flip: among LONG cohorts with a working readout, NO cohort loses more than 0.05 of variance-compression ratio under masked_avg.

## Rationale
exp-w12 returned directional_only with failure_mode test_invalid: g1 passed at +0.2412 (4.8x its bar) but g2 was scored on a MEAN over eight heterogeneous LONG cohorts, and one degenerate cell flipped it. mmash (n=42) reads avg ratio 4.4902 at r=0.009 -- predictions varying 4.5x more than truth with no correlation at all is a broken readout, not a compressed signal -- and it moved -1.2695, dragging the mean to -0.1436 while the MEDIAN moved +0.0040 and no other cohort lost more than 0.05.

The vault already owned this rule (rule_out that-the-mean-gap-naive-pooled-mean-present-5bb2e3: never score this diagnostic on a mean) and I preregistered a mean anyway. So the operator was never actually tested for harm. This hypothesis restates the no-harm gate on a statistic that a single broken cell cannot flip, with the degeneracy criterion fixed IN ADVANCE and defined only on the control arm.

## Plan
PURE RE-SCORE, no recomputation, no GPU: logs/w12_extent_heldout.json already holds every per-cohort ratio and r from the parent run. scratch/w13_extent_rescore.py reads that artifact, applies the declared degeneracy filter, and scores g1 / g2r / g3r -> logs/w13_extent_rescore.json.

HONESTY CONSTRAINT, stated because it is the weak point of this design: I have ALREADY SEEN these numbers, so this is a post-hoc re-scoring and I am recording it as such. The filter rule and all three gates are written into this hypothesis BEFORE the re-score runs, and the filter is defined on the AVG arm only (the control), so it cannot be tuned to favour the treatment. This does not have the epistemic standing of a fresh preregistration; what it does have is a stated rule that a future run can be held to. The genuinely clean test is the same gate applied to a NEW encoder or corpus, which is the natural follow-up.

## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Gate `g1-repair-generalises-to-unseen-cohorts` (blocking): g1 BLOCKING (out-of-sample generalisation, UNCHANGED from the superseded hypothesis): on popane_s5 + popane_s6, masked_avg's mean variance-compression ratio must exceed avg's by >= +0.05. Both are non-degenerate by the filter (r 0.87/0.87 in the avg arm), so the mean is safe HERE -- two homogeneous cohorts, not eight heterogeneous ones.
Gate `g2r-long-cohorts-no-harm-worst-case` (blocking): g2r BLOCKING, RESTATED AS PER-COHORT WORST CASE: among LONG cohorts passing the degeneracy filter, NO SINGLE cohort may lose more than 0.05 of ratio under masked_avg. This is a strictly HARDER test than the median and cannot be rescued by averaging. Degeneracy filter (declared before re-scoring): a cohort is excluded iff ratio > 2.0 in either arm AND |r| < 0.15 in the avg arm -- a readout whose predictions vary more than twice the truth while carrying no correlation is not measuring compression. Cohorts excluded by this filter must be REPORTED, never silently dropped.
Gate `g3r-filter-excludes-at-most-one-cohort` (blocking): g3r BLOCKING (the filter must not be doing the work): the degeneracy filter must exclude AT MOST ONE cohort, and that cohort's exclusion must be justified by its avg-arm |r| alone. If the filter has to remove several cohorts to let g2r pass, the filter is the result and the gate fails.
Supersedes [[the-mask-weighted-pooling-repair-w6-corrects-a-6a722b]].

#extent-heterogeneity #pooling #wearables #control #baseline #denominator #state-supervision #deployment-distribution #matched-baseline #subject-heldout #optihealth-eiv #data-contract-violation
