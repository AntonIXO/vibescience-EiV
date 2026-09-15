---
artifacts:
- outputs/checkpoints/v31detached/phase_a
- outputs/checkpoints/v31detached/phase_b
- outputs/adapters/v31detached_c
- outputs/adapters/v31detached_c/manifest.json
- logs/v31_geometry.json
- logs/v31_geom.log
- logs/v31_driver.log
- logs/v31_phase_a_report.json
- logs/v31_phase_b_report.json
- logs/v31_phase_c_report.json
- logs/emission_v31detached_supa_summary.json
- logs/emission_v31detached_dense_summary.json
- logs/v31_posthoc_probes.log
- scratch/_v31_calibration_probe.py
- scratch/_v31_error_shape.py
- scratch/run_v31_detached.sh
closed: true
config_note: 'v31detached: phase_a_12ch_v31detached = phase_a_12ch_v29all01 + detached_probes:true,
  dict-diff verified by scratch/_verify_v31_diff.py (single key) and tests/test_detached_probes_guard.py
  (3 passed: severance + control + phase_config recording). Provenance re-checked
  post-hoc from outputs/checkpoints/v31detached/phase_a/phase_config.json -> training.detached_probes
  = True (the driver''s own inline check printed a FALSE ''NOT SET'' because it read
  the top level; fixed in commit a3cef54). B = phase_b_12ch_v25bigbatch @30, C = lora_proxy_12ch_wl_band04,
  same scoring as v29/v30. Runner scratch/run_v31_detached.sh, driver log logs/v31_driver.log.'
cost:
  gpu_hours: 1.52
  wall_hours: 1.53
crash_reason: ''
created_at: '2026-08-31T12:03:48+00:00'
diagnostics_measured:
- after: 10.1057
  before: 8.599
  diagnostic_id: eiv_effective_rank
- after: 2.6987
  before: 3.0297
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
do_not_retry:
- Do NOT report v31's supabase MAE 2.699 as a win over v28's 3.030. The -0.331 bpm
  gap is inside 2x the registered paired arm-vs-arm sigma (0.195) and the paired per-row
  test on the same 200 ids reads t -1.62 with median exactly 0.000. It is NOT a demonstrated
  difference; corr (0.755 vs 0.793), distinct (14 vs 20) and top1_mass (0.415 vs 0.470)
  all move slightly the WRONG way.
- Do NOT deploy v31detached_c or treat it as the new incumbent. It failed the blocking
  dense no-harm gate at MAE 7.312 vs the band [2.23, 3.83], with a paired +4.461 bpm
  regression at t +5.90 on shared rows. v28affect01 remains the incumbent.
- 'Do NOT conclude that detached probes are dead. g0 is the cleanest one-variable
  geometry result in the campaign: 6.783 ATTACHED -> 10.106 DETACHED, +3.323 rank
  on a single config key. The mechanism works; what fails is downstream emission calibration.'
- 'Do NOT chase v31''s dense failure with another Phase-A geometry knob. The evidence
  localises the damage AFTER the encoder: dense corr stays 0.733 while std_ratio blows
  to 1.652 and an out-of-fold affine fit needs slope 0.444 to repair it (v28 needs
  0.842, v31''s own supabase draw needs 0.961). The next variable belongs in Phase
  B/C or in the band/target construction, not in A.'
- 'Do NOT read the ''detach-provenance: NOT SET'' line at logs/v31_driver.log:11 as
  evidence the arm ran attached. That check read the top level of phase_config.json
  instead of the training block; the file records training.detached_probes = True.
  Fixed in commit a3cef54.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'MEASURED eff_rank 10.1057 (cos_p50 0.0960, cos_p95 0.6619), logs/v31_geometry.json
    / logs/v31_geom.log, gate >= 7.0 -> PASS by +3.106. THE DISCRIMINATING COMPARISON:
    the identical supervision set ATTACHED (v29all01) scored 6.783, so severing the
    head->encoder gradient is worth +3.323 eff_rank on one variable. It also clears
    every anchor on the books: v28affect01 8.599, healthy 8.35, no-heads v20strat
    7.438, v30state 7.403, v22 0.3-triple 5.936, v19 5.19, carrier-collapse 4.47 --
    10.106 is the highest Phase-A rank measured in this campaign. Provenance confirmed:
    outputs/checkpoints/v31detached/phase_a/phase_config.json training.detached_probes
    = True (re-read post-hoc; the driver''s inline check at logs/v31_driver.log:11
    printed a FALSE ''NOT SET'' by reading the top level instead of the training block
    -- fixed in commit a3cef54, so that log line is a reporting bug, not a config
    miss).'
  gate_id: g0-detach-rescues-geometry
  passed: true
