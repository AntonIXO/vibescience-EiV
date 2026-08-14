---
artifacts: []
closed: true
config_note: E24 hidden-state probe on adapter outputs/adapters/medgemma_12ch_e23v2
  (BASELINE checkpoint, whose mechanism evidence survived its controls; NOT arm1 whose
  null_delta inverted). 4-bit QLoRA load, RTX 5070. Teacher-forced true target JSON;
  residual stream captured via output_hidden_states at the token position preceding
  the first digit of hr_peak and of hr_raw. Strict-clean population (no zero-bpm present
  steps, daily mean>40, coverage>0.9). RidgeCV 70/30 seed 0, with mandatory increment-over-true-mean
  control, hr_raw positive control, and shuffled-target null. NO training run, NO
  generation.
crash_reason: ''
created_at: '2026-08-05T17:54:39+00:00'
diagnostics_measured:
- after: 0.685
  before: 0.0665
  diagnostic_id: eiv_lm_hidden_peak_r2
external_run: ''
gate_results:
- evidence: 'PASSED, but the number that satisfied it is NOT the number that should
    be credited, and the honest value is the leakage-controlled one.


    AS PREREGISTERED (E24, n=900, teacher-forced true context): final-layer increment
    +0.7476 (R2 0.8029 vs control 0.0553). That clears the +0.25 bar by 3x. Per-layer:
    L0 -0.0675 (nothing before the prefix integrates -- apparatus sanity check), L8
    +0.7672, L17 +0.7691, L25 +0.7544, L34 +0.7476.


    BUT E24b FOUND THE FIGURE WAS MOSTLY LEAKAGE. To place the read head at the hr_peak
    emission position, the probe teacher-forces the true answer prefix, which contains
    the true bucket label AND the true hr_raw digits. Zeroing the soft-token prefix
    entirely (arm B) still scores R2 0.7560 -- the text alone nearly saturates the
    metric. So the preregistered increment does NOT isolate the prefix''s contribution,
    and I am not claiming the +0.7476.


    HONEST VALUE (E24c, arm C, neutral uninformative context, n=600): R2 = 0.6850
    vs control 0.0665, increment +0.6185. Its own null (arm E, DONOR prefix + same
    neutral context) is -0.0014, i.e. EXACTLY zero, so the per-sample prefix effect
    is +0.6864. Shuffled-target null -0.0125.


    The gate therefore passes on both the preregistered measurement (+0.7476) and
    the stricter leakage-controlled one (+0.6185). Recorded as PASS on the stricter
    figure; the +0.7476 is withdrawn as contaminated.'
  gate_id: gate-lm-hidden-peak-increment
  passed: true
- evidence: 'PASSED decisively, and it is what licenses reading the rest. Same probe
    at the hr_raw emission position recovering the true daily mean, final layer: R2
    = 0.9905 (n=900). Per layer: L0 -0.0145, L8 0.9889, L17 0.9951, L25 0.9915, L34
    0.9905.


    The L0 value is the key apparatus check: at the embedding layer, before any attention
    has integrated the prefix, the probe recovers NOTHING (-0.0145 for hr_raw, -0.0675
    increment for hr_peak, both indistinguishable from the control). By L8 both are
    near their maxima. That is exactly the signature of a correctly-placed read head
    reading prefix-derived information, and it rules out the failure mode where the
    probe is accidentally reading teacher-forced digits at position 0.


    Independently confirmed under the neutral-context condition in E24c: arm C recovers
    hr_mean at R2 = 0.9701 with NO informative text in the context at all.'
  gate_id: gate-hr-raw-positive-control
  passed: true
- evidence: 'PASSED on every configuration tested. Shuffled-target Ridge on identical
    features: -0.0034 (E24, n=900, final layer, teacher-forced context) and -0.0125
    (E24c, n=600, arm C neutral-context features). Both well inside the |R2| < 0.05
    bar, so the p>n regime (d=2560 vs n_train 630/420) is not producing memorisation.


    Additional null not required by the gate but measured: arm E (donor prefix + neutral
    context) = -0.0014, and arm D (donor prefix + true context) = 0.7526 vs arm A
    0.7813. The D/A comparison shows the true-context configuration is almost entirely
    donor-insensitive, which is the same leakage the B arm exposed.'
  gate_id: gate-shuffle-null
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-failure-is-a-readout-failure-not-a-representation-failure-and-not-a-
id: exp-e24-lm-hidden-peak-20260805
observed_effects:
- delta: 0.6185
  diagnostic_id: eiv_lm_hidden_peak_r2
  direction: up
