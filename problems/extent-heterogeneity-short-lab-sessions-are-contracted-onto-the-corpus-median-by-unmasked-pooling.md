---
created_at: '2026-08-20T21:12:15+00:00'
former_ids: []
id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
paper_refs: []
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
status: resolved
target: null
title: 'Short lab sessions are contracted onto the corpus median by unmasked pooling:
  CASE retains 2.85% of its between-subject spread inside the 1440-minute window'
topic_tags:
- extent-heterogeneity
- pooling
- denominator
- wearables
- state-supervision
- deployment-distribution
updated_at: '2026-08-21T04:53:38+00:00'
updates:
- at: '2026-08-21T04:53:38+00:00'
  text: 'RESOLVED 2026-08-21. All five hypotheses closed (2 confirmed / 3 refuted),
    zero open, zero interpretation debt. The mechanism was measured (W4/W4b), shown
    architectural and reversible (W5), and FIXED END-TO-END (exp-w6b: short-cohort
    variance-compression 0.5630 -> 0.6897 at the production stage, dense no-harm 3.575
    -> 3.301 bpm, H-W6 CONFIRMED). The three follow-on levers were each eliminated
    on CPU before GPU spend: crop augmentation (W7 -- the frozen encoder retains 96.4%
    of its scale-free readout skill on 41-minute crops against a gate demanding a
    >=20% loss), coverage conditioning (W9 -- INERT: shuffled c scores at or above
    true c, because encoder_format_version=with_presence_v1 already feeds the mask
    as input channels), and attention pooling (W8 -- random-init matches or beats
    trained in all four readout x null cells).


    SHIPPED: patch_pool_mode=''masked_avg'' (665fff2; channel-layout fix 880c332),
    inert unless explicitly selected, covered by 21 tests including a k-parametrised
    all-present invariant.


    TWO SCOPE LIMITS THAT MUST TRAVEL WITH ANY CITATION. (1) The deployment number
    is NOT established: supabase +0.516 bpm sits ~12x INSIDE its own 6.29 seed band,
    so no claim in either direction; establishing the sign needs >=5 seeds or a paired-row
    bootstrap on the same 200-row draw. (2) The end-to-end figure is 0.5630 -> 0.6897
    (+0.1267), NOT W5''s frozen-token 0.1822 -> 0.8159 -- the projector''s cross-attention
    already undoes much of the contraction unaided, so quoting the probe number as
    an end-to-end result is a stage error.


    RESIDUAL FILED ELSEWHERE, NOT HERE: W8 established that momentary state is SUPPLY-limited
    rather than architecture-limited (the trained encoder carries no more state signal
    than a random projection of the same input, and the older RF-only ''random-init
    is inert'' claim was itself a readout artifact). That belongs to the state/label-supply
    track.'
---

## Description
[OPENED 2026-08-20 on corpus ac7d2b1c, tensor [12547,1440,12], after the W4/W4b premise audit. All numbers below are MEASURED on this corpus, not inherited.]

THE CORPUS IS BIMODAL IN EXTENT AND THE PIPELINE PRETENDS IT IS NOT. Median HR-present minutes per window, by dataset (W4/P1, PASS):
  SHORT  popane_s4 27 | neuro_stress_resilience_hci 32 | popane_s3 36 | nurse_stress 38 |
         case 41 | brain_wearable_monitoring 88 | wesad 117 | wearable_exam_stress 224
  LONG   dryad_ifh_affect 512 | dreamt 532 | mmash 799 | optihealth_supabase 1064 |
         big_ideas_glycemic 1275 | lifesnaps 1357 | pmdata 1382
Seven cohorts sit under 120 measured minutes inside a 1440-minute window. Six of the eight short
cohorts are the labelled induced-stress corpora -- i.e. exactly the supply the state slot needs.

THE MECHANISM IS ARITHMETIC, AND IT IS A CONTRACTION, NOT AN OFFSET. The fill value 0.0 in
population-normalised space IS the corpus median (73.00 bpm for hr_mean). Unmasked pooling
therefore computes exactly
    naive_bpm = median + c * (present_bpm - median),   c = present fraction
Measured (W4/P2, n=12,503): regressing naive on c*present gives slope 1.0000, intercept 0.00000,
R2 1.00000. This is not a fitted approximation; it is the identity the code implements.

