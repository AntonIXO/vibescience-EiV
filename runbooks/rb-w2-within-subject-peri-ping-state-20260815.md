---
created_at: '2026-08-14T21:35:40+00:00'
former_ids: []
gates:
- g1_median_subject_skill_over_strongest_trivial
- g2_fraction_subjects_positive
- g3_within_subject_W_profile_shape
hypothesis_id: null
id: rb-w2-within-subject-peri-ping-state-20260815
notes: []
priority: 10
problem_id: peri-ema-window-destroys-momentary-labels-20260815
status: queued
topic_tags:
- wearables
- n-of-1
- baseline
- control
updated_at: '2026-08-14T21:35:40+00:00'
---

## Prompt
W2 — WITHIN-SUBJECT (N-of-1) peri-ping state detection with walk-forward splits. Pure CPU, no GPU, no model weights. Repo /root/optiHealth-EiV.

WHY THIS ARM EXISTS (context for a fresh session with no memory of the prior chat).
The project is optiHealth-EiV / SensorLM: a temporal encoder over wearable physiology emits soft-token prefixes for MedGemma. Canonical tensor is [11318 days, 1440 minutes, 12 channels], one window per civil day anchored to local midnight, git_sha e849eb7.

On 2026-08-14 commit a7fc419 placed EMA ground truth on the epoch it was actually recorded in, making the labels genuinely MOMENTARY for the first time (verified in the landed data: epoch mod60==0 on 0.019 of lifesnaps mood events vs 0.0167 chance; top-epoch share 0.004; flat minute-of-hour histogram; hour-of-day peaks at 10/15/20h = the SEMA3 ping schedule).

Experiment exp-e16-peri-ema-window-20260815 then swept peri-ping window lengths CROSS-SUBJECT and REFUTED the alignment hypothesis. Rule-out id `shortening-the-analysis-window-to-align-with-0f6d56`. The decisive numbers, which you must not re-derive or contradict without new evidence:
  - delta of [HR-before-ping + clock] over [clock-only], lifesnaps TENSE/ANXIOUS, by W in minutes:
      15 -0.0064 | 30 +0.0005 | 60 +0.0046 | 120 +0.0167 | 240 +0.0030 | 480 +0.0217 | 1440 +0.0228
    i.e. RISING with W, maximal at the full day — the opposite of the predicted sub-daily optimum.
  - THE CEILING IS TRAIT DOMINANCE: the within-subject constant predictor (each subject's own positive rate, ZERO physiology, ZERO clock) scores OOF AUC 0.7381 on TENSE/ANXIOUS and 0.6731 on TIRED, while the best physiological arm at ANY window is 0.5818. The model is 0.156 AUC BELOW a per-user constant.

THAT IS THE PREMISE FOR THIS ARM. Cross-subject affect variance is dominated by WHO the subject is. In the N-of-1 deployment regime that trait constant is absorbed by construction and is unavailable as a shortcut — so the cross-subject ceiling does NOT bound the within-subject question. E14 (exp id in vault) already found within-subject affect skill EXISTS on DAILY features: lifesnaps hr_only AUC 0.6133, CI-positive over both trivials (median-forward +0.0733 [0.045,0.103]; persistence +0.1200 [0.084,0.158]), and its closing note states sub-daily/temporal features remain UNTESTED and need a new hypothesis. This is that hypothesis.

WHAT TO BUILD.
POPULATION: per-subject models on the lifesnaps subjects E14 certified as powered (26 of 55, >=~30 labelled days) plus the 22 dryad_ifh_affect subjects. NEVER pool subjects into one fit. One model per subject; aggregate as a DISTRIBUTION over subjects with a sign test, never a single pooled AUC.
EXCLUDE pmdata as a pre-commitment: its label epochs cluster at 07-09h with top-epoch share 0.060 — a retrospective morning diary, not a momentary ping.

SPLIT: forward-chaining by TIME within each subject (expanding window), never random K-fold. The deployment question is 'predict now from this user's history'; a random split inside one timeline leaks future into past. Mirror the E13 per-user head discipline already validated in this repo (walk-forward RidgeCV on the user's own features).

ARMS, per W in {30, 120, 480, 1440} minutes:
  t0 within-subject constant  = that user's own running positive rate      <- TRIVIAL 1
  t1 persistence              = previous ping's / yesterday's label        <- TRIVIAL 2
  t2 clock-only               = hour-of-day sin/cos + minute-of-day        <- CONFOUND FLOOR
  t3 peri-ping HR/HRV over [ping-W, ping), strictly causal
  t4 t3 + clock
Reuse hr_features() from scratch/e16_peri_ema_window.py so the feature definition is identical to the cross-subject run.

PRIMARY METRIC: t4 minus max(t0, t1, t2) — skill over the STRONGEST trivial, never over chance. Report per-subject distribution, fraction of subjects with positive skill, and a paired bootstrap CI on the median. Always quote the three numbers together (arm, strongest trivial, n).

PREREGISTER BEFORE RUNNING (propose_hypothesis with predicted_effects + gates, then start_experiment):
  g1 BLOCKING: median per-subject skill over strongest trivial >= +0.05 AUC, bootstrap CI excluding 0.
  g2 BLOCKING: fraction of subjects with positive skill >= 0.60, sign-test p < 0.05.
  g3 INFO: the within-subject W-profile differs in SHAPE from the E16 cross-subject profile (which rose monotonically with W). Commit a direction for this before looking — the honest possibility is that long windows helped cross-subject only because daily-rhythm structure is trait-like across people and constant within a person, in which case the within-subject profile should FLATTEN or invert.

WHY SWEEPING W AGAIN IS NOT A BANNED RETRY: rule-out `shortening-the-analysis-window-to-align-with-0f6d56` is explicitly scoped to CROSS-SUBJECT affect and its own scope note reserves '(a) N-of-1 / within-subject state heads with a within-subject baseline' as NOT ruled out. Cite that scope note in the hypothesis so the distinction is on the record.

MANDATORY REPORTING RULE inherited from E16: every affect result on this corpus must show the clock-only control AND the trivial baselines in the SAME table as the headline. An arm that beats chance but loses to a per-user constant is not state detection.

DELIVERABLE IF IT PASSES: the PerUserHeadStore state head — the surviving product lever named in the E14 closure. IF IT FAILS: state detection is dead on this corpus in BOTH grouping regimes, and the recommendation is to drop the `state` slot from the emitted schema rather than ship noise. Record that verdict as a rule-out either way; a powered null here is a genuinely useful result.

NOTE ON DEPLOYMENT SUPPLY: optihealth_supabase currently carries ZERO state labels, so even a passing result cannot ship until EMA collection starts there (~50-60 labelled days needed). Say so plainly in the closure rather than implying the feature is immediately shippable.

## Preconditions
Corpus at data/canonical with git_sha e849eb7 (tensor [11318,1440,12], label_events.parquet 102982 rows). Read shape, epoch_minutes and git_sha out of merge_manifest.json rather than assuming — this corpus has been rebuilt several times. Reusable feature builder already exists: hr_features() in scratch/e16_peri_ema_window.py (strictly causal, present-only, denormalised to bpm via population_stats.json median/IQR). No GPU needed.

## Links
Queued for problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Intended gates: `g1_median_subject_skill_over_strongest_trivial`, `g2_fraction_subjects_positive`, `g3_within_subject_W_profile_shape`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#wearables #n-of-1 #baseline #control
