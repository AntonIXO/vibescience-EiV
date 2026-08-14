---
id: eiv-medgemma-last-attended-cache-v2
name: EiV fail-loud MedGemma final-attended-token cache v2
topic_tags:
- medgemma
- padding
- text-encoder
---

## Description
Require google/medgemma-1.5-4b-it with no FrozenHash fallback; pool the largest attention-mask-present hidden-state position so left/right padding are both correct; invalidate v1 caches; content-address tensor/mask/metadata inputs; precompute bf16 batch4 targets on RTX 5070 and freeze corpus hashes before training.

#optihealth-eiv #medgemma #cache-provenance #padding #text-encoder

#medgemma #padding #text-encoder

#medgemma #padding #text-encoder
