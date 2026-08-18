---
artifacts: []
closed: true
config_note: 'Runner: scratch/e23_power_check.py -> logs/e23_power_check.json. Pure
  CPU, parallel over subjects (joblib n_jobs=12, RF n_jobs=1 to avoid nested oversubscription).


  SYNTHETIC-CONTROL POWER CHECK on E18''s EXACT design: same 40 powered lifesnaps
  subjects, same W=480 arm, same per-subject forward-chaining walk-forward, same RF(200,
  min_samples_leaf=3), same trivial set (running base rate / persistence / clock-only),
  same MIN_PINGS=25 / MIN_TEST=10 filters.


  The ONLY change: the label is SYNTHETIC, generated from a real feature (hr_std)
  as y ~ Bernoulli(sigmoid(beta*z + intercept)), with the intercept bisected so the
  realised base rate matches the true 12.4%, and beta swept. An ORACLE arm scores
  the planted feature itself on the held-out tail, reporting the true achievable AUC
  actually present in the data — so the planted effect size is measured, not assumed.


  This answers a question E18 never asked: given ~10-40 pings per subject at 12.4%
  positive (2-5 positives in a held-out tail), what effect size can this design RECOVER?'
crash_reason: ''
created_at: '2026-08-15T07:39:55+00:00'
diagnostics_measured:
- after: -0.0015
  before: 0.05
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: logs/e23_power_check.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_median_subject_skill_over_strongest_trivial
  passed: false
- evidence: ''
  gate_id: g2_fraction_subjects_positive
  passed: false
git_ref: ''
hypothesis_id: hyp-e18-within-subject-state-20260815
id: exp-e23-power-check-20260815
key_numbers:
  e18_claimed_mde: 0.05
  e18_observed_skill: 0.0037
  median_positives_in_test_at_060: 8.0
  n_subjects: 40.0
  recovered_at_oracle_0589: -0.0375
  recovered_at_oracle_0667: -0.0015
  recovered_at_oracle_0741: 0.0741
  recovered_at_oracle_0812: 0.1736
  true_auc_planted_055: 0.5898
  true_auc_planted_060: 0.6667
  true_auc_planted_065: 0.7412
  true_auc_planted_075: 0.8125
observed_effects:
- delta: -0.0515
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: exp-e18-within-subject-state-20260815
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e18-within-subject-state-20260815]].
Branches from [[exp-e18-within-subject-state-20260815]].
External run: logs/e23_power_check.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.05 → after -0.0015  (Δ -0.0515, down ✗ predicted up)

## Gates
- `g1_median_subject_skill_over_strongest_trivial`: **FAIL**
- `g2_fraction_subjects_positive`: **FAIL**

**Verdict:** refutes

## Key numbers
- `e18_claimed_mde`: 0.05
- `e18_observed_skill`: 0.0037
- `median_positives_in_test_at_060`: 8
- `n_subjects`: 40
- `recovered_at_oracle_0589`: -0.0375
- `recovered_at_oracle_0667`: -0.0015
- `recovered_at_oracle_0741`: 0.0741
- `recovered_at_oracle_0812`: 0.1736
- `true_auc_planted_055`: 0.5898
- `true_auc_planted_060`: 0.6667
- `true_auc_planted_065`: 0.7412
- `true_auc_planted_075`: 0.8125

## Notes
E18 WAS NOT A POWERED NULL. Its MDE was asserted by analogy to E14 and never measured. Measured now on E18's exact design, it is false by a wide margin.

Plant a real effect, ask what the design recovers:
  oracle AUC 0.5898 -> median skill -0.0375  (frac>0 0.375)
  oracle AUC 0.6667 -> median skill -0.0015  (frac>0 0.425)   <- USEFUL DETECTOR, READS AS NOTHING
  oracle AUC 0.7412 -> median skill +0.0741  (frac>0 0.725)
  oracle AUC 0.8125 -> median skill +0.1736  (frac>0 0.875)

The design does not clear +0.05 median skill until the TRUE achievable AUC is between 0.6667 and 0.7412. E18's real MDE is ~0.72 ORACLE AUC, not '+0.05 skill'. E18 observed +0.0037; a planted 0.6667-AUC effect yields -0.0015. Indistinguishable. E18 could not have separated a genuinely useful state detector from nothing.

MECHANISM, not subtle. At 12.4% positives with 10-40 pings/subject the held-out tail holds a median of 6-8 positives. Per-subject AUC on 6 positives is noise-dominated, and the trivial baselines (running rate, persistence, clock) are estimated on that SAME tail, so max-of-three is upward-biased by selection. That is why weak-effect arms come out NEGATIVE rather than flat — and it fully explains E18's headline that physiology 'makes two-thirds of subjects worse'. That was a small-sample artifact of maximising over three noisy baselines, not evidence that physiology hurts.

RETRACTED BY THIS RUN:
  - E18's 'POWERED NULL' label -> UNDERPOWERED NULL
  - both do_not_retry entries recorded on exp-e18 (per-user affect heads on peri-ping HR/HRV; short peri-ping windows in any regime) — both rested on E18 being powered
  - 'adding peri-ping HR/HRV makes ~2/3 of subjects worse'

UNAFFECTED: E16/E17 are CROSS-SUBJECT with n=4208 over 61 subjects. Their power is a separate question and this run says nothing about them.

WHAT A CORRECT N-of-1 ARM WOULD NEED: either subjects with far denser labels (the lab-protocol cohorts in exp-e24 carry hundreds to thousands of events each), or pooled hierarchical estimation instead of per-subject AUC, or a target with a base rate nearer 50% instead of 12.4%.

VAULT LESSON: an MDE inherited by analogy is not an MDE. Any per-subject null on sparse labels must plant a known effect and report the recovery curve before claiming to have excluded anything. Third time today a number was asserted rather than measured (population_stats units, H5's premise, this MDE) — the pattern is asserting a quantity that was cheap to measure.

#wearables #n-of-1 #baseline #control #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
