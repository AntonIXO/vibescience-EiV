---
arxiv_id_or_url: '2510.24058'
id: pulse-2025-privileged-eda-teacher-2510-24058
title: 'PULSE: Privileged Knowledge Transfer from Rich to Deployable Sensors for Embodied
  Multi-Sensory Learning'
topic_tags:
- optihealth
- wearables
- state-supervision
---

## Key claims
- Exactly EiV's LUPI setting: EDA as a frozen privileged TEACHER at train time, ECG/BVP/ACC/TEMP as deployable students. WESAD LOSO without EDA at inference: 0.994 AUROC (0.965 on STRESS), matching a model that keeps EDA at test time.
- Mechanism: each student splits into a shared (modality-invariant) and a private embedding; the shared subspace is aligned across students and matched to the teacher via multi-layer hidden-state + pooled-embedding distillation; the private path does self-supervised reconstruction and is what prevents collapse. Ablations cover hidden-state matching depth, shared/private capacity, hinge margin, fusion, modality dropout.
- This is representation-level distillation from a trained EDA encoder, NOT a per-patch regression head predicting the raw EDA value — the design NEXT.md Track 3 sketched. The head-regression design is the one Yang 2022 warns about; PULSE's is the one with a published LOSO result.

#optihealth #wearables #state-supervision
