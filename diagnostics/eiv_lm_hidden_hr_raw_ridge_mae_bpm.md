---
direction: lower_better
id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
name: eiv_lm_hidden_hr_raw_ridge_mae_bpm
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: eiv_dense128_hr_raw_mae_bpm
topic_tags:
- optihealth
- readout
- medgemma
unit: bpm
---

## Description
MAE (bpm) of a RidgeCV probe predicting the window's masked-mean hr_raw from the FROZEN LM's final-layer residual-stream hidden state, read at the exact token position that emits the first hr_raw digit under teacher forcing, on the SAME 128 held-out rows as eiv_dense128_hr_raw_mae_bpm and the t1/t1b token-head arms (subject-grouped folds inside the 128). E36's instrument (scratch/probe_e36_hr_raw_stages.py STAGE_3), which read 1.4806 on medgemma_12ch_e33.seed_1 on 2026-08-06 against a donor-prefix null of 6.98 and always-median 7.59. It is the MIDDLE rung of the HR tower and exists to split the LLM's loss into 'lost in the forward pass' (tokens -> hidden) and 'lost at emission' (hidden -> emitted digits). MANDATORY reporting beside it: (a) the attention-head MAE over the projector tokens on the same rows (the rung above), (b) the adapter's own emitted MAE (the rung below), (c) always-predict-median, (d) the donor-prefix and zeroed-prefix nulls. A RidgeCV scalar readout is NOT in lm_head's function class and OVERSTATES what the digit head can emit (exp-e25/e26 lesson) -- this number is an information bound, never a claim about what the model can say.

*Unit:* `bpm` — *lower_better*

#optihealth #readout #medgemma
