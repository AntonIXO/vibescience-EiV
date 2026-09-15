---
created_at: '2026-08-07T12:11:01+00:00'
former_ids:
- lifesnaps-hourly-hr-splits-train-and-eval-populations-20260807
id: lifesnaps-hr-is-hourly-so-the-strict-15550c
paper_refs: []
problem_tags:
- optihealth-eiv
- data-contract-violation
status: parked
target: null
title: LifeSnaps HR is HOURLY, so the strict-clean eval population is 100% pmdata
  and 76% of hr_peak training targets are ~10 bpm suppressed
topic_tags:
- sensor-text
- cross-modal
updated_at: '2026-09-01T07:41:54+00:00'
updates:
- at: '2026-09-01T07:41:54+00:00'
  text: 'PARKED 2026-09-01: THE PREMISE NO LONGER HOLDS ON THIS CORPUS. Measured,
    not assumed, and cross-checked two independent ways before retiring the record.


    The problem states LifeSnaps HR is HOURLY, that the strict-clean eval population
    is therefore 100% pmdata, and that 76% of hr_peak training targets are ~10 bpm
    suppressed. On the current bundle that is false.


    READ 1 -- inter-reading spacing (scratch/_hourly_peak_audit.py). Median gap between
    consecutive present HR minutes, per dataset: lifesnaps 1.0 minutes, and only 16
    of its 4,778 windows (0.3%) have a median gap >= 30 min. Across the whole Phase-A
    roster 17/9,365 windows (0.2%) are hourly-sampled; across the Phase-C band04 roster
    16/7,542 (0.2%). There is no hourly population left to mask.


    READ 2 -- cross-check, because a median of 1 is compatible with a day that is
    dense in bursts and hourly elsewhere (scratch/_hourly_crosscheck.py). Two further
    statistics per dataset: the share of gaps that are exactly 1 minute, and readings
    per PRESENT hour (60 = fully dense, ~1 = hourly knots). lifesnaps reads frac(gap==1)
    p10/p50/p90 = 0.994/0.999/1.000 and readings-per-hour p10/p50/p90 = 36.4/59.4/60.0,
    with only 99/4,778 windows (2.1%) under 5 reads/hour. Both reads agree: lifesnaps
    is natively 1-minute in this bundle.


    WHAT IS STILL TRUE AND WORTH KEEPING. The MECHANISM the record establishes is
    sound and was independently reconfirmed today: subsampling dense pmdata days to
    an hourly grid costs a mean peak deficit of 27.32 bpm (median 23.83, p90 52.98,
    n=2,074) on this corpus -- larger than the 10.06 bpm the record measured on the
    older bundle, because these windows are denser. So the physics is right; only
    the population it applied to is gone. The record''s other standing conclusion
    -- DO NOT INTERPOLATE HR to repair peaks, because the max of a piecewise-linear
    interpolant is the max of its knots and PCHIP degrades the per-sample correlation
    0.8456 -> 0.7664 -- remains valid and is worth preserving as a do-not-retry.


    WHAT THIS MEANS FOR THE PROPOSED FIX. The record''s prescription was per-SLOT
    supervision masking: train state and hr_raw on everything, render hr_peak only
    on natively dense days. Grep confirms it was never implemented (no hourly / per-slot
    / hr_density logic in src/training/datasets.py). It should NOT be implemented
    now: it would gate 0.2% of rows, which cannot move any measured quantity, and
    it would add a masking path that a future denser-or-sparser corpus could silently
    mis-apply.


    PARKED rather than resolved: this is a data-quality report with no hypothesis
    attached, and ''resolved'' requires a confirmed one. If a future merge reintroduces
    an hourly source, the mechanism and the reads above are the ready-made test --
    re-run both scripts and the population is visible in one pass.'
---

## Description
=== REVISION 2026-08-07 (same day). INTERPOLATION TESTED AND REJECTED FOR PEAKS; LIFESNAPS IS FIT FOR
STATES AND UNFIT ONLY FOR hr_peak; AND THE state SLOT IS DATASET-CONFOUNDED. Unpreregistered problem evidence. ===

