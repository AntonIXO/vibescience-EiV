---
created_at: '2026-09-05T00:10:34+00:00'
former_ids: []
id: soft-token-medgemma-lora-over-frozen-pulse-ppg-tokens-matches-an-attention-head-on-hr-raw
problem_id: is-the-frozen-llm-load-bearing-or-a-formatter
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-05T00:10:34+00:00'
topic_tags:
- readout
- control
- optihealth
---

## Claim
That the soft-token bridge's loss to a small head was a property of the custom 12-channel tower, and that MedGemma-1.5-4B + LoRA over the tokens of a pretrained, field-trained PPG foundation model (Pulse-PPG) would read hr_raw within the seed band (0.416 bpm) of an attention head given the identical frozen tokens and training rows.

## Method
exp-p3-llm-vs-head-over-pulse-ppg-tokens-20260905: identical frozen Pulse-PPG per-minute tokens (outputs/ppgfm/pulse_ppg), identical 159 train / 58 held-out PPG-carrying windows split by split_subjects.json. Head: t1b's ~2M AttnReadout over 96 x 15-min slots (scratch/p3_ppgfm_tokens.py). LLM: Phase C recipe (lora_proxy_12ch_ppgfm, sensor_tower: ppgfm, 2600 steps, projector/fusion/adapter/LoRA all trained, hr_augment off), greedy decode, scored on the same 58 rows (scratch/emission_diversity_audit.py --eval-bundle data/canonical --rows-from ...). Paired per-row bootstrap, 5000 resamples.

## Evidence
Head hr_raw MAE 4.338 (trivial 8.544, corr 0.858, scale 0.97; random-twin tower 5.334; mean-pool ridge 5.924). LLM 8.547 = the always-median constant (skill -0.003), corr 0.485, scale 0.76, 58/58 parsed, 18 distinct values. Paired LLM - head = +4.209 bpm, CI95 [+2.154, +6.275], head better on 65.5% of rows, ratio 1.97x. hr_peak: +0.83 [-3.45, +4.96], no difference. Third tower on which the soft-token path loses to a head (PL120 +3.38, PL60 +2.03, Pulse-PPG +4.21 bpm). One seed, 58 rows; the LLM twin arm was not bought because the trained-tower LLM already sits at chance. logs/p3/paired_llm_vs_head.json.

## Links
Rules out for problem [[is-the-frozen-llm-load-bearing-or-a-formatter]].

#readout #control #optihealth