parent_experiment_id: hyp-eiv-patching-is-linear-not-average-20260805-exp-2026-08-05t16-54-54
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_lm_hidden_peak_r2: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-hr-peak-failure-is-a-readout-failure-not-a-representation-failure-and-not-a-]].
Branches from [[hyp-eiv-patching-is-linear-not-average-20260805-exp-2026-08-05t16-54-54]].

## Measurements
- [[eiv_lm_hidden_peak_r2]]: before 0.0665 → after 0.685  (Δ +0.6185, up ✓ predicted up)

## Gates
- `gate-lm-hidden-peak-increment`: PASS — PASSED, but the number that satisfied it is NOT the number that should be credited, and the honest value is the leakage-controlled one.

AS PREREGISTERED (E24, n=900, teacher-forced true context): final-layer increment +0.7476 (R2 0.8029 vs control 0.0553). That clears the +0.25 bar by 3x. Per-layer: L0 -0.0675 (nothing before the prefix integrates -- apparatus sanity check), L8 +0.7672, L17 +0.7691, L25 +0.7544, L34 +0.7476.

BUT E24b FOUND THE FIGURE WAS MOSTLY LEAKAGE. To place the read head at the hr_peak emission position, the probe teacher-forces the true answer prefix, which contains the true bucket label AND the true hr_raw digits. Zeroing the soft-token prefix entirely (arm B) still scores R2 0.7560 -- the text alone nearly saturates the metric. So the preregistered increment does NOT isolate the prefix's contribution, and I am not claiming the +0.7476.

HONEST VALUE (E24c, arm C, neutral uninformative context, n=600): R2 = 0.6850 vs control 0.0665, increment +0.6185. Its own null (arm E, DONOR prefix + same neutral context) is -0.0014, i.e. EXACTLY zero, so the per-sample prefix effect is +0.6864. Shuffled-target null -0.0125.

The gate therefore passes on both the preregistered measurement (+0.7476) and the stricter leakage-controlled one (+0.6185). Recorded as PASS on the stricter figure; the +0.7476 is withdrawn as contaminated.
- `gate-hr-raw-positive-control`: PASS — PASSED decisively, and it is what licenses reading the rest. Same probe at the hr_raw emission position recovering the true daily mean, final layer: R2 = 0.9905 (n=900). Per layer: L0 -0.0145, L8 0.9889, L17 0.9951, L25 0.9915, L34 0.9905.

The L0 value is the key apparatus check: at the embedding layer, before any attention has integrated the prefix, the probe recovers NOTHING (-0.0145 for hr_raw, -0.0675 increment for hr_peak, both indistinguishable from the control). By L8 both are near their maxima. That is exactly the signature of a correctly-placed read head reading prefix-derived information, and it rules out the failure mode where the probe is accidentally reading teacher-forced digits at position 0.

Independently confirmed under the neutral-context condition in E24c: arm C recovers hr_mean at R2 = 0.9701 with NO informative text in the context at all.
- `gate-shuffle-null`: PASS — PASSED on every configuration tested. Shuffled-target Ridge on identical features: -0.0034 (E24, n=900, final layer, teacher-forced context) and -0.0125 (E24c, n=600, arm C neutral-context features). Both well inside the |R2| < 0.05 bar, so the p>n regime (d=2560 vs n_train 630/420) is not producing memorisation.

Additional null not required by the gate but measured: arm E (donor prefix + neutral context) = -0.0014, and arm D (donor prefix + true context) = 0.7526 vs arm A 0.7813. The D/A comparison shows the true-context configuration is almost entirely donor-insensitive, which is the same leakage the B arm exposed.

**Verdict:** supports

## Notes
RESULT: the hr_peak failure is localised to the READOUT. All three preregistered gates pass, but READ THE METHOD CAVEAT -- my own headline number was an artifact and I withdrew it.

=== WHAT WAS MEASURED ===
Three probes on adapter medgemma_12ch_e23v2 (BASELINE, not arm1), strict-clean population (no zero-bpm present steps, mean>40, cov>0.9), RidgeCV 70/30 seed 0, no training, no generation.

