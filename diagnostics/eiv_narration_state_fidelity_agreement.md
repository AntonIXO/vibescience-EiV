---
direction: higher_better
id: eiv_narration_state_fidelity_agreement
name: eiv_narration_state_fidelity_agreement
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags:
- optihealth
- readout
unit: fraction of rows
---

## Description
Narrator state fidelity: the fraction of rows on which the state the LLM narrates (stress vs flow, parsed from free text) agrees with the decision of the classifier whose probability was placed in its prompt (threshold 0.5). The state analogue of eiv_narration_numeric_fidelity_mae_bpm: a perfect narrator reads 1.0 regardless of whether the classifier is right, because the narrator's job is to carry the head's answer, not to improve it. Report beside the narrated-state AUC vs truth per device (eiv_state_auc_over_null_lab / _freeliving) so that a narrator that quietly overrides the head is visible.

*Unit:* `fraction of rows` — *higher_better*

#optihealth #readout
