---
created_at: '2026-08-06T11:49:23+00:00'
gates: []
id: the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: none
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: ''
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: testing
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak native-emission deficit that E24-E33 spent ten experiments explaining does not exist at the claimed magnitude. On a properly powered strict-clean population (n>=300) the e33 adapter's native hr_peak skill vs the strongest trivial baseline will be statistically indistinguishable from zero (CI95 crossing 0, |point estimate| < 2 bpm), while the donor-prefix control shows a LARGE positive gap. i.e. per-sample conditioning is real and the system sits at roughly baseline on native hr_peak emission -- neither the -5.88 bpm catastrophe the problem was opened on, nor a solved slot. COROLLARY: the Stage C driver's verdict 'FAIL: all 3 seeds collapsed' is an instrumentation artifact (p3 swap_attribution_acc vacuous, p6 scoring against an unreachable label), not a model property.

## Rationale
exp-e33 showed the -5.88 bpm headline came from n=96 containing 8 physiologically impossible true peaks of 0.0 bpm, and that the estimate moves across a 3.8 bpm range purely with the filter, with the strict-clean CI at n=34 crossing zero. Every mechanism arm since E24 was aimed at that number. Meanwhile the localisation chain is intact and positive: prefix R2 0.8084 (increment +0.7211), emission-position R2 0.6850 with donor null -0.0014, and a fitted free-running digit readout in lm_head's own function class reaches 14.263 bpm vs 18.492 median (+4.2 bpm, CI excludes 0). A pathway carrying the signal that far does not usually emit a large NEGATIVE skill; an unpowered contaminated estimator does. e33 is additionally a fresh run carrying the E23 shared-bucket sampler fix (realised marginal 0.164/0.289/0.309/0.238, not saturated) and hr_regression_weight 0.1.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_peak_skill_bpm]] (none).
Predicts [[eiv_gen_peak_donor_gap_bpm]] (up).
Predicts [[eiv_gen_pred_bucket_entropy]] (up).
Predicts [[eiv_gen_peak_corr]] (up).

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
