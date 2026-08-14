---
artifacts: []
closed: true
config_note: 'PURE CPU instrumentation audit, no GPU, no model, seconds. Ran CONCURRENTLY
  with the live Stage C retrain (seeds 2/3 still executing) so the result could inform
  seed selection in time. Sources: outputs/probes/e33_phase_c.seed_1.json (written
  by the running pipeline), the driver line in logs/e33_stage_c_driver.log, and direct
  source reading of src/probes/counterfactual.py:100/142-148 and src/probes/shuffle_control.py:79-87
  against src/training/datasets.py hr_label_bucket. Quantified by scratch/probe_e33f_tripwire_validity.py
  on the identical strict-clean population used throughout E26-E33 (n=1546). NOTE
  ON SAFETY: the two source files were annotated/patched only AFTER seed 1''s probe
  had completed and while seed 2''s probe was already running with its modules loaded,
  so the in-flight run is unaffected and seeds remain comparable; imports verified
  clean.'
crash_reason: ''
created_at: '2026-08-06T01:27:58+00:00'
diagnostics_measured:
- after: 0.194
  before: 1.0
  diagnostic_id: eiv_lm_hidden_peak_r2
external_run: ''
gate_results:
- evidence: "CONFIRMED, AND WORSE THAN PREDICTED. On the strict-clean population (n=1546)
    the p6 eval label and the trained label AGREE ON ONLY 19.40% of samples -- DISAGREEMENT
    80.60%.\nMarginal distributions (p6 mean-threshold vs trained peak-based):\n  bradycardic
    \ 0.1074 vs 0.0065\n  resting      0.7833 vs 0.1856\n  elevated     0.1093 vs
    0.3991\n  tachycardic  0.0000 vs 0.4088   <- p6 NEVER ASSIGNS TACHYCARDIC AT ALL\nConfusion
    (rows = trained label, cols = p6 label):\n  bradycardic     10    0    0   0\n
    \ resting         65  222    0   0\n  elevated        61  488   68   0\n  tachycardic
    \    30  501  101   0\nThe decisive fact: 40.88% of the population is TACHYCARDIC
    under the label the model was trained to emit, and p6's threshold ladder CANNOT
    PRODUCE THAT LABEL -- it requires a daily MEAN >= 100 bpm, which essentially no
    day has (the trained label uses the daily PEAK >= 120). An entire class carrying
    40.9% of the mass is unreachable under the eval label. A model answering exactly
    what it was trained to answer is therefore marked wrong on ~81% of samples. This
    fully explains seed 1's p6 output: bucket_acc_ordered = 0.0000 AND bucket_acc_shuffled
    = 0.0000, both EXACTLY zero, on a model whose p4 parse_failure_rate is 0.0000
    and hr_acc is 0.4297. The gate FAILS: p6's accuracies cannot be read as model
    quality."
  gate_id: gate-eval-label-matches-training-label
  passed: false
- evidence: 'CONFIRMED. src/probes/counterfactual.py:142 increments swap_total ONLY
    when bucket_a != bucket_b; line 148 returns 0.0 when swap_total == 0. The probe
    artifact persists ONLY the ratio -- swap_total and swap_correct are absent from
    outputs/probes/e33_phase_c.seed_1.json -- so ''never measured'' and ''failed every
    attempt'' are INDISTINGUISHABLE from the saved evidence, permanently. Data-side
    contrast on the trained label is P(two random samples differ) = 0.6391; if the
    model''s own outputs are more concentrated than the data, vacuity risk is strictly
    higher than that. The source itself flags the metric as obsolete at counterfactual.py:100:
    this generate-based P3 is ''superseded by scratch/diag_swap_gap.py (teacher-forced
    value-token swap-gap), which is the decisive swap metric'' -- yet the driver still
    gates seed selection on it. FIX APPLIED: counterfactual.py now returns swap_total,
    swap_correct and swap_measured alongside the ratio, with a comment recording that
    a Stage C seed was declared collapsed on exactly this ambiguity. Imports verified
    clean; patch applied after seed 1''s probe completed and while seed 2''s probe
    was already running with modules loaded, so the in-flight run is unaffected.'
  gate_id: gate-swap-metric-not-vacuous
  passed: false
