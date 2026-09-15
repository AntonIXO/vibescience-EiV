---
created_at: '2026-08-07T20:36:25+00:00'
former_ids:
- p4-bucket-probe-scores-hr-acc-against-a-different-bucket-definition-than-the-training-target-perfect-model-scores-0-26-majority-scores-0-52
id: p4-bucket-probe-scores-hr-acc-against-a-d2175a
paper_refs: []
problem_tags: []
status: parked
target: null
title: P4 bucket probe scores hr_acc against a different bucket definition than the
  training target (perfect model scores 0.26, majority scores 0.52)
topic_tags: []
updated_at: '2026-09-01T07:36:26+00:00'
updates:
- at: '2026-09-01T07:36:26+00:00'
  text: "FIXED IN CODE AND VERIFIED 2026-09-01. Parking: the record's framing -- that
    P4 grades a bucket definition the model was never trained to emit, so a perfect
    model scores 0.26 while a constant scores 0.52 -- is no longer true of the shipped
    scorer, and leaving it open hands a future session a false account of the instrument.\n\nVERIFIED
    by reading src/probes/bucket_parser.py rather than trusting the note. Lines 588-602
    now derive the truth from hr_PEAK, not the day mean, and read the thresholds from
    the training module itself:\n    _tach = getattr(_eiv_ds, 'HR_BUCKET_TACHYCARDIC',
    120.0)\n    _elev = getattr(_eiv_ds, 'HR_BUCKET_ELEVATED', 100.0)\n    hr_peak
    >= _tach -> 3 ; >= _elev -> 2 ; else -> 1\nwith an explicit comment that index
    0 (bradycardic) is unreachable on the peak axis, matching the training rule which
    has no bradycardic peak class. So the scorer and the training target now share
    BOTH the axis and the thresholds, and the thresholds are imported rather than
    hardcoded -- which is what stops the two drifting apart again.\n\nThis was the
    third member of the instrument family this problem set documents (p3 swap 0.0,
    p6 shuffle_gap 0.0, p4 wrong axis, W&B hr_r2 on an ungradiented head, mutable
    HR cut points). The sibling record two-probes-in-the-shipped-eiv-probe-ca39d5
    was parked in the same pass, with p3/p6 verified fixed and 31 tests passing.\n\nPARKED
    rather than resolved: this is a defect report with no hypothesis attached, and
    'resolved' requires a confirmed one. Nothing here is outstanding."
---

## Description
P4 (`bucket_parser.py::run_bucket_accuracy_probe`) scores the generated `heart_rate_bucket` against a
bucket definition that is NOT the one the model is trained to emit. The two use different AXES and
different THRESHOLDS.

  TRAINING TARGET  src/training/datasets.py:1183 `hr_label_bucket(hr_peak, hr_mean)`
      hr_PEAK >= 120 -> tachycardic
      hr_PEAK >= 100 -> elevated
      hr_MEAN <   55 -> bradycardic
      else           -> resting
  P4 SCORER        src/probes/bucket_parser.py:242 (`true_hr`)
      hr_MEAN <  60 -> 0 ; < 80 -> 1 ; < 100 -> 2 ; else -> 3
  and the prediction is mapped through HR_MAP {bradycardic:0, resting:1, elevated:2, tachycardic:3}.

So the target bucket is driven by the within-day PEAK while p4's truth is driven by the daily MEAN.

MEASURED CONSEQUENCE on the exact population the probe scored (data/eval_v2, seed 42, n=96):
  p4 true-label marginal (mean bins) : {1: 71, 2: 23, 0: 2}
  training-label marginal (peak rule): {2: 50, 3: 30, 1: 16}
  the two definitions AGREE on 26.0% of samples
  => A MODEL THAT EMITS ITS TRAINING LABEL PERFECTLY SCORES hr_acc = 0.2604.
  => MAJORITY-CLASS BASELINE on the same draw = 0.5208 ("always elevated"), which is ABOVE the
     perfect-model ceiling. No correctly-trained model can beat a constant on this metric, ever.

