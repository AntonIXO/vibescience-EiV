---
created_at: '2026-08-08T00:27:30+00:00'
id: the-encoder-cross-modal-projector-soft-token-stack-adds-nothing-beyond-the-handf
problem_id: the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid
retracted: true
retraction_reason: "RETRACTED 2026-08-08 BY THE AUTHOR. It is a COMPOUND claim; I
  refuted the narrow half, filed it as\nif I had refuted both, and my own later measurements
  support the broad half.\n\nTHE CLAIM BUNDLED TWO THINGS:\n  (i) \"the stack adds
  nothing beyond the handful of scalars its captions already contain\"\n  (ii) \"so
  the deep pipeline is unnecessary for reading physiology\"\n\n(i) IS GENUINELY REFUTED
  AND STANDS. Matched protocol, same strict clean 1-minute pool, same\nsubject-grouped
  GroupKFold folds, same hr_peak target, trivial always-median MAE 17.023 bpm:\n    exactly
  the six caption scalars (RF)        R2 +0.0706   +0.97 bpm\n    deep encoder->projector->soft
  tokens        R2 +0.4305   +4.32 bpm\nThe stack beats its own supervision by +0.36
  R2, roughly 6x, and it does so because it reads the\nraw tensor rather than the
  caption. That measurement is sound and I am not withdrawing it.\n\n(ii) IS NOT REFUTED,
  AND TWO OF MY OWN NUMBERS POINT THE OTHER WAY.\n  1. A SHALLOW MODEL BEATS THE STACK
  ON THE SAME READOUT. Adding ONE scalar, hr_std, to those six\n     caption numbers
  gives a random forest R2 +0.5751 / +6.37 bpm, against the stack's +0.4305 /\n     +4.32
  bpm. The best shallow arm reaches +0.8190 / +10.08 bpm. So on the very quantity
  this\n     rule_out used as its instrument, three lines of numpy outperform the
  learned pipeline.\n  2. AT THE DELIVERABLE THE STACK IS CURRENTLY WORTH LESS THAN
  A CONSTANT. The first complete\n     end-to-end 1-minute run emits p4 hr_acc 0.4609
  against an always-predict-majority baseline of\n     0.569 on the same pool -- newly
  registered as eiv_gen_bucket_acc_over_majority = -0.108 --\n     while a linear
  probe on that same model's hidden states reads hr at R2 0.9649. The pipeline's\n
  \    purpose is to make the LLM read physiology, and end to end it does not.\nTaken
  together, \"the deep pipeline is unnecessary for reading physiology\" is a live
  question that my\nevidence arguably SUPPORTS, and I recorded it as eliminated.\n\nWHY
  THE ORIGINAL INSTRUMENT WAS TOO NARROW: I measured ENCODER READOUT (a Ridge probe
  on pooled soft\ntokens) and used it to answer a claim about the PIPELINE. Those
  are different objects, and the\nend-to-end run showed they disagree in sign.\n\nRE-FILE
  NARROWLY: \"the stack extracts substantially more hr_peak information than its captions\ncontain\"
  is measured, matched and defensible. \"The deep pipeline is necessary\" is not established\nand
  should be treated as open, with eiv_gen_bucket_acc_over_majority as its gate."
ruled_out_at: '2026-08-08T00:27:30+00:00'
topic_tags:
- baseline
- control
- epoch-resolution
---

## Claim
The encoder -> cross-modal projector -> soft-token stack adds nothing beyond the handful of scalars its captions already contain, so the deep pipeline is unnecessary for reading physiology (the MAS.S60 lecture-4 shallow-beats-deep worry, strong form).

## Method
Matched honest-baseline bakeoff on the STRICT clean 1-minute pool: RandomForest / HistGradientBoosting / RidgeCV on 15-28 hand-computed per-day scalars versus a RidgeCV probe on the 2560-dim pooled soft-token embedding from the trained 1-minute Phase A checkpoint (outputs/checkpoints/12ch_v3/phase_a). Identical target definitions, identical GroupKFold(5)-by-subject out-of-fold protocol, identical trivial baseline, 123 subjects both sides, with a shuffled-target negative control. scratch/h0_rf_honest_baseline.py and scripts/probe_phase_a_hr.py.

## Evidence
UNPREREGISTERED ad-hoc measurement (MIT MAS.S60 lecture-4 "honest baseline" gate); problem
evidence, no calibration weight. Script scratch/h0_rf_honest_baseline.py, results
scratch/h0_rf_results.json, log logs/h0_rf.log.

PROTOCOL, MATCHED ON BOTH SIDES. Same STRICT clean 1-minute pool (data/eval_v3_1min: no zero-bpm
present step, day-mean>40, HR cov>0.5), same 123 subjects, same GroupKFold(5) BY SUBJECT,
out-of-fold, same hr_peak target definition (max over present 1-minute steps, raw bpm), same
always-predict-median trivial baseline. Shallow n=6624, deep n=6552 (the probe drops 72 samples
that produce no valid target) - a 1.1% difference in n, not a confound.

THE LADDER, hr_peak, subject-grouped out-of-fold, trivial always-predict-median MAE 17.0 bpm:
  arm                                               R2        skill vs trivial
  level only (day mean, 1 feature)                +0.0226      +0.03 bpm
  FS0 = the CAPTION SCALARS (15 feats, RF)        +0.1242      +1.50 bpm
  DEEP encoder->projector->soft tokens (2560-d)   +0.4209      +4.18 bpm
  FS1 = FS0 + dispersion (20 feats, RF)           +0.7075      +8.22 bpm
  FS2 = FS1 + 6-hour blocks (28 feats, HistGB)    +0.7236      +8.57 bpm
  patch maxes + HistGB (information bound)        +0.9706     +14.72 bpm
