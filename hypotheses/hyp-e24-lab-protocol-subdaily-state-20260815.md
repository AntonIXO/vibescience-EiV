---
created_at: '2026-08-15T07:41:42+00:00'
former_ids: []
gates:
- blocking: true
  description: 'PRIMARY. On at least one lab-protocol cohort, the best sub-daily arm
    (HR or HR+clock, W<=120) must reach absolute cross-subject OOF AUC >= 0.70 AND
    exceed the clock-only control on the identical split by >= +0.10. Both parts required:
    0.70 absolute rules out a marginal effect, and the clock margin rules out circadian
    bookkeeping. MDE 0.70 on the absolute.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_lab_cohort_beats_clock_substantially
  mde: 0.7
- blocking: true
  description: SUBJECT-GROUPED, NOT SESSION-SPLIT. Every cohort scored with GroupKFold
    by subject_id, so no result can be within-session memorisation reported as accuracy
    (the MAS.S60 lecture-4 trap and this project's standing splitting rule). Cohorts
    with <2 subjects or <2 classes must be SKIPPED and reported as skipped, never
    silently dropped.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_subject_grouped_split_enforced
  mde: null
- blocking: false
  description: REPLICATION across cohorts. At least 2 of the 4 lab cohorts should
    show a positive delta over clock at the best sub-daily window. A single positive
    among four is a multiple-comparisons draw.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_replicates_across_cohorts
  mde: null
id: hyp-e24-lab-protocol-subdaily-state-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: At least one lab-protocol cohort reaches absolute cross-subject
    AUC >= 0.70 at a sub-daily window with a clock-margin >= +0.10. These cohorts
    carry INDUCED stress with dense minute-level labels, which is the regime where
    an acute autonomic response should be largest and cleanest — and they are exactly
    the cohorts my earlier nulls never touched, because at the 24h grid they have
    ZERO usable windows.
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
status: confirmed
supersedes: null
topic_tags:
- wearables
- epoch-resolution
- baseline
- control
---

## Statement
H-E24 (lab-protocol cohorts, sub-daily). Sub-daily windows DO recover state detection — on the four cohorts that are structurally invisible at the 24h grid. wearable_exam_stress, nurse_stress, wesad and neuro_stress_resilience_hci carry DENSE minute-level labels from INDUCED stress protocols and have ZERO usable windows at W=1440, so every prior state arm in this project silently excluded them.

PREDICTION: at least one cohort reaches absolute cross-subject OOF AUC >= 0.70 at a sub-daily window, exceeding the clock-only control by >= +0.10 on the identical subject-grouped split.

IF CONFIRMED: 'state detection is dead on this corpus' is refuted by counterexample. The correct statement becomes that state detection fails on SPARSE IN-THE-WILD self-report and succeeds on DENSE INDUCED-STRESS protocols — which is a claim about label quality and effect size, not about the sensor stack, and it makes the sub-daily grid necessary rather than optional for those cohorts.
IF REFUTED: the null becomes far stronger, because it would then hold even where the labels are dense, the stressor is deliberately induced, and the autonomic response should be largest.

## Rationale
E16/E17/E18 tested lifesnaps and dryad only — both IN-THE-WILD, sparse, self-reported EMA. The label-supply census shows four cohorts go from ZERO usable windows at 24h to usable sub-daily: wearable_exam_stress 0->242, nurse_stress 0->105, wesad 0->63, neuro_stress 0->41 (at W=30). They are LAB PROTOCOLS with INDUCED stress and DENSE minute-level labels.

That is the regime where an acute autonomic response should be strongest, and it is the entire substantive argument for going sub-daily — yet I generalised 'state detection is dead on this corpus' without ever testing them. If sub-daily state detection works anywhere here, it works on these. A null on cohorts with induced stress and dense labels would be a far stronger null than anything E16/E18 produced; a positive means the daily window was hiding real signal and my closing summary was wrong.

## Plan
scratch/e24_lab_cohorts.py -> logs/e24_lab_cohorts.json. Pure CPU, minutes.

FOUR COHORTS, each binarised to stress-vs-not:
  wearable_exam_stress  stress_level, split at cohort median
  nurse_stress          stress_level {0,1,2}
  wesad                 protocol_condition == 2 ('stress'), vocabulary-coded
  neuro_stress_resilience_hci  stress_level, split at cohort median

WINDOWS: W in {15, 30, 60, 120} minutes before each labelled epoch — the sub-daily range that is STRUCTURALLY UNAVAILABLE at the 24h grid for these cohorts.

ARMS on the identical rows, GroupKFold by SUBJECT (never within-session), RF(300, min_samples_leaf=3):
  clock-only    hour sin/cos + minute-of-day        CONFOUND FLOOR
  hr            HR/HRV stats over [epoch-W, epoch)
  hr+clock      both
Report absolute AUC, delta over clock, and the majority baseline together in one table — the standing three-numbers rule.

Feature builder identical to E16/E18 so any difference is the COHORT, not the features.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_lab_cohort_beats_clock_substantially` (blocking): PRIMARY. On at least one lab-protocol cohort, the best sub-daily arm (HR or HR+clock, W<=120) must reach absolute cross-subject OOF AUC >= 0.70 AND exceed the clock-only control on the identical split by >= +0.10. Both parts required: 0.70 absolute rules out a marginal effect, and the clock margin rules out circadian bookkeeping. MDE 0.70 on the absolute.
Gate `g2_subject_grouped_split_enforced` (blocking): SUBJECT-GROUPED, NOT SESSION-SPLIT. Every cohort scored with GroupKFold by subject_id, so no result can be within-session memorisation reported as accuracy (the MAS.S60 lecture-4 trap and this project's standing splitting rule). Cohorts with <2 subjects or <2 classes must be SKIPPED and reported as skipped, never silently dropped.
Gate `g3_replicates_across_cohorts` (advisory): REPLICATION across cohorts. At least 2 of the 4 lab cohorts should show a positive delta over clock at the best sub-daily window. A single positive among four is a multiple-comparisons draw.

#wearables #epoch-resolution #baseline #control #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
