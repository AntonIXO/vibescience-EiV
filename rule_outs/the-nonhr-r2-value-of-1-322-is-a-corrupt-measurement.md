---
created_at: '2026-09-01T08:41:31+00:00'
former_ids: []
id: the-nonhr-r2-value-of-1-322-is-a-corrupt-measurement
problem_id: vault-hygiene-and-record-readability
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:41:31+00:00'
topic_tags: []
---

## Claim
The out-of-bound eiv_nonhr_channel_grouped_r2 values in exp-e13-nof1-calibration (before -1.676, after +1.322) are a corrupt or unexplained measurement of that diagnostic.

## Method
Parsed the experiment's YAML front matter for the recorded pair, then read its own Interpretation section, where the number appears verbatim with its true unit and confidence interval. Cross-read the diagnostic's registered description for its documented range. No GPU.

## Evidence
NOT CORRUPT -- THE VALUES ARE CORRECT AND WERE FILED UNDER THE WRONG DIAGNOSTIC ID. The experiment's own interpretation names them exactly:
  'hrv is NOT established at this data volume: skill +1.322 CI[0.179,2.483] (n_test 137)'
So 1.322 is an hrv SKILL IN BPM, not an R2. The paired 'before' of -1.676 is the same quantity's baseline. The diagnostic they were recorded against, eiv_nonhr_channel_grouped_r2, is a subject-grouped RidgeCV R2 whose own description pins the expected range as NEGATIVE (respiratory_rate -0.0757, sleep_prob_deep -0.1847 against hr_mean +0.9750).

HOW IT WAS FOUND: an admissibility sweep over all 610 recorded diagnostic values flagged 7 out of bound; 6 were my own bounds being wrong (a signed over_null quantity, and a cosine of exactly 1.0), leaving this one. Reading the experiment's Interpretation section resolved it in one step -- the number is quoted verbatim there with its unit and CI.

WHY IT MATTERS BEYOND TIDINESS: exp-e13 is a substantive result -- the campaign's FIRST supabase-side beat of the production emission path (hr_raw walk-forward OOS MAE 2.689 against trivials 4.33 and 5.455, skill +1.641 CI[1.049,2.235], corr 0.747). Its headline is sound. But its diagnostics_measured block attributes an hrv skill to a non-HR R2 diagnostic, so any future recall() or aggregate over eiv_nonhr_channel_grouped_r2 silently mixes two different quantities in two different units -- and this diagnostic carries only 5 evaluations, so a single wrong pair is 40% of its record.

CORRECT VALUE FOR THE RECORD: the experiment establishes hrv skill as NOT established at this data volume (the +1.322 concentrates entirely in fold 5 at +4.02, while folds 1-4 pool to +0.613 CI[-0.555,1.680], and fold 5 rails RidgeCV to the grid edge even on an extended 1e-6..1e6 grid). Quoting +1.322 as an achieved R2 -- or as an achieved skill -- would invert the experiment's actual finding.

## Links
Rules out for problem [[vault-hygiene-and-record-readability]].
