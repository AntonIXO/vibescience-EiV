---
artifacts: []
closed: true
config_note: 'Successor to exp-w6-masked-pooling-20260820 (aborted: 24-vs-12 channel
  bug, fixed at 880c332 and verified by a 12-step real-path run, RC=0). Runner scratch/run_w6_treatment.sh;
  the three CONTROL arms from the first pass were NOT retrained because the control
  recipe did not change (masked_avg is provably inert unless selected), which preserves
  the pairing and avoids adding control seed noise.


  ALL SIX ARMS COMPLETED CLEAN: grep for OutOfMemoryError|Traceback across all six
  chain logs returns 0 for every arm. 12 emission captures on disk (3 seeds x 2 modes
  x {supabase 200-row, dense 128-row}).


  SCORER VALIDATED BEFORE SCORING THE NEW ARMS, per the vault rule that a fresh scorer
  is itself an unvalidated instrument. First attempt printed MISMATCH (median 3.138
  vs recorded 2.84) -- diagnosed, not overridden: the v10conly family is conly_s44
  + conly_s45 + ab_ctrl_s43, because seed 43''s C stage was trained inside the ab_ctrl
  chain and reused (run_conly_family.sh says so explicitly). Scoring only the two
  conly_* files gave a 2-seed median that cannot match a recorded 3-seed median. With
  the correct three files the scorer reproduces the record: median 2.836 vs recorded
  2.84, band 0.800 vs recorded 0.80 -> TRUSTED. That is what licenses every number
  below.


  PRIMARY diagnostic measured at the PRODUCTION stage (scratch/w6_primary_ratio.py)
  on n=12,471 windows / 587 subjects, readout held identical to W5 (subject-grouped
  GroupKFold(5) RidgeCV -> present-only mean HR; ratio = std(OOF pred)/std(truth)
  per cohort).'
cost: null
crash_reason: ''
created_at: '2026-08-21T04:14:35+00:00'
diagnostics_measured:
- after: 0.6897
  before: 0.563
  diagnostic_id: eiv_extent_variance_compression_ratio
- after: 3.301
  before: 3.575
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: 3.377
  before: 2.861
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
do_not_retry: []
external_run: logs/w6_scoring.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g0-hw5-passed
  passed: true
- evidence: ''
  gate_id: g1-dense-no-harm
  passed: true
- evidence: ''
  gate_id: g2-seed-band-respected
  passed: true
- evidence: ''
  gate_id: g3-single-variable-diff
  passed: true
- evidence: ''
  gate_id: g4-excursion-guard
  passed: false
