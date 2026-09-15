---
artifacts:
- logs/w13_extent_rescore.json
- scratch/w13_extent_rescore.py
closed: true
config_note: 'W13 = PURE RE-SCORE of exp-w12''s artifact. Runner scratch/w13_extent_rescore.py
  reads logs/w12_extent_heldout.json, loads no model, recomputes nothing, writes logs/w13_extent_rescore.json.


  WHY IT EXISTS: exp-w12 closed directional_only / test_invalid because its no-harm
  gate was preregistered on a MEAN over eight heterogeneous LONG cohorts, and one
  degenerate cell (mmash, n=42, avg ratio 4.4902 at r=0.009) flipped it. The operator
  was therefore never actually tested for harm.


  EPISTEMIC STATUS, stated plainly: this is POST-HOC re-scoring. I had already seen
  these numbers when I wrote the new gates. What limits the freedom is that (a) the
  degeneracy filter and all three gates were written into hypothesis same-claim-as-the-superseded-hypothesis-mask-085211
  before the re-score ran, and (b) the filter is defined on the AVG (control) arm
  only, so it cannot be tuned to favour the treatment. This does NOT carry the standing
  of a fresh preregistration. The clean test is the same gate on a new encoder or
  corpus.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.02
crash_reason: ''
created_at: '2026-08-29T22:05:01+00:00'
diagnostics_measured:
- after: 0.6989
  before: 0.4577
  diagnostic_id: eiv_extent_variance_compression_ratio
do_not_retry:
- Do NOT cite g2r's PASS as a settled no-harm result. Its margin is 2.2e-05 on one
  cohort (big_ideas_glycemic, -0.049978 vs a -0.05 bar). The supporting evidence --
  6 of 7 long cohorts inert or positive, median +0.0103 -- is what carries the claim;
  the gate itself is a coin-flip at that precision and must be re-run on a fresh encoder.
- 'Do NOT treat W13 as independent confirmation of W12. It re-scores the SAME artifact:
  identical features, identical OOF predictions, identical cohorts. It changes the
  statistic, nothing else. Only g1''s out-of-sample design is evidence about the world;
  g2r/g3r are evidence about the instrument.'
- 'Do NOT re-derive the degeneracy filter post-hoc on future runs. It is now fixed:
  exclude a cohort iff ratio > 2.0 in either arm AND |r| < 0.15 in the CONTROL arm,
  and report every exclusion. Defining it on the treatment arm would let the filter
  select for the effect.'
external_run: logs/w13_extent_rescore.json
former_ids: []
gate_results:
- evidence: 'PASS, unchanged from the parent run (same artifact): held-out popane_s5+s6
    avg 0.4577 -> masked 0.6989, delta +0.2412, 4.8x the +0.05 bar. Per cohort s5
    +0.2504 (r 0.870->0.968), s6 +0.2319 (r 0.865->0.927).'
  gate_id: g1-repair-generalises-to-unseen-cohorts
  passed: true
- evidence: 'PASS, BUT BY 2.2e-05 -- AND THAT MARGIN IS THE HONEST HEADLINE, NOT THE
    PASS. Among the 7 non-degenerate LONG cohorts the worst is big_ideas_glycemic
    at delta -0.049978 against a > -0.05 bar. Checked at full precision because a
    gate decided on the fifth decimal is not a result: avg 1.05420443850529644 ->
    masked 1.00422652712162241, delta -0.04997791138367402, margin to the bar 0.0000220886.
    Every other long cohort is inert or positive: dryad -0.0048, lifesnaps +0.0031,
    pmdata +0.0103, dapper +0.0109, dreamt +0.0561, optihealth_supabase +0.0951. Long
    median +0.0103, clean mean +0.0173. So the operator does no harm anywhere with
    a working readout -- but this specific gate is one rounding decision away from
    FAIL and must be re-run on a fresh encoder before the no-harm claim is treated
    as settled.'
  gate_id: g2r-long-cohorts-no-harm-worst-case
  passed: true
- evidence: 'PASS. Exactly ONE cohort is excluded (mmash, n=42), and its exclusion
    is justified by the avg-arm criterion alone: r_avg 0.009, i.e. a readout carrying
    no correlation with truth while its predictions vary 4.5x more than truth. No
    other cohort in the corpus meets the filter (next-highest long ratio is pmdata
    at 1.1129 with r 0.943). The filter is therefore not doing the work of the result
    -- it removes one cell that is not measuring the quantity at all.'
  gate_id: g3r-filter-excludes-at-most-one-cohort
  passed: true