WHY THE OBVIOUS DIAGNOSTIC MISSES IT -- AND THIS IS THE MOST USEFUL THING IN THE RECORD.
I preregistered the mean gap |naive - present| > 10 bpm on the four short labelled cohorts (W4/P4).
It FAILED, 2 of 4: wesad 5.8, case 4.9, nurse_stress 12.1, wearable_exam_stress 26.3. The
refutation is of the INSTRUMENT, not the mechanism: the gap equals (1-c)*|present - median|, so a
cohort whose true mean happens to sit on the corpus median posts a tiny gap no matter how severely
diluted it is. WESAD sits at 74.7 and CASE at 71.5 against a corpus median of 73.0. The mean is
structurally blind here.

THE CORRECT QUANTITY IS BETWEEN-SUBJECT SPREAD, and it confirms the mechanism exactly (W4b/P5,
PASS; new diagnostic `eiv_extent_variance_compression_ratio`):
  cohort                        mean_c   sd_true  sd_naive   ratio     |median-73|
  popane_s4                     0.0187    10.41      0.20   0.0189        8.4
  neuro_stress_resilience_hci   0.0226    15.35      0.35   0.0231        6.8
  popane_s3                     0.0257     9.61      0.24   0.0251        6.5
  case                          0.0285     7.59      0.22   0.0285        1.5
  nurse_stress                  0.0419     6.67      0.67   0.1008       12.7
  wesad                         0.0831     7.61      0.63   0.0825        1.7
  wearable_exam_stress          0.1794     8.13      2.31   0.2838       33.0
  lifesnaps                     0.7958     9.29      6.50   0.6992        5.5
  pmdata                        0.8703     8.35      7.15   0.8567        2.7
  optihealth_supabase           0.6672     9.88      5.01   0.5071       15.5
The ratio tracks c to the fourth decimal where the cohort sits near the median (case 0.0285 vs c
0.0285). CASE retains 2.85% of the between-subject spread that distinguishes one of its subjects
from another; popane_s4 retains 1.89%. No downstream head, however well trained, can separate
subjects whose representations have been contracted onto a single point. THIS is the quantitative
content of the long-standing observation that four labelled cohorts are 'structurally invisible at
the daily grid'.

EXTENT IS ALSO A PROVENANCE SHORTCUT (W4/P3, PASS). Four extent/coverage scalars alone
(hr_present_minutes, coverage, coverage_epochs, contiguity), subject-grouped GroupKFold(5),
RF(300): dataset identity at balanced accuracy 0.7520 against a 0.0625 chance rate over 16 classes
= 12.03x chance. Duration alone nearly names the cohort, and cohort determines the label
distribution -- so 'short == lab == stressed' is learnable with zero physiology. NOTE: this does
NOT open a new front. `h5-the-shortcut-is-the-coverage-2aaaa0` already owns the coverage-shortcut
claim (re-priced to bal-acc 0.8278 on the live corpus, status inconclusive/test_invalid, never
run). The W4/P3 number is an INDEPENDENT REPLICATION of H5's mechanism through a different feature
set, and any suppression arm belongs to H5, not here.

SCOPE -- WHAT THIS PROBLEM IS NOT, so it cannot be over-cited later.
* NOT a window-shortening proposal. `ro-window-shortening-20260805` (hr_peak: increment falls to
  exactly 0.000 at W<=2h because max converges to mean) and
  `shortening-the-analysis-window-to-align-with-0f6d56` (cross-subject affect: W=15 is WORSE than
  clock-only) both stand and are not challenged. This problem does not propose changing W. It
  proposes fixing what pooling does to a short recording INSIDE the existing 1440 window.
* NOT a finer patch grid. `that-the-encoder-s-23-token-patch-grid-is-under-bc9aef` measured the
  incumbent 120m/23tok as carrying the MOST linear peak content of four geometries; that stands.
* NOT a re-litigation of E11. `e11-s-supabase-transfer-failure-is-5472c8` refuted coverage
  covariate shift as the mechanism of the dense->supabase transfer failure and its lesson says do
  not spend more on synthetic-coverage domain matching. E11 is genuinely adjacent and RAISES THE
  PRIOR AGAINST this family. Two scope facts keep a residual alive and both must be stated
  whenever this problem is cited: (i) E11 intervened at the HEAD level on frozen L34 features,
  which cannot restore spread that pooling already destroyed upstream; (ii) E11's block masks were
  hr coverage ~U[0.57,0.95], while the cohorts here sit at c=0.019-0.18 -- an order of magnitude
  more extreme, and outside the regime E11 tested.
* The 12 canonical channels, the 1-minute grid, and the 23-token geometry are all held FIXED.

DEPLOYMENT NOTE. optihealth_supabase sits at c=0.667 with ratio 0.5071 -- it loses roughly half its
between-subject spread too, so this is not purely an academic-corpus problem.

