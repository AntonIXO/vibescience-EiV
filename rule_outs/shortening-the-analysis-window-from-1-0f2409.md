---
created_at: '2026-08-07T22:07:48+00:00'
former_ids:
- shortening-the-analysis-window-from-1-day-to-1-hour-or-2-6-12-hours-is-worth-doi
id: shortening-the-analysis-window-from-1-0f2409
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
retracted: true
retraction_reason: "RETRACTED 2026-08-08 BY THE AUTHOR, FOR THE SAME REASON THE DATASET-WHITELIST
  RULE_OUT WAS.\n\nIts own method line says it: \"Data-level re-test at 1-minute resolution,
  NO MODEL AND NO GPU\". I\nruled out a claim of the form \"X is worth doing to the
  pipeline\" using only data-level proxies, and\nnever trained a shortened-window
  model. That is the identical error I retracted the whitelist\nrule_out for, and
  consistency requires retracting this one too.\n\nWHAT SURVIVES, AND IT IS THE LARGER
  PART. The claim's STATED RATIONALE was itself a data-level\nassertion -- \"the daily
  HR peak becomes much easier to predict at short windows\" -- and against that\nrationale
  a data-level instrument is the right one. Independently re-derived from scratch,
  not\ncopied from the subagent:\n    W(min)   n windows   R2(peak <- window mean)
  \  median excursion\n     1440       6624        +0.0342                 58.74 bpm\n
  \     720      12295        +0.1884                 47.47\n      360      24431
  \       +0.4394                 34.50\n      120      72079        +0.6589                 20.68\n
  \      60     143418        +0.7495                 14.86\nSo 74.9% of a one-hour
  \"peak\" IS the one-hour mean, and the tachycardic base rate collapses\n32.1% ->
  2.5% at the corpus cut point. The apparent gain is triviality and the label is destroyed.\nThat
  much is measured and stands.\n\nWHAT DOES NOT SURVIVE, AND IS THE REASON FOR RETRACTION:
  \"worth doing\" is a claim about the trained\npipeline, and one specific untested
  confound is large enough to matter on its own. Shortening the\nwindow to 60 minutes
  takes the training pool from 6624 windows to 143418 -- a 21x increase in\nsamples
  for a contrastive objective that is known to be data-hungry and bistable on this
  corpus\n(the config records seeds landing in a good basin only ~33% of the time
  at some settings). More\nwindows could plausibly buy more than the label collapse
  costs, and nothing I measured speaks to\nthat trade. A shortened-window arm was
  never trained, never probed, and never scored on generation.\n\nRE-FILE NARROWLY
  IF WANTED: \"the peak-prediction RATIONALE for shortening the window is trivial
  and\nthe label collapses\" is measured and defensible. \"Shortening the window is
  not worth doing\" is not."
ruled_out_at: '2026-08-07T22:07:48+00:00'
topic_tags:
- epoch-resolution
- baseline
- control
---

## Claim
Shortening the analysis window from 1 day to 1 hour (or 2/6/12 hours) is worth doing at 1-minute epoch resolution because the daily HR peak becomes much easier to predict at short windows.

## Method
Data-level re-test at 1-minute resolution, no model and no GPU, on the rebuilt corpus; non-overlapping W-minute tiles over the STRICT clean pool (n=6624 days, 123 subjects), every R2 out-of-fold under GroupKFold(5) by subject, every gain priced in bpm against both always-predict-median and the predict-from-the-window-mean control. Scripts scratch/h3_window_length_1min.py; full tables in scratch/H2_H3_RESULTS.md and scratch/h2_h3_results.json.

## Evidence
Re-tested at 1-MINUTE resolution on the rebuilt corpus. Method: pure data-level, no model, no GPU.
STRICT clean pool n=6624 days / 123 subjects (no zero-bpm present step, day-mean>40, HR cov>0.5).
Non-overlapping W-minute tiles with window HR coverage>0.5. Scripts scratch/h3_window_length_1min.py,
results scratch/H2_H3_RESULTS.md + scratch/h2_h3_results.json. All probe R2 out-of-fold under
GroupKFold(5) BY SUBJECT.

W(min) | n windows | R2(peak<-window mean) CONTROL | resid SD bpm | median excursion | tachy>=146 | tachy>=120
 1440  |    6624   | 0.0225                        | 20.79        | 58.74 bpm        | 32.1%      | 76.1%
  720  |   12295   | 0.1809                        | 20.00        | 47.47            | 18.0%      | 53.9%
  360  |   24431   | 0.4312                        | 18.23        | 34.50            |  9.8%      | 33.5%
  120  |   72079   | 0.6556                        | 13.77        | 20.68            |  4.1%      | 15.5%
   60  |  143418   | 0.7475                        | 11.18        | 14.86            |  2.5%      |  9.7%

