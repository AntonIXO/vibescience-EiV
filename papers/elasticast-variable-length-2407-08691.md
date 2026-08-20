---
arxiv_id_or_url: https://arxiv.org/abs/2407.08691
id: elasticast-variable-length-2407-08691
title: 'ElasticAST: An Audio Spectrogram Transformer for All Length and Resolutions'
topic_tags:
- extent-heterogeneity
- pooling
- padding
---

## Key claims
- Fixed-length transformers COLLAPSE when evaluated off their training length; training on native/variable lengths holds performance across lengths. Cleanest published cross-length transfer evidence found, and the exact failure shape expected from a 1440-fixed encoder evaluated on 41-minute CASE sessions.
- Replaces the CLS token with MASK ATTENTION POOLING so packed padding never enters the pooled representation -- the published form of the fix our variance-compression measurement calls for.
- Sequence packing of native-length inputs: pad ratio 31.1% -> 13.9-19.2%, cut ratio 15.2% -> 0% on VoxCeleb.
- Code public: github.com/JiuFengSC/ElasticAST.
- MODALITY CAVEAT: audio spectrograms, not wearable physiology. The mechanism transfers; the effect sizes do not. arXiv id verified 2026-08-20 (Interspeech 2024).

#extent-heterogeneity #pooling #padding
