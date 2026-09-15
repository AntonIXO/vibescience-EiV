---
artifacts:
- outputs/checkpoints/v31detached/phase_a
- outputs/checkpoints/v31detached/phase_b
- outputs/adapters/v31detached_c
- logs/v31_geometry.json
- logs/emission_v31detached_supa_summary.json
- logs/emission_v31detached_dense_summary.json
- logs/v31_driver.log
closed: true
config_note: 'v31detached = phase_a_12ch_v31detached, EXACTLY phase_a_12ch_v29all01
  plus detached_probes: true (one-key dict-diff, enforced as a driver pre-flight by
  scratch/_verify_v31_diff.py). Heads read fused.detach(): they still learn the labels
  but cannot reshape the representation. B = phase_b_12ch_v25bigbatch @30, C = lora_proxy_12ch_wl_band04,
  scoring identical to v28/v29/v30. Runner scratch/run_v31_detached.sh, systemd unit
  eiv-v31. Guard tests/test_detached_probes_guard.py (severance + attached control
  + wiring + provenance nesting).'
cost:
  gpu_hours: 1.52
  wall_hours: 1.52
crash_reason: ''
created_at: '2026-08-31T12:02:15+00:00'
diagnostics_measured:
- after: 10.106
  before: 6.783
  diagnostic_id: eiv_effective_rank
- after: 2.699
  before: 3.03
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
- after: 7.312
  before: 2.851
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- Do NOT promote v31detached to the deployment arm on the strength of its 2.699 supabase
  MAE. That is a -1.70 sigma move against the measured 0.195 paired floor (not demonstrated),
  while its dense capture is a +4.461 bpm regression with NEGATIVE skill. The incumbent
  stays v28affect01.
- 'Do NOT read the 2.699 as ''detaching improves deployment MAE''. Two things differ
  from the incumbent at once here: the detach AND the diary/context heads. The attribution
  is settled by exp-v32 (incumbent config + detach only), not by this arm.'
- Do NOT conclude from the dense failure that detaching is harmful per se. Geometry
  improved by the largest margin ever recorded (+3.323 over the identical attached
  config), and the failure signature is CALIBRATION (std_ratio 1.65 vs 1.11) with
  ranking intact (corr 0.733). Those are separable; the fix to test is an emission-scale
  correction, not re-attaching the heads.
- Do NOT score an arm on supabase alone from here on. v31 passes every supabase gate
  and fails no-harm on dense; a supabase-only readout would have shipped a model that
  is worse than the corpus median on the dense draw.
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS, and by the largest margin ever measured on this diagnostic. eff_rank
    10.1057, cos_p50 0.0960, cos_p95 0.6619 (logs/v31_geometry.json). Against the
    anchors: attached-identical-config v29 6.783, incumbent v28 8.599, healthy reference
    8.35, carrier-collapse 4.47. So detaching is +3.323 over the SAME head configuration
    attached, and +1.507 over the incumbent that carries only one head. cos_p50 fell
    from v28''s 0.198 to 0.096, i.e. the tokens spread apart rather than merely surviving.
    detached_probes=True verified live in the run''s own phase_config.json training
    block, not assumed from the profile.'
  gate_id: g0-triple-at-01-keeps-geometry
  passed: true
- evidence: 'PASS on the letter of the gate. supabase MAE 2.699 <= 2.836 and corr
    0.755 >= 0.60 (logs/emission_v31detached_supa_summary.json, n=200, parse_ok 200).
    2.699 is the best supabase MAE on record, beating the incumbent''s 3.030. BUT
    the margin must be priced: -0.331 bpm against the measured 0.195 paired arm-vs-arm
    sigma is -1.70 sigma, INSIDE the k=2 bar, so v31 beating v28 on deployment MAE
    is NOT a demonstrated difference. corr moved -0.038 = -0.55 sigma, also inside
    noise. Honest reading: v31 matches the incumbent on the supabase draw, it does
    not beat it.'
  gate_id: g1-beats-baseline-letter
  passed: true
- evidence: 'FAIL, decisively, and this is the finding of the arm. dense hr_raw MAE
    7.312 against the no-harm band [2.23, 3.83] and the incumbent''s 2.851 (logs/emission_v31detached_dense_summary.json,
    n=128, parse_ok 128). Skill is NEGATIVE at -0.326 bpm against the same 6.986 trivial
    baseline v28 beats by +4.135, i.e. on the dense draw v31 is worse than emitting
    the corpus median. The mechanism is visible in the dispersion statistics rather
    than in MAE alone: corr 0.733 (ranking largely intact) while std_ratio is 1.65
    against v28''s 1.11 and distinct 38 vs 39. So the model still knows WHICH windows
    are high-HR and has lost the SCALE it emits them on -- over-dispersion, the mirror
    image of v30state''s failure (std_ratio 2.71 with corr 0.777 preserved). MAE alone
    would have read as generic degradation; the calibration/ranking split is what
    identifies it.'
  gate_id: g2-no-harm-vs-v28
  passed: false
