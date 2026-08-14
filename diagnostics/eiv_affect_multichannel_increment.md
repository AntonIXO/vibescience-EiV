---
direction: higher_better
id: eiv_affect_multichannel_increment
name: Affect-label skill increment of multichannel daily features over rich HR-only,
  subject-grouped OOF
requires_control: null
topic_tags:
- cross-modal
- wearables
unit: skill_delta
---

## Description
Out-of-fold skill of a shallow model (RandomForest, GroupKFold by subject) predicting an affect label from daily per-channel summary features, MINUS the same model class on a RICH HR-only feature set (mean/std/p10/p25/p75/p90/min/max/iqr/frac-above-own-p75/coverage — never a strawman level-only baseline). Two cohorts, reported separately: lifesnaps TENSE/ANXIOUS binarized y>0 (AUC delta; 55 subjects; the only cohort where rr/temp/hrv exist) and pmdata stress ordinal 0-5 (Spearman delta; 16 subjects; only sleep+time can be added — hrv/rr/temp are 0% present there by construction). PRIMARY = lifesnaps AUC delta.

MUST be reported with: (a) the trivial baseline on the same pool (majority rate / always-median), (b) the ABSOLUTE multichannel skill, because a significant delta on top of chance-level absolute skill is engineering-irrelevant, (c) a WITHIN-SUBJECT label-permutation null of the DELTA (>=20 perms, same permuted labels applied to both arms; within-subject permutation preserves subject-level label marginals so between-subject trait signal stays in the null — what survives is day-level within-person signal, which is what the N-of-1 product actually serves), (d) per-family attribution (sleep / rr / temp / hrv / time families added to HR separately), because if one cheap family carries everything, the fusion architecture question dissolves.

This is a pure-CPU pre-training SUPPLY diagnostic on raw canonical features: it upper-bound-gates any encoder/fusion/caption change aimed at state detection BEFORE GPU is spent, in the same way eiv_peak_excursion_increment_r2 gates window/architecture changes.

*Unit:* `skill_delta` — *higher_better*

#cross-modal #wearables
