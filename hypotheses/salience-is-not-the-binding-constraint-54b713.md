---
created_at: '2026-08-06T13:56:16+00:00'
former_ids:
- salience-is-not-the-binding-constraint-increasing-the-magnitude-of-the-soft-toke
gates: []
id: salience-is-not-the-binding-constraint-54b713
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: none
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: none
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
SALIENCE IS NOT THE BINDING CONSTRAINT. Increasing the magnitude of the soft-token prefix at inference (a direct, training-free increase in both its attention-logit contribution and its share of the residual stream) will NOT make the emitted hr_peak track the true peak. Across a gain sweep alpha in {0.5, 1.0, 1.72, 3.0} on the same strict-clean samples, corr(emitted, true) stays below +0.25 and skill vs always-predict-median stays NEGATIVE at every alpha, while emitted std DOES rise with alpha. i.e. more salience buys DISPERSION, not DISCRIMINATION. If true, 'make the LLM attend to the soft tokens' is the wrong objective: the tokens are already attended to (response->sensor attention is 0.56-0.98x uniform, not ~0) and their content already reaches the emission position (R2 0.685, donor null -0.001); what fails is that the output head does not USE it.

## Rationale
The premise 'the model ignores the sensor per the attention map' rests on response_to_sensor_attention 0.0013-0.0023 vs uniform_attention_baseline 0.2238. attention_map.py:245-246 states in-source that this key is a per-COLUMN mean (total/S, S=96); the comparable quantity is response_to_sensor_total = 0.1255-0.2194, i.e. 0.56-0.98x uniform. The probe also runs on num_samples=5. Independently, three causal facts contradict 'ignores': output_diff_rate_zero = 1.0000, monotonic_scale_response 0.84-0.89, and hr_peak decodable at the emission position at R2 0.685 with a donor-prefix null of -0.0014 -- information cannot be at the emission position if attention never moved it there. alpha=1.72 is the principled point: exp-e31 measured soft-token RMS 29.52 vs fed-to-stack embedding RMS 50.84, an effective ratio of 0.581, so 1.72x matches real token magnitude.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_peak_corr]] (none).
Predicts [[eiv_gen_peak_skill_bpm]] (none).
Predicts [[eiv_gen_between_sample_std]] (up).

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