git_ref: ''
hypothesis_id: full-utilisation-at-the-safe-dose-the-v29-chain-616efb
id: exp-v31-detached-probes-20260831
interpretation:
  at: '2026-08-31T12:03:10+00:00'
  failure_mode: recipe_conditional
  lesson: 'Stop-gradient on the auxiliary heads is CONFIRMED as the fix for the label-head
    geometry poison: the identical config that scored eff_rank 6.783 attached scores
    10.106 detached (+3.323, and +1.507 over the one-head incumbent) -- the highest
    rank ever measured here. The heads were reformatting the representation, not competing
    for capacity. But geometry did not convert: supabase MAE 2.699 is only -1.70 sigma
    against the 0.195 paired floor (a match, not a win) and dense MAE regressed to
    7.312 with NEGATIVE skill. The failure signature is the reusable part: corr 0.733
    and distinct 38 with std_ratio 1.65 vs v28''s 1.11 means RANKING SURVIVED AND
    CALIBRATION DIED -- the same split as v30state (std_ratio 2.71, corr 0.777) in
    the opposite direction. So on this pipeline, ranking and emitted scale are separable
    failure axes, effective rank predicts neither, and MAE alone cannot distinguish
    them: always read corr, std_ratio and distinct together. Practical consequence:
    eff_rank is now demonstrated to be a THRESHOLD (below ~6 = collapse) and NOT a
    quality metric even at the top of its range -- the best-rank encoder in the project''s
    history produced a deployment-worse adapter than one 1.5 rank below it.'
  transfers_to:
  - state-detection-was-substrate-limited-on-the-46fa39
  - the-window-level-ce-state-term-trains-on-a35561
key_numbers:
  cos_p50: 0.096
  delta_dense_mae_vs_v28: 4.461
  delta_rank_vs_v28_attached_incumbent: 1.507
  delta_rank_vs_v29_attached: 3.323
  delta_supa_mae_sigma: -1.7
  dense_corr: 0.733
  dense_mae: 7.312
  dense_skill_bpm: -0.326
  dense_std_ratio: 1.65
  eff_rank: 10.106
  supa_corr: 0.755
  supa_mae: 2.699
  supa_std_ratio: 0.79
observed_effects:
- delta: 3.323
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: -0.331
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
- delta: 4.461
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v29-all01-chain-20260831
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[full-utilisation-at-the-safe-dose-the-v29-chain-616efb]].
Branches from [[exp-v29-all01-chain-20260831]].