- evidence: 'MEASURED logs/emission_v31detached_supa_summary.json (n=200, parse_ok
    200/200): hr_raw MAE 2.6987 <= 2.836 PASS, corr 0.7553 >= 0.60 PASS. Skill +1.256
    bpm vs trivial 3.954. HONEST FRAMING OF THE MARGIN: the gap to v28affect01 (3.0297)
    is -0.331 bpm, inside 2x the registered paired arm-vs-arm sigma of 0.195 and far
    inside the 3.19 bpm seed-retrain band; the paired per-row test on the same 200
    ids gives mean -0.331, se 0.205, t -1.62, median exactly 0.000 (logs/v31_posthoc_probes.log).
    So the LETTER of g1 is met but v31 is NOT demonstrated better than v28 on supabase
    MAE. Other channels read the same way: corr 0.755 vs 0.793 is inside the 0.069
    finite-draw sigma (k=2); distinct 14 vs 20 and top1_mass 0.415 vs 0.470 are both
    slightly worse, i.e. the emission vocabulary narrowed rather than widened.'
  gate_id: g1-beats-baseline-letter
  passed: true
- evidence: 'MEASURED logs/emission_v31detached_dense_summary.json (n=128, parse_ok
    128/128): hr_raw MAE 7.3119, OUTSIDE the v28 band [2.23, 3.83] -> FAIL by 3.48
    bpm. This is a demonstrated regression, not a draw: the paired per-row comparison
    on the 128 shared dense ids gives mean +4.461 bpm, se 0.756, t +5.90, median +2.450,
    with v31 worse on 68.8% of rows and 25 rows where v31 breaks past 10 bpm while
    v28 held (logs/v31_posthoc_probes.log, scratch/_v31_error_shape.py). MECHANISM:
    it is a SCALE failure, not an information failure. v31 dense keeps corr 0.733
    (v28 0.937) but posts std_ratio 1.652 (v28 1.112), and a cross-fitted 2-parameter
    affine recalibration (5-fold, out-of-fold, scratch/_v31_calibration_probe.py)
    pulls dense MAE 7.312 -> 4.608 with slope a = 0.444, i.e. the emissions overshoot
    true spread by ~2.25x. v28 needs almost no such repair (2.851 -> 2.226, a = 0.842)
    and v31''s own supabase draw needs none at all (2.699 -> 2.755, a = 0.961). So
    the Phase-C head learned a scale that fits the sparse supabase draw and blows
    up on the dense draw. Even after recalibration 4.608 stays above the band, so
    the failure is not purely affine.'
  gate_id: g2-no-harm-dense
  passed: false
