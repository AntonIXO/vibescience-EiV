---
aliases:
- session-length
- recording-duration
- variable-length
- ragged-extent
- short-session
- lab-vs-field
- dynamic-window
- extent-transfer
- coverage-dilution
- padding-dilution
- duration-mismatch
- crop-augmentation
axis: topic
created_at: '2026-08-20T21:11:35+00:00'
id: extent-heterogeneity
---

## Description
Heterogeneity in the DURATION/EXTENT of the recordings pooled into one fixed-width tensor, and everything that follows from it. The EiV corpus splits into a LONG mode (free-living days, median 512-1382 HR-present minutes) and a SHORT mode (lab sessions: popane_s4 27 min, neuro_stress 32, popane_s3 36, nurse_stress 38, case 41, wesad 117), all merged into the same [1440,12] window. Covers: the contraction of a short session toward the fill value under unmasked pooling, masked/attention pooling and cardinality-invariant pooling as corrections, duration/coverage as an explicit conditioning input, random-extent crop augmentation, and extent as a dataset-identity shortcut. DISTINCT from `epoch-resolution` (the grid the tensor is merged ON, 15-min vs 1-min) and from `padding` (the mechanics of pad tokens): this tag is about the SPREAD OF DURATIONS ACROSS COHORTS being the thing that breaks transfer.

*Axis:* `topic`

## Aliases
- session-length
- recording-duration
- variable-length
- ragged-extent
- short-session
- lab-vs-field
- dynamic-window
- extent-transfer
- coverage-dilution
- padding-dilution
- duration-mismatch
- crop-augmentation
