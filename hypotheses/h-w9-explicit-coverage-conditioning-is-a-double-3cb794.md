---
ambition_class: null
created_at: '2026-08-20T21:18:31+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: BLOCKING SHORTCUT CONTROL, and it is the whole point of this hypothesis.
    Report eiv-dataset-provenance-shortcut-balacc from the conditioned model's representation
    against the W4/P3 extent-only reference (bal-acc 0.7520, chance 0.0625, 12.03x)
    and H5's coverage-pattern reference (0.8278). This diagnostic is lower_better.
    If provenance decodability RISES, the conditioning is being consumed as a cohort
    label and the arm is REFUTED regardless of any readout gain.
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  id: g1-shortcut-must-not-rise
  mde: 0.05
- blocking: true
  description: Report eiv_extent_variance_compression_ratio per cohort with mean coverage
    c alongside. The primary claim is that explicit c lets the reader INVERT the contraction,
    so the short-cohort ratio must rise above the ratio~=c null model, not merely
    above zero.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g2-ratio-beats-c-null
  mde: 0.6
- blocking: true
  description: 'DENSE NO-HARM: eiv_dense128_hr_raw_mae_bpm must not regress beyond
    1.0 bpm versus the matched control arm.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-no-harm
  mde: 1.0
- blocking: false
  description: 'DETERIORATION CONTROL: shuffle the coverage scalar across samples
    so each window is paired with a WRONG c. If skill is unchanged under a randomised
    c, the model is not using the conditioning at all and any apparent gain is attributable
    to something else in the arm.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g4-shuffled-coverage-control
  mde: 0.05
id: h-w9-explicit-coverage-conditioning-is-a-double-3cb794
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: PRIMARY. The contraction naive = median + c*(present-median) is
    algebraically INVERTIBLE given c, so supplying c is the minimum sufficient information
    to de-dilute. Predict short-cohort ratio >= 0.60 against the ratio~=c null.
- diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  direction: none
  magnitude_note: 'MECHANISM GUARD, direction none: provenance decodability must NOT
    rise above the W4/P3 extent-only reference of 0.7520 (chance 0.0625). A RISE means
    the conditioning was consumed as a cohort label rather than as a scale correction,
    which refutes the arm even if the ratio improves.'
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
reopen_reason: ''
reopened_from: null
status: proposed
supersedes: null
topic_tags:
- extent-heterogeneity
- dataset-whitelist
- denominator
- wearables
- pooling
- state-supervision
- deployment-distribution
---

## Statement
H-W9 (EXPLICIT COVERAGE CONDITIONING IS A DOUBLE-EDGED FIX). Lowest priority of the family, and proposed with a genuine expectation that it may do net harm -- which is why it is written as a two-sided prediction rather than as a lever.

CLAIM. Because the contraction is exactly naive = median + c*(present - median), it is ALGEBRAICALLY INVERTIBLE if and only if the reader knows c. Supplying coverage/duration explicitly (a scalar embedding or FiLM modulation) is therefore the minimum sufficient information for the model to de-dilute, and should raise the short-cohort variance-compression ratio.

THE COUNTER-CLAIM, WHICH I RATE ROUGHLY AS LIKELY. Extent alone already predicts dataset identity at 12.03x chance (W4/P3: bal-acc 0.7520 over 16 classes from four scalars), and cohort determines the label distribution. Handing the model a clean coverage scalar may simply hand it a cleaner PROVENANCE LABEL, letting it satisfy its objective by recognising 'this is CASE, CASE is film-elicited affect' with zero physiology. That is the exact failure `h5-the-shortcut-is-the-coverage-2aaaa0` describes, and this intervention could make it WORSE rather than better.

THIS IS WHY THE SHORTCUT CONTROL IS THE BLOCKING GATE AND THE READOUT GAIN IS NOT. An arm that improves the ratio while raising provenance decodability has not solved anything -- it has laundered a shortcut into a scale correction. If the two move in opposite directions, the honest report is that explicit coverage conditioning trades physiological reading for provenance recognition, and it should be recorded as a dead end with that mechanism named.

RELATION TO H5, stated so no one opens a parallel record: `h5-the-shortcut-is-the-coverage-2aaaa0` owns the claim that the coverage SIGNATURE is a shortcut and that SUPPRESSING it is the intervention. H-W9 is the OPPOSITE intervention on the same object -- AMPLIFYING coverage into an explicit input. They are complementary tests of one mechanism and their results must be read together. If H5's suppression arm and H-W9's amplification arm BOTH improve readout, the mechanism story is wrong and both should be re-examined.

No published work was found (2026-08-20 sweep) that names duration/coverage conditioning as a fix for multi-cohort wearable pad bias, so there is no external effect size to anchor on -- the prediction rests on the algebra alone.

## Rationale


## Plan


## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Predicts [[eiv-dataset-provenance-shortcut-balacc]] (none).
Gate `g1-shortcut-must-not-rise` (blocking): BLOCKING SHORTCUT CONTROL, and it is the whole point of this hypothesis. Report eiv-dataset-provenance-shortcut-balacc from the conditioned model's representation against the W4/P3 extent-only reference (bal-acc 0.7520, chance 0.0625, 12.03x) and H5's coverage-pattern reference (0.8278). This diagnostic is lower_better. If provenance decodability RISES, the conditioning is being consumed as a cohort label and the arm is REFUTED regardless of any readout gain.
Gate `g2-ratio-beats-c-null` (blocking): Report eiv_extent_variance_compression_ratio per cohort with mean coverage c alongside. The primary claim is that explicit c lets the reader INVERT the contraction, so the short-cohort ratio must rise above the ratio~=c null model, not merely above zero.
Gate `g3-dense-no-harm` (blocking): DENSE NO-HARM: eiv_dense128_hr_raw_mae_bpm must not regress beyond 1.0 bpm versus the matched control arm.
Gate `g4-shuffled-coverage-control` (advisory): DETERIORATION CONTROL: shuffle the coverage scalar across samples so each window is paired with a WRONG c. If skill is unchanged under a randomised c, the model is not using the conditioning at all and any apparent gain is attributable to something else in the arm.

#extent-heterogeneity #dataset-whitelist #denominator #wearables #pooling #state-supervision #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
