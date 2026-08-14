---
created_at: '2026-08-06T01:27:49+00:00'
former_ids:
- the-stage-c-collapse-tripwires-are-defective-and-seed-1-s-collapsed-yes-swap-0-3
gates:
- blocking: true
  description: 'LABEL-FUNCTION IDENTITY. The p6 shuffle control''s ground truth (src/probes/shuffle_control.py:79-87,
    hardcoded thresholds on the daily MEAN: <60/<80/<100/else) must be the SAME function
    as the training target (datasets.hr_label_bucket, which keys off the daily PEAK:
    >=120 tachycardic, >=100 elevated, bradycardic requires low peak AND low resting
    baseline). Apply both to the identical strict-clean population and compute agreement
    rate, marginal distributions and the full 4x4 confusion matrix. If agreement is
    low, or if any class has near-zero support under the eval label while carrying
    real support under the training label, then p6 scores a correctly-answering model
    as WRONG and its accuracies cannot be read as model quality.'
  diagnostic_id: null
  id: gate-eval-label-matches-training-label
  mde: null
- blocking: true
  description: VACUITY OF swap_attribution_acc. src/probes/counterfactual.py:142 counts
    a pair only when bucket_a != bucket_b, and line 148 returns 0.0 when swap_total
    == 0, making 'never measured' numerically identical to 'failed every attempt'.
    Establish whether the returned 0.0 can be distinguished from a genuine failure
    using the persisted artifact alone. If swap_total is not persisted, the metric
    cannot support a collapse verdict and must be made self-reporting.
  diagnostic_id: null
  id: gate-swap-metric-not-vacuous
  mde: null
- blocking: true
  description: INTERNAL CONSISTENCY OF THE COLLAPSE VERDICT. A verdict of 'collapsed'
    must not be contradicted by sibling metrics in the SAME probe artifact. Check
    the p2 ablation family (output_diff_rate_zero, output_diff_rate_random, output_diff_rate_permute,
    monotonic_scale_response, null_sensor_bucket_disagreement) and p4 (parse_failure_rate,
    hr_acc). A model that ignores its prefix cannot show output_diff_rate_zero near
    1.0 or a high monotonic scale response. If those metrics show strong conditioning
    while the verdict says collapsed, the verdict is not supported by the evidence
    in its own file.
  diagnostic_id: null
  id: gate-verdict-consistent-with-sibling-metrics
  mde: null
id: the-stage-c-collapse-tripwires-are-2211e6
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'PREDICTION: both flagged metrics are defective and the seed-1 collapse
    verdict is unsupported. Specifically I expect (i) the p6 mean-threshold label
    to disagree substantially with the trained peak-based label on real data, plausibly
    leaving the tachycardic class unreachable under the eval label, which would explain
    bucket_acc_ordered = bucket_acc_shuffled = 0.0000 on a model with parse_failure_rate
    = 0.0000; and (ii) swap_attribution_acc''s 0.0 to be indistinguishable from ''not
    measured'' because swap_total is not persisted. Direction ''none'' because this
    is an instrumentation audit -- no model is run, no readout is refit, and no system
    state changes. FALSIFIER: if the two label functions agree closely and the tachycardic
    class is reachable under the eval label, then p6''s zeros reflect the model and
    the collapse verdict stands. NOTE this cannot establish that the seed is GOOD
    -- p4 hr_acc 0.4297 clears its 0.40 threshold only narrowly and p1 hr_r2 0.9254
    is a probe on hidden states, which E24-E32 established proves presence and never
    usability.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The Stage C collapse tripwires are DEFECTIVE, and seed 1's 'collapsed=yes:swap<0.3' verdict is an instrumentation artifact rather than a finding about the model.

TWO SPECIFIC DEFECTS:
(1) swap_attribution_acc (src/probes/counterfactual.py:142-148) increments its denominator only for pairs where the model emitted DIFFERENT buckets, and returns 0.0 when that denominator is zero. 'Never measured' is therefore numerically identical to 'failed every attempt', and swap_total is not persisted to the probe artifact, so the two cases cannot be distinguished after the fact.
(2) The p6 shuffle control (src/probes/shuffle_control.py:79-87) derives ground truth from hardcoded thresholds on the daily MEAN, while the model is trained against datasets.hr_label_bucket which keys off the daily PEAK. These are different functions of different statistics, so a model emitting exactly what it was trained to emit is scored WRONG.