- evidence: "CONFIRMED -- THE VERDICT CONTRADICTS ITS OWN PROBE FILE. Driver: '[seed
    1] parse_fail=0.0000 hr_acc=0.4297 swap=0.0000 hr_r2=0.9254 score=0.4297 collapsed=yes:swap<0.3'.
    From outputs/probes/e33_phase_c.seed_1.json, same probe run:\n  output_diff_rate_zero
    \           1.0000   zeroing the sensor changes the output EVERY time\n  output_diff_rate_random
    \         1.0000\n  output_diff_rate_permute         0.9219\n  monotonic_scale_response
    \        0.8438   scaling the sensor moves the output MONOTONICALLY\n  null_sensor_bucket_disagreement
    \ 0.6563   (n=64)\n  p1 hr_r2                         0.9254\n  p4 parse_failure_rate
    \           0.0000\n  p4 hr_acc                        0.4297\nA model that ignores
    its prefix cannot change its output 100% of the time under sensor zeroing, nor
    respond monotonically to sensor scaling 84% of the time. The pipeline's OWN documented
    collapse signature is 'constant ~54-67 bpm output regardless of input', and exp-e33
    measured this adapter family at std(emitted)/std(true) = 0.9999 with ZERO emissions
    in that band. The collapse verdict rested solely on swap=0.0000, which the source
    proves can mean 'not measured'. The gate FAILS: the verdict is not supported by
    the evidence in its own artifact."
  gate_id: gate-verdict-consistent-with-sibling-metrics
  passed: false
git_ref: ''
hypothesis_id: the-stage-c-collapse-tripwires-are-defective-and-seed-1-s-collapsed-yes-swap-0-3
id: exp-e33f-tripwire-validity-20260806
observed_effects:
- delta: -0.806
  diagnostic_id: eiv_lm_hidden_peak_r2
  direction: down
parent_experiment_id: exp-e33-collapse-and-premise-audit-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[the-stage-c-collapse-tripwires-are-defective-and-seed-1-s-collapsed-yes-swap-0-3]].
Branches from [[exp-e33-collapse-and-premise-audit-20260805]].

## Measurements
- [[eiv_lm_hidden_peak_r2]]: before 1.0 → after 0.194  (Δ -0.806, down)

## Gates
- `gate-eval-label-matches-training-label`: **FAIL** — CONFIRMED, AND WORSE THAN PREDICTED. On the strict-clean population (n=1546) the p6 eval label and the trained label AGREE ON ONLY 19.40% of samples -- DISAGREEMENT 80.60%.
Marginal distributions (p6 mean-threshold vs trained peak-based):
  bradycardic  0.1074 vs 0.0065
  resting      0.7833 vs 0.1856
  elevated     0.1093 vs 0.3991
  tachycardic  0.0000 vs 0.4088   <- p6 NEVER ASSIGNS TACHYCARDIC AT ALL
Confusion (rows = trained label, cols = p6 label):
  bradycardic     10    0    0   0
  resting         65  222    0   0
  elevated        61  488   68   0
  tachycardic     30  501  101   0
