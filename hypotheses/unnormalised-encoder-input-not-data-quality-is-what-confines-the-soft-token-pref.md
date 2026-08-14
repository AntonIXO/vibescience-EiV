---
created_at: '2026-08-07T20:54:16+00:00'
gates: []
id: unnormalised-encoder-input-not-data-quality-is-what-confines-the-soft-token-pref
interventions:
- eiv-encoder-input-layernorm
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: ''
problem_id: canonical-merge-defaults-to-rolling-per-user-z-score-while-the-phase-c-target-builder-assumes-population-norm
problem_tags: []
status: proposed
supersedes: null
topic_tags: []
---

## Statement
Unnormalised encoder input, not data quality, is what confines the soft-token prefix to a single channel. MECHANISM: TemporalEncoder feeds raw [B,T,C] into FlexMLP, a bare Linear->GELU->Linear with no input normalisation (models/flexmlp.py:11-15); use_layernorm (encoders/temporal.py:35, its sole guard) is FALSE in every 12ch profile. A Linear at init weights all channels equally, so first-layer pre-activation variance splits by raw channel scale. MEASURED on the current corpus: hr_mean takes 96.07% of that variance on present steps and 99.77% as actually fed, vs hrv_rmssd 1.63%/0.06%, respiratory_rate 1.04%/0.01%, four sleep_prob_* 0.14%/0.00%, four time encodings 0.68%/0.12%. hr_mean std 16.88 vs next-largest 2.20 = 7.7x in std, 59x in variance. It widens because 45.7% of hr_mean cells are missing and filled with 0.0 RAW BPM = -4.18 SD of the present-step distribution, while presence is ALREADY supplied separately (with_presence_v1, input_dim=2*12=24), so that swing is redundant magnitude. Under population norm the same fill 0.0 IS the median -- verified, it sits at +0.00 SD for hrv_rmssd and skin_temperature today. This is a CONDITIONING claim, not expressivity: a Linear can represent a small weight on hr_mean, but dL/dW_c is proportional to x_c, so under one shared LR the hr_mean weights take ~8x-40x larger gradients from an init where the other eleven channels are numerically invisible. CONSISTENT (not yet causal): on data/eval_v2 with the 12ch_v2 Phase B checkpoint, same prefix/probe/samples, hr_mean subject-grouped R2 +0.9750 while respiratory_rate -0.0757 and sleep_prob_deep -0.1847 (both worse than their own mean). Two standing rule_outs are also what a one-scalar prefix would produce -- ro-sensor-slot-granularity-20260805 (23 slots -> 1 costs 0.009) and ro-window-shortening-20260805 (increment exactly 0.0000) -- both measured UNDER this input scaling, so conditional on it. I am NOT retracting either; the correspondence may be coincidence and that is what this tests. CONFOUND: respiratory_rate is only 11.6% present AND damaged by the rolling per-user z-score, so its negative R2 has two sufficient explanations. use_layernorm changes ONLY input scaling and leaves the corpus untouched, which is why it is the clean discriminator and is preferred over jumping to a re-merge. TEST: one-line config change, ~15 min on the 5070.

## Rationale


## Plan


## Links
Tests problem [[canonical-merge-defaults-to-rolling-per-user-z-score-while-the-phase-c-target-builder-assumes-population-norm]].
Applies [[eiv-encoder-input-layernorm]].
Predicts [[eiv_nonhr_channel_grouped_r2]] (up).
Predicts [[eiv_heldout_hr_r2]] (none).
