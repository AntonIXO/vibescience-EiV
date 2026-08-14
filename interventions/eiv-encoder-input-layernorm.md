---
id: eiv-encoder-input-layernorm
name: Enable use_layernorm on the temporal encoder input
topic_tags: []
---

## Description
Set use_layernorm: true in the Phase A profile. encoders/temporal.py:35 is its sole guard and it is the ONLY input normalisation available; it is currently false in every 12ch profile, so raw [B,T,C] goes straight into FlexMLP = Linear(input_dim->64)->GELU->Linear (models/flexmlp.py:11-15) with no scaling. One-line config change, ~15 min on a 5070, no re-merge required. Isolates the INPUT-SCALE question from the separate population-vs-rolling normalisation question.