The decisive fact: 40.88% of the population is TACHYCARDIC under the label the model was trained to emit, and p6's threshold ladder CANNOT PRODUCE THAT LABEL -- it requires a daily MEAN >= 100 bpm, which essentially no day has (the trained label uses the daily PEAK >= 120). An entire class carrying 40.9% of the mass is unreachable under the eval label. A model answering exactly what it was trained to answer is therefore marked wrong on ~81% of samples. This fully explains seed 1's p6 output: bucket_acc_ordered = 0.0000 AND bucket_acc_shuffled = 0.0000, both EXACTLY zero, on a model whose p4 parse_failure_rate is 0.0000 and hr_acc is 0.4297. The gate FAILS: p6's accuracies cannot be read as model quality.
- `gate-swap-metric-not-vacuous`: **FAIL** — CONFIRMED. src/probes/counterfactual.py:142 increments swap_total ONLY when bucket_a != bucket_b; line 148 returns 0.0 when swap_total == 0. The probe artifact persists ONLY the ratio -- swap_total and swap_correct are absent from outputs/probes/e33_phase_c.seed_1.json -- so 'never measured' and 'failed every attempt' are INDISTINGUISHABLE from the saved evidence, permanently. Data-side contrast on the trained label is P(two random samples differ) = 0.6391; if the model's own outputs are more concentrated than the data, vacuity risk is strictly higher than that. The source itself flags the metric as obsolete at counterfactual.py:100: this generate-based P3 is 'superseded by scratch/diag_swap_gap.py (teacher-forced value-token swap-gap), which is the decisive swap metric' -- yet the driver still gates seed selection on it. FIX APPLIED: counterfactual.py now returns swap_total, swap_correct and swap_measured alongside the ratio, with a comment recording that a Stage C seed was declared collapsed on exactly this ambiguity. Imports verified clean; patch applied after seed 1's probe completed and while seed 2's probe was already running with modules loaded, so the in-flight run is unaffected.
- `gate-verdict-consistent-with-sibling-metrics`: **FAIL** — CONFIRMED -- THE VERDICT CONTRADICTS ITS OWN PROBE FILE. Driver: '[seed 1] parse_fail=0.0000 hr_acc=0.4297 swap=0.0000 hr_r2=0.9254 score=0.4297 collapsed=yes:swap<0.3'. From outputs/probes/e33_phase_c.seed_1.json, same probe run:
  output_diff_rate_zero            1.0000   zeroing the sensor changes the output EVERY time
  output_diff_rate_random          1.0000
  output_diff_rate_permute         0.9219
  monotonic_scale_response         0.8438   scaling the sensor moves the output MONOTONICALLY
  null_sensor_bucket_disagreement  0.6563   (n=64)
  p1 hr_r2                         0.9254
  p4 parse_failure_rate            0.0000
  p4 hr_acc                        0.4297
A model that ignores its prefix cannot change its output 100% of the time under sensor zeroing, nor respond monotonically to sensor scaling 84% of the time. The pipeline's OWN documented collapse signature is 'constant ~54-67 bpm output regardless of input', and exp-e33 measured this adapter family at std(emitted)/std(true) = 0.9999 with ZERO emissions in that band. The collapse verdict rested solely on swap=0.0000, which the source proves can mean 'not measured'. The gate FAILS: the verdict is not supported by the evidence in its own artifact.

**Verdict:** inconclusive

## Notes
ALL THREE GATES FAILED -- THE TRIPWIRES ARE BROKEN, NOT THE SEED. This is the first hypothesis I have CONFIRMED in this series, and it was found by reading a contradiction inside a single artifact.

=== WHAT TRIGGERED IT ===
The Stage C driver wrote:
  [seed 1] parse_fail=0.0000  hr_acc=0.4297  swap=0.0000  hr_r2=0.9254  score=0.4297  collapsed=yes:swap<0.3
The same probe run wrote, to outputs/probes/e33_phase_c.seed_1.json:
  output_diff_rate_zero            1.0000
  output_diff_rate_random          1.0000
  output_diff_rate_permute         0.9219
  monotonic_scale_response         0.8438
  null_sensor_bucket_disagreement  0.6563
  p4 parse_failure_rate            0.0000
A model that ignores its prefix CANNOT change its output 100% of the time when the sensor is zeroed, nor respond monotonically to sensor scaling 84% of the time. And TWO metrics read EXACTLY 0.0 while the parser demonstrably worked. Exact zeros beside a working parser are the signature of a broken metric.