--- 1. INTERPOLATING HOURLY -> 15-MIN RECOVERS NOTHING FOR THE PEAK. MEASURED, n=1159 pmdata strict-clean days
    (subsample to the lifesnaps hourly grid, interpolate back, recompute) ---
  true 15-min peak            121.69 bpm
  hourly knots only           111.63 bpm   deficit 10.06
  + LINEAR interpolation      111.63 bpm   deficit 10.06   RECOVERED 0.0%
  + PCHIP shape-preserving    114.66 bpm   deficit  7.03   recovered 30.1%
  corr(estimate, true peak):  linear 0.8456 | hourly-only 0.8456 | PCHIP 0.7664
LINEAR ADDS EXACTLY ZERO, and this is necessary rather than empirical: the maximum of a piecewise-linear
interpolant equals the maximum of its knots. Its correlation with truth is IDENTICAL to the hourly knots to
four decimals. PCHIP appears to recover 30% of the AVERAGE deficit only because it overshoots at knots, and it
makes the PER-SAMPLE estimate WORSE (0.8456 -> 0.7664). That is the same false-PASS shape as the ruled-out
window-shortening arm: a headline number improves while the per-sample control degrades.
THE REAL DANGER IS NOT THAT IT FAILS, IT IS THAT IT HIDES: interpolating would set hr_cov to 1.0 and let all
6632 lifesnaps days pass the 'strict-clean' filter while carrying no additional peak information. It would
defeat the only instrument that currently detects the problem. DO NOT INTERPOLATE HR FOR PEAK SUPERVISION.
The daily MEAN is unaffected by hourly sampling (bias -0.081 bpm), so mean-based slots need no repair at all.

--- 2. SO LIFESNAPS DOES NOT NEED FIXING FOR WHAT IT IS GOOD AT ---
lifesnaps carries a real 7-dimensional EMA response on 2242/6632 days (33.8%): ALERT, HAPPY, NEUTRAL,
RESTED/RELAXED, SAD, TENSE/ANXIOUS, TIRED, all seven present on all 2242. That is the richest affect ground
truth in the corpus. State is a DAILY-level label and the daily mean survives hourly sampling exactly, so
lifesnaps is already fit for purpose for state/hr_raw supervision. It is unfit ONLY for hr_peak.
CORRECTION TO THE PRIOR REVISION'S FRAMING: the fix is not per-DATASET inclusion/exclusion, it is PER-SLOT
supervision masking. Train state and hr_raw on everything including lifesnaps; render hr_peak and
heart_rate_bucket only on natively dense days. One masking rule, no data surgery, no interpolation.
NUANCE WORTH RECORDING: pmdata is not far behind on labels (74.6% labelled, with fatigue/mood/stress/
readiness/sleep_quality/sleep_duration_h) AND has dense HR; dryad is 82.8% labelled but single-dimension
stress only. So 'lifesnaps is the only rich source' is true for multi-dimensional AFFECT, not for labels generally.

--- 3. THE state SLOT IS LARGELY A DATASET-IDENTITY CLASSIFIER (new, and it explains an exp-e40 observation) ---
infer_state_label marginals differ wildly by dataset:
  lifesnaps          neutral 68.8%  stress 19.2%  flow 12.0%
  pmdata             stress  72.8%  neutral 27.0%  flow  0.1%
  dryad_ifh_affect   neutral 46.7%  flow   35.7%  stress 17.6%
'burnout' is never emitted by any dataset despite being in the prompt's four-way schema.
Because the scoring cohort is 100% pmdata, ALWAYS ANSWERING 'stress' SCORES 72.8% THERE. exp-e40's adapter
emits 'stress' on 10/10 sampled days, which is not a collapse to be debugged but the loss-optimal answer for
that cohort. And since sampling density is itself a near-perfect dataset fingerprint (lifesnaps 0.250 coverage,
pmdata >0.9), the model can infer the dataset from the prefix and read the state prior off it -- a shortcut
that would inflate any state metric computed within a single dataset.
CONSEQUENCE: the 7 EMA dimensions are being collapsed into 3 effective classes with dataset-confounded priors.
The state target needs redesign more than the sensor data needs repair. Any future state result MUST be scored
against the per-dataset majority baseline and, ideally, cross-dataset.

