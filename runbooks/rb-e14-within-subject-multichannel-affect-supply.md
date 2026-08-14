---
created_at: '2026-08-14T12:58:43+00:00'
former_ids: []
gates: []
hypothesis_id: hyp-e14-within-subject-affect-supply-20260814
id: rb-e14-within-subject-multichannel-affect-supply
notes: []
priority: 3
problem_id: affect-state-multichannel-supply-20260814
status: done
topic_tags:
- cross-modal
- wearables
- optihealth
- n-of-1
updated_at: '2026-08-14T19:25:35+00:00'
---

## Prompt
E14: WITHIN-SUBJECT multichannel affect supply probe (Anton's steer, N-of-1 framing).

CONTEXT (self-contained): Repo /root/optiHealth-EiV. E5 (2026-08-14, logs/e5_affect_supply_probe.json) refuted CROSS-subject multichannel affect supply: lifesnaps tense/anxious AUC ~0.48-0.52 chance in every family, multichannel delta -0.028, perm p=0.84; pmdata sleep delta +0.004. That rule-out is subject-grouped OOF only — deployment is N-of-1 (all 223 supabase eval rows are one subject), and E13 proved within-user heads beat everything cross-subject built. Anton's standing hypothesis: sleep/rr/temp channels 'probably will improve states detection'. NOTE: no spo2 channel exists in the 12-ch canonical schema (schema/pipeline work, separate track).

STEP 0 — HONEST POWER ANALYSIS FIRST (CPU, minutes, blocking): per-subject label supply on lifesnaps (55 subjects, 76% exact-0.0 tense) and pmdata (16 subjects, ordinal 0-5 p50=3 std 0.71): n labeled days per subject, within-subject label variance, minimum detectable AUC/Spearman delta at that n (permutation-based). If no subject cohort supports detecting delta >= 0.05, record supply-infeasible and STOP — that is a useful negative, do not fish.

STEP 1 (only if powered): within-subject walk-forward (expanding folds, min_train per power analysis) per subject, rich-HR-only vs HR+family (sleep, temp, rr, hrv, time) RF + linear, pooled across subjects with within-subject permutation null (>=50 perms). Trivials: label median-forward and day-persistence.

GATES to preregister at run time: g1 BLOCKING pooled within-subject delta (multichannel - rich-HR) > 0 with perm p < 0.05; g2 BLOCKING absolute within-subject skill beats BOTH trivials CI-positive on the pooled folds; g3 informational per-family attribution (name the winning family — if one family carries it, the lever is captioning THAT channel, not generic fusion).

METHOD invariants: features from raw canonical tensor (data/canonical), M_mask-aware, coverage-law respected; supabase state slot itself stays ruled out (logs/state_label_probe_v9band04.json) — this probes benchmark labels as a proxy for the class of within-subject state signal only.

## Preconditions
h4b caption-dispersion arm (priority-1 queued runbook under the 1-min-migration problem) has been run or explicitly deprioritized by Anton. E5's CROSS-subject rule-out stands (logs/e5_affect_supply_probe.json: all AUCs ~chance, delta -0.028, perm p=0.84) — this arm does NOT re-litigate cross-subject.

## Links
Queued for problem [[affect-state-multichannel-supply-20260814]].
Run as [[hyp-e14-within-subject-affect-supply-20260814]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#cross-modal #wearables #optihealth #n-of-1
