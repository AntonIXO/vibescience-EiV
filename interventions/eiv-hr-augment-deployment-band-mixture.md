---
id: eiv-hr-augment-deployment-band-mixture
name: hr-augment deployment-band mixture + sub-45 tail deletion
topic_tags:
- deployment-distribution
- optihealth
---

## Description
Reshape the hr_raw augmentation TARGET LAW toward the deployment distribution (_sample_hr_augment_peak, src/training/datasets.py, commit 14e2ee4). Three coupled knobs: aug_min_mean_bpm=45.0 (delete the sub-45 poison tail entirely — a region deployment truth never occupies but which held P=0.0719 of targets and seeded per-seed attractors at 38.9/31.1/31.3/31.6 bpm); band_prob=0.5 with band_mean_range=(45,65) (mixture component drawing the target MEAN uniformly from the deployment band, peak-space-truncated, no atom); hr_augment_prob 0.4->0.8 (declared part of the intervention — needed to lift in-band supply from 0.204 to 0.551 against demand 0.829 given the natural corpus is only 0.18 in-band). Defaults leave all pre-existing profiles byte-identical.

#deployment-distribution #optihealth
