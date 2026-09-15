---
arxiv_id_or_url: '1511.03643'
id: lopez-paz-2015-generalized-distillation-1511-03643
title: Unifying distillation and privileged information
topic_tags: []
---

## Key claims
- Unifies Hinton-style distillation with Vapnik's learning-using-privileged-information into ONE framework, 'generalized distillation': the privileged teacher is trained on x* (the privileged representation), then its SOFT outputs supervise a student that only ever sees x. This is the method Track 3 would use if its pre-check passes.
- Authors: David Lopez-Paz, Leon Bottou, Bernhard Scholkopf, Vladimir Vapnik. stat.ML/cs.LG, published 2015-11-11. arXiv id VERIFIED against the arXiv API on 2026-09-01, not quoted from memory.
- The framework's benefit is explicitly conditional on the privileged representation being a useful INTERMEDIATE for the target -- which is exactly the premise the EiV EDA pre-check measures rather than assumes.
- Extends to unsupervised, semi-supervised and multitask settings; evidence is simulations plus small real-world datasets, NOT a wearable/physiological benchmark, so it licenses the METHOD here and no quantitative expectation.