The five features that produce the FS0 -> FS1 jump are hr_std, hr_p25, hr_p75, hr_iqr and the
fraction of minutes above the day's own 75th percentile. Three lines of numpy.

WHAT THIS ELIMINATES. The strong lecture-4 worry - "if a shallow model on the caption scalars
matches the deep stack then the stack is adding nothing, because the supervision never contained
more than those numbers" - is REFUTED. The deep stack scores +0.4209 against the caption scalars'
+0.1242, an increment of +0.2967 R2 / +2.68 bpm. The encoder reads the raw tensor, not the
caption, and it demonstrably extracts peak-relevant structure the captions do not contain.

WHAT THIS DOES NOT LET US OFF. A random forest on ~20 hand-computed scalars beats the entire
learned stack roughly 2:1 on the quantity of interest: R2 0.708 vs 0.421, MAE 8.80 vs 12.80 bpm.
The deep stack reaches 43% of the measured information bound (0.4209 / 0.9706). This is the
lecture-4 shallow-beats-deep result landing on our own pipeline, and it should be reported plainly.

THE MECHANISM, AND IT IS CONSISTENT WITH EVERYTHING ELSE ON RECORD. Split by what the target is:
  hr_MEAN  (level)     deep stack R2 0.9713, MAE 1.145 bpm vs trivial 7.602  - essentially solved
  hr_PEAK  (excursion) deep stack R2 0.4209, MAE 12.80 bpm vs trivial 16.97  - half of trivial
                                                                                dispersion features
caption_style is hr_only: the captions carry LEVEL and not SPREAD. Contrastive alignment provably
captures the information shared between the two views and provably discards the rest, so an
encoder aligned against level-only text learns level almost perfectly and excursion poorly. The
deficit is in the SUPERVISION, not obviously in the architecture - which is also why every past
architectural intervention (pooling operator, slot count, window length) came back flat.

CAVEATS, STATED. (1) The hr_mean arm of the shallow model is VACUOUS by construction: hr_mean is
itself one of the input scalars, so it scores R2 1.0000 and MAE 0.000. That is not a defeat of the
deep stack, which has to reconstruct hr_mean from a learned representation that does not contain
it explicitly; the two hr_mean numbers are NOT comparable and only the hr_peak comparison is.
(2) The FS1 dispersion features are computed from the same HR series as the target. That is not
leakage - the max is not among them and the encoder sees exactly the same series - but it does mean
FS1 measures "what is trivially extractable from this day", which is precisely the intended
comparator. (3) The shuffled-target negative control returns R2 -0.1772, i.e. NEGATIVE, not
positive: no leakage. The magnitude is ordinary overfitting to permuted labels under a
subject-grouped split, not a broken split. (4) Phase A is self-supervised on these subjects, so
the deep number benefits from representation exposure the shallow model does not get - if anything
this flatters the deep stack, so the conclusion is conservative.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid]].

**RETRACTED:** RETRACTED 2026-08-08 BY THE AUTHOR. It is a COMPOUND claim; I refuted the narrow half, filed it as
if I had refuted both, and my own later measurements support the broad half.

THE CLAIM BUNDLED TWO THINGS:
  (i) "the stack adds nothing beyond the handful of scalars its captions already contain"
  (ii) "so the deep pipeline is unnecessary for reading physiology"

(i) IS GENUINELY REFUTED AND STANDS. Matched protocol, same strict clean 1-minute pool, same
subject-grouped GroupKFold folds, same hr_peak target, trivial always-median MAE 17.023 bpm:
    exactly the six caption scalars (RF)        R2 +0.0706   +0.97 bpm
    deep encoder->projector->soft tokens        R2 +0.4305   +4.32 bpm
The stack beats its own supervision by +0.36 R2, roughly 6x, and it does so because it reads the
raw tensor rather than the caption. That measurement is sound and I am not withdrawing it.

(ii) IS NOT REFUTED, AND TWO OF MY OWN NUMBERS POINT THE OTHER WAY.
  1. A SHALLOW MODEL BEATS THE STACK ON THE SAME READOUT. Adding ONE scalar, hr_std, to those six
     caption numbers gives a random forest R2 +0.5751 / +6.37 bpm, against the stack's +0.4305 /
     +4.32 bpm. The best shallow arm reaches +0.8190 / +10.08 bpm. So on the very quantity this
     rule_out used as its instrument, three lines of numpy outperform the learned pipeline.
  2. AT THE DELIVERABLE THE STACK IS CURRENTLY WORTH LESS THAN A CONSTANT. The first complete
     end-to-end 1-minute run emits p4 hr_acc 0.4609 against an always-predict-majority baseline of
     0.569 on the same pool -- newly registered as eiv_gen_bucket_acc_over_majority = -0.108 --
     while a linear probe on that same model's hidden states reads hr at R2 0.9649. The pipeline's
     purpose is to make the LLM read physiology, and end to end it does not.
Taken together, "the deep pipeline is unnecessary for reading physiology" is a live question that my
evidence arguably SUPPORTS, and I recorded it as eliminated.

WHY THE ORIGINAL INSTRUMENT WAS TOO NARROW: I measured ENCODER READOUT (a Ridge probe on pooled soft
tokens) and used it to answer a claim about the PIPELINE. Those are different objects, and the
end-to-end run showed they disagree in sign.

RE-FILE NARROWLY: "the stack extracts substantially more hr_peak information than its captions
contain" is measured, matched and defensible. "The deep pipeline is necessary" is not established
and should be treated as open, with eiv_gen_bucket_acc_over_majority as its gate.

#baseline #control #epoch-resolution
