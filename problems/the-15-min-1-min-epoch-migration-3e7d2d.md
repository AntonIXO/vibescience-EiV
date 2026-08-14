---
created_at: '2026-08-07T21:45:07+00:00'
former_ids:
- the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid
id: the-15-min-1-min-epoch-migration-3e7d2d
paper_refs: []
problem_tags: []
status: open
title: The 15-min -> 1-min epoch migration recovers a large, genuinely incremental
  hr_peak signal, and silently invalidates the dataset allowlist and coverage gates
  tuned for the 15-min grid
topic_tags:
- epoch-resolution
- dataset-whitelist
- wearables
- baseline
updated_at: '2026-08-08T08:57:24+00:00'
updates: []
---

## Description
=== UPDATE 2026-08-09. TWO CLAIMS FROM THE PHASE-C PLAN CHECKED BEFORE SPENDING GPU. ONE REFUTED,
ONE CONFIRMED. Unpreregistered pre-flight measurement; problem evidence, no calibration weight. ===

--- 1. THE "A/B-vs-C COHORT SPLIT" DOES NOT EXIST ON THE *_wl CHAIN. H-2 AS WRITTEN IS REFUTED ---
The plan asserted subject counts collapse 240 -> 270 -> 89 across Phase A -> B -> C, i.e. the encoder
is aligned on a cohort the LLM is then grounded on a subset of. Measured on the live corpus
[10344,1440,12] through the production SensorTextPairDataset with each profile's own filters
(scratch/h2_preflight_cohorts.py):

  PHASE A  phase_a_12ch_1min_wl   n=6151  subjects=216
  PHASE B  phase_b_12ch_wl        n=5621  subjects=215
  PHASE C  lora_proxy_12ch_wl     n=6151  subjects=216
  subjects in A but NOT in C: 0
  subjects in C but NOT in A: 0

The cohorts are the same population. Phase B loses ONE subject and 530 days purely to its
min_coverage=0.25 gate; A and C are identical. The 240/270/89 figures came from the OLD
3-entry Phase-C filter {lifesnaps, pmdata, optihealth_supabase}, which the *_wl chain replaced --
the v4w/v5c/v5d arms all already use the 6-dataset filter in ALL THREE phases.
CONSEQUENCE: do not run H-2 as specified. There is no variable to manipulate. Any future
"cohort unification" arm must first show a cohort difference EXISTS on the chain it targets.
METHOD NOTE: this is the third plan premise in two days that was stale because the corpus or the
profiles moved under it. Re-measure a premise on the live artifacts before building an arm on it;
this one cost one CPU minute and saved ~4 GPU-hours.

--- 2. min_coverage=0.25 IS CONFIRMED NEAR-VACUOUS, AND THE MECHANISM IS THE TIME ENCODINGS ---
min_coverage is applied to ALL-CHANNEL coverage. Four of twelve channels (hour_of_day_sin/cos,
day_of_week_sin/cos) are synthetic and always present, so coverage has a hard floor of
4/12 = 0.3333 -- ABOVE the 0.25 threshold. Measured on 1500 sampled days:
  all-channel coverage: min 0.1586, p1 0.3208, median 0.5257, max 0.7818
  fraction passing >0.25   : 0.9900
  fraction passing >0.3333 : 0.9873
  days below the structural floor: 0.0127
So the gate rejects ~1% of days, and nearly all of those are days that fall below the synthetic
floor (i.e. a merge artifact), not days chosen for poor sensor coverage. It is doing almost no work.
NOTE this is exactly what the Phase A/C -> Phase B difference above is made of: 530 of 6151 days.

--- 3. min_hr_coverage=0.05 IS MIS-SCALED FOR THE 1-MINUTE GRID, CONFIRMED ---
The in-config justification is "lifesnaps at hr_cov~0.15 (15-min sampling)". On the 1-minute grid
lifesnaps' median hr coverage is 0.9854. Per-dataset medians now:
  lifesnaps 0.9854 | pmdata 0.9635 | mmash 0.9719 | big_ideas_glycemic 0.9576
  optihealth_supabase 0.6979 | dryad_ifh_affect 0.3694 | dreamt 0.3632
  neuro_stress_resilience_hci 0.0222 | wesad 0.0792
A 0.05 gate admits days with as few as 72 usable minutes of 1440 and passes 99.4% of the corpus.
The gate that was tuned to keep 15-minute lifesnaps now keeps essentially everything.
NOT YET TESTED: whether tightening it changes anything downstream. That is a real arm; the cohort
arm is not.

#optihealth-eiv #data-contract-violation

=== UPDATE 2026-08-09. THE CORPUS IS [10344, 1440, 12], NOT 10338 OR 13909 -- AND IT MOVED UNDER A
RUNNING A/B. UNPREREGISTERED: problem evidence, no calibration weight. ===

