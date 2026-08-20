---
artifacts:
- logs/w2_within_subject.json
- logs/w2_run.log
- scratch/w2_within_subject.py
closed: true
config_note: 'Runner scratch/w2_within_subject.py (CPU-only, joblib across subjects
  with inner n_jobs=1 per the SSOT walk-forward lesson). Corpus [12547,1440,12] ac7d2b1c.
  Targets: lifesnaps_tense (mood_code==6.0) on subjects with >=30 labelled pings;
  dryad stress_level binarized per subject (val > subject median, >= max fallback
  with realized-rate printing — the binarisation trap guard). Splits: expanding-window
  forward chaining within subject, min_history 10 pings, refit per ping (LogisticRegression
  — tiny n per fit). Arms per W {30,120,480,1440}: t0 running rate, t1 persistence,
  t2 clock logistic, t3 HR-feature logistic (hr_features from e16 verbatim, canon_utils
  denorm — NOT the pop.get trap), t4 combined. Output logs/w2_within_subject.json
  incremental. Gates in the hypothesis; runs in parallel with exp-052''s GPU training.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.5
crash_reason: ''
created_at: '2026-08-20T03:36:42+00:00'
diagnostics_measured:
- after: -0.0497
  before: 0.05
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry:
- 'Do NOT buy another sub-daily within-subject peri-ping physiology arm on this corpus:
  8/8 (target x W) cells NEGATIVE with bootstrap CIs excluding zero (lifesnaps_tense
  median skill -0.050..-0.089 across W30-1440, frac_positive 0.16-0.29; dryad_stress
  -0.042..-0.092, frac+ 0.15-0.20). The per-subject trivials (running rate / persistence
  / clock) beat peri-ping HR+clock at EVERY window on BOTH cohorts in the walk-forward
  regime. Combined with E14''s positive on DAILY features (hr_only AUC 0.6133, CI-positive
  over both trivials), within-subject affect skill lives at the DAILY timescale; sub-daily
  N-of-1 forecasting from wrist HR/HRV is closed at this label supply.'
- 'Caveat that scopes but does not soften the null: the t4 arm refits a per-ping logistic
  on as few as 10 history points, so part of the deficit is small-sample estimator
  variance the trivials don''t pay. A shrinkage/partial-pooling estimator could in
  principle close SOME gap - but the g1 bar was +0.05 and the observed medians are
  -0.05 to -0.09: a ~0.10-0.14 AUC swing from estimator choice alone is not plausible
  at these n.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: w2-n-of-1-within-subject-peri-ping-state-d2d708
id: exp-053-w2-n-of-1-within-subject-peri-84959e
interpretation:
  at: '2026-08-20T03:40:12+00:00'
  failure_mode: claim_false
  lesson: 'The state timescale map is now COMPLETE: within-subject sub-daily peri-ping
    physiology carries NO skill over the user''s own trivials at any W (30min-24h),
    8/8 cells negative with CIs excluding zero, walk-forward (deployment-matched)
    regime — while E14''s DAILY features remain the one positive within-subject result.
    Four state regimes, four verdicts: cross-subject (trait-dominated, refuted E16),
    within-window transitions (real but weak, ~1/3 of shallow bound, supply-limited,
    exp-049), within-subject sub-daily (refuted here), within-subject daily (POSITIVE,
    E14). Product implication for eiv annotate: per-user state should come from daily
    aggregates + the user''s own label history; do not build momentary pre-ping physiology
    features. The residual worth carrying: exp-049''s shallow+trained-head ensemble
    idea operates on the within-window regime, not this one, and is unaffected.'
  transfers_to:
  - w2-n-of-1-within-subject-peri-ping-state-d2d708
  - w-sweep-rb-window-size-x-pooling-state-sweep-73469a
key_numbers:
  dryad_W1440_median: -0.0424
  dryad_W30_median: -0.0916
  g1_bar: 0.05
  lifesnaps_W120_median: -0.0497
  lifesnaps_W1440_median: -0.05
  lifesnaps_W30_median: -0.0718
  lifesnaps_W480_median: -0.0891
  n_subjects_dryad: 20.0
  n_subjects_lifesnaps: 31.0
  worst_frac_positive: 0.15
observed_effects:
- delta: -0.0997
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
recipe_fingerprint:
  interventions: analog-state-supervision-ladder
verdict: refutes
---

