---
created_at: '2026-09-01T07:38:31+00:00'
former_ids: []
id: v36labels-caption-reaches-47-percent-of-training-rows
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:38:31+00:00'
topic_tags:
- optihealth
- baseline
---

## Claim
v36labels' caption change reaches ~47% of windows (561/1200 from its pre-flight), so roughly half the Phase-A training rows carry label clauses.

## Method
Re-ran _label_clauses over 4,000 sampled windows and grouped the reach by dataset, then intersected with the dataset_filter of the Phase A (v11full) and Phase C (band04) profiles actually used by the arms. No GPU.

## Evidence
SELF-AUDIT, and the number was inflated by sampling the wrong population. The 46.8% figure came from a pre-flight draw over the WHOLE 14,041-window corpus. But Phase A trains on the v11full roster and Phase C on the 6-dataset band04 roster, and the label-bearing datasets are badly unaligned with both.

Recomputed over 4,000 sampled windows (scratch/_label_reach_by_roster.py, CPU):
  whole corpus       2000/4000 = 50.0%   <- the population I quoted
  phase A (v11full)   953/2673 = 35.7%   <- what Phase A actually sees
  phase C (band04)    557/2141 = 26.0%   <- what Phase C actually sees

The gap is one dataset. dryad_ifh_affect is 78.5% labelled and contributes 1,022 of the corpus's 2,000 labelled windows -- and it is in NEITHER roster. nurse_stress (100% labelled) is also in neither. So over half the label supply the headline counted is invisible to every arm run in this campaign.

Within the rosters the supply is carried by very few corpora: pmdata 472/607 (77.8%) and the POPANE studies plus the small lab cohorts at ~100% but tiny n (popane_s7 64, s5 57, s6 48, s3 44, case 7, wesad 4). lifesnaps -- the largest corpus in both rosters -- is only 5.4% labelled (74/1373), and FIVE band04 datasets are at 0.0%: optihealth_supabase (the deployment cohort itself), big_ideas_glycemic, dreamt, wearable_exam_stress, popane_s4.

CONSEQUENCE FOR THE RECORD: v36labels' measured supabase win (hr_raw 1.862 vs incumbent 3.030) is real -- it was measured, not modelled -- but its carrier in Phase A is 35.7% of rows, not ~47%, and in Phase C 26.0%. Also note the deployment cohort carries NO labels at all, so whatever the label clauses teach must transfer to unlabelled windows to help there; it cannot be a direct label-to-slot shortcut on that draw.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].

#optihealth #baseline
