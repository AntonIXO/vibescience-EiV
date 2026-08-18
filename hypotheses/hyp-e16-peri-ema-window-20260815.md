---
created_at: '2026-08-14T21:25:10+00:00'
former_ids: []
gates:
- blocking: true
  description: 'PRIMARY. lifesnaps TENSE/ANXIOUS, GroupKFold(5) by subject, OOF AUC.
    best-W delta = AUC[HR-before-ping + clock] - AUC[clock only], for some W <= 240,
    must be >= +0.03 with within-subject label-permutation p < 0.05 (>=24 perms, same
    permuted labels both arms). MDE: the E5 within-subject perm null std was 0.0271,
    so this design is powered for ~0.028; a |delta| under that with p>=0.05 is a POWERED
    NULL and must be recorded as null, not near-miss.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_delta_over_clock
  mde: 0.028
- blocking: true
  description: ABSOLUTE BAR. The best-W [HR+clock] absolute OOF AUC must be >= 0.58
    AND must exceed the within-subject constant-predictor AUC (per-subject positive
    rate, zero physiology) on the identical pool. A significant delta sitting on chance-level
    absolute skill, or one a per-user constant already achieves, is engineering-irrelevant
    and does not count as state detection.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_absolute_and_trivial
  mde: 0.58
- blocking: false
  description: 'SHAPE. delta(W) is non-monotone with an interior maximum at W in [30,240],
    i.e. best-W delta strictly exceeds BOTH the W=15 delta and the W=1440 delta. Tests
    the dilution mechanism specifically rather than ''shorter is better'' generically.
    Informational: a flat or 1440-maximal profile refutes the mechanism even if g1
    passes on level.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_interior_maximum
  mde: null
- blocking: false
  description: REPLICATION across targets. The sign of the best-W delta must agree
    on at least 2 of the 3 targets (lifesnaps TENSE/ANXIOUS, lifesnaps TIRED, dryad
    stress-VAS-above-own-median). A single-target positive with two negatives is a
    multiple-comparisons draw, not a finding.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g4_cross_target_sign
  mde: null
id: hyp-e16-peri-ema-window-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'PRIMARY. lifesnaps TENSE/ANXIOUS cross-subject OOF AUC delta of
    [HR-over-W-before-ping + clock] over [clock-only] at the best W<=240 exceeds +0.03,
    and strictly exceeds the same delta computed at W=1440. Reference: E5 measured
    the daily-summary regime at delta -0.0278 with perm p=0.84 and best-of-any-composition
    absolute AUC 0.5183. Predicted non-monotone in W with an interior maximum in [30,240]
    min.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- wearables
- epoch-resolution
- baseline
- control
---

## Statement
H-E16 (peri-EMA alignment). Cross-subject momentary affect detection from HR is supply-limited by TEMPORAL ALIGNMENT, not by an information ceiling: aggregating HR over the W minutes immediately BEFORE an EMA ping carries affect information that a 24h daily summary of the same day does not, and the gain survives a clock-only control.

WHY THE PRIOR NULL DOES NOT BOUND THIS. E5/E14 measured DAILY summaries against daily-reduced labels and found chance (lifesnaps hr_only AUC 0.4816, multichannel 0.4538, best-of-any 0.5183, delta -0.0278, within-subject perm p=0.84). Those probes fed the model a 1440-minute average and asked it about a state that lasted minutes. The corpus now places each ping on its own epoch, so target and feature can be put on the same timescale for the first time. E5's own scope note reserves "(c) sub-daily/event-level affect" as untested.

MECHANISM, and why it is NOT the hr_peak mechanism. ro-window-shortening-20260805 died because max->mean convergence inflated a raw R2 with exactly zero increment. That is an order-statistic artifact and cannot occur here: the target is a categorical point event, not a within-window extremum. The mechanism claimed instead is DILUTION — the autonomic response to an acute state is minutes-to-tens-of-minutes wide, so averaging it against ~1400 unrelated minutes attenuates it by roughly the duty-cycle ratio. Concretely: at W=1440 only 6775 usable labelled windows survive and 77.6% of multi-ping lifesnaps windows carry contradicting states, while at W<=60 disagreement is exactly 0.000 and median HR coverage inside the labelled window is 1.000.

PREREGISTERED PREDICTIONS, committed before any number exists:

P1 (PRIMARY, directional). On lifesnaps TENSE/ANXIOUS, cross-subject GroupKFold(5) OOF AUC of [HR-over-W-before-ping + clock] MINUS [clock only] will be strictly LARGER at some W <= 240 than the same delta at W=1440, and the best-W delta will exceed +0.03 with within-subject label-permutation p < 0.05 (>=24 perms).

P2 (SHAPE). delta(W) will be non-monotone with an interior maximum in [30,240] minutes. Literature anchor: peri-EMA aggregation windows in published EMA+wearable work cluster at 15-240 min with the PRE-ping window dominating the post-ping window. If the delta is flat or maximal at 1440, the dilution account is wrong.

P3 (FALSIFIER I WILL HONOUR). If clock-only matches or beats every HR arm at every W, then the apparent sub-daily skill is circadian bookkeeping and the ceiling is REAL, not an alignment defect. I will record that as a rule-out covering peri-ping HR alignment for cross-subject affect, and the window decision then rests on channel reconstruction rather than on state.

