---
ambition_class: null
created_at: '2026-08-20T21:15:13+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'PRECONDITION: H-W5 must have PASSED first (contraction confirmed to
    survive into the representation AND masked re-pooling shown to have headroom on
    frozen tokens). If H-W5 refuted, this arm is not funded and must not be run.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g0-hw5-passed
  mde: 0.6
- blocking: true
  description: NO-HARM ON THE DENSE POPULATION, BLOCKING. eiv_dense128_hr_raw_mae_bpm
    must not regress by more than 1.0 bpm vs the matched control arm. Mask-weighted
    pooling changes the output for EVERY input with any missing channel -- which is
    nearly every input -- so the dense population is the population at risk. cross_modal.py:260-270
    warns about exactly this. A short-cohort win bought with a dense regression is
    not a win.
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g1-dense-no-harm
  mde: 1.0
- blocking: true
  description: SEED DISCIPLINE. >=3 seeds, report per-seed values and the worst seed,
    never a single point. The deployment MAE seed band is 6.29 bpm (eiv_supabase_seed_band_mae_bpm)
    and EXCEEDS the 4.02 trivial baseline, so a single-seed comparison on that axis
    is uninterpretable by the vault's own recorded rule. Any claimed improvement must
    exceed the measured band or be a difference of means significant against it.
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-seed-band-respected
  mde: 6.29
- blocking: true
  description: SINGLE VARIABLE. yaml-diff the two profile bodies and assert the ONLY
    differences are the pooling mode, the seed, and free-text notes. If the arm required
    any additional knob to run, declare it inside the intervention description and
    pre-queue the isolation arm before reading the result.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g3-single-variable-diff
  mde: 0.01
- blocking: false
  description: EXCURSION GUARD. eiv_peak_excursion_increment_r2 must not fall by more
    than its recipe band 0.0457. Presence-weighting alters what the pool sees on partially-covered
    days, and hr_peak is the one axis where the incumbent is genuinely strong (production
    increment +0.7211); silently trading it away for short-cohort spread would be
    a bad deal made invisibly.
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: g4-excursion-guard
  mde: 0.0457
id: h-w6-mask-weighted-pooling-restores-short-9da5b6
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: 'PRIMARY, and deliberately the mechanism-decisive metric rather
    than a downstream one: short-cohort ratio at the PRODUCTION stage rises from the
    measured 0.019-0.28 to >=0.60 under mask-weighted pooling, with mean coverage
    c reported alongside per cohort as the null model.'
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: none
  magnitude_note: HONEST NONE. On the dense population (c>0.75) mask-weighting is
    nearly a no-op arithmetically, so no change is predicted. Declaring `none` here
    is calibration-relevant and stops the arm being sold as a general accuracy lever.
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'SECONDARY and explicitly UNDERPOWERED at <=3 seeds: supabase sits
    at c=0.667 / ratio 0.5071, so it should benefit, but the seed band is 6.29 bpm
    and exceeds the 4.02 trivial baseline. Directional only; no deployment claim will
    be made from this.'
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
reopen_reason: ''
reopened_from: null
status: testing
supersedes: null
topic_tags:
- extent-heterogeneity
- pooling
- padding
- deployment-distribution
- wearables
- denominator
- state-supervision
---

## Statement
H-W6 (MASK-WEIGHTED POOLING RESTORES SHORT-COHORT SPREAD END-TO-END). Conditional on H-W5 passing. ONE TARGET: the variance-compression ratio at the production stage. Deliberately NOT bundled with any state/affect target -- exp-e17 closed `null` because it bundled two targets behind one set of blocking gates, and that mistake is not being repeated here.

CLAIM. Replacing the presence-blind cross-patch pool with a mask-weighted pool (weight each patch by its measured fraction, normalise by the sum of weights) removes the (1-c) contraction term by construction and raises eiv_extent_variance_compression_ratio on the seven short cohorts from a measured 0.019-0.28 to >=0.60, WITHOUT regressing the dense population.

WHY THE OBVIOUS OBJECTION IS ANSWERED IN ADVANCE. cross_modal.py:260-270 says presence-weighting was rejected because it 'would move the output of every input that has a missing channel, which is every checkpoint and every recorded result in the repo'. That is a correct statement about BLAST RADIUS, not a measurement that the change is harmful -- and the same comment ends 'If it is wanted, it is its own change with its own control arm.' This hypothesis IS that control arm. The blast radius is exactly why the dense no-harm gate is blocking.

WHAT WOULD MAKE ME WRONG, stated before the run. (a) If the dense population regresses beyond 1.0 bpm, the contraction was load-bearing -- the model had LEARNED to expect the diluted scale, and correcting it invalidates the learned calibration. That is a real and plausible outcome and it refutes the arm even if the short cohorts improve. (b) If the short-cohort ratio rises but nothing downstream moves, the honest conclusion is that spread was never the binding constraint and the extent family should be retired in favour of label supply -- which the stressor-type literature (arXiv:2405.09563) independently argues.

COST: ~2h GPU on the 5070 for a Phase-C arm off a fixed Phase-B checkpoint, x3 seeds. The CPU-only ratio measurement is free and comes first.

## Rationale


## Plan


## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (none).
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g0-hw5-passed` (blocking): PRECONDITION: H-W5 must have PASSED first (contraction confirmed to survive into the representation AND masked re-pooling shown to have headroom on frozen tokens). If H-W5 refuted, this arm is not funded and must not be run.
Gate `g1-dense-no-harm` (blocking): NO-HARM ON THE DENSE POPULATION, BLOCKING. eiv_dense128_hr_raw_mae_bpm must not regress by more than 1.0 bpm vs the matched control arm. Mask-weighted pooling changes the output for EVERY input with any missing channel -- which is nearly every input -- so the dense population is the population at risk. cross_modal.py:260-270 warns about exactly this. A short-cohort win bought with a dense regression is not a win.
Gate `g2-seed-band-respected` (blocking): SEED DISCIPLINE. >=3 seeds, report per-seed values and the worst seed, never a single point. The deployment MAE seed band is 6.29 bpm (eiv_supabase_seed_band_mae_bpm) and EXCEEDS the 4.02 trivial baseline, so a single-seed comparison on that axis is uninterpretable by the vault's own recorded rule. Any claimed improvement must exceed the measured band or be a difference of means significant against it.
Gate `g3-single-variable-diff` (blocking): SINGLE VARIABLE. yaml-diff the two profile bodies and assert the ONLY differences are the pooling mode, the seed, and free-text notes. If the arm required any additional knob to run, declare it inside the intervention description and pre-queue the isolation arm before reading the result.
Gate `g4-excursion-guard` (advisory): EXCURSION GUARD. eiv_peak_excursion_increment_r2 must not fall by more than its recipe band 0.0457. Presence-weighting alters what the pool sees on partially-covered days, and hr_peak is the one axis where the incumbent is genuinely strong (production increment +0.7211); silently trading it away for short-cohort spread would be a bad deal made invisibly.

#extent-heterogeneity #pooling #padding #deployment-distribution #wearables #denominator #state-supervision #optihealth-eiv #data-contract-violation #matched-baseline