Primary diagnostic: `eiv_extent_variance_compression_ratio`. STATUS: RESOLVED 2026-08-21 — see
the update log and synthesis. The mechanism above is confirmed and FIXED end-to-end
(`patch_pool_mode='masked_avg'`); the three follow-on levers (crop augmentation, coverage
conditioning, attention pooling) were each refuted on CPU. Two limits travel with any citation:
the deployment number is NOT established (supabase delta sits ~12x inside its own seed band), and
the end-to-end figure is 0.5630 -> 0.6897, not W5's frozen-token 0.1822 -> 0.8159.

## Synthesis
CLOSED 2026-08-21. All five hypotheses resolved: 2 confirmed, 3 refuted, zero open. The defect was REAL, was fixed END-TO-END, and the three follow-on levers were each killed cheaply on CPU. Read the layers separately -- they are separate claims with separate scopes.

LAYER 1 -- THE ARITHMETIC (W4/W4b, settled). Fill 0.0 IS the corpus median under population normalisation, so presence-blind pooling computes exactly naive = median + c*(present - median): slope 1.0000, intercept 0.00000, R2 1.00000, n=12,503. Consequence: between-subject spread contracts by factor c. CASE keeps 2.85%, popane_s4 1.89%, wesad 8.25%, versus lifesnaps 69.9% and pmdata 85.7%.

LAYER 2 -- ARCHITECTURAL AND REVERSIBLE (exp-w5, SUPPORTS, all four gates, harness reproduced W4b to five decimals). Short-cohort means: naive 0.0804 | trained encoder 0.1822 | random-init 0.1682 | masked re-pool 0.8159 | production 0.5157 | shuffle 0.0362. Trained vs random-init differ by 0.0140, so the defect is POOLING GEOMETRY, not learned weights -- retraining alone could never remove it, which is what made the DENOMINATOR the correct object. Re-weighting the SAME frozen tokens recovered it with zero training and zero new parameters, so the information dies at the pooling step, not at patch construction. w5b confirmed the recovery is signal not magnitude-matched noise (r 0.497 -> 0.727, MAE 8.73 -> 5.23 bpm).