IF CONFIRMED: the tripwires cannot support a collapse verdict, the Stage C multi-seed selection may discard good adapters and retain bad ones, and every 'collapsed' verdict recorded in this project needs re-examination.

IF REFUTED -- the label functions agree closely and swap_total was genuinely non-zero -- the verdict stands and seed 1 really is collapsed.

SCOPE: this is an INSTRUMENTATION audit. It does NOT claim the seed is good. p4 hr_acc 0.4297 clears its threshold only narrowly, and p1 hr_r2 0.9254 is a probe on hidden states, which E24-E32 established proves presence and never usability. The claim is strictly that the VERDICT is not supported by the METRIC that produced it.

## Rationale
The Stage C seed-selection driver emitted a verdict that contradicts its own evidence:

  [seed 1] parse_fail=0.0000  hr_acc=0.4297  swap=0.0000  hr_r2=0.9254  score=0.4297  collapsed=yes:swap<0.3

The verdict rests entirely on swap_attribution_acc = 0.0. But outputs/probes/e33_phase_c.seed_1.json, written by the same probe run, reports output_diff_rate_zero 1.0000, output_diff_rate_random 1.0000, output_diff_rate_permute 0.9219, monotonic_scale_response 0.8438 and null_sensor_bucket_disagreement 0.6563. A model that ignores its prefix cannot change its output 100% of the time when the sensor is zeroed, and cannot respond monotonically to sensor scaling 84% of the time.

Two metrics in that file read EXACTLY 0.0 -- swap_attribution_acc, and BOTH arms of the p6 shuffle control (bucket_acc_ordered 0.0, bucket_acc_shuffled 0.0) -- while the p4 bucket probe on the same model reports parse_failure_rate 0.0000 and hr_acc 0.4297. Exact zeros in the presence of a demonstrably working parser are a signature of a broken metric, not a broken model.

Reading the source found two concrete defects: counterfactual.py:142-148 returns 0.0 when swap_total == 0 (so 'never measured' equals 'failed every time', and swap_total is not persisted to the artifact), and shuffle_control.py:79-87 derives ground truth from hardcoded thresholds on the daily MEAN while the training target uses datasets.hr_label_bucket on the daily PEAK. counterfactual.py:100 additionally states in-source that this generate-based P3 is superseded by scratch/diag_swap_gap.py.

This matters beyond one seed: if the tripwires are unreliable, the Stage C multi-seed selection can discard good adapters and retain bad ones, and every conclusion drawn from a 'collapsed' verdict in this project is suspect. Quantifying the defect is nearly free -- pure CPU, no GPU, no model -- and the Stage C retrain is still running, so the result arrives in time to inform seed selection.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-eval-label-matches-training-label` (blocking): LABEL-FUNCTION IDENTITY. The p6 shuffle control's ground truth (src/probes/shuffle_control.py:79-87, hardcoded thresholds on the daily MEAN: <60/<80/<100/else) must be the SAME function as the training target (datasets.hr_label_bucket, which keys off the daily PEAK: >=120 tachycardic, >=100 elevated, bradycardic requires low peak AND low resting baseline). Apply both to the identical strict-clean population and compute agreement rate, marginal distributions and the full 4x4 confusion matrix. If agreement is low, or if any class has near-zero support under the eval label while carrying real support under the training label, then p6 scores a correctly-answering model as WRONG and its accuracies cannot be read as model quality.
Gate `gate-swap-metric-not-vacuous` (blocking): VACUITY OF swap_attribution_acc. src/probes/counterfactual.py:142 counts a pair only when bucket_a != bucket_b, and line 148 returns 0.0 when swap_total == 0, making 'never measured' numerically identical to 'failed every attempt'. Establish whether the returned 0.0 can be distinguished from a genuine failure using the persisted artifact alone. If swap_total is not persisted, the metric cannot support a collapse verdict and must be made self-reporting.
Gate `gate-verdict-consistent-with-sibling-metrics` (blocking): INTERNAL CONSISTENCY OF THE COLLAPSE VERDICT. A verdict of 'collapsed' must not be contradicted by sibling metrics in the SAME probe artifact. Check the p2 ablation family (output_diff_rate_zero, output_diff_rate_random, output_diff_rate_permute, monotonic_scale_response, null_sensor_bucket_disagreement) and p4 (parse_failure_rate, hr_acc). A model that ignores its prefix cannot show output_diff_rate_zero near 1.0 or a high monotonic scale response. If those metrics show strong conditioning while the verdict says collapsed, the verdict is not supported by the evidence in its own file.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
