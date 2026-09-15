---
arxiv_id_or_url: '2603.03331'
id: pulselm-2026-ppg-text-benchmark-2603-03331
title: 'PulseLM: A Foundation Dataset and Benchmark for PPG-Text Learning (Pham et
  al., 2026)'
topic_tags:
- wearables
- cross-modal
---

## Key claims
- 1,079,562 standardised 10-s PPG segments from 16 public sources (incl. WESAD stress 2,998 segs, PPG-DaLiA HR 39,216, WildPPG 240k) harmonised into 12 closed-ended QA tasks, 2.48M QA pairs; data on Hugging Face, code on GitHub.
- Architecture = frozen pretrained PPG encoder (PaPaGei @125 Hz or Pulse-PPG @50 Hz, 512-d) -> linear projection -> ONE prefix token -> instruction-tuned LLM (Qwen3-4B, Qwen2.5-7B, Llama-3.2-3B, Llama-3.1-8B) with LoRA; greedy decode, exact-match accuracy.
- In-domain avg EM accuracy: Blind (question only) 56.7; Deaf (encoder + linear, no question) 45.5 PaPaGei / 42.6 PulsePPG; Fusion (encoder + BERT-question, linear) 60.2 / 63.9; LLM rows 66.4-72.8. Best = Qwen2.5-7B + PulsePPG 72.8. WESAD-stress column: Blind 18.3, Deaf 0.0 / 1.7, Fusion 23.3 / 41.5, LLM 39.5-49.7.
- CAVEAT for EiV: the Deaf baseline predicts over a SHARED answer vocabulary across all 12 tasks without seeing the question, so it is task-blind (hence ~0 on stress). PulseLM therefore does NOT contain a fair per-task frozen-probe-vs-LLM ablation on the same embedding; the fair comparator is Fusion, and the LLM beats it by ~6-9 points avg (stress 41.5 -> 49.7 with PulsePPG).
- PulsePPG beats PaPaGei in every LLM row and on in-the-wild sources; the authors attribute it to field pretraining with motion. RR, stress and SDB are the weak tasks: 'weaker or more indirect physiological signatures within 10-second PPG segments'.

#wearables #cross-modal
