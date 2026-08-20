---
created_at: '2026-08-20T21:12:15+00:00'
former_ids: []
id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
paper_refs: []
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
status: open
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
updated_at: '2026-08-20T22:15:51+00:00'
updates: []
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

Primary diagnostic: `eiv_extent_variance_compression_ratio`. STATUS: open.

## Synthesis
STATE 2026-08-20 EVENING. The mechanism is MEASURED, CONFIRMED AT THE REPRESENTATION, and shown REVERSIBLE at zero cost. The end-to-end test is running. Read the three layers separately.

LAYER 1 -- THE ARITHMETIC (W4/W4b, settled). Fill 0.0 IS the corpus median, so presence-blind pooling computes exactly naive = median + c*(present - median): slope 1.0000, intercept 0.00000, R2 1.00000, n=12,503. The consequence is a CONTRACTION of between-subject spread by factor c. CASE keeps 2.85%, popane_s4 1.89%, wesad 8.25%, against lifesnaps 69.9% and pmdata 85.7%.

LAYER 2 -- IT SURVIVES INTO THE REPRESENTATION AND IS REVERSIBLE (exp-w5-three-stage-contraction-20260820, SUPPORTS, all four preregistered gates passed, harness reproduced W4b to five decimals). Short-cohort means:
  naive 1440-mean 0.0804 | trained encoder 0.1822 | random-init 0.1682 | MASKED RE-POOL 0.8159 | production stage 0.5157 | shuffle 0.0362
  g1 (bar <0.5): contraction survives into the encoder's own tokens.
  g2 (bar <=0.15): trained vs random-init differ by 0.0140 -> the defect is ARCHITECTURAL, a property of presence-blind pooling geometry, NOT of learned weights. This is what makes the denominator the correctly-targeted object; retraining alone would not remove it.
  g3 (bar >=0.60): re-weighting the SAME FROZEN tokens recovers 0.1822 -> 0.8159 with zero training and zero new parameters. So the information was never destroyed at patch construction -- it is destroyed at the pooling step. That branch of the decision rule is CLOSED.
  g4: shuffle 0.0362, so not a readout artifact.
Unpreregistered follow-up (w5b) because three cohorts overshot ratio 1.0: the recovery is real, not magnitude-matched noise. Within-cohort r 0.497 -> 0.727 and MAE 8.73 -> 5.23 bpm on short cohorts; optihealth_supabase r 0.630 -> 0.962 and MAE 7.46 -> 1.67 bpm. NOTE that supabase figure is a PROBE on frozen features, NOT an emission, and the emission seed band (6.29 bpm, itself exceeding its 4.02 trivial baseline) still governs any deployment claim.

LAYER 3 -- END-TO-END, IN FLIGHT (exp-w6-masked-pooling-20260820). masked_avg implemented at commit 665fff2 and plumbed profile -> cli_train -> pipeline, which it never was before. 6 chains (3 seeds x avg/masked_avg) off the frozen ab_ctrl_s43 phase_b, single variable proven by programmatic profile diff. NOT YET SCORED -- no claim about emission may be made from this problem until it closes.

THE PRE-FLIGHT IS PART OF THE RESULT. scratch/w6_preflight.py caught two bugs on real corpus rows before any GPU was spent, each of which would have produced a confidently wrong answer: (i) channel-MEAN presence read 0.4155 on a pmdata day with 98.7% HR coverage, which would have diluted the correction on exactly the dense rows the no-harm gate protects; (ii) collapsing to one global weighted mean broke k>1, and the shipped config runs k=4, so the all-present invariant diverged by 1.53 while k=1-only unit tests passed. Both fixed, a k-parametrised invariant test added, full suite 770 passed.

WHAT REMAINS UNTOUCHED AND MUST NOT BE OVER-CITED. Nothing here changes the window (W stays 1440), the patch grid (120m/23tok), or the pooling OPERATOR. ro-window-shortening-20260805, shortening-the-analysis-window-to-align-with-0f6d56, that-the-encoder-s-23-token-patch-grid-is-under-bc9aef, ro-dual-avg-max-pooling-downstream-20260805 and ro-avgmax-pooling-production-site-20260815 all STAND. The E11 lesson (do not spend more on synthetic-coverage domain matching) still raises the prior against H-W7 and its three scope carve-outs must be restated whenever W7 is cited.

SIDE FINDING WORTH CARRYING (exp-e4-stage0-excursion-gate-20260820, same pooling line, DIFFERENT target): the same presence-blind pool also destroys ~0.43 R2 of daily EXCURSION, reproducibly across corpora (+0.4344 here vs E19's +0.4042 two rebuilds earlier), and max-pooling ALONE (+0.7348) matches avgmax (+0.7334) -- the concat's avg half is dead weight. That is an operator finding on an aggregate target; this problem is a denominator finding on a between-subject target. Do not merge them.

HONEST RESIDUAL: if W6's dense no-harm gate fails, the contraction was LOAD-BEARING (the model had learned to expect the diluted scale) and the correct reading is that the fix must move upstream of Phase C, not that the mechanism was wrong.

#extent-heterogeneity #pooling #denominator #wearables #state-supervision #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
