---
created_at: '2026-07-27T21:03:32+00:00'
former_ids: []
id: optihealth-contrastive-loss-20260727
paper_refs: []
problem_tags:
- contrastive-loss
- subject-heldout
- representation-learning
- optihealth-eiv
status: open
title: Which cross-modal objective best preserves raw heart-rate information in OptiHealth
  EiV?
topic_tags:
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
updated_at: '2026-07-27T21:03:32+00:00'
updates: []
---

## Description
Compare the current DCL + uniformity + auxiliary-DCL Phase A package against RBF-HSIC dependence regularization, Soft CCA, and VICReg on frozen raw 12-channel tensors. Use an experiment-local correction for HR==0 values incorrectly marked present, deterministic dataset-stratified subject splits, validation-only pilot selection, and eight-seed subject-held-out confirmation on the local RTX 5070. Population stats are disabled because HR is schema-excluded and merge-time fitting before the test split would leak.

#contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #representation-learning #optihealth-eiv
