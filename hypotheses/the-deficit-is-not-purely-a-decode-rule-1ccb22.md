---
created_at: '2026-08-06T15:47:32+00:00'
former_ids:
- the-deficit-is-not-purely-a-decode-rule-artifact-because-the-dominant-error-is-b
gates: []
id: the-deficit-is-not-purely-a-decode-rule-1ccb22
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: none
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_gen_between_sample_std
  direction: up
  magnitude_note: ''
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
THE DEFICIT IS NOT PURELY A DECODE-RULE ARTIFACT, BECAUSE THE DOMINANT ERROR IS BIAS, NOT VARIANCE. Enumerating the exact conditional pmf p(hr_raw | sensors) over a candidate grid will show that the distribution's MEAN and MEDIAN track the truth BETTER than its argmax -- corr rises and MAE falls by a material margin -- but that E[v] and median[v] STILL LOSE to always-predict-median on the strict-clean population, because greedy hr_raw emission is centred ~13 bpm BELOW the truth (emitted ~57 vs true ~70) and a decode rule cannot remove a systematic location bias. PREDICTED: corr(E[v], true) > corr(argmax, true) and MAE(median[v]) < MAE(argmax) by >=2 bpm, BUT skill(median[v]) vs always-predict-median remains NEGATIVE.

## Rationale
Lukasik et al. arXiv:2403.04182 and OmniPred arXiv:2402.14547 establish that greedy/mode decoding is Bayes-optimal only for exact-match loss, and that median-of-samples beats mode by ~47% relative on regression metrics; Lovering et al. arXiv:2410.16007 documents numeric probability mass collapsing onto one option. My exp-e34/e35 numbers were all do_sample=False, i.e. the MODE. That is a real confound and it must be tested. BUT the specific shape of my data argues the fix is partial: greedy hr_raw emissions on seed_1 were 51-61 bpm (mean ~57) against a true population mean of 70.3, i.e. a ~13 bpm systematic LOCATION offset, not a symmetric spread around the right centre. Re-centring is what a decode rule cannot buy. exp-e36 independently showed the information IS at that token (ridge R2 0.816, MAE 1.48 bpm, donor null 0.137), so if the pmf were merely wide-but-centred, E[v] should recover most of the 6.1 bpm the probe achieves; I predict it will not.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_peak_skill_bpm]] (none).
Predicts [[eiv_gen_peak_corr]] (up).
Predicts [[eiv_gen_between_sample_std]] (up).

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
