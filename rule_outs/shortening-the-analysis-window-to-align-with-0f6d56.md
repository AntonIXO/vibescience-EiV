---
created_at: '2026-08-14T21:34:08+00:00'
former_ids: []
id: shortening-the-analysis-window-to-align-with-0f6d56
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-14T21:34:08+00:00'
topic_tags: []
---

## Claim
Shortening the analysis window to align with momentary EMA pings (peri-ping windows of 15-240 minutes before the ping, instead of the 24h civil-day window) recovers cross-subject momentary affect/state detection from HR. Covers: sub-daily window arms whose success gate is state/affect accuracy; peri-ping HR aggregation as the fix for the E5/E14 affect null; and any claim that the daily window's label-dilution (77.6% within-window disagreement) is the binding constraint on cross-subject state detection.

## Method
exp-e16-peri-ema-window-20260815 (scratch/e16_peri_ema_window.py + scratch/e16_perm_null.py -> logs/e16_peri_ema_window_sweep.json, logs/e16_perm_null.json). Corpus git_sha e849eb7, tensor [11318,1440,12], 102982 label events, on the 2026-08-14 rebuild that first placed EMA on its recorded epoch.

EMA momentariness VERIFIED IN THE LANDED DATA before any modelling (scratch/ema_time_reality_check.py), because the whole arm depends on it: epoch mod60==0 on 0.019 of lifesnaps mood events vs 0.0167 chance (not an hourly broadcast); top single epoch carries 0.004 of events (not a hardcoded stamp); minute-of-hour histogram flat (min 55, max 101, mean 73.6, std 11.0); hour-of-day peaks at 10/15/20h = the SEMA3 ping schedule. pmdata EXCLUDED as a pre-commitment: 07-09h clustered, top-epoch share 0.060 -> a retrospective morning diary, not a momentary ping.

DESIGN. Per window W in {15,30,60,120,240,480,1440} minutes, three arms on the IDENTICAL subject-grouped split: (i) clock-only [sin, cos, minute-of-day] = the CONFOUND FLOOR, (ii) HR/HRV stats over [ping-W, ping) strictly causal (mean/std/p10/p25/p50/p75/p90/min/max/IQR/coverage/n, OLS slope, last-minus-first, last value, hrv mean/std/coverage), (iii) both. W=1440 reproduces the E5 daily-summary regime as the incumbent anchor. GroupKFold(5) by subject_id (cross-subject), RF(300, min_samples_leaf=5, seed 0) headline plus LogisticRegression linear check, NaNs median-imputed within the training fold only. Trivials computed on the same pool: majority rate AND the within-subject constant predictor (each subject's own positive rate). Null: 24 within-subject label permutations on the primary delta, identical permuted labels applied to both arms so between-subject trait signal stays inside the null.

THREE TARGETS: lifesnaps TENSE/ANXIOUS (n=4418, 61 subjects, pos 0.124 — the E5 target, now momentary), lifesnaps TIRED (pos 0.224), dryad stress VAS above that subject's own median (n=3926, 22 subjects).

## Evidence
REFUTED IN THE PREDICTED DIRECTION AND BURIED BY A TRIVIAL BASELINE. exp-e16-peri-ema-window-20260815, ~6 CPU minutes, no GPU.

1. THE SHAPE IS BACKWARDS. lifesnaps TENSE/ANXIOUS, delta of [HR-before-ping + clock] over [clock-only], by window W in minutes:
     W=15   -0.0064   <- WORSE than the clock alone
     W=30   +0.0005
     W=60   +0.0046
     W=120  +0.0167   <- best sub-daily, still UNDER the 0.028 MDE
     W=240  +0.0030
     W=480  +0.0217
     W=1440 +0.0228   <- MAXIMUM at the incumbent full-day window
   The preregistered prediction was an interior maximum in [30,240] exceeding the W=1440 delta. Observed the opposite: the delta rises with W and peaks at 24h. lifesnaps TIRED agrees (max +0.0340 at W=480, not sub-daily). The dilution mechanism — that a 1440-minute average washes out a minutes-wide autonomic response — is measured false on this corpus.

2. THE CEILING IS TRAIT DOMINANCE, NOT ALIGNMENT. The within-subject constant predictor (each subject's own positive rate; ZERO physiology, ZERO clock) scores OOF AUC 0.7381 on TENSE/ANXIOUS and 0.6731 on TIRED. The best physiological arm anywhere in the sweep, any W, any composition, is 0.5818. The model sits 0.156 AUC BELOW a per-user constant. Cross-subject affect variance is dominated by WHO the subject is, so no window length can fix it — this is a between-subject design problem, not a timescale problem.

3. BOTH BLOCKING GATES FAIL. g1 (delta >= +0.03 at W<=240 with perm p<0.05): best +0.0167, under the preregistered MDE 0.028 -> POWERED NULL, not a near-miss. The permutation null at the largest-delta arm (W=1440, +0.0228) gives perm p=0.083 over 24 within-subject permutations (null mean -0.0151, std 0.0249). g2 (absolute AUC >= 0.58 AND above the within-subject constant): 0.5818 vs 0.7381 — fails on the trivial comparison by a wide margin.

4. THE SUPPLY ARGUMENT IS REAL BUT DOES NOT CONVERT. Sub-daily windows genuinely fix label supply: within-window label disagreement falls 0.776 (W=1440) -> 0.000 (W<=60); usable labelled windows rise 6775 (W=1440) -> 10412 (W=360); four labelled cohorts (wearable_exam_stress, wesad, neuro_stress_resilience_hci, nurse_stress) go from 0 usable windows at 24h to 242/63/41/105 at W=30. None of that converts into cross-subject affect skill. Cleaner supervision on a target whose variance is between-subject buys nothing.

WHAT IS **NOT** RULED OUT, and must not be read as covered:
  (a) N-of-1 / within-subject state heads with a within-subject baseline. The 0.7381 constant is exactly the trait signal a per-user model absorbs by construction, so the cross-subject ceiling says nothing about the within-user regime. E14 already found within-subject affect skill EXISTS in rich HR-only features (lifesnaps hr_only AUC 0.6133, CI-positive over both trivials).
  (b) Sub-daily windows for NON-state objectives — channel reconstruction, hrv/rr/temp slots, event detection. This rule-out is scoped to state/affect gates only.
  (c) The clock-controlled increment itself, which is genuinely new: with hour-of-day controlled, HR clears a within-subject permutation null on two targets (TIRED +0.0340 at W=480 p=0.000; dryad stress-above-own-median +0.0998 at W=15 p=0.000). Note the dryad arm survives on only 534 of 3926 pings (the 13.6% with dense peri-ping HR) and collapses to +0.0015 at W=60 on n=1331 — a coverage SELECTION effect, not a timescale effect. Do not cite the +0.0998 as a short-window result.
  (d) Multi-scale / hierarchical patching as an ARCHITECTURE question for the encoder, which was never tested here.

METHOD NOTE. The clock-only control is what makes this readable: hour-of-day alone scores 0.5354-0.5722 depending on W, so an uncontrolled peri-ping arm would have posted ~0.57 AUC and been misread as state detection when it is circadian bookkeeping. Any future affect arm on this corpus must report the clock-only control AND the within-subject constant AUC in the same table as its headline.

INSTRUMENT DEFECT found and fixed mid-run: scratch/e16_peri_ema_window.py parsed N_PERM and never used it — the sweep shipped without the permutation null it declared. Closed by scratch/e16_perm_null.py.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].
