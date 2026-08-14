---
created_at: '2026-08-06T16:46:10+00:00'
former_ids:
- the-hr-raw-hr-peak-asymmetry-is-substantially-a-decode-artifact-under-expectatio
gates: []
id: the-hr-raw-hr-peak-asymmetry-is-507782
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
  magnitude_note: ''
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
THE hr_raw/hr_peak ASYMMETRY IS SUBSTANTIALLY A DECODE ARTIFACT. Under expectation decoding on the exact conditional pmf, hr_peak will show POSITIVE prefix-attributable skill (real-minus-donor) in the range +1 to +4 bpm with corr(mean[v], true) >= +0.35, and the fraction of the linearly-available signal that the head realises will be comparable to hr_raw's ~28% (i.e. within roughly 15-45%). Consequently exp-e34's headline -- hr_peak native skill -1.91 bpm with a donor gap of +0.48 -- is an ARGMAX artifact and understates the head, and the long-standing framing that hr_peak is a qualitatively dead slot while hr_raw works is wrong: both are the same under-use of an available signal, differing in degree, not in kind.

## Rationale
exp-e37 measured on hr_raw: argmax skill +0.85 / corr +0.523, expectation decoding +1.84 / +0.769, donor-corrected prefix-attributable gap +1.73 bpm against a ridge ceiling of +6.11 bpm at the same token = 28% realised. Every hr_peak number in this problem (exp-e34, exp-e35, and the -5.88 premise before them) was produced with do_sample=False, i.e. the MODE, which Lukasik arXiv:2403.04182 and OmniPred arXiv:2402.14547 establish is the wrong estimator for a distance metric. The hr_peak representation is independently known to be strong at the emission position (exp-e24c R2 0.6850 donor null -0.0014; exp-e28 LIN_SCALAR MAE 10.270 vs always-median 18.492 = +8.22 bpm available). If the head realises a similar ~28% of that, the prefix-attributable gap should be ~+2.3 bpm -- small, but POSITIVE and nothing like the -1.91/-2.58 that argmax reports. The order-statistic explanation for the asymmetry predicts the opposite: that hr_peak stays flat under any decode rule.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_peak_donor_gap_bpm]] (up).
Predicts [[eiv_gen_peak_corr]] (up).
Predicts [[eiv_gen_peak_skill_bpm]] (up).

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
