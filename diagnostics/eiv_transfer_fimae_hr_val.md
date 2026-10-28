---
direction: lower_better
id: eiv_transfer_fimae_hr_val
name: Transfer HR validation failure-inclusive MAE
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: eiv_transfer_parse_rate
topic_tags: []
unit: bpm
---

## Description
On locked val rows: mean min(abs(pred-truth),100); nonfinite or unparseable pred costs100. No generated-row exclusions. Subject-clustered paired bootstrap accompanies comparisons. Never mix with historical valid-only MAE.

*Unit:* `bpm` — *lower_better*