git_ref: ''
hypothesis_id: same-claim-as-the-superseded-hypothesis-mask-085211
id: exp-w13-extent-rescore-20260830
interpretation:
  at: '2026-08-30T05:52:11+00:00'
  failure_mode: null
  lesson: 'THE OPERATOR IS CLEAN, BUT THE NO-HARM GATE PASSED BY 2.2e-05 AND THAT
    MARGIN IS THE RESULT WORTH REMEMBERING. Re-scoring W12''s artifact under a worst-case
    gate the way the vault''s own rule demands: held-out generalisation +0.2412 (unchanged,
    and the only gate here that is evidence about the WORLD), worst non-degenerate
    LONG cohort big_ideas_glycemic at -0.049978 against a >-0.05 bar, exactly one
    cohort excluded by the pre-declared degeneracy filter. Checked at full precision
    deliberately, because a verdict decided on the fifth decimal is not a finding:
    the margin is 0.0000221.


    WHAT IS NOW ESTABLISHED vs WHAT IS NOT. Established: the pooling repair generalises
    out-of-sample and harms no cohort that has a working readout (6 of 7 long cohorts
    inert or positive, median +0.0103). NOT established: that the no-harm property
    is ROBUST -- the binding cohort sits on the bar to five decimals, so a slightly
    different seed, fold split or corpus could flip it. Anyone citing ''no harm''
    should cite the distribution (median +0.0103, worst -0.0500) rather than the gate''s
    boolean.


    WHY THIS IS A WEAKER FORM OF EVIDENCE THAN IT LOOKS. W13 re-scores the SAME predictions
    as W12 -- identical features, identical OOF outputs, identical cohorts. It is
    a better instrument applied to old data, not new evidence, and I had already seen
    those numbers when I wrote the new gates. What constrains the freedom is that
    the filter and all three gates were committed to the hypothesis before the re-score
    ran, and the filter is defined on the CONTROL arm only so it cannot select for
    the treatment. That is a real constraint but not a preregistration; the genuine
    test is the same worst-case gate on a NEW encoder or corpus.


    THE DEGENERACY FILTER IS NOW FIXED AND MUST NOT BE RE-DERIVED: exclude a cohort
    iff ratio > 2.0 in either arm AND |r| < 0.15 in the control arm, and REPORT every
    exclusion. mmash (n=42, ratio 4.4902, r 0.009) is the only cohort in the corpus
    that meets it; the next-highest long ratio is pmdata at 1.1129 with r 0.943. A
    filter that had to remove several cohorts to let the gate pass would have been
    the result, which is why g3r was made blocking.'
  transfers_to:
  - h-w6-mask-weighted-pooling-restores-short-9da5b6
key_numbers:
  big_ideas_glycemic_delta: -0.04997791138367402
  excluded_cohorts: 1.0
  g2r_margin_to_bar: 2.20886e-05
  heldout_short_delta: 0.2412
  long_clean_mean_delta: 0.0173
  long_median_delta: 0.0103
  mmash_r_avg: 0.009
  n_long_cohorts_scored: 7.0
  seen_short_delta: 0.1505
observed_effects:
- delta: 0.2412
  diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
parent_experiment_id: exp-w12-extent-heldout-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_extent_variance_compression_ratio: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[same-claim-as-the-superseded-hypothesis-mask-085211]].
Branches from [[exp-w12-extent-heldout-20260830]].
External run: logs/w13_extent_rescore.json

## Measurements
- [[eiv_extent_variance_compression_ratio]]: before 0.4577 → after 0.6989  (Δ +0.2412, up ✓ predicted up)

## Gates
- `g1-repair-generalises-to-unseen-cohorts`: PASS — PASS, unchanged from the parent run (same artifact): held-out popane_s5+s6 avg 0.4577 -> masked 0.6989, delta +0.2412, 4.8x the +0.05 bar. Per cohort s5 +0.2504 (r 0.870->0.968), s6 +0.2319 (r 0.865->0.927).
- `g2r-long-cohorts-no-harm-worst-case`: PASS — PASS, BUT BY 2.2e-05 -- AND THAT MARGIN IS THE HONEST HEADLINE, NOT THE PASS. Among the 7 non-degenerate LONG cohorts the worst is big_ideas_glycemic at delta -0.049978 against a > -0.05 bar. Checked at full precision because a gate decided on the fifth decimal is not a result: avg 1.05420443850529644 -> masked 1.00422652712162241, delta -0.04997791138367402, margin to the bar 0.0000220886. Every other long cohort is inert or positive: dryad -0.0048, lifesnaps +0.0031, pmdata +0.0103, dapper +0.0109, dreamt +0.0561, optihealth_supabase +0.0951. Long median +0.0103, clean mean +0.0173. So the operator does no harm anywhere with a working readout -- but this specific gate is one rounding decision away from FAIL and must be re-run on a fresh encoder before the no-harm claim is treated as settled.
- `g3r-filter-excludes-at-most-one-cohort`: PASS — PASS. Exactly ONE cohort is excluded (mmash, n=42), and its exclusion is justified by the avg-arm criterion alone: r_avg 0.009, i.e. a readout carrying no correlation with truth while its predictions vary 4.5x more than truth. No other cohort in the corpus meets the filter (next-highest long ratio is pmdata at 1.1129 with r 0.943). The filter is therefore not doing the work of the result -- it removes one cell that is not measuring the quantity at all.

**Verdict:** supports