MEASURED 2026-08-07 on data/canonical (X_canonical.npy, M_mask.npy, sample_metadata.json), pure CPU. Unpreregistered: problem evidence, no calibration weight. Triggered by the question 'why is pmdata the only valid data source?' after exp-e41 noted the strict-clean pool is entirely pmdata.

--- 1. THE STRICT FILTER IS A SAMPLING-RATE FILTER, NOT A QUALITY FILTER ---
Attrition of the strict-clean criteria, per dataset:
  dataset              total   has HR   cov>0.9   zero_frac==0   mean>40   peak>=40
  lifesnaps             6632     6632         0              0         0          0
  pmdata                2142     2142      1630           1546      1546       1546
  dryad_ifh_affect      4605     4605      4423              0         0          0
  optihealth_supabase    285      285        72             55        55         55
LIFESNAPS DIES ENTIRELY AT cov>0.9, AND NOT BECAUSE OF DROPOUT: 6593/6632 days have EXACTLY 24 present epochs, and the gap between consecutive present epochs is EXACTLY 4 epochs (60 min) in 9190/9190 measured gaps. Coverage is therefore 24/96 = 0.250 by construction (mean 0.250, median 0.250, 0.000% above 0.9). pmdata is natively 15-min (modal gap 1 epoch; 76.1% of days above 0.9 coverage).
ROOT CAUSE IN CODE: src/data/processors/lifesnaps.py defaults use_hourly=True and reads 'hourly_fitbit_sema_df_unprocessed.csv'. The docstring lists a mongo_rais_anonymized/ BSON dump as 'optional' -- if finer intraday HR exists anywhere it is there, but the raw CSVs are no longer on disk so this was NOT verified and must not be asserted.
dryad dies at zero_frac==0 instead: 4423 days have dense coverage and every one is zero-contaminated.
lifesnaps is ALSO 30.97% zero on its present epochs (p1 = 0.0 bpm) against pmdata's 0.72% (p1 = 40.5 bpm), which is the M_mask defect of problem canonical-mask-marks-degenerate-zero-channels-as-present-20260805, concentrated in lifesnaps.

--- 2. CONSEQUENCE A: TRAIN AND EVAL POPULATIONS ARE DISJOINT IN THE MOST IMPORTANT RESPECT ---
Phase C dataset_filter is {lifesnaps, pmdata}; lifesnaps is 6632/8774 = 75.6% of it. So ~76% of TRAINING samples carry 24 hourly HR points of which ~31% are literally 0 bpm, while 100% of EVALUATION samples (E26-E41, every number in the Phase C problem) carry 96 dense 15-min points with zero zeros. The model is trained mostly on sparse partly-degenerate data and scored exclusively on dense clean data.

--- 3. CONSEQUENCE B, QUANTIFIED: THE hr_peak TARGET IS CORRUPTED ON 76% OF THE CORPUS ---
Emulating the lifesnaps grid on pmdata's OWN strict-clean days (n=1463: keep every 4th epoch, recompute):
  daily PEAK   15-min 120.6 bpm -> hourly 110.7 bpm   UNDERESTIMATE +9.90 bpm
               73.1% of days affected, p90 26.4 bpm, max 76.2 bpm
  daily MEAN   15-min  70.0 bpm -> hourly  70.1 bpm   bias          -0.03 bpm
A max is an order statistic and hourly sampling misses transient spikes; a mean is unaffected. So on 76% of the training corpus hr_peak is supervised against a target that is ~10 bpm too LOW and noisily so, while hr_raw's target is unbiased. Evaluation then scores against TRUE 15-min peaks.

--- 4. THIS IS A NEW, INDEPENDENT EXPLANATION OF THE hr_raw / hr_peak ASYMMETRY ---
The problem phase-c-hr-peak-saturated-prior-20260805 has spent ~18 experiments on why hr_raw conditions and hr_peak does not, under an ORDER-STATISTIC hypothesis about the encoder's patch averaging. That hypothesis is about the REPRESENTATION. This is about the LABEL, it is orthogonal, and it predicts the observed saturation directly: exp-e40's adapter emits hr_peak ~121.5 while true peaks run to 185 (n=10 dump), and ~121 is close to what an hourly-sampled corpus teaches as the top of the range. It also predicts that hr_raw survives every intervention that hr_peak fails, which is exactly the observed pattern (exp-e40: hr_raw absolute skill +5.03 bpm, hr_peak +0.29 bpm, despite near-identical 5x gains in prefix-attributable terms).