LAYER 3 -- FIXED END-TO-END (exp-w6b, SUPPORTS; H-W6 CONFIRMED). masked_avg implemented at 665fff2, channel-layout bug fixed at 880c332, plumbed profile -> cli_train -> pipeline for the first time. 3 seeds x avg/masked_avg off the frozen ab_ctrl_s43 phase_b, single variable proven by programmatic profile diff, scorer validated against the v10conly record first (median 2.836 vs recorded 2.84, band 0.800 vs 0.80).
  PRIMARY: short-cohort ratio 0.5630 -> 0.6897 at the PRODUCTION stage (bar >=0.60), 7/7 short cohorts improve, long cohorts move <0.03 -- the predicted signature, since the correction is near-inert where coverage is already high.
  DENSE NO-HARM (blocking): 3.575 -> 3.301 bpm. The blast-radius warning in cross_modal.py:260-270 is measured and does NOT materialise.
  DEPLOYMENT: supabase +0.516 bpm, ~12x INSIDE its own 6.29 seed band -> UNINTERPRETABLE. No deployment claim in either direction.
  CALIBRATE AGAINST W5: the frozen-token probe promised 0.1822 -> 0.8159; end-to-end the baseline is already 0.5630 (the projector's cross-attention undoes much of it unaided) and the fix lands 0.6897. The honest number for what TRAINING bought is +0.1267. Quoting 0.8159 as an end-to-end result is a stage error.

LAYER 4 -- THE THREE FOLLOW-ON LEVERS ARE ALL DEAD, each killed on CPU before GPU spend.
  H-W7 crop augmentation REFUTED at its own blocking Stage-0 gate. Paired on 1200 dense days / 81 subjects, the frozen encoder retains 96.4% (avg) and 95.0% (masked_avg) of its scale-free readout skill on 41-minute crops; the gate demanded a >=20% loss. THE DISTINCTION THIS BUYS: there are two separable defects and only one existed. Representation CONTRACTION (scale) was real and is fixed. Readout FRAGILITY on partial windows -- the thing crop augmentation would teach out -- was never real. A representation can be badly scaled and perfectly readable once the scale is corrected.
  H-W9 coverage conditioning REFUTED, and INERT rather than harmful. Ratio moved -0.0048 (masked) / -0.0261 (avg) against a >= +0.05 bar, while SHUFFLED coverage scored 0.6902 -- at or above the true-coverage arm. Wrong c is indistinguishable from right c. Reason: encoder_format_version is with_presence_v1, i.e. the presence flags are ALREADY concatenated as input channels, so the encoder has been fed the mask all along. The algebra was right and the premise was already satisfied.
  H-W8 attention pooling REFUTED, and it takes a load-bearing prior claim with it. On the clean pool (n=2272, 95 subjects, exact reproduction of the ro d55a19 reference): enc RF +0.0382 / LINEAR +0.0263; rand-init RF +0.0443 / LINEAR +0.0312. RANDOM-INIT MATCHES OR BEATS TRAINED IN ALL FOUR CELLS (trained-minus-random -0.0049 linear). Two prior claims die together: the old 'random-init sits exactly on its null so there is no headroom' was an RF-ONLY artifact (under linear it clears at z+4.61), and the trained encoder's momentary-state signal is no better than a random projection of the same input. Changing HOW you pool a representation cannot help when its learned content on that target is nil.

WHAT THIS PROBLEM DOES **NOT** LICENSE. Window (1440), patch grid (120m/23tok) and pooling OPERATOR are untouched: ro-window-shortening-20260805, shortening-the-analysis-window-to-align-with-0f6d56, that-the-encoder-s-23-token-patch-grid-is-under-bc9aef, ro-dual-avg-max-pooling-downstream-20260805 and ro-avgmax-pooling-production-site-20260815 ALL STAND. The masked_avg win is on between-subject SPREAD (scale); W7 separately measured that masked_avg retains slightly LESS within-condition READABILITY on 41-minute crops (0.950 vs 0.964). Those are different axes and not substitutes -- say which one you mean.

SIDE FINDING, SAME POOLING LINE, DIFFERENT TARGET (exp-e4-stage0): the presence-blind pool also destroys ~0.43 R2 of daily EXCURSION, reproducibly across corpora (+0.4344 vs E19's +0.4042 two rebuilds earlier), and max ALONE (+0.7348) matches avgmax (+0.7334) -- the concat's avg half is dead weight. Operator finding on an aggregate target; this problem is a denominator finding on a between-subject target. Do not merge them.

WHERE THE EFFORT SHOULD GO NEXT, and it is not here. The extent axis is closed. H-W8's collapse says state is SUPPLY-limited, not architecture-limited: no further encoder-side state arms until dense induced-stress labels land, grouped by STRESSOR TYPE (arXiv:2405.09563 -- transfer is governed by stressor type, not intensity or device). The one open EMPIRICAL question left on this problem is the deployment sign: all three masked seeds sit above all-but-one avg seed on supabase, which is suggestive and nothing more; establishing it needs >=5 seeds or a paired-row bootstrap on the same 200-row draw.

## Updates
- **2026-08-21T04:53:38+00:00** — RESOLVED 2026-08-21. All five hypotheses closed (2 confirmed / 3 refuted), zero open, zero interpretation debt. The mechanism was measured (W4/W4b), shown architectural and reversible (W5), and FIXED END-TO-END (exp-w6b: short-cohort variance-compression 0.5630 -> 0.6897 at the production stage, dense no-harm 3.575 -> 3.301 bpm, H-W6 CONFIRMED). The three follow-on levers were each eliminated on CPU before GPU spend: crop augmentation (W7 -- the frozen encoder retains 96.4% of its scale-free readout skill on 41-minute crops against a gate demanding a >=20% loss), coverage conditioning (W9 -- INERT: shuffled c scores at or above true c, because encoder_format_version=with_presence_v1 already feeds the mask as input channels), and attention pooling (W8 -- random-init matches or beats trained in all four readout x null cells).

SHIPPED: patch_pool_mode='masked_avg' (665fff2; channel-layout fix 880c332), inert unless explicitly selected, covered by 21 tests including a k-parametrised all-present invariant.

TWO SCOPE LIMITS THAT MUST TRAVEL WITH ANY CITATION. (1) The deployment number is NOT established: supabase +0.516 bpm sits ~12x INSIDE its own 6.29 seed band, so no claim in either direction; establishing the sign needs >=5 seeds or a paired-row bootstrap on the same 200-row draw. (2) The end-to-end figure is 0.5630 -> 0.6897 (+0.1267), NOT W5's frozen-token 0.1822 -> 0.8159 -- the projector's cross-attention already undoes much of the contraction unaided, so quoting the probe number as an end-to-end result is a stage error.

RESIDUAL FILED ELSEWHERE, NOT HERE: W8 established that momentary state is SUPPLY-limited rather than architecture-limited (the trained encoder carries no more state signal than a random projection of the same input, and the older RF-only 'random-init is inert' claim was itself a readout artifact). That belongs to the state/label-supply track.

#extent-heterogeneity #pooling #denominator #wearables #state-supervision #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