## Summary
Tests hypothesis [[w2-n-of-1-within-subject-peri-ping-state-d2d708]].

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.05 → after -0.0497  (Δ -0.0997, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The state timescale map is now COMPLETE: within-subject sub-daily peri-ping physiology carries NO skill over the user's own trivials at any W (30min-24h), 8/8 cells negative with CIs excluding zero, walk-forward (deployment-matched) regime — while E14's DAILY features remain the one positive within-subject result. Four state regimes, four verdicts: cross-subject (trait-dominated, refuted E16), within-window transitions (real but weak, ~1/3 of shallow bound, supply-limited, exp-049), within-subject sub-daily (refuted here), within-subject daily (POSITIVE, E14). Product implication for eiv annotate: per-user state should come from daily aggregates + the user's own label history; do not build momentary pre-ping physiology features. The residual worth carrying: exp-049's shallow+trained-head ensemble idea operates on the within-window regime, not this one, and is unaffected.

Transfers to: [[w2-n-of-1-within-subject-peri-ping-state-d2d708]], [[w-sweep-rb-window-size-x-pooling-state-sweep-73469a]]

## Key numbers
- `dryad_W1440_median`: -0.0424
- `dryad_W30_median`: -0.0916
- `g1_bar`: 0.05
- `lifesnaps_W120_median`: -0.0497
- `lifesnaps_W1440_median`: -0.05
- `lifesnaps_W30_median`: -0.0718
- `lifesnaps_W480_median`: -0.0891
- `n_subjects_dryad`: 20
- `n_subjects_lifesnaps`: 31
- `worst_frac_positive`: 0.15

## Artifacts
- `logs/w2_within_subject.json`
- `logs/w2_run.log`
- `scratch/w2_within_subject.py`

## Do not retry
- Do NOT buy another sub-daily within-subject peri-ping physiology arm on this corpus: 8/8 (target x W) cells NEGATIVE with bootstrap CIs excluding zero (lifesnaps_tense median skill -0.050..-0.089 across W30-1440, frac_positive 0.16-0.29; dryad_stress -0.042..-0.092, frac+ 0.15-0.20). The per-subject trivials (running rate / persistence / clock) beat peri-ping HR+clock at EVERY window on BOTH cohorts in the walk-forward regime. Combined with E14's positive on DAILY features (hr_only AUC 0.6133, CI-positive over both trivials), within-subject affect skill lives at the DAILY timescale; sub-daily N-of-1 forecasting from wrist HR/HRV is closed at this label supply.
- Caveat that scopes but does not soften the null: the t4 arm refits a per-ping logistic on as few as 10 history points, so part of the deficit is small-sample estimator variance the trivials don't pay. A shrinkage/partial-pooling estimator could in principle close SOME gap - but the g1 bar was +0.05 and the observed medians are -0.05 to -0.09: a ~0.10-0.14 AUC swing from estimator choice alone is not plausible at these n.

## Notes
VERDICT: REFUTES, cleanly and with power. g1 FAIL: median per-subject skill NEGATIVE in all 8 cells (range -0.042..-0.092), every bootstrap CI excludes zero FROM ABOVE — not an underpowered null but a powered negative: the strongest per-subject trivial beats peri-ping physiology everywhere. g2 FAIL: frac_positive 0.15-0.29 (needed 0.60); sign-test p > 0.99 in all cells. g3 (committed direction: within-subject W-profile flattens or inverts vs cross-subject monotone rise): PARTIALLY CONFIRMED — the profile is flat-to-noisy with no monotone rise (lifesnaps: -0.072 @30, -0.050 @120, -0.089 @480, -0.050 @1440), consistent with the trait-explanation of E16's cross-subject W-profile.

WHAT THIS SETTLES. Within-subject state now has a complete timescale map: DAILY features carry real skill (E14: +0.073/+0.120 over trivials, CI-positive); SUB-DAILY peri-ping physiology carries NONE over the person's own history at any W from 30 min to 24 h, in the deployment-matched (walk-forward) regime. The N-of-1 product implication: a per-user state feature should be built from daily aggregates + the user's own label history, not from momentary pre-ping physiology. This also closes the last open state-side hypothesis family: cross-subject (E16 refuted), within-window transitions (weak, supply-limited, exp-049), within-subject sub-daily (this, refuted), within-subject daily (E14, positive).

#state-supervision #n-of-1 #wearables #control #epoch-resolution #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
