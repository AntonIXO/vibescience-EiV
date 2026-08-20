---
id: eiv-extent-crop-augmentation
name: Random-extent crop augmentation of dense days to lab-session lengths
topic_tags:
- extent-heterogeneity
- wearables
---

## Description
During training, randomly crop a DENSE long window (lifesnaps/pmdata, c>0.75) to a contiguous span drawn from the empirical short-cohort extent distribution (27-224 measured minutes), zero-fill the remainder, and update M_mask accordingly, so the encoder sees the same subject at both extents and cannot hardcode a single expected coverage. Targets, captions and labels are rebuilt from the CROPPED span so supervision stays consistent with the input. This is the T1 rung of rb-dynamic-window-20260812 and the physiological analogue of ElasticAST/NaViT native-length training and ACL-ECG 80-100% random scale cropping.

#extent-heterogeneity #wearables
