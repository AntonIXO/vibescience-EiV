---
created_at: '2026-08-14T07:53:33+00:00'
id: affect-multichannel-supply-dead-20260814
problem_id: affect-state-multichannel-supply-20260814
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-14T07:53:33+00:00'
topic_tags:
- cross-modal
- wearables
- baseline
- optihealth
---

## Claim
Adding the non-HR channels (sleep probabilities, skin_temperature, respiratory_rate, hrv) to the model's inputs will improve state/affect detection — or more broadly, that ANY daily-summary feature set from the 12 canonical channels can discriminate affect labels across subjects on the available labeled cohorts. This covers: multichannel fusion arms for the state slot, caption enrichment with sleep/rr/temp facts FOR AFFECT, channel-dropout or multiplicative-interaction arms whose success gate is state accuracy, and re-running this same probe with a deeper model (shallow-at-chance bounds deep on daily summaries).

## Method
exp-e5-affect-supply-20260814 (scratch/e5_affect_supply_probe.py → logs/e5_affect_supply_probe.json). RF(400)+logistic linear check, GroupKFold(5) by subject, present-only masked daily features (rich HR set incl. dispersion; sleep stage minutes/fractions/efficiency/fragmentation; rr/temp/hrv stats; season), lifesnaps TENSE/ANXIOUS y>0 (n=1780, 55 subj, pos 0.223) and pmdata stress (n=1549, 16 subj). Within-subject label-permutation null (24 perms) on the delta, per permutation-null-for-weak-signals discipline. 151s CPU.

## Evidence
hr_only RF AUC 0.4816 / linear 0.5329 (chance 0.50); multichannel 0.4538; BEST arm of any composition hr+time 0.5183; primary delta -0.0278 with within-subject perm p=0.84 (24 perms, null std 0.0271). Per-family: hr+sleep 0.4621, hr+temp 0.4808, hr+rr 0.5161, hr+hrv 0.4925. pmdata: hr_only Spearman 0.1244, +sleep+time delta +0.0036, p=0.84 (null mean +0.0244 exceeds the observed delta). Consistent with the prior bounding fact corr(label, emitted hr) <= 0.09. NOT covered by this rule-out: (a) N-of-1 per-subject models with within-subject baselines (the actual product regime — untestable here: max 55 subjects, ~32 labeled days each, between-subject design), (b) deployment-side labels once optihealth_supabase rows stop being labels:{}, (c) sub-daily/event-level affect (the labels are daily self-reports; nothing sub-daily is testable), (d) channel-value slots (hrv/rr/temp reporting) — encoder work there is a separate question with its own supply already established.

## Links
Rules out for problem [[affect-state-multichannel-supply-20260814]].

#cross-modal #wearables #baseline #optihealth