P4 (ABSOLUTE BAR). A significant delta whose absolute AUC lands below 0.58 is engineering-irrelevant. Per the eiv_affect_multichannel_increment contract I must report absolute AUC, majority rate, AND the within-subject constant-predictor AUC alongside the delta; a delta that fails to beat that per-user constant does not count as state detection.

COST: pure CPU, no GPU, no model, minutes. Gates the whole window-size decision (1h/6h/24h/dynamic) and the state-slot design BEFORE any retrain.

## Rationale
The autonomic signature of an acute affective state is minutes-to-tens-of-minutes wide. A 1440-minute average attenuates it roughly by its duty cycle, which is why every prior affect probe on this corpus landed at chance while HR LEVEL reads back at R2 0.97 from the same encoder. The corpus change of 2026-08-14 (a7fc419) is what makes the alternative testable: the label now sits on the epoch it was recorded in, verified in the landed data (mod60 0.019 vs chance 0.0167, top-epoch share 0.004, flat minute-of-hour histogram, SEMA3 10/15/20h peaks).

The decisive design feature is the CLOCK-ONLY control. Hour-of-day predicts mood for free and needs no physiology; without that control, a short peri-ping window would post a higher AUC for circadian bookkeeping and be misread as state detection. This is the direct analogue of the R2(peak | true daily mean) control that killed the earlier window-shortening claim, and it is deliberately built so that the most likely failure mode of THIS hypothesis is the one that gets caught.

## Plan
scratch/e16_peri_ema_window.py (pure CPU, no GPU, no model weights).

ARMS per W in {15,30,60,120,240,480,1440}:
  clock       : [sin(2pi*epoch/1440), cos(...), epoch]            <- CONFOUND FLOOR
  hr@W        : present-only HR stats over [ping-W, ping): mean/std/p10/p25/p50/p75/p90/min/max/IQR/coverage/n, OLS slope, last-minus-first, last value, plus hrv mean/std/coverage
  hr@W+clock  : concatenation   <- PRIMARY ARM
  W=1440 uses the whole day and reproduces the E5 daily-summary regime as the incumbent anchor.

The window is strictly CAUSAL (before the ping only), so anything that passes is deployable as a just-in-time predictor rather than a retrospective fit.

SPLIT: GroupKFold(5) by subject_id -> cross-subject, the honest regime. NaN features median-imputed WITHIN the training fold only.
MODELS: RandomForest(300, min_samples_leaf=5, seed 0) headline + LogisticRegression linear check (per the E5 discipline where the linear check contradicted RF).
NULL: within-subject label permutation on the PRIMARY DELTA, >=24 perms, identical permuted labels applied to both arms so subject-level trait signal stays inside the null.

TARGETS (3, all cross-subject):
  lifesnaps_tense  mood_code == TENSE/ANXIOUS   (the E5 target, now momentary)
  lifesnaps_tired  mood_code == TIRED           (arousal axis, different construct)
  dryad_stress_hi  stress_raw > that subject's own median (continuous VAS, subject-relative)

EXCLUDED ON PURPOSE: pmdata. Its label epochs cluster at 07-09h with top-epoch share 0.060 and mod60 0.077 -> a retrospective MORNING DIARY, not a momentary ping. Pooling it would let 'yesterday' supervise 'now'. Recorded here so the exclusion is a pre-committed design choice, not a post-hoc filter.

Output: logs/e16_peri_ema_window_sweep.json

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_delta_over_clock` (blocking): PRIMARY. lifesnaps TENSE/ANXIOUS, GroupKFold(5) by subject, OOF AUC. best-W delta = AUC[HR-before-ping + clock] - AUC[clock only], for some W <= 240, must be >= +0.03 with within-subject label-permutation p < 0.05 (>=24 perms, same permuted labels both arms). MDE: the E5 within-subject perm null std was 0.0271, so this design is powered for ~0.028; a |delta| under that with p>=0.05 is a POWERED NULL and must be recorded as null, not near-miss.
Gate `g2_absolute_and_trivial` (blocking): ABSOLUTE BAR. The best-W [HR+clock] absolute OOF AUC must be >= 0.58 AND must exceed the within-subject constant-predictor AUC (per-subject positive rate, zero physiology) on the identical pool. A significant delta sitting on chance-level absolute skill, or one a per-user constant already achieves, is engineering-irrelevant and does not count as state detection.
Gate `g3_interior_maximum` (advisory): SHAPE. delta(W) is non-monotone with an interior maximum at W in [30,240], i.e. best-W delta strictly exceeds BOTH the W=15 delta and the W=1440 delta. Tests the dilution mechanism specifically rather than 'shorter is better' generically. Informational: a flat or 1440-maximal profile refutes the mechanism even if g1 passes on level.
Gate `g4_cross_target_sign` (advisory): REPLICATION across targets. The sign of the best-W delta must agree on at least 2 of the 3 targets (lifesnaps TENSE/ANXIOUS, lifesnaps TIRED, dryad stress-VAS-above-own-median). A single-target positive with two negatives is a multiple-comparisons draw, not a finding.

#wearables #epoch-resolution #baseline #control #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
