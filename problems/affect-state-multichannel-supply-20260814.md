---
created_at: '2026-08-14T07:47:01+00:00'
id: affect-state-multichannel-supply-20260814
paper_refs: []
problem_tags:
- subject-heldout
- representation-learning
status: parked
title: 'state/affect detection: non-HR channels (sleep, rr, skin_temp, hrv) are unread
  by the encoder AND their supply-side label information is unmeasured'
topic_tags:
- cross-modal
- wearables
- optihealth
updated_at: '2026-08-14T19:25:48+00:00'
---

## Description
[2026-08-14 UPDATE — BOTH REGIMES NOW TESTED, LEVER RULED OUT; PARKED]
E5 (cross-subject, subject-grouped OOF): multichannel delta -0.028, perm p=0.84, all AUCs ~chance. E14 (within-subject walk-forward, powered null — MDE ~0.028 < 0.05 bar): lifesnaps delta +0.0003 perm p=0.65, linear check negative (-0.025); pmdata delta +0.0286 p=0.098 AND multichannel CI-worse than day-persistence trivial. The trait-dominance escape hatch is closed: multichannel daily-summary features add no affect information over rich HR-only in EITHER grouping regime. DO-NOT-RETRY multichannel daily-summary affect supply.
POSITIVE residue (E14): within-subject affect skill EXISTS in rich-HR features alone — lifesnaps hr_only AUC 0.6133, CI-positive over both trivials (median-forward +0.0733 [0.045,0.103], persistence +0.1200 [0.084,0.158]). The surviving product lever is a rich-HR-only within-user state head (PerUserHeadStore) once supabase EMA labels accrue (~50-60 days), NOT channel fusion. Sub-daily/temporal features remain UNTESTED (daily summaries only) — would need a new hypothesis.
Parked: no open hypothesis; unblocks only on supabase EMA label supply or a sub-daily-features prereg.

[ORIGINAL 2026-08-14] Anton's steer: sleep/rr/temp channels 'probably will improve states detection' for the supabase N-of-1 deployment. E5 ruled this out cross-subject; deployment is N-of-1, so the within-subject regime (E13's winning regime for numeric slots) remained the untested residual. Question: for a FIXED subject, do multichannel daily features carry affect-label information that rich HR-only does not?

#cross-modal #wearables #optihealth #subject-heldout #representation-learning