PRONG 1, PRICED IN bpm AGAINST THE TRIVIAL BASELINE. At W=1440, always-predict-median-peak has
MAE 17.02 bpm and predict-peak-from-the-window-mean has 16.99 - a gain of +0.03 bpm. At W=60 the
same pair is 17.33 vs 8.18 - a gain of +9.15 bpm. Every bit of that apparent 9.12 bpm improvement
is bought by LEVEL, not by peak detection: R2(peak<-mean) rises 0.0225 -> 0.7475, i.e. 74.8% of a
one-hour "peak" simply IS the one-hour mean. The peak-specific content collapses in step: residual
SD 20.79 -> 11.18 bpm (-46%), median excursion above the mean 58.74 -> 14.86 bpm (-75%). At W=5 the
pair is 3.96/2.86 - the peak has become the mean. The window gets easier and the mechanism of the
easiness is triviality.

PRONG 2, LABEL DESTRUCTION. Tachycardic base rate at the corpus-derived cut point 145.583:
32.1% -> 2.5%, a 12.8x collapse (the 15-minute-era record was 18.3% -> 1.4%, 13.1x - unchanged).
At the clinical 120 cut: 76.1% -> 9.7%. Four-bucket normalised entropy 0.690 -> 0.609 with 72.6%
of one-hour windows in a single class. Shortening converts a conditioning-collapse problem into a
class-imbalance problem and makes the entropy gate look better while meaning less.

WHAT THE 1-MINUTE GRID DID BUY (real, but does not rescue W=60): at EVERY W the 1-minute grid is
less level-redundant than the 15-minute grid on the same days (W=60: 0.7475 vs 0.9167; W=1440:
0.0225 vs 0.0644) and carries ~4x the tachycardic base rate. The ORDERING over W is unchanged and
the DAY window remains the best cell on both criteria. So the migration raises the ceiling
everywhere without altering the window verdict.

ONE NEW NUANCE, LOGGED AS A HYPOTHESIS AND NOT A RECOMMENDATION: four-bucket entropy is
non-monotone at 1 minute - it peaks at W=360 (0.832) above both the day (0.690) and the hour
(0.609). But buying that balance costs R2(peak<-mean) 0.0225 -> 0.4312, i.e. 43% of the resulting
label is a restatement of its own mean. That is a different and separately priceable proposal
from "shrink to one hour", which loses on both axes simultaneously.

CORRECTION TO A NUMBER I PROVIDED: I had quoted R2(hr_peak <- day mean) = 0.034 at cov>0.5; the
out-of-fold subject-grouped value is 0.0225. The difference is the split, not the data, and it
makes the day window look BETTER, not worse.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].

**RETRACTED:** RETRACTED 2026-08-08 BY THE AUTHOR, FOR THE SAME REASON THE DATASET-WHITELIST RULE_OUT WAS.

Its own method line says it: "Data-level re-test at 1-minute resolution, NO MODEL AND NO GPU". I
ruled out a claim of the form "X is worth doing to the pipeline" using only data-level proxies, and
never trained a shortened-window model. That is the identical error I retracted the whitelist
rule_out for, and consistency requires retracting this one too.

WHAT SURVIVES, AND IT IS THE LARGER PART. The claim's STATED RATIONALE was itself a data-level
assertion -- "the daily HR peak becomes much easier to predict at short windows" -- and against that
rationale a data-level instrument is the right one. Independently re-derived from scratch, not
copied from the subagent:
    W(min)   n windows   R2(peak <- window mean)   median excursion
     1440       6624        +0.0342                 58.74 bpm
      720      12295        +0.1884                 47.47
      360      24431        +0.4394                 34.50
      120      72079        +0.6589                 20.68
       60     143418        +0.7495                 14.86
So 74.9% of a one-hour "peak" IS the one-hour mean, and the tachycardic base rate collapses
32.1% -> 2.5% at the corpus cut point. The apparent gain is triviality and the label is destroyed.
That much is measured and stands.

WHAT DOES NOT SURVIVE, AND IS THE REASON FOR RETRACTION: "worth doing" is a claim about the trained
pipeline, and one specific untested confound is large enough to matter on its own. Shortening the
window to 60 minutes takes the training pool from 6624 windows to 143418 -- a 21x increase in
samples for a contrastive objective that is known to be data-hungry and bistable on this corpus
(the config records seeds landing in a good basin only ~33% of the time at some settings). More
windows could plausibly buy more than the label collapse costs, and nothing I measured speaks to
that trade. A shortened-window arm was never trained, never probed, and never scored on generation.

RE-FILE NARROWLY IF WANTED: "the peak-prediction RATIONALE for shortening the window is trivial and
the label collapses" is measured and defensible. "Shortening the window is not worth doing" is not.

#epoch-resolution #baseline #control