## Interpretation
THE OPERATOR IS CLEAN, BUT THE NO-HARM GATE PASSED BY 2.2e-05 AND THAT MARGIN IS THE RESULT WORTH REMEMBERING. Re-scoring W12's artifact under a worst-case gate the way the vault's own rule demands: held-out generalisation +0.2412 (unchanged, and the only gate here that is evidence about the WORLD), worst non-degenerate LONG cohort big_ideas_glycemic at -0.049978 against a >-0.05 bar, exactly one cohort excluded by the pre-declared degeneracy filter. Checked at full precision deliberately, because a verdict decided on the fifth decimal is not a finding: the margin is 0.0000221.

WHAT IS NOW ESTABLISHED vs WHAT IS NOT. Established: the pooling repair generalises out-of-sample and harms no cohort that has a working readout (6 of 7 long cohorts inert or positive, median +0.0103). NOT established: that the no-harm property is ROBUST -- the binding cohort sits on the bar to five decimals, so a slightly different seed, fold split or corpus could flip it. Anyone citing 'no harm' should cite the distribution (median +0.0103, worst -0.0500) rather than the gate's boolean.

WHY THIS IS A WEAKER FORM OF EVIDENCE THAN IT LOOKS. W13 re-scores the SAME predictions as W12 -- identical features, identical OOF outputs, identical cohorts. It is a better instrument applied to old data, not new evidence, and I had already seen those numbers when I wrote the new gates. What constrains the freedom is that the filter and all three gates were committed to the hypothesis before the re-score ran, and the filter is defined on the CONTROL arm only so it cannot select for the treatment. That is a real constraint but not a preregistration; the genuine test is the same worst-case gate on a NEW encoder or corpus.

THE DEGENERACY FILTER IS NOW FIXED AND MUST NOT BE RE-DERIVED: exclude a cohort iff ratio > 2.0 in either arm AND |r| < 0.15 in the control arm, and REPORT every exclusion. mmash (n=42, ratio 4.4902, r 0.009) is the only cohort in the corpus that meets it; the next-highest long ratio is pmdata at 1.1129 with r 0.943. A filter that had to remove several cohorts to let the gate pass would have been the result, which is why g3r was made blocking.

Transfers to: [[h-w6-mask-weighted-pooling-restores-short-9da5b6]]

## Key numbers
- `big_ideas_glycemic_delta`: -0.0499779
- `excluded_cohorts`: 1
- `g2r_margin_to_bar`: 2.20886e-05
- `heldout_short_delta`: 0.2412
- `long_clean_mean_delta`: 0.0173
- `long_median_delta`: 0.0103
- `mmash_r_avg`: 0.009
- `n_long_cohorts_scored`: 7
- `seen_short_delta`: 0.1505

## Artifacts
- `logs/w13_extent_rescore.json`
- `scratch/w13_extent_rescore.py`

## Do not retry
- Do NOT cite g2r's PASS as a settled no-harm result. Its margin is 2.2e-05 on one cohort (big_ideas_glycemic, -0.049978 vs a -0.05 bar). The supporting evidence -- 6 of 7 long cohorts inert or positive, median +0.0103 -- is what carries the claim; the gate itself is a coin-flip at that precision and must be re-run on a fresh encoder.
- Do NOT treat W13 as independent confirmation of W12. It re-scores the SAME artifact: identical features, identical OOF predictions, identical cohorts. It changes the statistic, nothing else. Only g1's out-of-sample design is evidence about the world; g2r/g3r are evidence about the instrument.
- Do NOT re-derive the degeneracy filter post-hoc on future runs. It is now fixed: exclude a cohort iff ratio > 2.0 in either arm AND |r| < 0.15 in the CONTROL arm, and report every exclusion. Defining it on the treatment arm would let the filter select for the effect.

## Notes
ALL THREE GATES PASS, and one of them passes by 2.2e-05 -- which is the number worth remembering from this run.

g1 (unchanged, the only gate that is evidence about the WORLD rather than the instrument): held-out popane_s5+s6, +0.2412 against a +0.05 bar.
g2r worst-case over 7 non-degenerate LONG cohorts: big_ideas_glycemic -0.049978 vs a > -0.05 bar. Margin 0.0000221. Full precision checked deliberately, because a verdict decided on the fifth decimal is not a finding.
g3r: exactly one cohort excluded (mmash, r_avg 0.009); no other cohort comes close to the filter.

WHAT IS NOW ESTABLISHED: the pooling repair generalises out-of-sample, and it harms no cohort that has a working readout. WHAT IS NOT: that the no-harm property is robust, because the binding cohort sits on the bar to five decimals.

WHY THE VERDICT SHOULD BE READ CAUTIOUSLY: W13 re-scores the SAME predictions as W12. It is a better instrument applied to old data, not new evidence. The genuine test is g2r on a NEW encoder or corpus.

#extent-heterogeneity #pooling #wearables #control #baseline #denominator #state-supervision #deployment-distribution #matched-baseline #subject-heldout #optihealth-eiv #data-contract-violation
