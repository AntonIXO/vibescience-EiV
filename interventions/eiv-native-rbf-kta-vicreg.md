---
id: eiv-native-rbf-kta-vicreg
name: Native duplicate-aware RBF-KTA plus exact variance/covariance
topic_tags:
- kernel
- vicreg
- cross-modal
- anti-collapse
---

## Description
Remove learned loss-only projections. On L2-normalized native 2560D sensor/text embeddings, minimize duplicate-aware cross-view RBF kernel-target-alignment loss plus a single weighted exact native-space variance and covariance penalty computed without materializing a 2560x2560 covariance matrix.

#kernel #vicreg #cross-modal #anti-collapse
