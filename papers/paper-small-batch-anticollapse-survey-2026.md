---
arxiv_id_or_url: https://arxiv.org/abs/2405.18045
id: paper-small-batch-anticollapse-survey-2026
title: 'Small-batch anti-collapse for InfoNCE: DHEL/KCL, CLOP, VICReg-terms, RankMe
  diagnostics (Perplexity research synthesis 2026-08-30)'
topic_tags:
- contrastive
- effective-rank
- collapse
---

## Key claims
- InfoNCE mini-batch losses (InfoNCE/SimCLR/DCL) share optima only when batch M <= d+1; DHEL and kernel-contrastive losses (KCL) decouple alignment from uniformity and are batch-size-independent in expectation (ICML'24) -- the direct fix class for a 16-anchor regime.
- CLOP (orthonormal prototypes, 2403.18699) reports batch-32 performance matching SupCon at batch-2048; IConE holds RankMe/LiDAR stable from batch 1 to 64 by decoupling collapse prevention from batch statistics.
- Dimensional collapse has two mechanisms (Jing et al. 2110.09348): augmentation-dominated variance and implicit low-rank regularization -- more negatives alone does not fix the second; Mitrovic et al. show more negatives at fixed batch can HURT gradient SNR.
- RankMe (effective rank of embedding spectrum) predicts downstream performance without labels; our eff_rank probe is a participation-ratio variant, independently validated today by rank->C-health monotonicity (12.21/9.70/8.71/4.47 -> corr 0.78/0.31/0.13/collapse).
- Uniformity loss (Wang & Isola) is the exact term our shipped configs log (healthy -6.83 vs carrier -5.41): an explicit uniformity weight or VICReg variance-covariance term is the literature-backed guard if anchor count must ever shrink.

#contrastive #effective-rank #collapse