--- 1. LIVE CORPUS STATE, MEASURED ---
data/canonical/X_canonical.npy is [10344, 1440, 12], mtime 2026-08-08 16:36:05, epoch_minutes 1,
window_timesteps 1440, normalization.mode population, stats_sha256 6f5de9c1b55b.
Zero-sensor windows: 0. Corpus hr_mean coverage 0.6754, hrv_rmssd 0.2136.
Per-dataset (n / hr_cov / hrv_cov / hr density / hr held%):
  lifesnaps        3613 / 0.923 / 0.214 / 0.994 / 0.0%
  dryad_ifh_affect 4100 / 0.362 / 0.309 / 0.402 / 63.5%
  pmdata           2045 / 0.908 / 0.000 / 0.973 / 0.0%
  optihealth_supabase 269 / 0.713 / 0.303 / 0.800 / 0.0%
  big_ideas_glycemic  102 / 0.863 / 0.610 / 0.967 / 0.0%
  dreamt            100 / 0.368 / 0.287 / 1.000 / 0.0%
  mmash              22 / 0.954 / 0.898 / 0.982 / 0.0%
  six others (8-35 rows each), hr_cov <= 0.157 -- SHORT RECORDINGS, not merge defects.

--- 2. THE PROVENANCE GAP ---
merge_manifest.json records git_sha a85d085d40ec7a880942c5052323cf8332143b6e. That object DOES NOT
RESOLVE in this repo, even after git fetch --all (the pre-rebuild archive records 012ce08996, which
does resolve). The live corpus cannot be reproduced from any committed tree.

--- 3. THE CORPUS WAS REPLACED WHILE AN A/B WAS RUNNING ---
X_canonical.npy mtime 16:36:05 falls INSIDE the v4c arm's Phase A window (15:40:10 -> 16:45:15, from
logs/hdataset_ab.log stage banners). Whether v4c Phase A read the old or the new tensor is not
recoverable from the log, so that A/B may have had TWO variables rather than one, and its refutation of
the dataset-allowlist lever is correspondingly weaker than recorded.
STANDING CHECK, cheap: stat the tensor and diff its mtime against the run's stage banners before
trusting any controlled comparison.

--- 4. THE 1-MINUTE GRID ALSO MOVED THE LABEL AXIS, WHICH MOVED THE BASELINE ---
hr_label_bucket's cut points are corpus-derived quantiles (q0.80 / q0.45 of the daily peak) cached
beside the tensor. On the live corpus they are 157.72 / 127.00; the A/B logged 143 / 109 at every stage.
143/109 is p66.3 / p19.0 of the live peak distribution. Because the same quantiles define both the LABEL
and its MAJORITY BASELINE, the identical v4w emission (hr_acc 0.6016 = 77/128) reads +0.0176 skill at
143/109, +0.1523 at 157.7/127, and -0.1491 against the clinical 120/100. This is the migration's
"silently invalidates the gates tuned for the 15-min grid" thesis, instantiated on the metric itself
rather than on the dataset filters. Cut points are now PINNED into the adapter manifest (commit 84b145f).

--- 5. THE DRYAD BROADCAST DEFECT IS FIXED, AND THE PEAK IS REAL ---
The anchor fix (4a59ca5) landed. Measured (scratch/plan_audit_dryad_tradeoff_20260808.py):
  modal HR inter-sample gap 1 minute, on 98.5% of gaps
  0/600 dryad days have a peak cell that is itself HELD
  median(peak | all present) == median(peak | unheld only) == 124.97 bpm, to 2 dp
  the same identity holds for lifesnaps, pmdata and optihealth_supabase
So dryad's hr_peak label is a MEASURED EXCURSION, not a broadcast artefact -- although 63.5% of its
present HR cells are still carried, so min_hr_coverage currently counts carried values as coverage and
that decision should be made explicitly rather than inherited.


The canonical tensor migrated from 15-minute to 1-minute epochs (commits 0f0c84f, 012ce08;
tensor [13909, 1440, 12], git_sha 012ce08). N=23 patches and the 2-hour token span are held FIXED
across the migration (patch_len 8->120, stride 4->60), so the ONLY variable is that each soft token
now sees 120 real minutes instead of 8 pre-averaged 15-minute means: a 15x higher within-patch
compression ratio over 15x more information.

=== MEASURED AT THE DATA LEVEL, 2026-08-07, BEFORE ANY RETRAIN (unpreregistered; problem evidence,
no calibration weight). Method: ONE corpus, one sample set; the 15-minute arm is produced by
block-averaging the 1-minute arm exactly as the old merger did, so corpus/cohort/filter are held
identical and only the epoch grid varies. Scripts: scratch/audit_1min_resolution.py,
scratch/audit_1min_peak_robustness.py, scratch/audit_1min_composition.py, scratch/audit_hrcov_shift.py.
STRICT clean pool: no zero-bpm present HR step at all AND day-mean>40 AND HR coverage>0.5, n=6624
of 13909 (at cov>0.9, n=4444; conclusions are stable across 0.5/0.7/0.9). ===

