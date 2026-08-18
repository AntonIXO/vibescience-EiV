---
created_at: '2026-08-14T22:21:37+00:00'
former_ids: []
gates:
- blocking: true
  description: 'PRIMARY. Median per-subject skill = AUC(t4: peri-ping HR/HRV + clock)
    MINUS max(t0 within-subject running positive rate, t1 persistence, t2 clock-only),
    over powered subjects at the best W, must be >= +0.05 with a 2000-sample bootstrap
    CI95 on the median excluding 0. MDE 0.05: E14''s within-subject daily-feature
    effect was +0.0733 [0.045,0.103] over median-forward, so this design is powered
    at that scale. A median skill under +0.05 with a CI spanning zero is a POWERED
    NULL and closes the state question in BOTH grouping regimes.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_median_subject_skill_over_strongest_trivial
  mde: 0.05
- blocking: true
  description: CONSISTENCY. Fraction of powered subjects with positive skill over
    their own strongest trivial must be >= 0.60 at the best W. A median lifted by
    a few subjects while most are negative is a per-subject lottery, not a deployable
    head — and the product ships per user, so per-user reliability is the requirement,
    not a cohort average.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_fraction_subjects_positive
  mde: 0.6
- blocking: false
  description: 'SHAPE. The within-subject W-profile differs from the E16 cross-subject
    profile, which rose monotonically with W and peaked at 1440. Committed direction:
    within-subject the profile FLATTENS or favours SHORTER W, because the long-window
    advantage cross-subject is trait-like daily-rhythm structure that is constant
    within a person and therefore uninformative once the person is fixed. Informational.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_within_subject_W_profile_shape
  mde: null
id: hyp-e18-within-subject-state-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'PRIMARY. Median per-subject skill of [peri-ping HR/HRV + clock]
    over the STRONGEST of three trivials (within-subject running positive rate, persistence,
    clock-only) exceeds +0.05 AUC with a bootstrap CI95 on the median excluding zero,
    on >=60% of powered subjects. Anchor: E14 measured within-subject daily-feature
    skill at +0.0733 [0.045,0.103] over median-forward and +0.1200 [0.084,0.158] over
    persistence, so an effect of this size is detectable if it exists.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- wearables
- n-of-1
- baseline
- control
- epoch-resolution
---

## Statement
H-E18 (N-of-1 state head). The cross-subject affect ceiling measured in E16/E17 is a BETWEEN-SUBJECT design artifact, not an information ceiling: within a single subject, where the per-user constant is absorbed by construction, peri-ping HR/HRV features carry momentary state information that beats every trivial available to that same subject — their own running base rate, their own previous label, and the clock.

PREDICTION: median per-subject skill of [peri-ping HR/HRV + clock] over the STRONGEST per-subject trivial exceeds +0.05 AUC with a bootstrap CI95 on the median excluding zero, and holds on >= 60% of powered subjects.

IF CONFIRMED: the deployable product lever is a per-user state head (PerUserHeadStore), and the state slot survives — though it remains supply-blocked on optihealth_supabase, which still carries ZERO state labels.
IF REFUTED (powered null): state detection is dead on this corpus in BOTH grouping regimes, and the recommendation is to DROP the state slot from the emitted schema rather than ship a noise generator. That is the decision this arm exists to make.

## Rationale
E16 and E17 hit the same ceiling from opposite directions — peri-ping summary statistics and patch-token pooling both land ~0.58-0.60 AUC against a per-user constant of 0.7381. That constant IS the trait signal. In the N-of-1 regime the subject is fixed, so the constant is absorbed by construction and cannot be used as a shortcut; the cross-subject ceiling therefore does not bound this question, and the rule-out `shortening-the-analysis-window-to-align-with-0f6d56` explicitly reserves '(a) N-of-1 / within-subject state heads with a within-subject baseline' as NOT covered.

There is prior positive evidence: E14 found within-subject affect skill EXISTS in rich HR-only DAILY features (lifesnaps hr_only AUC 0.6133, CI-positive over both trivials) and its closure states sub-daily/temporal features remain untested. E16 adds that with the clock controlled, HR clears a within-subject permutation null on two targets (TIRED +0.0340 p=0.000; dryad +0.0998 p=0.000).

The honest counter-consideration, recorded before running: the deep-research sweep found NO personalization architecture reporting a clean win over a per-user constant at comparable AUC deltas, and per-subject sample sizes here are small (~30-70 pings), so variance is high and the walk-forward design is deliberately conservative. A powered null here would close state detection in BOTH grouping regimes and justify dropping the state slot from the emitted schema — which is a genuinely useful outcome, not a failure.

## Plan
scratch/e18_within_subject_state.py — pure CPU, no GPU, no model weights. Runs in parallel with the GPU encoder-readout arm.

PER SUBJECT, NEVER POOLED. Forward-chaining expanding window: for each ping t beyond an initialisation prefix (max(10, 40% of that subject's pings)), fit on that subject's pings 0..t-1 and predict ping t. RF(200, min_samples_leaf=3, seed 0). NaNs median-imputed from the TRAINING prefix only.

ARMS per W in {30, 120, 480, 1440} minutes:
  t0 within-subject constant = running positive rate over 0..t-1   TRIVIAL 1
  t1 persistence             = previous ping's label               TRIVIAL 2
  t2 clock-only              = hour sin/cos + minute-of-day        CONFOUND FLOOR
  t3 peri-ping HR/HRV over [ping-W, ping), strictly causal
  t4 t3 + clock                                                    PRIMARY ARM
PRIMARY METRIC = AUC(t4) - max(AUC(t0), AUC(t1), AUC(t2)) per subject, then the MEDIAN over subjects with a 2000-sample bootstrap CI95, plus the fraction of subjects positive.

POWER FILTER (pre-committed): a subject needs >=25 usable pings, both classes present overall AND in its held-out tail, and >=10 scored pings. Subjects failing this are excluded and counted, never silently dropped.

TARGETS: lifesnaps TENSE/ANXIOUS, lifesnaps TIRED, dryad stress-VAS-above-own-median.
EXCLUDED as a pre-commitment: pmdata (07-09h retrospective morning diary, top-epoch share 0.060 — not momentary).

Output: logs/e18_within_subject_state.json (per-subject rows retained so the distribution is auditable, not just its median).

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_median_subject_skill_over_strongest_trivial` (blocking): PRIMARY. Median per-subject skill = AUC(t4: peri-ping HR/HRV + clock) MINUS max(t0 within-subject running positive rate, t1 persistence, t2 clock-only), over powered subjects at the best W, must be >= +0.05 with a 2000-sample bootstrap CI95 on the median excluding 0. MDE 0.05: E14's within-subject daily-feature effect was +0.0733 [0.045,0.103] over median-forward, so this design is powered at that scale. A median skill under +0.05 with a CI spanning zero is a POWERED NULL and closes the state question in BOTH grouping regimes.
Gate `g2_fraction_subjects_positive` (blocking): CONSISTENCY. Fraction of powered subjects with positive skill over their own strongest trivial must be >= 0.60 at the best W. A median lifted by a few subjects while most are negative is a per-subject lottery, not a deployable head — and the product ships per user, so per-user reliability is the requirement, not a cohort average.
Gate `g3_within_subject_W_profile_shape` (advisory): SHAPE. The within-subject W-profile differs from the E16 cross-subject profile, which rose monotonically with W and peaked at 1440. Committed direction: within-subject the profile FLATTENS or favours SHORTER W, because the long-window advantage cross-subject is trait-like daily-rhythm structure that is constant within a person and therefore uninformative once the person is fixed. Informational.

#wearables #n-of-1 #baseline #control #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
