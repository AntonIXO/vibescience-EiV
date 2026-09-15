---
arxiv_id_or_url: '2602.14744'
id: qiu-2026-rethinking-llm-tsf-2602-14744
title: Rethinking the Role of LLMs in Time Series Forecasting (Qiu, Tong, Sun, Ma,
  Zhang, Shen)
topic_tags:
- optihealth
- readout
- deployment-distribution
---

## Key claims
- Direct rebuttal of Tan 2024 at scale: 8B observations, 17 scenarios, 4 horizons; claims LLM4TSF DOES improve, with the largest gains in CROSS-DOMAIN generalisation and under distribution shift.
- Pre-alignment (align modality before task training) beats post-alignment in >90% of tasks -- matches EiV's Phase A->B->C ordering.
- Pretraining matters under distribution shift; architecture matters for complex dynamics; under large mixed distributions an intact LLM is claimed indispensable (token-level routing analysis).
- Code: github.com/EIT-NLP/LLM4TSF. Modality: general TS, not physiological. UNVERIFIED beyond abstract -- the two papers disagree, and EiV's 21-corpus -> 1-subject-OOD setting is closer to the regime this one claims wins, so the ablation from Tan must actually be RUN here rather than assumed either way.

#optihealth #readout #deployment-distribution
