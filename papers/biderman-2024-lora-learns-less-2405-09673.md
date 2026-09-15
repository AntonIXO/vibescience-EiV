---
arxiv_id_or_url: '2405.09673'
id: biderman-2024-lora-learns-less-2405-09673
title: LoRA Learns Less and Forgets Less
topic_tags:
- optihealth
- cross-modal
---

## Key claims
- In standard low-rank settings LoRA substantially underperforms full fine-tuning on the target domain (code, math), in both instruction-tuning (~100k pairs) and continued-pretraining (20B tokens) regimes; it forgets less and keeps generations more diverse.
- Full fine-tuning learns weight perturbations of rank 10–100x a typical LoRA rank — a rank-32 adapter is structurally unable to absorb a genuinely new input distribution the way full FT does. Licenses Track 6 (full FT of a ~1B LM) as the honest test of whether the LM can learn physiology, but only after the interface is shown to carry the signal.

#optihealth #cross-modal
