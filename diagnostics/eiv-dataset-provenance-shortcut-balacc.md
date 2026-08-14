---
direction: lower_better
id: eiv-dataset-provenance-shortcut-balacc
name: eiv_dataset_provenance_shortcut_balacc
requires_control: null
topic_tags: []
unit: accuracy
---

## Description
Balanced accuracy of a subject-grouped, out-of-fold RandomForest classifier predicting DATASET IDENTITY from STRUCTURAL features only — per-channel presence coverage (12 channels) plus per-channel contiguity (fraction of present cells whose left neighbour is present) for hr/hrv/resp/temp. NO physiological VALUES enter the feature matrix, only presence geometry.

WHY THIS EXISTS. Phase A optimises InfoNCE, which is free to discriminate samples by ANY feature that separates them. `encoder_format_version: with_presence_v1` concatenates the presence flags as INPUT CHANNELS, handing the model the sampling-rate fingerprint explicitly. If dataset provenance is near-perfectly decodable from that fingerprint, the contrastive objective can satisfy itself on provenance and never touch physiology — an upstream shortcut no downstream fix can undo.

MUST be reported with (a) the chance rate 1/n_classes on the SAME pool, because a 3-class problem and an 8-class problem are not comparable, and (b) the class composition, because a bal-acc dominated by two 8-sample singleton classes is an artifact of the grouped split rather than a property of the allowlist. Prefer comparing allowlists at their bal-acc MINUS chance, and state both numbers.

MEASURED 2026-08-08 on the rebuilt corpus (10338 samples, scratch/h_dataset_mechanism.py), min_hr_coverage 0.05 gate:
  INCUMBENT Phase A/B 10-dataset list : bal-acc 0.9945, chance 0.1250, 8 classes, n=5907
  REPAIRED 5-dataset list             : bal-acc 0.7941, chance 0.2000, 5 classes, n=6051
  REPAIRED + dreamt (6)               : bal-acc 0.8252, chance 0.1667, 6 classes, n=6151
  INCUMBENT Phase C 3-dataset list    : bal-acc 0.6592, chance 0.3333, 3 classes, n=5927
This is a pure-CPU pre-training diagnostic: it can gate an allowlist change before any GPU time is spent.

*Unit:* `accuracy` — *lower_better*