1. THE ZERO-CONTAMINATION TRAP IS GONE IN THIS CORPUS. zero-but-present HR cells = 0; present HR
   range exactly [30.00, 209.44]. The min_valid enforcement landed (merger.py:809 _null_out_of_range).
   The strict pool is now n=6624, not the historical n=1641.

2. THE 15-MINUTE GRID WAS DESTROYING THE PEAK, AND THE LOSS IS LARGE.
   median daily HR peak  133.55 bpm @1min  vs  110.84 bpm @15min   (attenuation median 19.13,
   mean 21.02 bpm); 98.2% of days attenuated >5 bpm, 46.2% attenuated >20 bpm.
   This independently reproduces the 135->109 / ~26 bpm claim in configs/canonical/base_12.yaml.

3. IT WAS ALSO DESTROYING THE LABEL. tachycardic base rate (peak>=120): 76.1% @1min vs 31.6% @15min.
   The training code now derives cut points from the corpus and prints
   "tachycardic>=146 elevated>=112 (clinical defaults were 120/100; the peak axis moves with epoch
   resolution)" - i.e. the label axis itself is resolution-dependent. Any peak number from a 15-minute
   run and a 1-minute run are NOT comparable without restating the cut points.

4. THE RECOVERED PEAK IS REAL PHYSIOLOGY, NOT SENSOR NOISE. corr(day max, max of 5-minute rolling
   mean) = 0.943; median (max - roll5max) = 7.35 bpm; median roll5-max = 123.2 bpm. Stable at
   cov>0.5/0.7/0.9. So the 1-minute maximum marks a SUSTAINED excursion.

5. THE HEADROOM ARGUMENT HAS INVERTED, AND THIS IS THE IMPORTANT ONE.
   R2(hr_peak <- day mean) = 0.0342 (cov>0.5), 0.0204 (cov>0.7), 0.0164 (cov>0.9).
   On the clean 1-minute corpus the daily peak is very nearly ORTHOGONAL to the daily level:
   ~97-98% of peak variance is incremental over knowing the mean. Historical analyses that
   concluded "the increment of max over mean is small" were computed on a corpus where the peak
   had already been averaged away AND on a zero-contaminated pool that manufactures cross-cluster
   correlation. hr_MEAN remains saturated (recorded R2 0.975-0.98, MAE 0.69 bpm vs trivial 6.60)
   and has NO headroom; hr_PEAK is where the headroom now lives. Instrument choice is decisive:
   measuring the 1-minute migration on hr_mean can only return "no change".

6. THE MIGRATION SILENTLY CHANGED WHAT THE DATASET WHITELIST MEANS. Per-dataset HR-channel
   coverage, 15-min -> 1-min (from the archived M_mask vs the new one, same datasets):
     dryad_ifh_affect      0.300 -> 0.067  (ratio 0.22; exactly 1/15 -> dryad is 15-MINUTE NATIVE)
     consumer_grade_wear.  0.002 -> 0.000
     wesad 0.084->0.083, neuro_stress 0.022->0.022, wearable_exam 0.153->0.148,
     nurse_stress 0.155->0.157, brain_wearable 0.070->0.061  (already sparse before; unchanged)
     lifesnaps 0.588->0.579, pmdata 0.911->0.889, optihealth_supabase 0.729->0.693,
     mmash 0.975->0.954, big_ideas 0.835->0.825, dreamt 0.373->0.368  (>=1-minute native; hold)
   Clean days contributed per dataset at 1-minute: lifesnaps 4042, pmdata 2015,
   optihealth_supabase 452, big_ideas_glycemic 93, mmash 22, EVERYTHING ELSE 0.
   The Phase A/B allowlist (phase_a_12ch_pmdata / *id_12ch) is
   {lifesnaps, pmdata, wesad, nurse_stress, dreamt, wearable_exam_stress,
    neuro_stress_resilience_hci, big_ideas_glycemic, brain_wearable_monitoring,
    consumer_grade_wearables}: SIX of those ten now contribute ZERO clean days and together are
   97 of 13909 samples (0.7%), while optihealth_supabase (452 clean days) and mmash are EXCLUDED
   from A/B but optihealth_supabase IS included in Phase C (commit 71dad09). So Phase A/B and
   Phase C are training on different cohorts.

7. A COVERAGE GATE IS NOW MIS-CALIBRATED. phase_a_12ch_pmdata sets min_hr_coverage: 0.05 with the
   comment "lifesnaps at hr_cov~0.15 per window (15-min sampling within 24h windows)". At 1 minute
   lifesnaps reads 0.579, ~4x higher, so the 0.05 gate no longer excludes what it was tuned to
   exclude and admits days with ~72 usable minutes.

