---
artifacts: []
closed: true
config_note: 'SUBSTRATE SUBSTITUTION, DECLARED BEFORE ANY NUMBER: the hypothesis targets
  the optihealth_analysis WORKER repo (sibling experiments record external_run local:/opt/optihealth_analysis/...).
  THAT PATH DOES NOT EXIST ON THIS HOST (verified). The worker-integration half of
  the claim (''...the worker currently cannot see'') is therefore NOT TESTED and must
  not be read as tested.


  What IS tested is the scientific core, on the closest available substrate: optihealth_supabase
  in the canonical corpus = 302 windows, ONE subject (Anton), 1-minute epochs, 2025-10-09
  -> 2026-08-08. Same person, same signal family, finer resolution than the worker''s
  inputs. 188 days survived the usability filter (>=4h real HR, >=18/24 hours present).


  Scripts: scratch/rar_circadian_on_supabase.py (Q1-Q4 preregistered PRE-DATA) and
  the follow-up scratch/rar_power_check.py (W1 preregistered PRE-DATA). numpy-only,
  zero new dependencies. Logs: logs/rar_circadian_on_supabase.json, logs/rar_power_check.json.


  The Q3 day-shuffle null (300 perms) was included specifically because the sibling
  experiment rhr-cusum-realdata-2026-07 recorded that its false-positive number moved
  1.27 -> 0.24 on null DESIGN alone, and its lesson mandates that any future work
  in this family report the specific null it used.'
cost: null
crash_reason: ''
created_at: '2026-08-20T21:54:53+00:00'
diagnostics_measured:
- after: 0.0
  before: 0.0
  diagnostic_id: insights_yield
- after: 0.0
  before: 0.0
  diagnostic_id: new_deps
- after: 0.007
  before: 0.24
  diagnostic_id: fp_rate_null
do_not_retry: []
external_run: logs/rar_circadian_on_supabase.json
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: circadian-rar-adds-rhythm
id: exp-rar-circadian-supabase-20260820
interpretation:
  at: '2026-08-20T21:55:39+00:00'
  failure_mode: test_invalid
  lesson: 'SPLIT THIS THREE WAYS OR IT MISLEADS. (a) The RAR metrics themselves WORK
    on Anton''s own 1-minute HR: 188 usable days, RA 0.2538 +- 0.0525, M10 74.0 /
    L5 43.7 bpm, and a 24h cosinor with amplitude 15.84 bpm explaining R2 0.4186 of
    hourly variance -- a strong, real rhythm, computed with numpy alone (new_deps
    0, as predicted). (b) The DISRUPTION DETECTOR found zero episodes and is demonstrably
    underpowered, so that zero is not evidence about biology. (c) The worker-integration
    half was NOT TESTED because /opt/optihealth_analysis does not exist on this host.


    THE POWER CURVE IS THE DELIVERABLE, and it is a reusable number: planting known
    RA collapses into the real series (200 trials/cell) gives recall 0.000 at 1 robust
    SD (any duration), 0.265-0.515 at 2, 0.605-0.820 at 3, 0.845-0.920 at 4, and ~1.000
    at 6. The preregistered W1 bar (>=0.80 at depth 3 / 5 days) measured 0.700 and
    FAILED. So this detector''s honest detection floor is roughly 4 robust SD (~0.21
    RA units) sustained 3+ days. Zero episodes means ''nothing that deep happened'',
    not ''no disruption happened''.


    WHY I RAN THE POWER CHECK AT ALL, and this is the transferable habit: the day-shuffle
    null came back at 0.007 episodes. A null that quiet is a warning sign, not a triumph
    -- it usually means the detector cannot fire, which makes the observed zero uninterpretable.
    The vault already paid for this lesson once (exp-e23 planted a known effect in
    E18''s design and found the design could not recover it). A detector''s false-positive
    rate and its power are two halves of one instrument: reporting fp=0.007 without
    the recall curve would have looked like a clean negative and been worthless.


    DO NOT COMPARE fp_rate_null 0.007 TO THE SIBLING RHR-CuSum''s 0.24. Different
    detector, different signal, different run-length rule. The low fp here is the
    flip side of the low power, not an improvement.


    DO NOT COMPARE THIS IS (0.0188) TO PUBLISHED ACTIGRAPHY IS VALUES. Interdaily
    stability here rides on HR, not accelerometry, on a subject with 30% median wear
    coverage. Different carrier, different denominator.


    WHAT WOULD MAKE THIS TESTABLE PROPERLY: (i) a detector with a lower floor -- CuSum
    on RA rather than a fixed-threshold run rule would likely reach 2 robust SD, which
    is where the power curve says the information is; (ii) accelerometry or step counts
    as the rhythm carrier instead of HR; (iii) access to the worker repo for the integration
    half. Until then the hypothesis stays open and this run is a power bound, not
    an answer.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: 0.0
  diagnostic_id: insights_yield
  direction: none
