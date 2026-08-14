---
created_at: '2026-08-06T01:26:22+00:00'
former_ids:
- the-stage-c-seed-selection-tripwires-can-be-trusted-as-is-to-decide-whether-a-tr
id: the-stage-c-seed-selection-tripwires-2b9d24
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-06T01:26:22+00:00'
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Claim
The Stage C seed-selection tripwires can be trusted as-is to decide whether a trained adapter is mode-collapsed. Specifically: (a) swap_attribution_acc (src/probes/counterfactual.py) is a valid collapse detector whose 0.0 means the model failed the swap test; (b) the p6 shuffle control (src/probes/shuffle_control.py) validly scores HR bucket accuracy; (c) the driver line 'collapsed=yes:swap<0.3' is sufficient evidence to discard a seed.

## Method
Triggered by a contradiction inside a single artifact. The Stage C driver declared seed 1 collapsed on the strength of one number, while the same probe JSON reported strong sensor conditioning. Exact zeros in the presence of a working parser (parse_failure_rate = 0.0000) are a signature of a broken metric rather than a broken model, so both metrics were read at source and then tested against the data.

SOURCE READING:
  * src/probes/counterfactual.py:142-148 -- swap_attribution_acc increments swap_total only when bucket_a != bucket_b, and returns 0.0 when swap_total == 0.
  * src/probes/counterfactual.py:100 -- in-source note that this generate-based P3 is superseded by scratch/diag_swap_gap.py, 'the decisive swap metric'.
  * src/probes/shuffle_control.py:79-87 -- ground truth assigned from the daily MEAN with hardcoded cutoffs (<60/<80/<100/else).
  * src/training/datasets.py hr_label_bucket -- the TRAINING target keys off the daily PEAK (peak>=120 tachycardic, peak>=100 elevated, bradycardic requires low peak AND low resting baseline).

QUANTIFICATION (scratch/probe_e33f_tripwire_validity.py, pure CPU, no GPU, no model): both label functions were applied to the identical strict-clean population used throughout E26-E33 (n=1546), and their agreement rate, marginal distributions and full 4x4 confusion matrix computed. The vacuity condition for swap_attribution_acc was characterised by computing the data-side contrast P(two random samples differ) under the trained label.

## Evidence
MEASURED ON THE STRICT-CLEAN POPULATION (n=1546) by scratch/probe_e33f_tripwire_validity.py:

(b) THE p6 SHUFFLE CONTROL SCORES AGAINST A LABEL FUNCTION THE MODEL WAS NEVER TRAINED ON.
  AGREEMENT between p6's mean-threshold label and the trained hr_label_bucket: 0.1940. DISAGREEMENT: 0.8060.
  Label distributions (p6 vs trained):
    bradycardic  0.1074 vs 0.0065
    resting      0.7833 vs 0.1856
    elevated     0.1093 vs 0.3991
    tachycardic  0.0000 vs 0.4088   <- p6 NEVER assigns tachycardic
  Confusion (rows = trained label, cols = p6 label):
    bradycardic     10    0    0   0
    resting         65  222    0   0
    elevated        61  488   68   0
    tachycardic     30  501  101   0
  40.88% of the strict-clean population is tachycardic under the trained label, and p6 CANNOT EMIT THAT LABEL AT ALL -- its threshold ladder tops out at 'else 3' on the daily MEAN, and essentially no sample has a daily mean >= 100. A model answering exactly what it was trained to answer is scored WRONG on ~81% of samples. This fully explains the observed p6 output for seed 1: bucket_acc_ordered = 0.0 AND bucket_acc_shuffled = 0.0, both EXACTLY zero, on a model whose p4 parse_failure_rate is 0.0000 and whose p4 hr_acc is 0.4297.

(a) swap_attribution_acc RETURNS 0.0 WHEN IT MEASURES NOTHING.
  counterfactual.py:142 counts a pair only if bucket_a != bucket_b; line 148 returns 0.0 when swap_total == 0. 'Never measured' is therefore numerically identical to 'failed every attempt'. The probe JSON stores only the ratio -- swap_total and swap_correct are not persisted -- so the two cases CANNOT be distinguished after the fact from the artifact. Data-side contrast is P(two random samples differ) = 0.6391 on the trained label; if the model's own outputs are more concentrated than the data, vacuity risk is strictly higher. counterfactual.py:100 states in-source that this generate-based P3 is 'superseded by scratch/diag_swap_gap.py (teacher-forced value-token swap-gap), which is the decisive swap metric'.

(c) THE COLLAPSE VERDICT ON SEED 1 IS CONTRADICTED BY THE SAME PROBE FILE.
  Driver: '[seed 1] parse_fail=0.0000 hr_acc=0.4297 swap=0.0000 hr_r2=0.9254 score=0.4297 collapsed=yes:swap<0.3'
  outputs/probes/e33_phase_c.seed_1.json, same run:
    output_diff_rate_zero            1.0000   zeroing the sensor changes the output EVERY time
    output_diff_rate_random          1.0000
    output_diff_rate_permute         0.9219
    monotonic_scale_response         0.8438   scaling the sensor moves the output MONOTONICALLY
    null_sensor_bucket_disagreement  0.6563   (n=64)
    p4 parse_failure_rate            0.0000
    p4 hr_acc                        0.4297
  A model that ignores its prefix cannot produce output_diff_rate_zero = 1.0 or an 84% monotonic scale response. The pipeline's own documented collapse signature is 'constant ~54-67 bpm output regardless of input'; exp-e33 separately measured this adapter family's emission dispersion at std(emitted)/std(true) = 0.9999 with ZERO emissions in that band. The collapse verdict rested solely on swap=0.0000, which the source shows can mean 'not measured'.

SCOPE -- WHAT IS NOT CLAIMED. This does NOT establish that the seed is good. p4 hr_acc 0.4297 clears the 0.40 threshold only narrowly, and p1 hr_r2 0.9254 is a PROBE on hidden states, which E24-E32 established proves presence and never usability. The claim is strictly about instrumentation: the collapse VERDICT is not supported by the metric that produced it, and seeds may be discarded or kept for reasons unrelated to their quality.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#cross-modal #medgemma #collapse #sensor-text