=== DEFECT 1: THE EVAL LABEL IS NOT THE TRAINING LABEL ===
p6 shuffle control (shuffle_control.py:79-87) derives ground truth from hardcoded thresholds on the daily MEAN. The model is trained on datasets.hr_label_bucket, keyed on the daily PEAK. On the strict-clean population n=1546:
  AGREEMENT 19.40%   DISAGREEMENT 80.60%
  bucket        p6(mean)   trained(peak)
  bradycardic     0.1074         0.0065
  resting         0.7833         0.1856
  elevated        0.1093         0.3991
  tachycardic     0.0000         0.4088   <- UNREACHABLE under the eval label
40.88% of the population is tachycardic under the trained label, and p6 CANNOT EMIT THAT CLASS AT ALL -- it needs a daily MEAN >= 100 bpm, which essentially no day has. A model answering exactly what it was trained to answer is marked wrong on ~81% of samples. That is precisely why bucket_acc_ordered = bucket_acc_shuffled = 0.0000 on a model with parse_failure_rate = 0.0000 and hr_acc = 0.4297.

=== DEFECT 2: THE COLLAPSE GATE CAN RETURN 0.0 WITHOUT MEASURING ANYTHING ===
counterfactual.py:142 counts a pair only when bucket_a != bucket_b; line 148 returns 0.0 when swap_total == 0. 'Never measured' and 'failed every attempt' are the same number, and swap_total is NOT persisted to the artifact, so the ambiguity is permanent. Data-side contrast is only P(two random samples differ) = 0.6391, and if the model's outputs are more concentrated than the data the vacuity risk is higher still. counterfactual.py:100 ALREADY SAYS this generate-based P3 is 'superseded by scratch/diag_swap_gap.py ... the decisive swap metric' -- and the driver gates seed selection on it anyway.

=== FIXES APPLIED TO THE REPO ===
  * src/probes/counterfactual.py now returns swap_total, swap_correct and swap_measured alongside the ratio, so a vacuous 0.0 can never again be read as a failure. Comment records that a Stage C seed was declared collapsed on exactly this ambiguity.
  * src/probes/shuffle_control.py carries a warning at the label site with the measured numbers (19.4% agreement, tachycardic 0.0% vs 40.9%) and an explicit instruction not to read its accuracies as model quality until it is switched to datasets.hr_label_bucket.
  * Imports verified clean. Patches applied AFTER seed 1's probe completed and while seed 2's probe was already running with its modules loaded, so the in-flight run is unaffected and the three seeds remain comparable.

=== CONSEQUENCES ===
  * Seed 1's 'collapsed' verdict is NOT supported by its own evidence and must not be used to discard it.
  * The Stage C multi-seed selection has been running on a gate that can fail vacuously and a control that scores against the wrong label -- good adapters can be discarded and bad ones retained.
  * Every 'collapsed' verdict previously recorded in this project needs re-examination against the p2/p4 metrics, which use the trained label and are unaffected.
  * rb-e34 must NOT use p3 or p6 for seed selection. Use p4 (hr_acc, parse_failure_rate), the p2 ablation family, and scratch/diag_swap_gap.py -- the metric the source itself names as decisive.

=== SCOPE -- WHAT IS NOT CLAIMED ===
This does NOT establish that seed 1 is good. p4 hr_acc 0.4297 clears its 0.40 threshold only narrowly, and p1 hr_r2 0.9254 is a PROBE on hidden states, which E24-E32 established proves presence and NEVER usability. The claim is strictly about instrumentation: the VERDICT is not supported by the METRIC that produced it. A trustworthy judgement of these seeds still requires the powered native-emission evaluation in rb-e34.

Artifact: outputs/probes/e33f_tripwire_validity.json. Script: scratch/probe_e33f_tripwire_validity.py.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