- delta: 0.0
  diagnostic_id: new_deps
  direction: none
- delta: -0.233
  diagnostic_id: fp_rate_null
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    insights_yield: false
    new_deps: true
provenance: {}
recipe_fingerprint:
  interventions: circadian-rar
verdict: inconclusive
---

## Summary
Tests hypothesis [[circadian-rar-adds-rhythm]].
External run: logs/rar_circadian_on_supabase.json

## Measurements
- [[insights_yield]]: before 0.0 → after 0.0  (Δ +0, flat ✗ predicted up)
- [[new_deps]]: before 0.0 → after 0.0  (Δ +0, flat ✓ predicted none)
- [[fp_rate_null]]: before 0.24 → after 0.007  (Δ -0.233, down)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `test_invalid`

SPLIT THIS THREE WAYS OR IT MISLEADS. (a) The RAR metrics themselves WORK on Anton's own 1-minute HR: 188 usable days, RA 0.2538 +- 0.0525, M10 74.0 / L5 43.7 bpm, and a 24h cosinor with amplitude 15.84 bpm explaining R2 0.4186 of hourly variance -- a strong, real rhythm, computed with numpy alone (new_deps 0, as predicted). (b) The DISRUPTION DETECTOR found zero episodes and is demonstrably underpowered, so that zero is not evidence about biology. (c) The worker-integration half was NOT TESTED because /opt/optihealth_analysis does not exist on this host.

THE POWER CURVE IS THE DELIVERABLE, and it is a reusable number: planting known RA collapses into the real series (200 trials/cell) gives recall 0.000 at 1 robust SD (any duration), 0.265-0.515 at 2, 0.605-0.820 at 3, 0.845-0.920 at 4, and ~1.000 at 6. The preregistered W1 bar (>=0.80 at depth 3 / 5 days) measured 0.700 and FAILED. So this detector's honest detection floor is roughly 4 robust SD (~0.21 RA units) sustained 3+ days. Zero episodes means 'nothing that deep happened', not 'no disruption happened'.

