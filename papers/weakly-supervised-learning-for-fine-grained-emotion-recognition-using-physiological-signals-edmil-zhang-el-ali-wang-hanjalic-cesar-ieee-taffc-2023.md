---
arxiv_id_or_url: https://www.computer.org/csdl/journal/ta/2023/03/09732650/1BD8BVTEwG4
id: weakly-supervised-learning-for-fine-grained-emotion-recognition-using-physiological-signals-edmil-zhang-el-ali-wang-hanjalic-cesar-ieee-taffc-2023
title: Weakly-Supervised Learning for Fine-Grained Emotion Recognition Using Physiological
  Signals (EDMIL; Zhang, El Ali, Wang, Hanjalic, Cesar, IEEE TAFFC 2023)
topic_tags:
- state-supervision
- wearables
---

## Key claims
- Deep Multiple Instance Learning (EDMIL) recognizes fine-grained valence-arousal from ONLY post-stimulus (whole-trial) labels by identifying which 1-2s instances carry the bag label; subject-independent 3-class CASE V/A 75.6%/79.7%, MERCA 70.5%/67.6%.
- Weak MIL supervision reduced overfitting from temporal mismatch vs fully-supervised fine labels - temporally MISALIGNED dense hard labels can hurt more than weak bags.
- 1-2s instances best for EDA/EEG-scale signals; HRV-stability requires longer instances.
- Relevant to A1: our per-patch dist targets are the middle ground - real per-epoch labels scattered to patches, not inherited whole-window argmax.

#state-supervision #wearables