git_ref: main@d12855e
hypothesis_id: detached-probes-rescue-geometry-and-carry-utilisation-v31
id: exp-v31-detached-probes-chain-20260831
interpretation:
  at: '2026-08-31T12:05:32+00:00'
  failure_mode: mechanism_wrong
  lesson: 'DETACHING THE PROBES WORKS AS ADVERTISED ON GEOMETRY AND BUYS NOTHING AT
    DEPLOYMENT. One config key (probe_input = fused.detach()) moved Phase-A eff_rank
    from 6.783 ATTACHED to 10.106 DETACHED on the identical supervision set -- the
    largest single-variable geometry effect in the campaign and the highest rank ever
    measured here, above v28''s 8.599 and the 8.35 healthy anchor. Yet the chain it
    produced failed the dense no-harm gate at 7.312 bpm (band [2.23, 3.83], paired
    +4.461 vs v28 at t +5.90) and its supabase ''win'' (2.699 vs 3.030) is a powered
    null inside the noise floors. THE GENERALISABLE CLAIM: Phase-A effective rank
    is NOT a sufficient statistic for deployment quality. The gate is still worth
    keeping as a cheap veto -- v29 proved low rank kills arms -- but rank above threshold
    buys no deployment credit, and ''more rank'' is not a direction. SECOND, THE ACTIONABLE
    PART: v31 and v30state now both fail deployment through emission SCALE while their
    encoders are healthy. Out-of-fold affine recalibration diagnoses it precisely
    -- v31 dense needs slope 0.444 and v30 dense 0.268 to repair, while v28 needs
    0.842 and v31''s own supabase draw 0.961. A single adapter is carrying two incompatible
    output scales (right on the sparse supabase draw, ~2.25x too wide on the dense
    draw). That localises the next variable in Phase C''s band/target construction,
    not in any Phase-A supervision knob. THIRD, METHODOLOGICAL: run the cross-fitted
    affine probe on every arm''s captures before interpreting an MAE regression --
    it separates ''the representation lost the signal'' (corr collapses) from ''the
    head learned the wrong scale'' (corr survives, slope is off), and those two demand
    opposite fixes.'
  transfers_to:
  - full-utilisation-at-the-safe-dose-the-v29-chain-616efb
  - state-detection-was-substrate-limited-on-the-46fa39
  - e1-band-mass-starvation-drives-supabase-mae-and-seed-band
key_numbers:
  cos_p50: 0.096
  cos_p95: 0.6619
  delta_rank_vs_v28_attached_affect_only: 1.5067
  delta_rank_vs_v29_attached_same_heads: 3.3223
  dense_affine_cv_mae_v28: 2.226
  dense_affine_cv_mae_v31: 4.608
  dense_affine_slope_v28: 0.842
  dense_affine_slope_v31: 0.444
  dense_corr_v28: 0.937
  dense_corr_v31: 0.7328
  dense_mae_v28: 2.851
  dense_mae_v31: 7.3119
  dense_paired_delta_bpm: 4.461
  dense_paired_t: 5.9
  dense_std_ratio_v28: 1.112
  dense_std_ratio_v31: 1.6517
  eff_rank: 10.1057
  eff_rank_gate: 7.0
  eff_rank_v29_attached: 6.7834
  phase_c_final_loss: 1.3684
  phase_c_seconds: 2301.68
  phase_c_steps: 2600.0
  supa_corr_v28: 0.793
  supa_corr_v31: 0.7553
  supa_mae_v28: 3.0297
  supa_mae_v31: 2.6987
  supa_paired_delta_bpm: -0.331
  supa_paired_t: -1.62
  supa_skill_v31: 1.2558
observed_effects:
- delta: 1.5067
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: -0.331
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-v29-all01-chain-20260831
prediction_match:
  overall: true
  per_diagnostic:
    eiv_effective_rank: true
    eiv_supabase_hr_raw_mae_bpm: true
provenance: {}
recipe_fingerprint:
  git_ref: main@d12855e
verdict: 'null'
---

## Summary
Tests hypothesis [[detached-probes-rescue-geometry-and-carry-utilisation-v31]].
Branches from [[exp-v29-all01-chain-20260831]].
At commit `main@d12855e`.