git_ref: ''
hypothesis_id: h-w6-mask-weighted-pooling-restores-short-9da5b6
id: exp-w6b-masked-pooling-scored-20260821
interpretation:
  at: '2026-08-21T04:15:55+00:00'
  failure_mode: null
  lesson: 'MASK-WEIGHTED POOLING WORKS END-TO-END, AND THE HONEST HEADLINE IS SMALLER
    THAN THE PROBE PROMISED. Trained through Phase C at 3 seeds, short-cohort variance-compression
    at the PRODUCTION stage goes 0.5630 -> 0.6897 (bar >=0.60), all 7/7 short cohorts
    improve, long cohorts move <0.03, dense MAE 3.575 -> 3.301 (no harm, slight gain),
    supabase +0.516 bpm which is ~12x inside its own 6.29 seed band and therefore
    uninterpretable.


    THE STAGE-CALIBRATION LESSON, which is the most reusable thing here. W5''s post-hoc
    re-pool of FROZEN encoder tokens moved 0.1822 -> 0.8159 and looked spectacular.
    End-to-end the same intervention starts from 0.5630 -- because the projector''s
    cross-attention ALREADY undoes much of the contraction by itself -- and lands
    at 0.6897. Both numbers are correct and they measure different things. A frozen-token
    probe measures the headroom AT THAT STAGE, not the headroom the deployed system
    has. Anyone quoting 0.8159 as the end-to-end result is quoting the wrong stage,
    and the +0.1267 delta is the number that describes what training actually bought.
    This is the third time this vault has paid for stage-mismatched citation (hr_peak
    encoder-vs-production 4.3x, E4''s +0.4344 -> +0.0235, now this); the pattern is
    always the same direction -- the encoder-stage number flatters.


    WHAT THE COHORT TABLE SHOWS THAT THE MEAN HIDES: wesad''s within-cohort r more
    than doubles (0.276 -> 0.629) while its ratio only goes 0.4162 -> 0.5421. Ratio
    and tracking are separate axes, and on the cohort with the worst readout the tracking
    gain is the larger one. neuro_stress is the laggard (ratio +0.049, r DOWN 0.950
    -> 0.855) and is the one cohort where this intervention is not obviously good
    -- worth naming rather than averaging away.


    DENSE NO-HARM IS THE GATE THAT MATTERED AND IT HELD. cross_modal.py:260-270 warned
    in the source that presence-weighting ''would move the output of every input that
    has a missing channel, which is every checkpoint and every recorded result''.
    Measured: dense median -0.274 bpm, i.e. the blast radius is real but benign, because
    masked_avg is arithmetically near-inert exactly where coverage is high. The source
    comment asked for its own control arm; this is that arm and the answer is no harm.


    METHOD NOTE THAT NEARLY COST THE RUN: the scorer''s first validation pass printed
    MISMATCH (2-seed median 3.138 vs recorded 2.84). The temptation is to shrug and
    proceed. Diagnosing it found that the baseline family''s seed-43 C stage lives
    under a different tag (ab_ctrl_s43, per run_conly_family.sh), and with the correct
    three files the scorer reproduced the record to 0.004 bpm on the median and exactly
    on the band. A scorer validated on the wrong file set would have mis-anchored
    every comparison. Validate against the RECORD, and when validation fails, find
    out why before touching the new arms.


    WHAT IS NOT ESTABLISHED: no deployment claim (supabase inside band), no dense
    win (delta << dense band), no state/hr_peak claim (not measured), and g4''s excursion
    guard is UNMEASURED rather than passed. The next honest test if anyone wants the
    deployment sign is >=5 seeds or a paired-row bootstrap on the same 200-row draw
    -- all three masked seeds sit above all-but-one avg seed, which is suggestive
    and nothing more.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: 0.1267
  diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
- delta: -0.274
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
- delta: 0.516
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-w6-masked-pooling-20260820
prediction_match:
  overall: true
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: false
    eiv_extent_variance_compression_ratio: true
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[h-w6-mask-weighted-pooling-restores-short-9da5b6]].
Branches from [[exp-w6-masked-pooling-20260820]].
External run: logs/w6_scoring.json

