---
created_at: '2026-08-06T19:29:45+00:00'
gates: []
id: giving-the-clm-objective-the-gradient-raises-the-fraction-of-the-available-signa
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_donor_gap_bpm
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
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
GIVING THE CLM OBJECTIVE THE GRADIENT RAISES THE FRACTION OF THE AVAILABLE SIGNAL THE HEAD REALISES. A Phase C run identical to e33 except probe_weight 1.0 -> 0.0, hr_regression_weight 0.1 -> 0.0 and lora_warmup_steps_aux_only 100 -> 0 (i.e. pure CLM, same 2600 steps, same seed, same data) will raise the PREFIX-ATTRIBUTABLE (real-minus-donor) hr_raw skill under expectation decoding above +2.5 bpm, from the +1.73 bpm measured on e33.seed_1, and raise corr(mean[v], true hr_raw) above +0.80 from +0.795.

## Rationale
exp-e39 closed the within-CLM question: value tokens already carry 96.2% of the CE mass and 65.9% of the gradient into the prefix, so token re-weighting is capped at ~1.5x and is not the lever. What remains is the BETWEEN-objective balance: measured on all three e33 seed logs the CLM term is a median 3.4-4.8% of the total loss (0.6-2.5% over the first 500 steps), the remainder being an auxiliary hidden-state regression run at probe_weight=1.0 on UNNORMALISED bpm-scale targets. Yuchi/Du/Eisner (arXiv:2602.07812) grid-searched exactly this auxiliary in a pure-text setting over alpha in {0,0.01,...,100} and SELECTED alpha=0, stating that scalar regression probing losses gave no improvement at any weight. exp-e36 shows the representation at the hr_raw emitting token supports +6.11 bpm while the head realises +1.73 (28%), so there is 4.4 bpm of headroom for a better-directed objective to claim.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_peak_donor_gap_bpm]] (up).
Predicts [[eiv_gen_peak_corr]] (up).

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
