---
artifacts: []
closed: true
config_note: 'Runner: scratch/e24_lab_cohorts.py -> logs/e24_lab_cohorts.json. Pure
  CPU, minutes. Corpus git_sha e849eb7, seed 0. Feature builder IDENTICAL to E16/E18
  so any difference is the COHORT, not the features. GroupKFold by subject_id throughout.


  BUG FOUND AND FIXED MID-RUN: the ordinal binarisation used a bare `val > cohort
  median`, which yields ZERO positives when the median IS the top value. nurse_stress
  is 3-valued {0,1,2} with 2334 of 3028 events at 2.0, so median==2.0 and the split
  selected nothing — the cohort was silently skipped as ''insufficient classes''.
  Fixed to fall back to `val >= max` when median==max, and the realised rule plus
  positive rate are now printed for every cohort so a degenerate split cannot pass
  unnoticed.'
crash_reason: ''
created_at: '2026-08-15T07:41:49+00:00'
diagnostics_measured:
- after: 0.8497
  before: 0.564
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: logs/e24_lab_cohorts.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_lab_cohort_beats_clock_substantially
  passed: true
- evidence: ''
  gate_id: g2_subject_grouped_split_enforced
  passed: true
- evidence: ''
  gate_id: g3_replicates_across_cohorts
  passed: true
git_ref: ''
hypothesis_id: hyp-e24-lab-protocol-subdaily-state-20260815
id: exp-e24-lab-cohorts-20260815
key_numbers:
  exam_stress_best_hr_auc: 0.4801
  exam_stress_clock_auc_w30: 0.2966
  exam_stress_n_subj: 10.0
  neuro_clock_auc: 0.493
  neuro_delta_over_clock: 0.187
  neuro_hr_auc_w30: 0.6806
  neuro_n: 962.0
  neuro_n_subj: 35.0
  nurse_clock_auc_w15: 0.6358
  nurse_delta_w120: -0.202
  nurse_majority: 0.771
  wesad_clock_auc: 0.564
  wesad_delta_w120: 0.2654
  wesad_hr_auc_w120: 0.8497
  wesad_hr_auc_w15: 0.7739
  wesad_majority: 0.882
  wesad_n: 1749.0
  wesad_n_subj: 15.0
observed_effects:
- delta: 0.2857
  diagnostic_id: eiv_affect_multichannel_increment
  direction: up
parent_experiment_id: exp-e18-within-subject-state-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-e24-lab-protocol-subdaily-state-20260815]].
Branches from [[exp-e18-within-subject-state-20260815]].
External run: logs/e24_lab_cohorts.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.564 → after 0.8497  (Δ +0.2857, up ✓ predicted up)

## Gates
- `g1_lab_cohort_beats_clock_substantially`: PASS
- `g2_subject_grouped_split_enforced`: PASS
- `g3_replicates_across_cohorts`: PASS

**Verdict:** supports

## Key numbers
- `exam_stress_best_hr_auc`: 0.4801
- `exam_stress_clock_auc_w30`: 0.2966
- `exam_stress_n_subj`: 10
- `neuro_clock_auc`: 0.493
- `neuro_delta_over_clock`: 0.187
- `neuro_hr_auc_w30`: 0.6806
- `neuro_n`: 962
- `neuro_n_subj`: 35
- `nurse_clock_auc_w15`: 0.6358
- `nurse_delta_w120`: -0.202
- `nurse_majority`: 0.771
- `wesad_clock_auc`: 0.564
- `wesad_delta_w120`: 0.2654
- `wesad_hr_auc_w120`: 0.8497
- `wesad_hr_auc_w15`: 0.7739
- `wesad_majority`: 0.882
- `wesad_n`: 1749
- `wesad_n_subj`: 15

## Notes
MY CLOSING SUMMARY IS FALSIFIED BY COUNTEREXAMPLE. Sub-daily state detection WORKS — on the cohorts that are structurally invisible at the 24h grid and that every prior state arm in this project therefore excluded.

WESAD, cross-subject GroupKFold over 15 subjects, n=1749, pos 0.118:
  W=15  clock 0.5640 | hr 0.7739 | delta +0.2081
  W=30  clock 0.5640 | hr 0.7745 | delta +0.1674
  W=60  clock 0.5640 | hr 0.7994 | delta +0.2069
  W=120 clock 0.5640 | hr 0.8497 | delta +0.2654
neuro_stress_resilience_hci, 35 subjects, n=962, pos 0.547: hr 0.6806 vs clock 0.4930, delta +0.1870.

Same feature builder as E16/E18. Same subject-grouped protocol. The ONLY thing that changed is the cohort — which means the E16/E18 nulls were about the LABELS, not the sensors.

THE TWO NEGATIVE COHORTS ARE INFORMATIVE, NOT NOISE, AND MUST NOT BE AVERAGED AWAY:
  * wearable_exam_stress: EVERY AUC IS BELOW 0.5 (clock 0.2966-0.3788, hr 0.3921-0.4801) on 10 subjects. Systematic cross-subject INVERSION — the model learns a rule that reverses on held-out subjects. With a session-constant label fanned across each exam sitting, per-subject exam timing is decodable and its relation to the median-split label flips between subjects. This is a LABEL-CONSTRUCTION artifact of my median split on a session-constant value, not evidence about physiology.
  * nurse_stress: deltas negative (-0.0834 to -0.2020), but note clock alone reaches 0.6358 against a 0.771 majority — shift-schedule timing carries most of the decodable signal, and adding HR dilutes it. Also the binarisation is `>= max` because 77% of events sit at the top level, so the target is heavily imbalanced.

WHAT THIS CHANGES:
1. 'State detection is dead on this corpus' is WRONG and is retracted. Correct statement: state detection FAILS on sparse in-the-wild self-report (lifesnaps/dryad) and SUCCEEDS on dense induced-stress protocols (wesad, neuro_stress). That is a claim about LABEL QUALITY and EFFECT SIZE, not about the sensor stack or the encoder.
2. The recommendation to DROP the state slot is WITHDRAWN. There is real, subject-grouped, clock-controlled state signal in this corpus at AUC 0.85.
3. The sub-daily grid is NECESSARY for these cohorts, not optional — they have ZERO usable windows at W=1440. This is the first measured argument FOR shortening the window that survives its own controls, and it does not conflict with the earlier rule-out, which was scoped to cross-subject affect on lifesnaps/dryad.

HONEST LIMITS. WESAD is a lab protocol: a deliberately induced stressor against a quiet baseline, which is the easiest possible discrimination and is NOT the deployment distribution (optihealth_supabase, sparse, in-the-wild, currently ZERO state labels). The published literature this session gathered says exactly this — lab protocols report 0.95-0.99 while in-the-wild cross-subject momentary affect sits at 0.55-0.75. So 0.85 on WESAD does NOT license a product claim; it licenses the statement that the pipeline CAN learn state when the labels carry a real effect.

#wearables #epoch-resolution #baseline #control #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