## Measurements
- [[eiv_extent_variance_compression_ratio]]: before 0.563 → after 0.6897  (Δ +0.1267, up ✓ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 3.575 → after 3.301  (Δ -0.274, down ✗ predicted none)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 2.861 → after 3.377  (Δ +0.516, up ✗ predicted down)

## Gates
- `g0-hw5-passed`: PASS
- `g1-dense-no-harm`: PASS
- `g2-seed-band-respected`: PASS
- `g3-single-variable-diff`: PASS
- `g4-excursion-guard`: **FAIL**

**Verdict:** supports

## Interpretation
MASK-WEIGHTED POOLING WORKS END-TO-END, AND THE HONEST HEADLINE IS SMALLER THAN THE PROBE PROMISED. Trained through Phase C at 3 seeds, short-cohort variance-compression at the PRODUCTION stage goes 0.5630 -> 0.6897 (bar >=0.60), all 7/7 short cohorts improve, long cohorts move <0.03, dense MAE 3.575 -> 3.301 (no harm, slight gain), supabase +0.516 bpm which is ~12x inside its own 6.29 seed band and therefore uninterpretable.

THE STAGE-CALIBRATION LESSON, which is the most reusable thing here. W5's post-hoc re-pool of FROZEN encoder tokens moved 0.1822 -> 0.8159 and looked spectacular. End-to-end the same intervention starts from 0.5630 -- because the projector's cross-attention ALREADY undoes much of the contraction by itself -- and lands at 0.6897. Both numbers are correct and they measure different things. A frozen-token probe measures the headroom AT THAT STAGE, not the headroom the deployed system has. Anyone quoting 0.8159 as the end-to-end result is quoting the wrong stage, and the +0.1267 delta is the number that describes what training actually bought. This is the third time this vault has paid for stage-mismatched citation (hr_peak encoder-vs-production 4.3x, E4's +0.4344 -> +0.0235, now this); the pattern is always the same direction -- the encoder-stage number flatters.

WHAT THE COHORT TABLE SHOWS THAT THE MEAN HIDES: wesad's within-cohort r more than doubles (0.276 -> 0.629) while its ratio only goes 0.4162 -> 0.5421. Ratio and tracking are separate axes, and on the cohort with the worst readout the tracking gain is the larger one. neuro_stress is the laggard (ratio +0.049, r DOWN 0.950 -> 0.855) and is the one cohort where this intervention is not obviously good -- worth naming rather than averaging away.

DENSE NO-HARM IS THE GATE THAT MATTERED AND IT HELD. cross_modal.py:260-270 warned in the source that presence-weighting 'would move the output of every input that has a missing channel, which is every checkpoint and every recorded result'. Measured: dense median -0.274 bpm, i.e. the blast radius is real but benign, because masked_avg is arithmetically near-inert exactly where coverage is high. The source comment asked for its own control arm; this is that arm and the answer is no harm.

METHOD NOTE THAT NEARLY COST THE RUN: the scorer's first validation pass printed MISMATCH (2-seed median 3.138 vs recorded 2.84). The temptation is to shrug and proceed. Diagnosing it found that the baseline family's seed-43 C stage lives under a different tag (ab_ctrl_s43, per run_conly_family.sh), and with the correct three files the scorer reproduced the record to 0.004 bpm on the median and exactly on the band. A scorer validated on the wrong file set would have mis-anchored every comparison. Validate against the RECORD, and when validation fails, find out why before touching the new arms.

WHAT IS NOT ESTABLISHED: no deployment claim (supabase inside band), no dense win (delta << dense band), no state/hr_peak claim (not measured), and g4's excursion guard is UNMEASURED rather than passed. The next honest test if anyone wants the deployment sign is >=5 seeds or a paired-row bootstrap on the same 200-row draw -- all three masked seeds sit above all-but-one avg seed, which is suggestive and nothing more.

## Notes
PRIMARY EFFECT CONFIRMED END-TO-END, DENSE NO-HARM PASSED, DEPLOYMENT UNINTERPRETABLE BY ITS OWN PREREGISTERED RULE. All four blocking gates passed; the one non-blocking gate is recorded UNMEASURED.

PRIMARY (eiv_extent_variance_compression_ratio, PRODUCTION stage, n=12,471 / 587 subjects, readout identical to W5). Short-cohort mean 0.5630 (avg) -> 0.6897 (masked_avg), delta +0.1267, clearing the >=0.60 absolute bar. Per cohort, avg -> masked:
  popane_s4        0.4864 -> 0.6218   r 0.933 -> 0.959
  neuro_stress     0.3834 -> 0.4320   r 0.950 -> 0.855
  popane_s3        0.5897 -> 0.7355   r 0.951 -> 0.952
  nurse_stress     0.5047 -> 0.6187   r 0.431 -> 0.515
  case             0.6293 -> 0.8542   r 0.933 -> 0.890
  wesad            0.4162 -> 0.5421   r 0.276 -> 0.629
  wearable_exam    0.9316 -> 1.0232   r 0.804 -> 0.682
LONG cohorts barely move (lifesnaps 1.0212 -> 1.0205, pmdata 1.1024 -> 1.1291, supabase 1.0676 -> 1.1177), which is exactly the predicted signature: the correction is arithmetically near-inert where coverage is already high. 7/7 short cohorts improve; wesad's within-cohort r more than doubles (0.276 -> 0.629), the single largest readout gain in the run.

CALIBRATE THIS AGAINST W5 RATHER THAN OVERSELLING IT. W5's post-hoc re-pool of FROZEN Phase-B tokens reached 0.8159 from 0.1822. Trained end-to-end at the production stage the same intervention reaches 0.6897 from a much higher baseline of 0.5630. Two honest readings, both worth recording: (a) the projector's cross-attention already recovers a large part of the contraction on its own, which is why the avg control starts at 0.5630 here versus 0.1822 at the encoder stage -- consistent with the production-stage figure of 0.5157 W5 measured; (b) the remaining headroom is real but SMALLER than the frozen-token probe suggested. Anyone citing 0.8159 as the end-to-end number is quoting the wrong stage.

DENSE NO-HARM (g1, BLOCKING): median 3.575 -> 3.301 bpm, delta -0.274, i.e. a slight improvement against a bar that only forbade a >1.0 regression. Per-seed avg [4.125, 2.642, 3.575] vs masked [4.701, 2.439, 3.301]; masked wins seeds 44/45, loses 43. Dense trivial 7.763. The blast-radius concern recorded verbatim in cross_modal.py:260-270 is measured and does NOT materialise. But the dense seed band (avg 1.483 / masked 2.261) is much larger than the -0.274 delta, so this is a NO-HARM result and must not be reported as a dense win.

DEPLOYMENT (g2, BLOCKING on procedure): supabase median 2.861 -> 3.377, delta +0.516 bpm. The registered seed band for this diagnostic is 6.29 bpm and itself exceeds the 4.02 trivial baseline, so +0.516 is roughly 12x INSIDE the band and is not interpretable as a regression. Per-seed avg [3.151, 2.861, 2.754] vs masked [3.377, 3.351, 3.737]; both medians remain below the 3.953 supabase trivial. Directional only, no deployment claim in either direction. Worth flagging for a future arm: all three masked seeds sit above all-but-one avg seed, so if the sign is real it would need ~5+ seeds or a paired-row bootstrap to establish, and that is the honest next test rather than a claim.

SCORER VALIDATED FIRST, AND ITS FAILURE WAS INSTRUCTIVE. The first validation pass printed MISMATCH (median 3.138 vs recorded 2.84). Rather than override it, I diagnosed it: the v10conly family's seed-43 C stage lives under the ab_ctrl chain (run_conly_family.sh states this), so globbing only conly_s4* scores a 2-seed median against a recorded 3-seed one. With all three files the scorer reproduces the record to 0.004 bpm on the median and to 0.000 on the band -> TRUSTED. A scorer that had been trusted on the strength of a 2-seed glob would have mis-anchored every comparison in this run.

g4-excursion-guard is UNMEASURED, not passed. It reads an encoder-stage diagnostic; all six arms share the identical frozen ab_ctrl_s43 encoder and masked_avg acts only downstream in the Phase-C projector, so the quantity is structurally untouched -- but that is an argument, not a measurement, and it is recorded as such.

PROVENANCE: the parent experiment exp-w6-masked-pooling-20260820 was ABORTED (crashed), not closed -- three treatment arms died at step 0 on a 24-vs-12 channel mismatch. Fixed at 880c332, verified by a 12-step run through the real CLI path, and only the treatment arms were relaunched so the control half and the pairing were preserved.

#extent-heterogeneity #pooling #padding #deployment-distribution #wearables #denominator #state-supervision #optihealth-eiv #data-contract-violation #matched-baseline
