---
created_at: '2026-08-14T07:47:01+00:00'
id: affect-state-multichannel-supply-20260814
paper_refs: []
problem_tags:
- subject-heldout
- representation-learning
status: open
title: 'state/affect detection: non-HR channels (sleep, rr, skin_temp, hrv) are unread
  by the encoder AND their supply-side label information is unmeasured'
topic_tags:
- cross-modal
- wearables
- optihealth
updated_at: '2026-08-14T07:47:01+00:00'
---

## Description
Anton's steer (2026-08-14): sleep/rr/spo2 channels currently contribute zero accuracy but 'probably will improve states detection'. Standing facts that frame this:
1. state slot RULED OUT as a target-law axis (logs/state_label_probe_v9band04.json): emitted state carries no label info raw or residual (all p>=0.29), and corr(label, emitted hr) <= 0.09 kills the entire class of levers operating through HR level. The rule-out explicitly names 'richer channels' as the surviving lever — but that was never measured.
2. eiv_nonhr_channel_grouped_r2 NEGATIVE (respiratory_rate -0.0757, sleep_prob_deep -0.1847 vs hr_mean +0.9750; 12ch_v2 Phase B, 2026-08-07): the current prefix does not carry non-HR channels.
3. E4a liveness audit 2026-08-14 (scratch/e4_channel_liveness_audit.py -> logs/e4_channel_liveness_audit.json, current data/canonical 10344x1440x12): the sleep 4-channel block is ALIVE in this build (daily prob-sum p50/p95/max = 1.0) — the 2026-08-07 'probability sum 0.027, ~37x too small' record is STALE, repaired by the rebuild. Coverage on the two labeled cohorts:
   - pmdata stress (n=1549 days, 16 subjects, ordinal 0-5, p50=3, std 0.71): ONLY hr + sleep + time exist; hrv/rr/temp days_any = 0.0000 (collinearity pre-flight P1: rr/temp/hrv untestable on pmdata BY CONSTRUCTION).
   - lifesnaps TENSE/ANXIOUS (n=1780 days, 55 subjects, 76% exact-0.0): skin_temp 95.1% days, 94.9% at >=50% cov (dense!); rr 66.6% days any (1.1% dense — nightly value); hrv 67.1% days any; sleep 96.9% days.
4. There is NO spo2 channel in the 12-ch canonical schema. Adding one is data-pipeline work (schema + supabase/processor support), out of scope for this problem; flagged to Anton.
THE OPEN QUESTION (supply side, decides everything downstream): do the non-HR channels carry affect-label information BEYOND a rich HR-only feature set, on subject-grouped held-out folds? If NO — the multichannel-fusion program for state is dead at the data level and no encoder work can save it (cross-modal transfer moves only information that is actually in the channels). If YES — the justified lever is encoder/objective repair so the prefix carries what the data provably does (Phase A caption enrichment with sleep/rr/temp facts, channel-dropout augment, or multiplicative fusion), NOT target-law reshaping.

#cross-modal #wearables #optihealth #subject-heldout #representation-learning
