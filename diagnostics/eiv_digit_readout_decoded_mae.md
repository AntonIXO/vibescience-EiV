---
direction: lower_better
id: eiv_digit_readout_decoded_mae
name: eiv_digit_readout_decoded_mae
requires_control: null
topic_tags:
- cross-modal
- medgemma
- collapse
unit: bpm
---

## Description
Free-running decoded MAE (bpm) of hr_peak when the emission is produced by the BEST LINEAR readout of the frozen final hidden state operating in lm_head's ACTUAL function class: a per-digit-position multinomial classifier over {'0'..'9','.',END}, argmax, with each predicted digit fed back through the model via KV cache. This is the correct capacity ceiling for any LoRA on lm_head of rank>=10 (full-rank over the digit rows). MUST NOT be confused with a scalar-regression readout: RidgeCV mapping h -> a real number is NOT in lm_head's function class and overstates the ceiling (this error was made in exp-e25 and corrected in exp-e26). ALWAYS report with (a) always-predict-median MAE and a bootstrap CI on the difference, (b) the teacher-forced arm separately labelled OPTIMISTIC since it uses an oracle prefix the model never has at generation time, (c) a donor-prefix null, and (d) per-position accuracy against each position's OWN majority-class baseline -- the leading digit is ~89% '1' on this population and looks accurate while carrying no numeric information.

*Unit:* `bpm` — *lower_better*

#cross-modal #medgemma #collapse