E24 (n=900), teacher-forced true target JSON, residual stream at the hr_peak emission position:
  layer      R2->peak   increment   |  hr_raw position R2->mean
  0          -0.0122    -0.0675     |  -0.0145
  8           0.8225    +0.7672     |   0.9889
  17          0.8244    +0.7691     |   0.9951
  25          0.8097    +0.7544     |   0.9915
  34 (final)  0.8029    +0.7476     |   0.9905
  control R2(peak | true mean alone) 0.0553; shuffled target -0.0034
The L0 row is the apparatus check: before any attention integrates the prefix the probe recovers nothing; by L8 both slots are near maximum.

=== THE HEADLINE +0.7476 WAS MOSTLY LEAKAGE -- E24b ===
Placing the read head at the hr_peak position requires teacher-forcing the answer prefix, which CONTAINS the true bucket label and the true hr_raw digits. Four arms, n=600, final layer:
  A real prefix + true context       0.7813
  B ZERO prefix + true context       0.7560   <- text alone nearly saturates
  C real prefix + NEUTRAL context    0.6850
  D shuffled prefix + true context   0.7526
  control 0.0665
(A-B) = +0.0253 only. My preregistered increment did NOT isolate the prefix. I withdraw the +0.7476.

=== MY GATE HAD A DESIGN ERROR, STATED PLAINLY ===
I wrote the E24b survival rule as (A-B) > +0.15 AND C > control+0.15. (A-B) is a REDUNDANCY test, not a presence test: when the context already reconstructs the peak at 0.756, no second source can add much regardless of whether it carries the information. Conjoining redundancy with presence was wrong. The script printed 'E24 IS RETRACTED' on that rule and it is recorded as printed.

=== E24c SUPPLIED THE MISSING NULL AND THE ANSWER IS CLEAN ===
Arm C had never been given its own null. n=600, neutral uninformative context throughout:
  C real prefix    + NEUTRAL context -> peak     0.6850
  E SHUFFLED prefix + NEUTRAL context -> peak   -0.0014   <- EXACTLY zero
  F shuffled target on C features               -0.0125
  C real prefix    + NEUTRAL context -> hr_mean  0.9701
  per-sample prefix effect (C - E) = +0.6864
A donor's prefix carries literally nothing about this sample's peak, while the real prefix carries R2 0.685 with no informative text present. The peak DOES traverse the frozen forward pass and arrives at the emission position, from THIS sample's prefix.

=== WHAT THIS MEANS ===
The pipeline now reads: prefix HAS the peak (R2 0.81, increment +0.72 over mean) -> it SURVIVES 34 frozen layers and is linearly present at the exact token that emits it (R2 0.685, null -0.0014) -> the emitted value has NEGATIVE skill against an always-predict-median constant (-5.88 bpm). The loss is at the final linear step, not in the representation and not in the forward pass. That is the first direct evidence localising this defect, and it makes rb-e4-lora-lm-head-20260805 (LoRA on the LM head / final layers, currently NOT in DEFAULT_TARGET_MODULES -- verified in src/training/lora.py:15-23, and attach_lora_adapters is called at pipeline.py:2896 with no override) the mechanistically targeted arm rather than merely the next untried lever.

=== SCOPE LIMITS, HONESTLY ===
1. PRESENCE IS NOT USABILITY. This proves the information is there to be read by a LINEAR probe with a free scale. A frozen head cannot rescale, and Ridge multiplies by 1/sigma. E4 could still fail.
2. hr_raw sits at 0.9905/0.9701 while hr_peak sits at 0.8029/0.6850 at the SAME position, and hr_raw generates while hr_peak does not. So R2 at this position does not by itself predict generation quality -- the gap between 0.97 and 0.69 may be exactly what separates 'usable' from 'not', or may be irrelevant. This probe cannot distinguish those.
3. Measured on ONE checkpoint (e23v2). Per the standing rule, mechanism evidence is not inheritable across checkpoints.
4. No generation was run; eiv_gen_peak_corr is unchanged by construction, as predicted.

#cross-modal #medgemma #sensor-text #collapse #optihealth-eiv #conditioning-collapse #data-contract-violation
