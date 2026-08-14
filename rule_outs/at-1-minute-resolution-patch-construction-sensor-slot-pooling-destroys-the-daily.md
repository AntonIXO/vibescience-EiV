---
created_at: '2026-08-07T22:08:22+00:00'
id: at-1-minute-resolution-patch-construction-sensor-slot-pooling-destroys-the-daily
problem_id: the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid
retracted: true
retraction_reason: "RETRACTED 2026-08-08 BY THE AUTHOR AS A COMPOUND CLAIM, HALF OF
  WHICH IS PROVEN AND HALF OF WHICH\nRESTS ON 15-MINUTE-ERA EVIDENCE THIS VERY SESSION
  ARGUES DOES NOT TRANSFER.\n\nThe claim bundles two statements. They have very different
  evidential standing and should never have\nbeen ruled out together.\n\nPART (a)
  -- \"patch construction destroys the daily HR peak\" -- IS EXACTLY DISPROVEN AND
  STANDS.\nRe-derived independently from scratch, replicating src/models/patching.py
  (unfold patch_len=120,\nstride=60, N=23) on all 6624 strict-clean days:\n    max
  over the 23 per-patch maxima vs the true daily peak: max abs error 0.0000000000
  bpm\n    days where they differ at all: 0 of 6624\n    minutes of the day covered
  by the patch grid: 1440/1440\n    the unfold+Linear(120->192) operator has rank
  1440/1440, condition number 5.47\n    values:dims is 120:192, a 1.6x over-complete
  EXPANSION, not a compression\nThis is algebra, not statistics, and it is not affected
  by the retraction.\n\nPART (b) -- \"so removing or changing the pooling ('not pooling
  at all') would recover it\" -- IS THE\nPROBLEM. That is a claim about the LEARNED
  PROJECTOR's pool-to-sensor_slots stage. My own method\nline admits I did not measure
  it: \"The learned projector was deliberately NOT re-probed; the prior\nrule-out
  ro-dual-avg-max-pooling-downstream-20260805 already settled that stage and stands.\"\nBut
  that prior rule-out was measured on the 15-MINUTE corpus with a 15-minute checkpoint,
  and the\nentire premise of this session is that 15-minute conclusions do not transfer
  to the 1-minute grid --\nI demonstrated exactly that for the peak target, where
  R2(TRUE 1-minute peak <- 15-minute peak) is\nonly 0.7327 and a perfect 15-minute
  model cannot exceed it. Leaning on a 15-minute projector\nmeasurement to close a
  1-minute pooling question is the same inconsistency I am retracting the other\nrule_outs
  for.\n\nCONCRETE REASON IT MIGHT NOT TRANSFER: the projector pools 23 patches per
  channel to sensor_slots,\nand what it must preserve changed with the grid. At 15
  minutes the peak had already been averaged\naway upstream (median attenuation 19.13
  bpm), so the projector was never carrying much peak to\nbegin with and a max branch
  adding +0.0090 proves little. At 1 minute the peak IS present at the\ntokens --
  exactly, per part (a) -- so the projector now has something real to lose for the
  first\ntime. The old measurement cannot speak to that.\n\nRE-FILE NARROWLY: \"patch
  CONSTRUCTION is lossless for the daily peak at 1-minute resolution\" is\nexact and
  defensible. \"Pooling is not the bottleneck, so not pooling buys zero\" needs a
  1-minute\nprojector probe on outputs/adapters/medgemma_12ch_v3_1min_e2e, which now
  exists and costs no\ntraining -- it is a CPU probe of a checkpoint that is already
  on disk."
ruled_out_at: '2026-08-07T22:08:22+00:00'
topic_tags:
- pooling
- epoch-resolution
- control
- baseline
---

## Claim
At 1-minute resolution, patch construction / sensor-slot pooling destroys the daily HR peak, so removing or changing the pooling ("not pooling at all") would recover it.

## Method
CPU-only simulation of the production patching front end (src/models/patching.py, unfold patch_len=120 stride=60 -> N=23) on the STRICT clean 1-minute pool (n=6624 days, 123 subjects), plus an exact algebraic rank/conditioning analysis of the unfold+Linear operator, plus linear (RidgeCV) and nonlinear (HistGradientBoosting) readout arms on identical subject-grouped GroupKFold folds with shuffled-target controls. The learned projector was deliberately NOT re-probed; the prior rule-out ro-dual-avg-max-pooling-downstream-20260805 already settled that stage and stands.

