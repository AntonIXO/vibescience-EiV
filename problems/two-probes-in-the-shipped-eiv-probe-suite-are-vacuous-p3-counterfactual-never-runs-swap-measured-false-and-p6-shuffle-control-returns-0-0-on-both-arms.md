---
created_at: '2026-08-08T07:00:33+00:00'
id: two-probes-in-the-shipped-eiv-probe-suite-are-vacuous-p3-counterfactual-never-runs-swap-measured-false-and-p6-shuffle-control-returns-0-0-on-both-arms
paper_refs: []
problem_tags:
- conditioning-collapse
status: open
title: 'Two probes in the shipped eiv probe suite are vacuous: P3 counterfactual never
  runs (swap_measured=False) and P6 shuffle control returns 0.0 on both arms'
topic_tags:
- control
- baseline
- medgemma
updated_at: '2026-08-08T08:57:02+00:00'
---

## Description
=== UPDATE 2026-08-09. BOTH PROBES FIXED, AND THE ROOT CAUSE OF p3 IS NOW MEASURED.
UNPREREGISTERED: problem evidence, no calibration weight. Commits 84b145f, d6927e3. ===

--- p3: THE PROBE NEVER RAN, AND THE CAUSE WAS ITS OWN THRESHOLD, NOT THE MODEL ---
p3 pairs a "low" day against a "high" day and takes min(len(low), len(high), 32) pairs. It selected
those pools with DAY-MEAN HR thresholds of < 70 and > 110 bpm. A day-mean runs over 1440 one-minute
epochs INCLUDING SLEEP, so a mean above 110 bpm is pathological rather than "a high day".
MEASURED on the rebuilt eval_v3_1min (n=5955, scratch/p0_3_why_p3_never_runs.py):
    day-mean HR: min 46.1, p1 53.8, median 74.6, p99 92.8, max 140.4
    low  pool (mean <  70): 1926 days (32.34%)
    high pool (mean > 110):    1 day  (0.02%)   -> attemptable pairs = 1
A rescore of v4c and v4w confirmed it live: swap_attempted = 1 on both arms.
So swap_total = 0 was structural. EVERY "p3 shows no attribution" reading in this project's history is
that threshold. The rule-out filed against the Stage-C seed tripwires rested on this 0.0 and should be
re-read accordingly.

FIX: pair on the PEAK axis, which is the quantity hr_label_bucket actually grades. On the same pool
peak<120 selects 1477 days and peak>160 selects 816, so both sides have a real population.
Helper masked_hr_peak_bpm takes the max over PRESENT cells only, since missing timesteps are stored as
literal 0.0 rather than NaN. NOT YET RE-RUN with the new gates -- that measurement is the first causal
read on conditioning this project will have.

--- p6: 0.0 WAS "NOTHING PARSED", NOT "NO SENSOR DEPENDENCE" ---
Both arms divided by a possibly-zero total with `else 0.0`, so a run where nothing parsed emitted
bucket_acc_ordered = bucket_acc_shuffled = shuffle_gap = 0.0 -- which reads as the strongest negative
claim the probe can make. Confirmed on the v4c/v4w rescore: 0 ordered / 0 shuffled parsed on both arms.

--- THE FIX APPLIED TO BOTH ---
Unmeasured metrics are now None, never 0.0, and each probe ships *_measured, *_unmeasured_reason and
its DENOMINATORS. A genuinely measured 0.0 is preserved and tested for. 12 tests in
tests/test_probe_null_discipline.py.

--- THIS IS THE FIFTH INSTRUMENT OF THIS FAMILY ---
p3 swap 0.0; p6 shuffle_gap 0.0; the p4 scorer grading against the wrong bucket definition (bba9bcf);
W&B hr_r2 logging a frozen random head at ~-47 while the same checkpoint probed at 0.9713; and the HR
bucket cut points, which are a corpus-derived quantile that silently redefined the label axis AND the
majority baseline (the A/B logged 143/109, the corpus five hours later implied 157.72/127.00). Cut
points are now pinned into the adapter manifest, which is immutable, rather than recomputed from the
corpus, which is not.
COMMON SHAPE: a divide-by-possibly-zero with an `else 0.0`, or a constant read from a mutable location.
STANDING RULE: before any comparative claim, verify the instrument CAN produce the number; and quote
every accuracy with its denominator, its majority baseline, and the convention in force, in the same
sentence.