MEASURED ARMS (v2, 2026-08-07, both on the same eval population and the same Phase B checkpoint):
  ARM1 (dataset_filter = lifesnaps+pmdata+optihealth_supabase)  hr_acc 0.0521
  ARM2 (dataset_filter cleared to all 13 datasets)              hr_acc 0.1667
The +0.1146 "3.2x improvement" for ARM2 is an ARTEFACT and must not be credited. p4's true labels
are 71/96 in the 60-80 bpm bin; ARM2's training corpus is shifted toward low HR (dryad = 33% of it,
cohort mean 61.3 bpm; ARM2 train marginal resting 38.4% vs ARM1 21.4%), so ARM2 emits low buckets
more often and accidentally matches the WRONG scorer's marginal. It is a prior artefact, not better
sensor reading.

ALTERNATIVE EXPLANATION TESTED AND REFUTED: I first suspected I had created a train/eval mismatch by
scoring on a strict-coverage pool. ARM1's training marginal (resting 21.4 / elevated 45.0 /
tachycardic 32.3 / brady 1.3) nearly equals the eval marginal (19.1 / 48.1 / 32.2 / 0.6), so that is
not the cause.

ALSO IN THE SAME PROBE: p4 hrv_acc and deep_sleep_acc are EXACTLY 0.0000 on 96 samples for BOTH arms
with parse_failure_rate 0.0. `true_hrv` is built by denormalising channel 1 with population_stats,
but channel 1 currently holds a ROLLING PER-USER z-score, so every subject denormalises to a median
of exactly 61.0 ms and `true_hrv` is a near-constant fabricated label. See problem
canonical-merge-defaults-to-rolling-per-user-z-score-while-the-phase-c-target-builder-assumes-population-norm.

SCOPE: this invalidates p4 hr_acc as a MODEL metric in every historical run that quoted it
(e.g. W&B kta1m4pu hr_acc 0.0 with parse_failure 0.0, and the Stage-C tripwire in
run_12ch_pipeline.sh which gates on hr_acc). It does NOT touch the e34-harness numbers
(hr_raw skill / donor gap / correlation), which are computed from parsed numeric values, not buckets.

FIX: route p4's `true_hr` through `hr_label_bucket` so the scorer and the training target are the
same function -- the same "make divergence unrepresentable" argument that function's own docstring
already makes about the sampler. Until then, p4 hr_acc should be reported as UNINTERPRETABLE, and the
Stage-C tripwire that gates on it should be treated as broken alongside p3 and p6.

## Updates
- **2026-09-01T07:36:26+00:00** — FIXED IN CODE AND VERIFIED 2026-09-01. Parking: the record's framing -- that P4 grades a bucket definition the model was never trained to emit, so a perfect model scores 0.26 while a constant scores 0.52 -- is no longer true of the shipped scorer, and leaving it open hands a future session a false account of the instrument.

VERIFIED by reading src/probes/bucket_parser.py rather than trusting the note. Lines 588-602 now derive the truth from hr_PEAK, not the day mean, and read the thresholds from the training module itself:
    _tach = getattr(_eiv_ds, 'HR_BUCKET_TACHYCARDIC', 120.0)
    _elev = getattr(_eiv_ds, 'HR_BUCKET_ELEVATED', 100.0)
    hr_peak >= _tach -> 3 ; >= _elev -> 2 ; else -> 1
with an explicit comment that index 0 (bradycardic) is unreachable on the peak axis, matching the training rule which has no bradycardic peak class. So the scorer and the training target now share BOTH the axis and the thresholds, and the thresholds are imported rather than hardcoded -- which is what stops the two drifting apart again.

This was the third member of the instrument family this problem set documents (p3 swap 0.0, p6 shuffle_gap 0.0, p4 wrong axis, W&B hr_r2 on an ungradiented head, mutable HR cut points). The sibling record two-probes-in-the-shipped-eiv-probe-ca39d5 was parked in the same pass, with p3/p6 verified fixed and 31 tests passing.

PARKED rather than resolved: this is a defect report with no hypothesis attached, and 'resolved' requires a confirmed one. Nothing here is outstanding.