--- 5. WHAT TO DO, CHEAPEST FIRST ---
(a) Do NOT supervise hr_peak on hourly-sampled days: gate the hr_peak slot on native sampling rate, or     render it only where cov>0.9. Costs nothing, testable in one training run against exp-e40 as the control.
(b) Re-derive lifesnaps HR from the mongo BSON dump if it carries intraday data -- would recover 76% of the     corpus for dense-HR training AND fix the peak targets. Verify the source first; NOT established.
(c) Stop calling the pmdata-only cohort 'strict-clean' -- name it what it is, and report the native sampling     rate beside every population count.
(d) Re-run the exp-e40 comparison with an eval cohort matched to the TRAINING sampling mix, to separate     'the model generalises' from 'the model was scored on its easiest 18% of samples'.

SCOPE: the 9.90 bpm figure is an EMULATION on pmdata, not a measurement of lifesnaps' own error (lifesnaps' true 15-min peaks are unavailable). It bounds the effect for days that look like pmdata's.

## Updates
- **2026-09-01T07:41:54+00:00** — PARKED 2026-09-01: THE PREMISE NO LONGER HOLDS ON THIS CORPUS. Measured, not assumed, and cross-checked two independent ways before retiring the record.

The problem states LifeSnaps HR is HOURLY, that the strict-clean eval population is therefore 100% pmdata, and that 76% of hr_peak training targets are ~10 bpm suppressed. On the current bundle that is false.

READ 1 -- inter-reading spacing (scratch/_hourly_peak_audit.py). Median gap between consecutive present HR minutes, per dataset: lifesnaps 1.0 minutes, and only 16 of its 4,778 windows (0.3%) have a median gap >= 30 min. Across the whole Phase-A roster 17/9,365 windows (0.2%) are hourly-sampled; across the Phase-C band04 roster 16/7,542 (0.2%). There is no hourly population left to mask.

READ 2 -- cross-check, because a median of 1 is compatible with a day that is dense in bursts and hourly elsewhere (scratch/_hourly_crosscheck.py). Two further statistics per dataset: the share of gaps that are exactly 1 minute, and readings per PRESENT hour (60 = fully dense, ~1 = hourly knots). lifesnaps reads frac(gap==1) p10/p50/p90 = 0.994/0.999/1.000 and readings-per-hour p10/p50/p90 = 36.4/59.4/60.0, with only 99/4,778 windows (2.1%) under 5 reads/hour. Both reads agree: lifesnaps is natively 1-minute in this bundle.

WHAT IS STILL TRUE AND WORTH KEEPING. The MECHANISM the record establishes is sound and was independently reconfirmed today: subsampling dense pmdata days to an hourly grid costs a mean peak deficit of 27.32 bpm (median 23.83, p90 52.98, n=2,074) on this corpus -- larger than the 10.06 bpm the record measured on the older bundle, because these windows are denser. So the physics is right; only the population it applied to is gone. The record's other standing conclusion -- DO NOT INTERPOLATE HR to repair peaks, because the max of a piecewise-linear interpolant is the max of its knots and PCHIP degrades the per-sample correlation 0.8456 -> 0.7664 -- remains valid and is worth preserving as a do-not-retry.

WHAT THIS MEANS FOR THE PROPOSED FIX. The record's prescription was per-SLOT supervision masking: train state and hr_raw on everything, render hr_peak only on natively dense days. Grep confirms it was never implemented (no hourly / per-slot / hr_density logic in src/training/datasets.py). It should NOT be implemented now: it would gate 0.2% of rows, which cannot move any measured quantity, and it would add a masking path that a future denser-or-sparser corpus could silently mis-apply.

PARKED rather than resolved: this is a data-quality report with no hypothesis attached, and 'resolved' requires a confirmed one. If a future merge reintroduces an hourly source, the mechanism and the reads above are the ready-made test -- re-run both scripts and the population is visible in one pass.

#sensor-text #cross-modal #optihealth-eiv #data-contract-violation
