---
arxiv_id_or_url: https://arxiv.org/abs/2506.05321
id: lsm2-aim-incomplete-wearable-2506-05321
title: 'LSM-2: Learning from Incomplete Wearable Sensor Data'
topic_tags:
- extent-heterogeneity
- wearables
- padding
- pooling
---

## Key claims
- Learnable MASK TOKENS represent both inherited (real) and artificial missingness, so the model never substitutes an imputed value for absent data (Adaptive and Inherited Masking, AIM). This is the published alternative to our median-fill contraction.
- Pre-trained on 40M hours of DAY-LONG multimodal wearable sensor data -- the same window shape as our [1440,12] tensor, so the substrate matches ours more closely than any other wearable foundation model.
- Maintains performance under TARGETED missingness (e.g. nighttime-only biosignals for hypertension), the direct analogue of our short-lab-session regime.
- Avoids explicit imputation by construction; our own present-mean imputation counterfactual was already measured DEAD (supabase MAE 16.72 -> 16.56 bpm), consistent with this paper's premise.
- CAVEAT: no public weights or training code, and NO cross-LENGTH transfer table (train 24h -> test 36min). It motivates the masking mechanism, not a drop-in. Verified via arxiv MCP 2026-08-20: title/authors/date confirmed, 25 authors incl. Xu, Narayanswamy, Spathis, Liu, McDuff.

#extent-heterogeneity #wearables #padding #pooling
