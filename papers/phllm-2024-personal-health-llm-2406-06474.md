---
arxiv_id_or_url: '2406.06474'
id: phllm-2024-personal-health-llm-2406-06474
title: Towards a Personal Health Large Language Model (PH-LLM, Google)
topic_tags:
- optihealth
- sensor-text
- medgemma
---

## Key claims
- Gemini fine-tuned on TEXTUAL summaries of wearable data (aggregated sleep/activity/physiology metrics) for insights, exam questions and self-reported sleep-outcome prediction; the wearable data enters as text or as a multimodal encoding, never as a raw minute-level stream.
- For predicting self-reported sleep outcomes, the text-only path underperforms and 'multimodal encoding is REQUIRED to match performance of specialized discriminative models' -- i.e. even with encoded sensor input the LLM only MATCHES a discriminative head, it does not beat it. No case where the LLM path exceeds the head.
- Positioning for EiV: PH-LLM is the narrator layer over derived metrics; SensorFM (2605.22759) later formalises this as FM -> task heads -> agent, with the LLM-reads-raw-data condition as the baseline it beats.

#optihealth #sensor-text #medgemma
