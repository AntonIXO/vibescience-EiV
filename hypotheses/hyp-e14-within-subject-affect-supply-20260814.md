---
created_at: '2026-08-14T17:12:03+00:00'
gates:
- blocking: true
  description: 'g1 BLOCKING: pooled within-subject delta (multichannel minus rich-HR-only),
    computed over walk-forward OOS folds pooled across powered subjects, > 0 with
    within-subject permutation p < 0.05 (>=50 perms, same permuted labels applied
    to both arms).'
  id: g1-pooled-delta-perm
- blocking: true
  description: 'g2 BLOCKING: absolute within-subject multichannel skill beats BOTH
    trivial baselines (train-label median-forward AND day-persistence) with bootstrap
    CI95 excluding zero on the pooled OOS folds. A significant delta on top of chance-level
    absolute skill fails this gate.'
  id: g2-absolute-skill-vs-trivials
- blocking: false
  description: 'g3 informational: per-family attribution (sleep / rr / temp / hrv
    / time added to rich-HR separately). Name the winning family; if one family carries
    the entire delta, the engineering lever is captioning THAT channel, not generic
    fusion.'
  id: g3-family-attribution
- blocking: false
  description: 'g0 informational (power precondition): per-subject minimum detectable
    delta from the census; subjects entering the pool must individually support detection
    (lifesnaps: n>=30 labeled days, pos_rate in [0.15,0.85], label_std>0; pmdata:
    n>=60, >=3 distinct labels). If the pooled powered cohort cannot detect delta
    >= 0.05, record supply-infeasible and stop.'
  id: g0-power-precondition
id: hyp-e14-within-subject-affect-supply-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'Hypothesis (Anton''s steer) predicts pooled within-subject multichannel-minus-rich-HR
    delta > 0 (lifesnaps AUC delta primary), perm p<0.05. Honest prior from the record:
    E5 measured delta -0.028 (p=0.84) CROSS-subject, and trait-dominance was the leading
    explanation for the null - within-subject removes trait variance, so a positive
    delta is genuinely plausible here; but zero-inflated EMA labels (76% exact-0.0)
    may leave nothing to detect. Either outcome is decision-relevant: a positive names
    the family to caption; a clean null closes the multichannel-affect lever entirely
    (both cross- and within-subject) and leaves EMA collection as the only path.'
problem_id: affect-state-multichannel-supply-20260814
problem_tags:
- subject-heldout
- representation-learning
status: inconclusive
supersedes: null
topic_tags:
- wearables
- n-of-1
- cross-modal
- optihealth
---

## Statement
E14 (within-subject affect supply): for a FIXED subject, daily multichannel features (sleep, rr, temp, hrv added to rich HR) carry affect-label information that rich HR-only features do not - i.e. the E5 cross-subject null was trait-dominance masking within-person signal, not absence of signal. Tested as walk-forward within-subject prediction on the powered census cohorts (26 lifesnaps subjects for TENSE/ANXIOUS binarized, 14 pmdata subjects for stress ordinal), pooled across subjects, against trivial baselines and a within-subject permutation null.

## Rationale
E5's cross-subject rule-out explicitly does NOT cover within-subject detection: GroupKFold-by-subject scoring pools between-person trait variance that swamps within-person state shifts (the same transfer wall that killed cross-subject numeric heads in E11 and that within-user heads cured in E13: hr_raw 2.689 OOS beating the emission path). Deployment is N-of-1 (all 223 supabase rows are one subject). If day-level affect signal exists in sleep/rr/temp/hrv for a FIXED person, a within-subject walk-forward - the exact regime the product runs in - is the instrument that can see it. Census (commit a2323af) shows 26 lifesnaps subjects (best 63 labeled days, pos_rate 0.33, min detectable AUC@95 0.628) and 14 pmdata subjects (up to 147 days, r_crit_95 0.137) are individually powered. Constraint honored: this does NOT re-litigate cross-subject; the supabase state slot itself stays ruled out (logs/state_label_probe_v9band04.json).

## Plan
E14 (rb-e14-within-subject-multichannel-affect-supply). Step 0: power screen from logs/affect_label_supply_census.json (26 lifesnaps + 14 pmdata usable-within subjects). Step 1: per-subject WALK-FORWARD (expanding, chronological by start_time, min_train 20 lifesnaps / 40 pmdata) with rich-HR-only vs HR+family vs multichannel feature sets reusing E5's day_features extractor (present-only masked stats, sleep stage minutes/fractions/fragmentation, rr/temp/hrv stats). RF(400) + linear check. Pool OOS predictions across subjects; score lifesnaps AUC (tense>0), pmdata Spearman (ordinal). Trivials: train-median-forward and day-persistence. Permutation null of the pooled DELTA: within-subject label permutation, >=50 perms, identical permuted labels in both arms. CPU only, systemd-run scoped. Script scratch/e14_within_subject_affect.py -> logs/e14_within_subject_affect.json.

## Links
Tests problem [[affect-state-multichannel-supply-20260814]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1-pooled-delta-perm` (blocking): g1 BLOCKING: pooled within-subject delta (multichannel minus rich-HR-only), computed over walk-forward OOS folds pooled across powered subjects, > 0 with within-subject permutation p < 0.05 (>=50 perms, same permuted labels applied to both arms).
Gate `g2-absolute-skill-vs-trivials` (blocking): g2 BLOCKING: absolute within-subject multichannel skill beats BOTH trivial baselines (train-label median-forward AND day-persistence) with bootstrap CI95 excluding zero on the pooled OOS folds. A significant delta on top of chance-level absolute skill fails this gate.
Gate `g3-family-attribution` (advisory): g3 informational: per-family attribution (sleep / rr / temp / hrv / time added to rich-HR separately). Name the winning family; if one family carries the entire delta, the engineering lever is captioning THAT channel, not generic fusion.
Gate `g0-power-precondition` (advisory): g0 informational (power precondition): per-subject minimum detectable delta from the census; subjects entering the pool must individually support detection (lifesnaps: n>=30 labeled days, pos_rate in [0.15,0.85], label_std>0; pmdata: n>=60, >=3 distinct labels). If the pooled powered cohort cannot detect delta >= 0.05, record supply-infeasible and stop.

#wearables #n-of-1 #cross-modal #optihealth #subject-heldout #representation-learning
