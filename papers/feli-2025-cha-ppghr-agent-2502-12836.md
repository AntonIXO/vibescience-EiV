---
arxiv_id_or_url: '2502.12836'
id: feli-2025-cha-ppghr-agent-2502-12836
title: 'An LLM-Powered Agent for Physiological Data Analysis: A Case Study on PPG-based
  Heart Rate Estimation (CHA-PPGHR / OpenCHA)'
topic_tags:
- optihealth
- readout
- sensor-text
---

## Key claims
- The LLM does NOT consume the waveform: an orchestrator (GPT-3.5-turbo on OpenCHA) calls a filter -> quality -> reconstruction -> peak-detection -> HR tool chain and narrates the result. Against ECG ground truth the agent reaches HR MAE ~2.8 bpm where GPT-4o given the data directly reads ~8.9 bpm (OVERVIEW.md figures).
- Direct evidence for the 'narrator' architecture: on a NUMERIC physiological target, an LLM reasoning over tool-extracted metrics beats an LLM reading the signal by ~3x. This is the published version of exp-t1/t1b's head-vs-LLM gap, with the same sign.

#optihealth #readout #sensor-text