## Measurements
- [[eiv_effective_rank]]: before 6.783 → after 10.106  (Δ +3.323, up)
- [[eiv_supabase_seed_band_mae_bpm]]: before 3.03 → after 2.699  (Δ -0.331, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 2.851 → after 7.312  (Δ +4.461, up)

## Gates
- `g0-triple-at-01-keeps-geometry`: PASS — PASS, and by the largest margin ever measured on this diagnostic. eff_rank 10.1057, cos_p50 0.0960, cos_p95 0.6619 (logs/v31_geometry.json). Against the anchors: attached-identical-config v29 6.783, incumbent v28 8.599, healthy reference 8.35, carrier-collapse 4.47. So detaching is +3.323 over the SAME head configuration attached, and +1.507 over the incumbent that carries only one head. cos_p50 fell from v28's 0.198 to 0.096, i.e. the tokens spread apart rather than merely surviving. detached_probes=True verified live in the run's own phase_config.json training block, not assumed from the profile.
- `g1-beats-baseline-letter`: PASS — PASS on the letter of the gate. supabase MAE 2.699 <= 2.836 and corr 0.755 >= 0.60 (logs/emission_v31detached_supa_summary.json, n=200, parse_ok 200). 2.699 is the best supabase MAE on record, beating the incumbent's 3.030. BUT the margin must be priced: -0.331 bpm against the measured 0.195 paired arm-vs-arm sigma is -1.70 sigma, INSIDE the k=2 bar, so v31 beating v28 on deployment MAE is NOT a demonstrated difference. corr moved -0.038 = -0.55 sigma, also inside noise. Honest reading: v31 matches the incumbent on the supabase draw, it does not beat it.
- `g2-no-harm-vs-v28`: **FAIL** — FAIL, decisively, and this is the finding of the arm. dense hr_raw MAE 7.312 against the no-harm band [2.23, 3.83] and the incumbent's 2.851 (logs/emission_v31detached_dense_summary.json, n=128, parse_ok 128). Skill is NEGATIVE at -0.326 bpm against the same 6.986 trivial baseline v28 beats by +4.135, i.e. on the dense draw v31 is worse than emitting the corpus median. The mechanism is visible in the dispersion statistics rather than in MAE alone: corr 0.733 (ranking largely intact) while std_ratio is 1.65 against v28's 1.11 and distinct 38 vs 39. So the model still knows WHICH windows are high-HR and has lost the SCALE it emits them on -- over-dispersion, the mirror image of v30state's failure (std_ratio 2.71 with corr 0.777 preserved). MAE alone would have read as generic degradation; the calibration/ranking split is what identifies it.

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `recipe_conditional`

Stop-gradient on the auxiliary heads is CONFIRMED as the fix for the label-head geometry poison: the identical config that scored eff_rank 6.783 attached scores 10.106 detached (+3.323, and +1.507 over the one-head incumbent) -- the highest rank ever measured here. The heads were reformatting the representation, not competing for capacity. But geometry did not convert: supabase MAE 2.699 is only -1.70 sigma against the 0.195 paired floor (a match, not a win) and dense MAE regressed to 7.312 with NEGATIVE skill. The failure signature is the reusable part: corr 0.733 and distinct 38 with std_ratio 1.65 vs v28's 1.11 means RANKING SURVIVED AND CALIBRATION DIED -- the same split as v30state (std_ratio 2.71, corr 0.777) in the opposite direction. So on this pipeline, ranking and emitted scale are separable failure axes, effective rank predicts neither, and MAE alone cannot distinguish them: always read corr, std_ratio and distinct together. Practical consequence: eff_rank is now demonstrated to be a THRESHOLD (below ~6 = collapse) and NOT a quality metric even at the top of its range -- the best-rank encoder in the project's history produced a deployment-worse adapter than one 1.5 rank below it.

Transfers to: [[state-detection-was-substrate-limited-on-the-46fa39]], [[the-window-level-ce-state-term-trains-on-a35561]]

## Key numbers
- `cos_p50`: 0.096
- `delta_dense_mae_vs_v28`: 4.461
- `delta_rank_vs_v28_attached_incumbent`: 1.507
- `delta_rank_vs_v29_attached`: 3.323
- `delta_supa_mae_sigma`: -1.7
- `dense_corr`: 0.733
- `dense_mae`: 7.312
- `dense_skill_bpm`: -0.326
- `dense_std_ratio`: 1.65
- `eff_rank`: 10.106
- `supa_corr`: 0.755
- `supa_mae`: 2.699
- `supa_std_ratio`: 0.79

## Artifacts
- `outputs/checkpoints/v31detached/phase_a`
- `outputs/checkpoints/v31detached/phase_b`
- `outputs/adapters/v31detached_c`
- `logs/v31_geometry.json`
- `logs/emission_v31detached_supa_summary.json`
- `logs/emission_v31detached_dense_summary.json`
- `logs/v31_driver.log`

## Do not retry
- Do NOT promote v31detached to the deployment arm on the strength of its 2.699 supabase MAE. That is a -1.70 sigma move against the measured 0.195 paired floor (not demonstrated), while its dense capture is a +4.461 bpm regression with NEGATIVE skill. The incumbent stays v28affect01.
- Do NOT read the 2.699 as 'detaching improves deployment MAE'. Two things differ from the incumbent at once here: the detach AND the diary/context heads. The attribution is settled by exp-v32 (incumbent config + detach only), not by this arm.
- Do NOT conclude from the dense failure that detaching is harmful per se. Geometry improved by the largest margin ever recorded (+3.323 over the identical attached config), and the failure signature is CALIBRATION (std_ratio 1.65 vs 1.11) with ranking intact (corr 0.733). Those are separable; the fix to test is an emission-scale correction, not re-attaching the heads.
- Do NOT score an arm on supabase alone from here on. v31 passes every supabase gate and fails no-harm on dense; a supabase-only readout would have shipped a model that is worse than the corpus median on the dense draw.

## Notes
DETACHING WORKS ON GEOMETRY AND DOES NOT BUY DEPLOYMENT. Two results, and they must be reported separately because they point opposite ways.

1. THE MECHANISM CLAIM IS CONFIRMED. The identical head configuration that scored eff_rank 6.783 attached (v29, gate FAIL) scores 10.106 detached -- +3.323, and +1.507 above the incumbent which carries only ONE head. This is the highest effective rank ever measured in the project, with cos_p50 falling 0.198 -> 0.096. So the label heads were not competing for capacity, they were REFORMATTING the shared representation, exactly as the v29 post-mortem proposed. Stop-gradient removes the damage while the heads keep learning their labels.

2. THE DEPLOYMENT CLAIM IS NOT. supabase MAE 2.699 is the best on record but only -1.70 sigma against the 0.195 paired floor, so it MATCHES rather than beats v28. Meanwhile dense MAE is 7.312 against v28's 2.851, with skill -0.326 -- worse than emitting the corpus median on that draw.

THE FAILURE HAS A SPECIFIC SIGNATURE, and it is not generic degradation: corr 0.733 and distinct 38 (v28: 0.937 / 39) say the ranking survived, while std_ratio 1.65 vs 1.11 says the emitted SCALE did not. The model still knows which windows are high-HR and has lost the units it reports them in. This is the mirror image of v30state (std_ratio 2.71, corr 0.777) and confirms as a pattern what the noise-floor work suggested this morning: ranking and calibration are separable failure axes on this pipeline, and MAE alone cannot tell them apart.

WHY THE ARM CANNOT ATTRIBUTE ITS OWN DEPLOYMENT NUMBER: relative to the deployed incumbent it changes TWO things, the detach and the diary/context heads. exp-v32 (v28affect01 + detached_probes, one key) is already running to settle which one moved deployment; it started at 15:01 with zero GPU idle after this arm finished at 14:55.

#deployment-distribution #sensor-text #baseline #contrastive #wearables #epoch-resolution #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
