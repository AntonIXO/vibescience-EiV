---
arxiv_id_or_url: '2510.02410'
id: opentslm-2025-time-series-language-models-2510-02410
title: 'OpenTSLM: Time-Series Language Models for Reasoning over Multivariate Medical
  Text- and Time-Series Data (Langer et al., 2025)'
topic_tags:
- wearables
- cross-modal
- medgemma
---

## Key claims
- Two bindings of time series into small pretrained LLMs (Llama-3.2-1B/3B, Gemma-3-270M/1B): SoftPrompt (tokens in the prefix) and Flamingo (gated cross-attention). Encoders either trained from scratch or Chronos-2 as a TSFM.
- HAR-CoT / Sleep-CoT / ECG-QA-CoT: TSLMs 65-70% F1 where fine-tuned text-only baselines read 60.44% (HAR) and 9.05% (Sleep); zero-shot text-only LLMs fail to follow the answer template (0 F1). A pretrained TSFM encoder (Chronos-2) did NOT dominate a from-scratch encoder (68.33 vs 69.88 sleep; 67.64 vs 65.44 HAR).
- Gemma soft-prompt models underperform because the soft prompt consumes Gemma's smaller context; Flamingo cross-attention scales better in memory as series count/length grow. Relevant to any MedGemma soft-token design over 1,440-minute days.
- No task-head (non-LLM) baseline over the same encoder is reported -- the LLM-vs-head ablation is absent here too. Code, data and models released.

#wearables #cross-modal #medgemma