## Evidence
Re-tested at 1-MINUTE resolution. This CONFIRMS and SHARPENS the existing rule-out
ro-dual-avg-max-pooling-downstream-20260805 rather than reopening it; the learned projector was not
re-probed and nothing here is a claim about it. Scripts scratch/h2_patch_pooling_1min.py and
scratch/h2_patch_pooling_1min_readout.py; scratch/H2_H3_RESULTS.md. CPU only.

(a) EXACT, NOT STATISTICAL. Simulating src/models/patching.py (unfold patch_len=120 stride=60,
N=23), max over the 23 per-patch maxima EQUALS the true daily hr_peak with MAXIMUM ABSOLUTE ERROR
0.0000 bpm across all 6624 days (R2 = 1.000000). Patch construction at 1 minute destroys ZERO peak
information. Stride 60 < patch_len 120 tiles the day with overlap, so no minute is unrepresented.

(b) THE OPERATOR IS INJECTIVE. unfold(120,60) followed by Linear(120->192), viewed as a matrix
R^1440 -> R^4416, has RANK 1440 of 1440 with condition number 5.47. The trained 15-minute
checkpoint's patching weight is also exactly full rank (96/96), so training does not collapse it.

(c) MY "15x COMPRESSION" PREMISE WAS FALSE, AND THIS IS THE CORRECTION THAT MATTERS. Values per
token went 8 -> 120 (the 15x is arithmetically right) but real MINUTES per token (120 = 2 hours)
and DIMS per token (192) are both UNCHANGED by the migration. The ratio that decides lossiness is
values:dims, which went from 8:192 (a 24x over-complete EXPANSION) to 120:192 (a 1.6x expansion) -
still an expansion, still lossless. The first lossy configuration is patch_len > d_model = 192;
the current setting sits at 63% of that threshold. THAT is the number to watch if patch_len is ever
raised or d_model lowered.

(d) WHAT THE MIGRATION ACTUALLY BOUGHT, AND WHERE THE LOSS ALWAYS WAS. At 15-minute epochs even a
PERFECT max reader over the patches missed the true daily peak by median 19.13 bpm (MAE 21.02).
That deletion happened at MERGE time, upstream of every architectural knob, which is exactly why
pooling operator, slot count and window length all came back flat or refuted: they were all trying
to recover information that was no longer in the tensor. Worth 19.1 bpm and fully consistent with
the pooling rule-out.

LINEAR ARMS (RidgeCV, subject-grouped GroupKFold, target = day hr_peak, trivial always-median
MAE 17.023 bpm), reported WITH the caveat that they are not an information measurement:
  (v)  day mean, 1 feature   R2 +0.0226  MAE 16.990  (+0.03 bpm)
  (i)  patch means, 23       R2 -0.0349  increment -0.0575  MAE 17.385  (-0.36 bpm)
  (ii) patch maxes, 23       R2 +0.2889  increment +0.2664  MAE 14.186  (+2.84 bpm)
  (iii) means+maxes, 46      R2 +0.4598  increment +0.4373  MAE 12.020  (+5.00 bpm)
  (iv) raw 1440 values       R2 -0.0290  increment -0.0516  MAE 17.347  (-0.32 bpm)
  shuffled-target control on (iv): -0.0005
Arm (iv) is the supposed LOSSLESS UPPER BOUND yet scores BELOW the level-only control (v) while
containing it exactly, with a clean shuffle control - so it is UNDERFIT, not leaked. A maximum is
not a linear functional of the day. This same artefact produces a spurious 15-min > 1-min inversion
at arm (iii), because block-averaged features are smoother and friendlier to a linear map.

NONLINEAR READOUT (HistGradientBoosting, identical subject-grouped folds):
  (v)  day mean        R2 +0.0066  MAE 17.035  (-0.01 bpm vs trivial)
  (i)  patch means     R2 +0.4438  increment +0.4372  MAE 12.327  (+4.70 bpm)
  (ii) patch MAXES     R2 +0.9706  increment +0.9640  MAE  2.308  (+14.72 bpm)
  (iii) means+maxes    R2 +0.9698  increment +0.9632  MAE  2.353  (+14.67 bpm)
  (iv) raw 1440        R2 +0.6479  increment +0.6413  MAE  9.563  (+7.46 bpm)
  15-min arm (ii)      R2 +0.7167  increment +0.7101  MAE  8.688  (+8.33 bpm)
  shuffled control: -0.0001