## Measurements
- [[eiv_effective_rank]]: before 8.599 → after 10.1057  (Δ +1.5067, up ✓ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 3.0297 → after 2.6987  (Δ -0.331, down ✓ predicted down)

## Gates
- `g0-detach-rescues-geometry`: PASS — MEASURED eff_rank 10.1057 (cos_p50 0.0960, cos_p95 0.6619), logs/v31_geometry.json / logs/v31_geom.log, gate >= 7.0 -> PASS by +3.106. THE DISCRIMINATING COMPARISON: the identical supervision set ATTACHED (v29all01) scored 6.783, so severing the head->encoder gradient is worth +3.323 eff_rank on one variable. It also clears every anchor on the books: v28affect01 8.599, healthy 8.35, no-heads v20strat 7.438, v30state 7.403, v22 0.3-triple 5.936, v19 5.19, carrier-collapse 4.47 -- 10.106 is the highest Phase-A rank measured in this campaign. Provenance confirmed: outputs/checkpoints/v31detached/phase_a/phase_config.json training.detached_probes = True (re-read post-hoc; the driver's inline check at logs/v31_driver.log:11 printed a FALSE 'NOT SET' by reading the top level instead of the training block -- fixed in commit a3cef54, so that log line is a reporting bug, not a config miss).
- `g1-beats-baseline-letter`: PASS — MEASURED logs/emission_v31detached_supa_summary.json (n=200, parse_ok 200/200): hr_raw MAE 2.6987 <= 2.836 PASS, corr 0.7553 >= 0.60 PASS. Skill +1.256 bpm vs trivial 3.954. HONEST FRAMING OF THE MARGIN: the gap to v28affect01 (3.0297) is -0.331 bpm, inside 2x the registered paired arm-vs-arm sigma of 0.195 and far inside the 3.19 bpm seed-retrain band; the paired per-row test on the same 200 ids gives mean -0.331, se 0.205, t -1.62, median exactly 0.000 (logs/v31_posthoc_probes.log). So the LETTER of g1 is met but v31 is NOT demonstrated better than v28 on supabase MAE. Other channels read the same way: corr 0.755 vs 0.793 is inside the 0.069 finite-draw sigma (k=2); distinct 14 vs 20 and top1_mass 0.415 vs 0.470 are both slightly worse, i.e. the emission vocabulary narrowed rather than widened.
- `g2-no-harm-dense`: **FAIL** — MEASURED logs/emission_v31detached_dense_summary.json (n=128, parse_ok 128/128): hr_raw MAE 7.3119, OUTSIDE the v28 band [2.23, 3.83] -> FAIL by 3.48 bpm. This is a demonstrated regression, not a draw: the paired per-row comparison on the 128 shared dense ids gives mean +4.461 bpm, se 0.756, t +5.90, median +2.450, with v31 worse on 68.8% of rows and 25 rows where v31 breaks past 10 bpm while v28 held (logs/v31_posthoc_probes.log, scratch/_v31_error_shape.py). MECHANISM: it is a SCALE failure, not an information failure. v31 dense keeps corr 0.733 (v28 0.937) but posts std_ratio 1.652 (v28 1.112), and a cross-fitted 2-parameter affine recalibration (5-fold, out-of-fold, scratch/_v31_calibration_probe.py) pulls dense MAE 7.312 -> 4.608 with slope a = 0.444, i.e. the emissions overshoot true spread by ~2.25x. v28 needs almost no such repair (2.851 -> 2.226, a = 0.842) and v31's own supabase draw needs none at all (2.699 -> 2.755, a = 0.961). So the Phase-C head learned a scale that fits the sparse supabase draw and blows up on the dense draw. Even after recalibration 4.608 stays above the band, so the failure is not purely affine.

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `mechanism_wrong`

DETACHING THE PROBES WORKS AS ADVERTISED ON GEOMETRY AND BUYS NOTHING AT DEPLOYMENT. One config key (probe_input = fused.detach()) moved Phase-A eff_rank from 6.783 ATTACHED to 10.106 DETACHED on the identical supervision set -- the largest single-variable geometry effect in the campaign and the highest rank ever measured here, above v28's 8.599 and the 8.35 healthy anchor. Yet the chain it produced failed the dense no-harm gate at 7.312 bpm (band [2.23, 3.83], paired +4.461 vs v28 at t +5.90) and its supabase 'win' (2.699 vs 3.030) is a powered null inside the noise floors. THE GENERALISABLE CLAIM: Phase-A effective rank is NOT a sufficient statistic for deployment quality. The gate is still worth keeping as a cheap veto -- v29 proved low rank kills arms -- but rank above threshold buys no deployment credit, and 'more rank' is not a direction. SECOND, THE ACTIONABLE PART: v31 and v30state now both fail deployment through emission SCALE while their encoders are healthy. Out-of-fold affine recalibration diagnoses it precisely -- v31 dense needs slope 0.444 and v30 dense 0.268 to repair, while v28 needs 0.842 and v31's own supabase draw 0.961. A single adapter is carrying two incompatible output scales (right on the sparse supabase draw, ~2.25x too wide on the dense draw). That localises the next variable in Phase C's band/target construction, not in any Phase-A supervision knob. THIRD, METHODOLOGICAL: run the cross-fitted affine probe on every arm's captures before interpreting an MAE regression -- it separates 'the representation lost the signal' (corr collapses) from 'the head learned the wrong scale' (corr survives, slope is off), and those two demand opposite fixes.

Transfers to: [[full-utilisation-at-the-safe-dose-the-v29-chain-616efb]], [[state-detection-was-substrate-limited-on-the-46fa39]], [[e1-band-mass-starvation-drives-supabase-mae-and-seed-band]]

## Key numbers
- `cos_p50`: 0.096
- `cos_p95`: 0.6619
- `delta_rank_vs_v28_attached_affect_only`: 1.5067
- `delta_rank_vs_v29_attached_same_heads`: 3.3223
- `dense_affine_cv_mae_v28`: 2.226
- `dense_affine_cv_mae_v31`: 4.608
- `dense_affine_slope_v28`: 0.842
- `dense_affine_slope_v31`: 0.444
- `dense_corr_v28`: 0.937
- `dense_corr_v31`: 0.7328
- `dense_mae_v28`: 2.851
- `dense_mae_v31`: 7.3119
- `dense_paired_delta_bpm`: 4.461
- `dense_paired_t`: 5.9
- `dense_std_ratio_v28`: 1.112
- `dense_std_ratio_v31`: 1.6517
- `eff_rank`: 10.1057
- `eff_rank_gate`: 7
- `eff_rank_v29_attached`: 6.7834
- `phase_c_final_loss`: 1.3684
- `phase_c_seconds`: 2301.68
- `phase_c_steps`: 2600
- `supa_corr_v28`: 0.793
- `supa_corr_v31`: 0.7553
- `supa_mae_v28`: 3.0297
- `supa_mae_v31`: 2.6987
- `supa_paired_delta_bpm`: -0.331
- `supa_paired_t`: -1.62
- `supa_skill_v31`: 1.2558

## Artifacts
- `outputs/checkpoints/v31detached/phase_a`
- `outputs/checkpoints/v31detached/phase_b`
- `outputs/adapters/v31detached_c`
- `outputs/adapters/v31detached_c/manifest.json`
- `logs/v31_geometry.json`
- `logs/v31_geom.log`
- `logs/v31_driver.log`
- `logs/v31_phase_a_report.json`
- `logs/v31_phase_b_report.json`
- `logs/v31_phase_c_report.json`
- `logs/emission_v31detached_supa_summary.json`
- `logs/emission_v31detached_dense_summary.json`
- `logs/v31_posthoc_probes.log`
- `scratch/_v31_calibration_probe.py`
- `scratch/_v31_error_shape.py`
- `scratch/run_v31_detached.sh`

## Do not retry
- Do NOT report v31's supabase MAE 2.699 as a win over v28's 3.030. The -0.331 bpm gap is inside 2x the registered paired arm-vs-arm sigma (0.195) and the paired per-row test on the same 200 ids reads t -1.62 with median exactly 0.000. It is NOT a demonstrated difference; corr (0.755 vs 0.793), distinct (14 vs 20) and top1_mass (0.415 vs 0.470) all move slightly the WRONG way.
- Do NOT deploy v31detached_c or treat it as the new incumbent. It failed the blocking dense no-harm gate at MAE 7.312 vs the band [2.23, 3.83], with a paired +4.461 bpm regression at t +5.90 on shared rows. v28affect01 remains the incumbent.
- Do NOT conclude that detached probes are dead. g0 is the cleanest one-variable geometry result in the campaign: 6.783 ATTACHED -> 10.106 DETACHED, +3.323 rank on a single config key. The mechanism works; what fails is downstream emission calibration.
- Do NOT chase v31's dense failure with another Phase-A geometry knob. The evidence localises the damage AFTER the encoder: dense corr stays 0.733 while std_ratio blows to 1.652 and an out-of-fold affine fit needs slope 0.444 to repair it (v28 needs 0.842, v31's own supabase draw needs 0.961). The next variable belongs in Phase B/C or in the band/target construction, not in A.
- Do NOT read the 'detach-provenance: NOT SET' line at logs/v31_driver.log:11 as evidence the arm ran attached. That check read the top level of phase_config.json instead of the training block; the file records training.detached_probes = True. Fixed in commit a3cef54.

## Notes
TERMINAL: chain completed all four stages (A 13:24:26-13:45:44, geometry gate PASS, B 13:45:48-13:58:20, C 13:58:20-14:36:51, scoring to 14:55:47; logs/v31_driver.log). Adapter bundle outputs/adapters/v31detached_c WAS written (manifest.json + lora/ + sensor_modules/), so unlike v29 every gate here rests on a real measurement.

THE HEADLINE IS g0 AND IT IS UNAMBIGUOUS. Same supervision set, one config key: ATTACHED (v29all01) eff_rank 6.783, DETACHED (v31) 10.106. +3.323 rank, 15x the 0.217 by which v29 missed its gate. 10.106 is the highest Phase-A effective rank measured anywhere in this campaign -- above v28affect01's 8.599, above the 8.35 healthy anchor, above the 7.438 no-heads control. cos_p50 also improves, 0.1381 -> 0.0960. So the v29 interpretation's own prescription was correct: the label heads' damage is their GRADIENT into the shared representation, not their loss term, and a stop-gradient removes it entirely while the heads keep learning their labels.

BUT THE CHAIN DOES NOT DEPLOY. g2 fails hard: dense hr_raw MAE 7.312 against the band [2.23, 3.83], a paired +4.461 bpm regression vs v28 at t +5.90 on 128 shared rows (68.8% of rows worse, 25 rows past 10 bpm that v28 held). And g1's PASS is a letter-pass only -- 2.699 vs 2.836 clears the bar, but 2.699 vs v28's 3.030 is -0.331 bpm at t -1.62, median 0.000, inside the noise floors, with corr/distinct/top1_mass all slightly worse.

WHERE THE DAMAGE LIVES (the post-hoc CPU probes, logs/v31_posthoc_probes.log). Dense corr survives at 0.733 while std_ratio blows to 1.652, and an out-of-fold 5-fold affine recalibration needs slope 0.444 to repair it -- the emissions overshoot true spread by ~2.25x. v28 needs slope 0.842 (repair worth only 0.63 bpm) and v31's OWN supabase draw needs 0.961 (repair worth nothing, -0.06). So a single adapter carries two different output scales: correct on the sparse supabase draw, ~2.25x too wide on the dense draw. Recalibration recovers 7.312 -> 4.608, i.e. ~61% of the excess, so scale explains most but not all of it.

THE SHAPE OF THE RESULT: a best-ever encoder feeding a mis-scaled emission head. That is a genuinely different failure from v29 (never reached deployment) and from v30state (which ALSO blew calibration: std_ratio 2.71/2.46, affine repair 15.085 -> 5.352 at slope 0.268). Two consecutive arms now fail deployment through emission SCALE while geometry is fine or excellent, which points at the Phase-C band/target construction as the shared suspect rather than at any Phase-A knob.

hr_peak: supabase skill -1.783 and dense -3.597, both far below v28's dense +0.543. No hr_peak progress here.

#deployment-distribution #sensor-text #contrastive #control #baseline #wearables #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
