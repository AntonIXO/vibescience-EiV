---
arxiv_id_or_url: '2512.06533'
id: chen-2025-rl-decoding-regression-2512-06533
title: 'Beyond Token-level Supervision: Unlocking the Potential of Decoding-based
  Regression via Reinforcement Learning'
topic_tags:
- optihealth
- readout
- target-style
---

## Key claims
- Token-level cross-entropy on digit strings does not see the global magnitude of the number; a sequence-level reward on |y_hat - y| optimised with ReMax/GRPO beats both token-level decoding baselines and traditional regression heads on tabular and code-metric regression.
- Implication for a frozen-LLM+LoRA that emits numbers: the loss, not the decoder, is what is misaligned with MAE. A numeric-reward stage on the emitted slot is the remedy the literature offers when the decoding path is kept.

#optihealth #readout #target-style