Adding a mean branch on top of a max branch costs -0.0008, echoing the +0.0090 that the original
dual avg+max rule-out measured at a different stage. 1-minute beats 15-minute at MATCHED arm and
MATCHED readout by +0.254 R2 / +6.38 bpm - a TENSOR gain, not a pooling gain. Arm (iv) still sits
below arm (ii) despite containing it, so even this readout is capacity-limited; only (a) is an
information bound.

ANSWERS. Patch construction loses exactly no peak information at 1 minute. "Not pooling at all"
buys exactly zero, because patch construction is not a pooling step - it is a 1.6x over-complete
linear expansion with nothing to un-pool. The only averaging that ever destroyed the peak was the
15-minute epoch grid itself, worth 19.13 bpm, and it is already fixed.

METHODOLOGICAL CONSEQUENCE, ACTED ON: because a maximum is not linear, a Ridge probe on pooled soft
tokens structurally UNDERSTATES peak content (+0.2889 vs +0.9706 on identical folds). A nonlinear
readout arm was therefore added to scripts/probe_phase_a_hr.py so the encoder evaluation cannot
manufacture a false negative on hr_peak.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid]].

**RETRACTED:** RETRACTED 2026-08-08 BY THE AUTHOR AS A COMPOUND CLAIM, HALF OF WHICH IS PROVEN AND HALF OF WHICH
RESTS ON 15-MINUTE-ERA EVIDENCE THIS VERY SESSION ARGUES DOES NOT TRANSFER.

The claim bundles two statements. They have very different evidential standing and should never have
been ruled out together.

PART (a) -- "patch construction destroys the daily HR peak" -- IS EXACTLY DISPROVEN AND STANDS.
Re-derived independently from scratch, replicating src/models/patching.py (unfold patch_len=120,
stride=60, N=23) on all 6624 strict-clean days:
    max over the 23 per-patch maxima vs the true daily peak: max abs error 0.0000000000 bpm
    days where they differ at all: 0 of 6624
    minutes of the day covered by the patch grid: 1440/1440
    the unfold+Linear(120->192) operator has rank 1440/1440, condition number 5.47
    values:dims is 120:192, a 1.6x over-complete EXPANSION, not a compression
This is algebra, not statistics, and it is not affected by the retraction.

PART (b) -- "so removing or changing the pooling ('not pooling at all') would recover it" -- IS THE
PROBLEM. That is a claim about the LEARNED PROJECTOR's pool-to-sensor_slots stage. My own method
line admits I did not measure it: "The learned projector was deliberately NOT re-probed; the prior
rule-out ro-dual-avg-max-pooling-downstream-20260805 already settled that stage and stands."
But that prior rule-out was measured on the 15-MINUTE corpus with a 15-minute checkpoint, and the
entire premise of this session is that 15-minute conclusions do not transfer to the 1-minute grid --
I demonstrated exactly that for the peak target, where R2(TRUE 1-minute peak <- 15-minute peak) is
only 0.7327 and a perfect 15-minute model cannot exceed it. Leaning on a 15-minute projector
measurement to close a 1-minute pooling question is the same inconsistency I am retracting the other
rule_outs for.

CONCRETE REASON IT MIGHT NOT TRANSFER: the projector pools 23 patches per channel to sensor_slots,
and what it must preserve changed with the grid. At 15 minutes the peak had already been averaged
away upstream (median attenuation 19.13 bpm), so the projector was never carrying much peak to
begin with and a max branch adding +0.0090 proves little. At 1 minute the peak IS present at the
tokens -- exactly, per part (a) -- so the projector now has something real to lose for the first
time. The old measurement cannot speak to that.

RE-FILE NARROWLY: "patch CONSTRUCTION is lossless for the daily peak at 1-minute resolution" is
exact and defensible. "Pooling is not the bottleneck, so not pooling buys zero" needs a 1-minute
projector probe on outputs/adapters/medgemma_12ch_v3_1min_e2e, which now exists and costs no
training -- it is a CPU probe of a checkpoint that is already on disk.

#pooling #epoch-resolution #control #baseline