Found 2026-08-08 on the first complete end-to-end 1-minute run (adapter
outputs/checkpoints/12ch_v3/phase_c, results outputs/probes/v3_full_suite.json). Unpreregistered;
problem evidence, no calibration weight.

P3 COUNTERFACTUAL / PREFIX SWAP
  swap_measured = False, swap_total = 0, swap_correct = 0, swap_attribution_acc = 0.0
The reported 0.0 is an ARTEFACT OF THE PROBE NOT RUNNING, not a measurement that attribution is
zero. This is the probe that would CAUSALLY verify conditioning -- the one test that distinguishes
"the model reads the soft tokens" from "the model emits a prior that happens to correlate". It
silently produced nothing and still wrote a number into the report, which is the dangerous failure
mode: a downstream reader sees 0.0 and concludes the conditioning is dead.

P6 SHUFFLE CONTROL
  bucket_acc_ordered = 0.0, bucket_acc_shuffled = 0.0, shuffle_gap = 0.0
Both arms are exactly zero, so the control measured nothing. A zero gap here is NOT evidence that
no shortcut exists; it is evidence the probe did not produce a usable bucket on either arm. Note
P4 on the same run and the same checkpoint DOES produce buckets (parse_failure_rate 0.0000), so
the failure is specific to P6's own path, not to the model's ability to emit a parseable answer.

WHY THIS MATTERS RIGHT NOW. The same run showed the classic readout gap: hr is decodable from the
LLM hidden states at R2 0.9649 (MAE 1.16 bpm) while the GENERATED bucket scores 0.4609 against a
0.569 always-predict-majority baseline, and P7 shows sensor attention mass 0.1277 against a 0.2238
uniform baseline. Diagnosing that gap is exactly what P3 and P6 exist for, and both are dark.

RELATED, ALREADY FIXED, SAME FAMILY OF BUG: P4 was grading the daily MEAN with hardcoded 60/80/100
cut points while training emits a bucket from the daily PEAK with corpus cut points 145.6/112.0.
A perfect model would have scored 24.11%. Fixed in commit bba9bcf; hr_acc went 0.0469 -> 0.4609 on
the identical checkpoint. Three of seven probes in the suite were therefore returning numbers that
could not mean what they appear to mean, which suggests the suite needs an audit pass rather than
three point fixes -- specifically, a rule that a probe which cannot run must emit null, never 0.0.

#control #baseline #medgemma #conditioning-collapse

=== THIRD INSTRUMENT OF THE SAME FAMILY, ADDED 2026-08-08: THE W&B `hr_r2` CURVE IS A FROZEN
RANDOM HEAD UNDER THE WINNING RECIPE. ===

`hr_r2` is logged to W&B on every Phase A step (src/training/pipeline.py:1774) and is computed from
`probe(fused_mean)` -- an auxiliary MSE-regression head. But the winning Phase A profiles
(`phase_a_12ch_pmdata`, `phase_a_12ch`, `phase_a_12ch_1min_wl`) set BOTH:
    disable_mse_regress: true      -> mse_regress is never added to total_loss
    disable_state_probe: true      -> ce_state is never added to total_loss
so that head receives NO GRADIENT FOR THE ENTIRE RUN and stays at its random initialisation.

MEASURED on the 1-minute control run (W&B gvcg7k14, 2000 steps, a HEALTHY run that ended at final
InfoNCE -18.57 against a collapse tripwire of > -5.0):
    steps    0- 200 : hr_r2 median -49.10
    steps  200- 500 : hr_r2 median -49.20
    steps  500- 800 : hr_r2 median -44.84
    steps  800-1100 : hr_r2 median -45.77
Flat at roughly -47 for the whole run, because nothing is training it. Meanwhile the SAME checkpoint
probes at hr_mean R2 0.9713 subject-grouped out-of-fold.

WHY IT BELONGS ON THIS PROBLEM: it is the same failure mode as p3, p6 and the p4 scorer -- an
instrument that emits a confident number it did not measure. A reader watching the dashboard would
conclude the encoder learned nothing, when it is in fact near-saturated on hr_mean. Four separate
instruments in this repo have now been caught reporting numbers that cannot mean what they appear
to mean.

FIX, SAME RULE AS THE OTHERS: when `disable_mse_regress` is set, `hr_r2` must be logged as null /
omitted, never as a float. A metric whose head gets no gradient is not a metric.

#control #baseline #medgemma #conditioning-collapse