WHY I RAN THE POWER CHECK AT ALL, and this is the transferable habit: the day-shuffle null came back at 0.007 episodes. A null that quiet is a warning sign, not a triumph -- it usually means the detector cannot fire, which makes the observed zero uninterpretable. The vault already paid for this lesson once (exp-e23 planted a known effect in E18's design and found the design could not recover it). A detector's false-positive rate and its power are two halves of one instrument: reporting fp=0.007 without the recall curve would have looked like a clean negative and been worthless.

DO NOT COMPARE fp_rate_null 0.007 TO THE SIBLING RHR-CuSum's 0.24. Different detector, different signal, different run-length rule. The low fp here is the flip side of the low power, not an improvement.

DO NOT COMPARE THIS IS (0.0188) TO PUBLISHED ACTIGRAPHY IS VALUES. Interdaily stability here rides on HR, not accelerometry, on a subject with 30% median wear coverage. Different carrier, different denominator.

WHAT WOULD MAKE THIS TESTABLE PROPERLY: (i) a detector with a lower floor -- CuSum on RA rather than a fixed-threshold run rule would likely reach 2 robust SD, which is where the power curve says the information is; (ii) accelerometry or step counts as the rhythm carrier instead of HR; (iii) access to the worker repo for the integration half. Until then the hypothesis stays open and this run is a power bound, not an answer.

## Notes
THE METRICS WORK; THE DISRUPTION DETECTOR IS UNDERPOWERED; THE WORKER HALF WAS NOT TESTED AT ALL. Three separable outcomes, and collapsing them would misinform.

(1) RAR METRICS ARE COMPUTABLE AND NON-DEGENERATE (Q1 PASS). On Anton's own 188 usable days, 1-minute HR, 2025-10-10 -> 2026-08-06:
  IS 0.0188 | IV 0.5544 | M10 74.0 bpm | L5 43.7 bpm | RA 0.2538 (std 0.0525)
  cosinor: MESOR 60.3 bpm, amplitude 15.84 bpm, acrophase 6.8h, R2 0.4186
A 24h cosinor explaining 42% of hourly HR variance with a ~16 bpm amplitude is a real, strong rhythm. Zero new dependencies (numpy only) -- new_deps prediction CONFIRMED.

NOTE ON IS: 0.0188 is very low against the literature's actigraphy range because IS here is computed on HR rather than accelerometry, over a subject whose wear coverage is 30% median, and the canonical merge already spends the circadian block on sin/cos channels. Do NOT compare this IS to published actigraphy IS values -- different carrier, different denominator.

(2) PRIMARY PREDICTION FAILED: insights_yield = 0. The preregistered detector (>=3 consecutive days with RA >= 2 robust SD below the trailing 28-day baseline) fired ZERO times in 188 days. Day-shuffle null over 300 permutations: mean 0.007 episodes, p90 0, max 1, permutation p = 1.0000.

(3) AND THE ZERO IS NOT CLEAN BIOLOGY -- THIS IS WHY THE RUN IS NOT A SIMPLE REFUTATION. A null of 0.007 is suspiciously quiet, so a synthetic-control power check was run (scratch/rar_power_check.py, W1 preregistered): plant a known RA collapse of controlled depth/duration into the real series and measure recall over 200 trials per cell.
  depth 1 robustSD:  recall 0.000 at 3/5/10 days   <- completely blind
  depth 2 robustSD:  0.265 / 0.350 / 0.515
  depth 3 robustSD:  0.605 / 0.700 / 0.820
  depth 4 robustSD:  0.875 / 0.845 / 0.920
  depth 6 robustSD:  0.980 / 1.000 / 1.000
W1 demanded >=0.80 recall at depth 3 / duration 5 days; measured 0.700. FAILED. The detector is effectively BLIND below ~2 robust SD (0.106 RA units) and only reliable at >=4 robust SD. So 'zero episodes' means 'no collapse deeper than roughly 4 robust SD lasting 3+ days', NOT 'no circadian disruption'. Any real disruption of moderate depth would have been missed roughly a third of the time or more.

WHY THIS IS test_invalid RATHER THAN refutes: the instrument cannot resolve the effect size the claim is about, so the zero carries little information about the biology. Recording it as a refutation would tell a future session 'Anton has no circadian disruptions', which the data does not support.

(4) NOT TESTED AT ALL: the worker-integration half. /opt/optihealth_analysis does not exist on this host, so 'the worker currently cannot see' is untouched. If the worker repo becomes available, the RAR metrics are ready to port -- they are numpy-only and the code is in scratch/rar_circadian_on_supabase.py.

FP-RATE NOTE: fp_rate_null moved 0.24 -> 0.007 versus the sibling RHR-CuSum experiment. That is NOT an improvement of the same detector; it is a DIFFERENT detector on a different signal with a stricter run-length requirement, and the low fp is the flip side of the low power documented above. The sibling's lesson (report the null you used) is honoured; its number is not comparable to this one.

#circadian #rest-activity-rhythm #optihealth #wearables #n-of-1 #missing-circadian #missing-early-warning #missing-directionality
