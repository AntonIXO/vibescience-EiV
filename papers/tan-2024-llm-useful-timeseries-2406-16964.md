---
arxiv_id_or_url: '2406.16964'
id: tan-2024-llm-useful-timeseries-2406-16964
title: Are Language Models Actually Useful for Time Series Forecasting?
topic_tags: []
---

## Key claims
- Tan, Merrill, Gupta, Althoff, Hartvigsen. NeurIPS 2024, arXiv:2406.16964v2, published 2024-06-22. Ablating three published LLM-for-time-series forecasters (OneFitsAll/GPT4TS, Time-LLM, CALF): removing the LLM or replacing it with a basic attention layer does not degrade forecasting -- 'in most cases, the results even improve'.
- THE ABLATION TAXONOMY, read from section 3.2 rather than from the abstract, because the EiV replication needs the exact design: (a) `w/o LLM` -- remove the language model entirely and pass the input tokens straight to the reference method's final layer; (b) `LLM2Attn` -- replace it with a SINGLE randomly-initialised multi-head attention layer; (c) `LLM2Trsf` -- replace it with a single randomly-initialised transformer block.
- CRITICAL DESIGN DETAIL, and the one place the EiV experiment deliberately deviates: 'In the above ablations, we keep left parts of the forecasters unchanged (TRAINABLE).' Tan lets the surrounding encoder adapt to the new consumer. exp-t1-tan-ablation-heads-20260901 instead holds the tower FROZEN, because the EiV reference system itself freezes it (band04 sets freeze_encoder: True and neither train_projector nor train_log_scale) -- so freezing is what makes the two consumers see identical inputs. The EiV arms therefore map as: ridge mean-pool ~ `w/o LLM`, cross-attention head ~ `LLM2Attn`. `LLM2Trsf` is NOT run there, and the reason it is not needed is internal: ridge and attention land 0.040 bpm apart, so added consumer capacity is already shown not to move the number.
- Also claims, beyond the headline: pretrained LLMs do no better than models trained from scratch; they do not represent sequential dependencies in time series; they do not help in few-shot settings; and patching + attention encoders perform similarly to LLM-based forecasters. The last of these is the one that bears on EiV's random-init-tower result.
- SCOPE LIMIT for citing this in EiV: Tan's task is multivariate FORECASTING on ETT/Illness/Weather/Traffic. EiV's hr_raw is a READOUT of the masked mean of an input channel over the same window -- reconstruction, not forecasting. The ablation logic transfers; the benchmark numbers do not.