OPEN QUESTIONS: (a) does the encoder actually convert the recovered peak information into a better
readout, measured on eiv_peak_excursion_increment_r2 and NOT on the saturated hr_mean;
(b) does repairing the allowlist per phase help, and by what mechanism in each phase.


=== UPDATE 2026-08-08: PHASE-FILTER AUDIT (answers "what is the state of dataset filtering in the
phases"). Unpreregistered; problem evidence, no calibration weight. ===
UNPREREGISTERED audit, 2026-08-08; problem evidence, no calibration weight.
Scripts scratch/audit_phase_filters.py and scratch/audit_phaseb_gate.py (committed, 45731c6).
This answers "what is the state of dataset filtering in the phases" with measured numbers rather
than by reading the YAML.

STATE OF THE THREE FILTERS ON THE 1-MINUTE CORPUS
  phase  profile                datasets  min_coverage  min_hr_coverage  samples  subjects
  A      phase_a_12ch_pmdata      10          0.0           0.05           7037      240
  B      phase_b_12ch             10          0.25          UNSET          8913      270
  C      lora_proxy_12ch           3          0.0           0.05           7352       89
A and B share the identical 10-dataset list (B uses the YAML anchor *id_12ch). C is a WEIGHTED
DICT {lifesnaps: 1.0, pmdata: 1.0, optihealth_supabase: 1.0}.

DEFECT 1 - PHASE B HAS NO HEART-RATE GATE AT ALL, AND ITS COVERAGE GATE IS VESTIGIAL.
min_coverage is applied to the ALL-CHANNEL coverage recorded in sample_metadata, not to HR. Four of
the twelve channels are the always-present time encodings (hour/day sin+cos), so all-channel
coverage has a hard FLOOR of 4/12 = 0.333, comfortably above the 0.25 threshold. Measured
consequence: the gate passes 95.7% of the entire corpus and is close to a no-op.
Phase B therefore admits 1981 samples that Phase A rejects, and of those:
    91.5% (1812) have EXACTLY ZERO HR coverage
    mean HR coverage of the extra samples = 0.0011, median 0.0000
    by dataset: lifesnaps 1935, neuro_stress_resilience_hci 35, consumer_grade_wearables 11
So roughly 22% of Phase B's 8913 training samples are days with no heart rate whatsoever, fed to a
triplet objective that is supposed to be orthogonalising physiological state. Phase A, which runs
immediately before it on the same corpus, excludes exactly these days.

DEFECT 2 - PHASE A/B AND PHASE C TRAIN ON DIFFERENT COHORTS.
  in A/B but NOT in C: big_ideas_glycemic, brain_wearable_monitoring, consumer_grade_wearables,
                       dreamt, neuro_stress_resilience_hci, nurse_stress, wearable_exam_stress, wesad
  in C but NOT in A/B: optihealth_supabase  (570 days, 452 of them strict-clean)
The encoder is contrastively aligned on a cohort that never contains optihealth_supabase, and the
LLM is then grounded on it in Phase C. Subject counts collapse 240 -> 270 -> 89 across the phases.

DEFECT 3 - min_hr_coverage: 0.05 IS MIS-SCALED FOR THE 1-MINUTE GRID.
The threshold carries an in-config comment justifying it as "lifesnaps at hr_cov~0.15 per window
(15-min sampling within 24h windows)". At 1 minute lifesnaps reads 0.579, about 4x higher, so the
gate no longer excludes what it was tuned to exclude; 0.05 now admits days with ~72 usable minutes.

DEFECT 4 - SIX OF THE TEN A/B DATASETS CONTRIBUTE ZERO CLEAN DAYS AT 1 MINUTE.
Per-dataset HR-channel coverage and strict-clean day counts:
  lifesnaps           0.579   4042 clean      big_ideas_glycemic  0.825    93 clean
  pmdata              0.889   2015 clean      mmash               0.954    22 clean  (NOT in any phase)
  optihealth_supabase 0.693    452 clean      dreamt              0.368     0 clean
  wesad 0.083 / nurse_stress 0.157 / wearable_exam_stress 0.148 / neuro_stress 0.022 /
  brain_wearable 0.061 / consumer_grade_wearables 0.000   -> all 0 clean days
  dryad_ifh_affect    0.067      0 clean  (15-MINUTE NATIVE: 0.300 -> 0.067 is exactly 1/15)

WHAT THIS DOES AND DOES NOT IMPLY. Repairing the ALLOWLIST alone was tested and REFUTED
(exp-1min-allowlist-20260808: peak increment -0.071, hr_mean -0.016, resp_rate -0.198, single seed
against a documented seed spread of 0.066). So defect 4 is real but is NOT by itself a lever.
Defects 1-3 are UNTESTED and are separate from the allowlist question: defect 1 in particular is a
coverage-GATE bug rather than a dataset-list bug, and it is the one with a clean mechanism
(a triplet objective trained on 22% HR-empty days) and no measurement against it yet.

CONSEQUENCE FOR ANY 1-MINUTE vs 15-MINUTE END-TO-END COMPARISON: the 15-minute pipeline carried the
SAME gates, so an incumbent-vs-incumbent comparison remains apples-to-apples and is still valid.
But an end-to-end run started before fixing defect 1 will train Phase B on ~2000 HR-empty days, and
that should be stated whenever its numbers are quoted.


=== UPDATE 2026-08-08: STATUS OF THE TWO OPEN QUESTIONS THIS PROBLEM WAS OPENED WITH ===

(a) "Does the encoder convert the recovered peak information into a better readout, measured on
eiv_peak_excursion_increment_r2 and NOT on the saturated hr_mean?" -- ANSWERED, YES.
exp-1min-control-20260808 closed "supports": increment +0.4086 against a level-only control of
+0.0123 on the strict clean 1-minute pool (n=6552, 123 subjects, subject-grouped out-of-fold),
MAE 12.80 bpm vs a trivial always-predict-median of 16.97. hr_mean did NOT move (0.9713 vs a
recorded 0.9750), confirming the preregistered instrument-headroom claim.
BUT the honest baseline reframes what that number is worth: a RandomForest on ~20 hand-computed
scalars reaches R2 +0.708 on the same target and folds, and the information bound is +0.971. The
encoder therefore captures 43% of what is available and is beaten roughly 2:1 by three lines of
numpy. Recorded as a rule_out ("the deep stack adds nothing" is REFUTED -- it beats its own
captions by +0.297 -- while the shallow-beats-deep result stands).

(b) "Does repairing the allowlist per phase help, and by what mechanism in each phase?" --
ANSWERED FOR THE ALLOWLIST, NO. exp-1min-allowlist-20260808 closed "refutes": every preregistered
direction was UP and every observed direction was DOWN (peak increment -0.0708, hr_mean -0.0164,
respiratory_rate -0.1981). The dataset-of-origin shortcut story predicted a gain and did not get
one. Single seed per arm against a documented seed spread of 0.066 for this profile, so the
correct reading is "no evidence of benefit", not "proven harmful"; >=3 seeds per arm is the
follow-up. Note the audit above shows the allowlist is only ONE of four filtering defects, and the
other three (Phase B having no HR gate at all, the A/B-vs-C cohort split, the mis-scaled
min_hr_coverage) are untested and are NOT covered by this refutation.

PROBLEM REMAINS OPEN. Resolved: the migration's value, the instrument, the window question, the
patch-construction question. Unresolved: whether the hr_peak deficit is closed by enriching the
CAPTIONS (H4, preregistered and untested) and whether the Phase B coverage-gate defect matters.

WHAT WAS NOT DONE, STATED SO NOBODY INFERS IT WAS: only PHASE A was trained and probed on the
1-minute grid. Phase B was started and then deliberately killed mid-run at operator request;
Phase C never launched; the full `eiv probe` P1-P7 suite was never run, because it requires a LoRA
adapter bundle that only exists after Phase C. No end-to-end 1-minute vs 15-minute comparison
exists yet. The only end-to-end artefact is `eiv train smoke`, which is a shape check on random
weights and is NOT evidence of anything. Assets that survive for a restart: both Phase A arms
(outputs/checkpoints/12ch_v3/phase_a, 12ch_v3_wl/phase_a), the 1-minute strict-clean eval pool
(data/eval_v3_1min), and BOTH text-embedding caches including the Phase B hierarchical one at
8913/8913 complete -- so a Phase B restart skips its ~60-minute CPU precompute.

#epoch-resolution #dataset-whitelist #wearables #baseline

=== CORRECTION + MECHANISM, 2026-08-08 (audit of my own closed results, unpreregistered) ===

(1) A DEFECT IN MY OWN H0 FEATURE SET, FOUND BY RE-AUDIT AND CORRECTED.
scratch/audit_h0_feature_defect.py. The feature I reported as
"fraction of minutes above the day's own 75th percentile" was computed as
nanmean(hr > p75) over all 1440 slots rather than over PRESENT minutes, which makes it
exactly 0.25 * coverage: correlation with HR coverage +0.9996, ratio 0.2496. It was a
DUPLICATE OF hr_cov wearing a dispersion costume, and hr_cov was already in the feature set.
Computed correctly it is the constant 0.25 (sd 0.0010) and carries no information at all.
It is dropped. The published "five features" attribution was therefore wrong in one of five.

CORRECTED LADDER (scratch/h0_ladder_corrected.py, same pool/folds/target, RF, trivial MAE 17.02):
  FS0 caption scalars, no dispersion      R2 +0.1245   incr +0.1020   MAE 15.52   +1.50 bpm
  FS0 + hr_std ONLY                       R2 +0.6015   incr +0.5789   MAE 10.29   +6.73 bpm
  FS0 + hr_iqr                            R2 +0.1983   incr +0.1758   MAE 14.84   +2.19 bpm
  FS0 + p25/p75                           R2 +0.3912   incr +0.3686   MAE 12.81   +4.22 bpm
  FS0 + hr_p90                            R2 +0.3303   incr +0.3077   MAE 13.41   +3.62 bpm
  FS1_clean (std,p25,p75,iqr,skew)        R2 +0.8217   incr +0.7991   MAE  6.87  +10.15 bpm
Correcting the defect made the shallow model BETTER, not worse: 0.708 -> 0.8217. So the
shallow-beats-deep gap is WIDER than first reported: 0.8217 vs the deep stack's 0.4209, MAE
6.87 vs 12.80 bpm. THE SINGLE DECISIVE FEATURE IS hr_std: on its own it moves the caption
scalars from 0.1245 to 0.6015 (+0.579). That sharpens H4 from "add dispersion structure"
to "add the within-day standard deviation", which is one number.
EXCLUDED FROM THE HEADLINE ON PURPOSE: FS0 + hr_p99 scores R2 +0.7916, but p99 of 1440 minutes
is the ~14th largest value, i.e. an order statistic adjacent to the target itself. It is
reported for information but must NOT be counted as a legitimate caption scalar, and no
shallow-beats-deep claim rests on it.

(2) WHY H1 REALLY FAILED - THE SHORTCUT IS NOT IN THE DATASET LIST, IT IS IN THE COVERAGE
SIGNATURE. scratch/h1_shortcut_mechanism.py, CPU only, no training, SUBJECT-GROUPED 5-fold so
it cannot be solved by memorising subjects. Predicting DATASET IDENTITY:
                                         INCUMBENT (10 ds)        REPAIRED (6 ds)
  majority-class baseline                     0.662                   0.614
  per-channel COVERAGE only (12 feats)   acc 0.9972 / bal 0.9675  acc 0.9966 / bal 0.9800
  HR hourly PRESENCE pattern (24 feats)  acc 0.9010 / bal 0.6416  acc 0.9041 / bal 0.6050
  physiology only, HR mean+std (2 feats) acc 0.6361 / bal 0.1850  acc 0.6710 / bal 0.3616
Dataset provenance is ~99.7% decodable from per-channel coverage ALONE, and it is EQUALLY
decodable in both arms. Physiology barely beats the majority baseline (balanced accuracy 0.19
and 0.36). Because encoder_format_version: with_presence_v1 concatenates the presence flags as
input channels, the model is handed a near-perfect provenance label directly.
THIS EXPLAINS THE REFUTATION RATHER THAN EXCUSING IT: editing the allowlist changes only HOW
MANY near-perfectly-separable classes exist, not whether the shortcut is available. 0.9972 ->
0.9966 is no reduction at all. So H1's intervention could not have worked, and the single-seed
caveat I attached to it is not the main story - the intervention was aimed at the wrong object.
The lever, if there is one, is to remove the coverage signature from the input or from the
objective, not to curate the dataset list.

(3) A TRAP IN THE 15-MIN vs 1-MIN MODEL COMPARISON, FLAGGED BEFORE ANYONE QUOTES IT.
The 15-minute Phase B checkpoint scores HIGHER on its own hr_peak probe (increment +0.5706,
MAE 9.18 bpm) than the 1-minute Phase A checkpoint does on its own (+0.4086, MAE 12.80). That
comparison is INVALID for two independent reasons, and reporting it as "15-min is better" would
repeat exactly the error the window-shortening analysis warned about.
  (a) STAGE MISMATCH: Phase B (alignment + triplet orthogonalisation) versus Phase A only.
  (b) TARGET MISMATCH - the 15-minute exam is easier and its answer key is wrong.
      Measured on the same days (scratch/audit_target_difficulty.py):
        peak@1min  median 133.55, SD 21.02, trivial MAE 17.02, R2(target <- day mean) 0.0342
        peak@15min median 110.84, SD 19.42, trivial MAE 14.68, R2(target <- day mean) 0.0720
      Twice as much of the 15-minute target is just level, and it has less spread to explain.
      Decisively: R2(TRUE 1-minute peak <- 15-minute peak) = 0.7327, residual SD 10.87 bpm.
      A PERFECT 15-minute model, scored against physiological truth, cannot exceed 0.7327,
      because its target is attenuated by a mean of 21.02 bpm.
THE VALID, FULLY MATCHED COMPARISON IS AT THE DATA LEVEL, same days, same readout, scored
against the TRUE peak: 1-minute 0.9706 versus 15-minute 0.7167 (+0.254 R2, +6.38 bpm). That is
what the epoch migration bought. The model-level comparison must wait for the 1-minute Phase B
checkpoint and must score both arms against the true 1-minute peak.

#epoch-resolution #dataset-whitelist #wearables #baseline

=== 2026-08-08: TWO OF MY OWN CLOSED EXPERIMENTS ARE MIS-SCORED. CLOSED EXPERIMENTS ARE IMMUTABLE
IN THIS SYSTEM, SO THE CORRECTION LIVES HERE. READ BEFORE TRUSTING EITHER VERDICT. ===

(A) exp-1min-allowlist-20260808, verdict "refutes" -- SCORED AGAINST A HYPOTHESIS IT DID NOT TEST.
H1 asked whether changing the dataset whitelist IN ALL THREE PHASES improves results. That arm
changed dataset_filter in PHASE A ONLY and ran nothing downstream: no Phase B, no Phase C, no
generation probe. A weaker experiment was run and the stronger claim was scored. The verdict is
valid ONLY as evidence about a Phase-A-only allowlist change. Its generalisation was retracted
(see the retracted rule_out) and the real question is reopened as H1b.
What it does legitimately show, with everything else held identical (programmatic profile diff:
dataset_filter and notes were the only differing keys), same seed, both arms in the good basin
(final InfoNCE -18.57 vs -18.34, collapse tripwire > -5.0): a Phase-A-only repair moved every axis
DOWN -- peak increment +0.4086 -> +0.3378, hr_mean 0.9713 -> 0.9549, hr_peak MAE 12.797 -> 13.560
bpm, respiratory_rate +0.5770 -> +0.3789. SINGLE SEED against a documented 0.066 seed spread, so
"no evidence of benefit", not "proven harmful".

(B) exp-1min-end-to-end-20260808, verdict "supports" -- TRUE ON ITS GATES, MISLEADING AS A HEADLINE.
It was filed against the PHASE-A 1-minute-migration hypothesis and scored on encoder readout, both
of which passed (peak increment 0.4086 -> 0.4182, hr_mean 0.9713 -> 0.9777). But that run was the
first complete A -> B -> C chain, and its actual headline finding was a FAILURE at the deliverable:
   p4 hr_acc 0.4609 against an always-predict-majority baseline of 0.569 on the same pool
   -> the LLM's EMITTED bucket is 10.8 points WORSE than a constant predictor,
   while a linear probe on that same model's hidden states reads hr at R2 0.9649 (MAE 1.16 bpm),
   and p7 sensor attention mass is 0.1277 against a 0.2238 uniform baseline.
I reused a convenient existing hypothesis instead of preregistering one for the end-to-end run, so
nothing in the gates could express that failure and the record reads as a validation. It is not.

ROOT CAUSE OF (B), AND THE DURABLE FIX: no registered diagnostic captured EMISSION accuracy against
its own trivial baseline, so a run could be closed "supports" while emitting worse than a constant.
A metric that cannot fail cannot gate anything. Registered eiv_gen_bucket_acc_over_majority
(= generated hr bucket accuracy MINUS the majority-class baseline, recomputed on the same pool).
Current value for adapter medgemma_12ch_v3_1min_e2e: 0.4609 - 0.569 = -0.108. Every future
end-to-end closure must carry it.

CALIBRATION NOTE: calibration() currently reads n=115, acc=0.443, and includes the (A) verdict as a
scored prediction on a question that experiment could not answer. It is a false negative in that
tally and should be discounted when reading my prediction accuracy.

WHAT IS ACTUALLY OPEN, STATED SO "all experiments closed" IS NEVER READ AS "the work is done":
all 43 experiment records in the vault are closed, and that is BOOKKEEPING, not science. Open right
now: H1b (dataset filtering across all three phases, untested), H4b (hr_std in the caption -- the
code change is committed at 97ba82b but NO arm has been trained), H5 (coverage-signature shortcut,
untested), the vacuous p3/p6 probes that block causal interpretation of any of them, and the fact
that no 1-minute-vs-15-minute comparison exists on GENERATION metrics.

#epoch-resolution #dataset-whitelist #wearables #baseline

=== 2026-08-08 FINAL AUDIT: ALL FOUR RULE_OUTS I FILED ON THIS PROBLEM ARE NOW RETRACTED. ===
Not because the measurements were wrong -- most of them re-derive exactly -- but because in every
case I measured a NARROW thing and filed a BROAD claim. The pattern was identical four times:
  whitelist  : measured Phase A only (+ data-level proxies), claimed all three phases.
  window     : measured data-level only ("no model and no GPU"), claimed "not worth doing".
  pooling    : proved patch construction lossless (exact), then leaned on a 15-MINUTE-era projector
               measurement -- in a session whose whole premise is that 15-min results do not transfer.
  deep stack : measured encoder readout, claimed the pipeline is unnecessary -- while my own numbers
               show a random forest beating the stack (0.5751 vs 0.4305) and the pipeline emitting
               worse than a constant (-0.108 vs the majority baseline).

WHAT REMAINS MEASURED AND SAFE TO REUSE (each stands on its own instrument, none is a pipeline claim):
 - max over the 23 patch maxima equals the true daily peak to 0.0000000000 bpm on all 6624 days;
   the unfold(120,60) grid covers 1440/1440 minutes; the operator has rank 1440/1440, cond 5.47;
   values:dims 120:192 is a 1.6x EXPANSION. Patch construction is lossless for the peak. (Algebra.)
 - R2(peak <- window mean) rises 0.0342 -> 0.7495 from W=1440 to W=60, and the tachycardic base rate
   collapses 32.1% -> 2.5%. The peak-prediction RATIONALE for shortening is trivial. (Data-level.)
 - Dataset identity is ~99.7% decodable from per-channel coverage, equally under both allowlists.
 - Phase B state labels are degenerate: 99.1% NONE, every non-NONE label a 100%-pure dataset id.
 - The Phase C hr_peak bucket marginal is filter-insensitive (tachy 0.311 vs 0.311).
 - The stack extracts far more hr_peak than its captions contain (0.4305 vs 0.0706), and one extra
   caption scalar (hr_std) beats the stack (0.5751).
 - 15-min vs 1-min at the data level, scored against the TRUE peak: 0.9706 vs 0.7167.

NOTHING ON THIS PROBLEM IS CURRENTLY RULED OUT. Every hypothesis is open, and the three priority-1
runbooks (repair p3/p6; dataset filtering across all three phases; H4b hr_std caption arm) are the
queue.

#epoch-resolution #dataset-whitelist #wearables #baseline

=== TWO MEASURED FACTS THAT WERE ONLY IN GIT/CONFIG AND NOT IN THIS RECORD. ADDED 2026-08-08 after
auditing my own insights against the vault. ===

(1) THE STRATIFIED SAMPLER'S WILDCARD BUCKET IS THE REAL SHAPE OF THE PHASE A COHORT, AND IT IS
SEVERELY OVERSAMPLED. `phase_a_12ch_pmdata` sets stratified_batches {lifesnaps: 45, pmdata: 12,
"*": 35}. The "*" key is ONE POOLED BUCKET of every dataset not named explicitly
(StratifiedDomainBatchSampler, src/training/datasets.py:2010), so it fills 35 of 92 slots --
38% OF EVERY CONTRASTIVE BATCH. Measured on the 1-minute corpus at the Phase A gate
(min_hr_coverage 0.05), scratch/audit_wildcard_pool.py:
    INCUMBENT allowlist: "*" pool =  255 samples, mean HR coverage 0.514, seen ~10.4x per epoch
    REPAIRED  allowlist: "*" pool =  796 samples, mean HR coverage 0.680, seen ~3.3x per epoch
    total training pool 7037 -> 7578
So under the incumbent recipe 38% of every batch is drawn from 255 samples, 51 of which are
near-empty days (HR coverage 0.061-0.157) from datasets contributing ZERO strict-clean days. This
was the concrete motivation for the H1 allowlist arm; the arm was still refuted (-0.0708), and the
mechanism audit later showed why -- provenance is ~99.7% decodable from coverage in BOTH arms, so
curating the list never removed the shortcut.

(2) THE TEXT-EMBEDDING CACHE IS KEYED BY CAPTION TEXT, WHICH IS WHY CAPTION ARMS ARE SAFE TO RUN
SIDE BY SIDE. `_caption_cache_key(raw_index, text)` (src/training/pipeline.py:652/669) hashes the
CAPTION STRING into the filename, so two caption versions coexist in one cache directory without
clobbering each other. The cache DIRECTORY hash, by contrast, covers only
(model_id, embedding_dim, pooling, max_length, caption_style, min_coverage, dataset_filter) -- it
does NOT cover the caption builder's content. Consequences, both verified empirically:
  - changing the caption builder while keeping caption_style: hr_only reuses the SAME directory and
    correctly recomputes every entry (observed: "Precomputing 7038 missing" on the H4b arm, then
    resuming at 6934 after a restart, proving per-text keying);
  - changing dataset_filter DOES change the directory hash, which is why the H1 arm needed a full
    fresh ~50 min precompute rather than reusing the control's.
Practical rule: a caption-content change costs a full precompute but cannot corrupt another arm; a
dataset_filter change costs a full precompute in a separate directory.

#epoch-resolution #dataset-whitelist #wearables #baseline
