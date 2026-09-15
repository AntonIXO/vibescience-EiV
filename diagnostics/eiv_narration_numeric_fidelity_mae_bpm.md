---
direction: lower_better
id: eiv_narration_numeric_fidelity_mae_bpm
name: eiv_narration_numeric_fidelity_mae_bpm
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: eiv_dense128_hr_raw_mae_bpm
topic_tags:
- optihealth
- sensor-text
- medgemma
- target-style
unit: bpm
---

## Description
For the NARRATOR architecture (numeric head + shallow state classifier -> numbers and state placed in the prompt -> LLM writes the day's description): MAE in bpm between the hr_raw value the scorer extracts from the generated narration and the hr_raw value the narrator was GIVEN in its prompt, on the same 128 split-honest rows as every tower number. It measures whether the LLM repeats what it was told, not whether it read the sensors; a perfect narrator reads 0.0. MANDATORY beside it: (a) the head's own MAE against truth on those rows (the number the product would actually ship), (b) the rate of narrations from which no number can be extracted, (c) the state-clause agreement with the label against a label-permutation null. The point of registering it: SensorFM (arXiv:2605.22759) and CHA-PPGHR (arXiv:2502.12836) both ship this architecture and neither reports numeric fidelity of the narration -- EiV should.

*Unit:* `bpm` — *lower_better*

#optihealth #sensor-text #medgemma #target-style
