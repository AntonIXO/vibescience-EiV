---
artifacts: []
closed: true
config_note: 'Confirmatory follow-up to exp-e33f, using data that did not exist when
  e33f closed. The Stage C 3-seed run (proc_c1940deb8e45, started 2026-08-05 22:25:19
  UTC) completed all three seeds and EXITED 1 at 03:36:37 UTC with ''FAIL: All 3 Stage
  C seeds collapsed'' (run_12ch_pipeline.sh:551-557), leaving the canonical adapter
  dir unpopulated. Seed 3''s probe ran AFTER the counterfactual.py patch from e33f,
  so its artifact is the first to carry the swap_total/swap_measured fields. Artifacts:
  outputs/probes/e33_phase_c.seed_{1,2,3}.json, logs/e33_stage_c_driver.log, adapters
  outputs/adapters/medgemma_12ch_e33.seed_{1,2,3}. Recovery performed by scratch/watch_stage_c.py
  (scheduled cron ''stage-c-watchdog'', no_agent, every 15m, silent while healthy).
  NOTE: my own timeout projection was WRONG in the safe direction -- I predicted the
  timeout would kill seed 3''s probe ~9 min short; it actually finished 03:36:36,
  about 22 min BEFORE the 03:58:39 kill, because I over-estimated probe duration by
  ~15 min. No data was lost to the timeout.'
crash_reason: ''
created_at: '2026-08-06T03:38:24+00:00'
diagnostics_measured:
- after: 0.0
  before: 0.3
  diagnostic_id: eiv_lm_hidden_peak_r2
external_run: ''
gate_results:
- evidence: "DIRECTLY MEASURED, NO LONGER AN INFERENCE. Seed 3's probe ran after the
    e33f patch to counterfactual.py, so it is the first artifact to persist the denominator.
    outputs/probes/e33_phase_c.seed_3.json p3_counterfactual reads VERBATIM:\n  {\"swap_attribution_acc\":
    0.0, \"swap_total\": 0, \"swap_correct\": 0, \"swap_measured\": false}\nswap_total
    == 0. The gate performed ZERO measurements and returned 0.0, and the driver treated
    that 0.0 as a failing score. In e33f this was established by source reading plus
    a data-side contrast argument; it is now an observed fact on a real run. All three
    seeds report swap_attribution_acc = 0.0000 and all three were failed on it --
    seed 1 SOLELY on it ('collapsed=yes:swap<0.3'). Because the gate is one of three
    tripwires in an OR, and it can never pass when the model's sampled pairs do not
    contrast, it can veto every seed regardless of model quality. That is exactly
    what happened: 0/3 non-collapsed, exit 1, ~9 GPU-hours refused."
  gate_id: gate-swap-metric-not-vacuous
  passed: false
- evidence: 'CONFIRMED ON ALL THREE SEEDS. bucket_acc_ordered and bucket_acc_shuffled
    are EXACTLY 0.0000 for seed 1, seed 2 and seed 3 alike, while p4 -- which scores
    against the trained label -- separates the seeds cleanly (hr_acc 0.4297 / 0.0234
    / 0.3516) with parse_failure_rate 0.0000 / 0.0000 / 0.0078. A metric that returns
    an identical hard zero for three models of visibly different quality is measuring
    nothing about the model. This is the predicted consequence of the label-function
    mismatch quantified in e33f: 19.40% agreement on n=1546, with the tachycardic
    class (40.88% of the population under the trained label) unreachable under p6''s
    daily-MEAN threshold ladder.'
  gate_id: gate-eval-label-matches-training-label
  passed: false
- evidence: "CONFIRMED, WITH AN IMPORTANT REFINEMENT -- THE VERDICT IS WRONG FOR SEED
    1 BUT RIGHT FOR SEED 2. Full comparison across seeds (honest metrics only):\n
    \ metric                            seed1    seed2    seed3\n  p4 hr_acc                        0.4297
    \  0.0234   0.3516\n  p4 parse_failure_rate            0.0000   0.0000   0.0078\n
    \ p2 output_diff_rate_zero         1.0000   1.0000   1.0000\n  p2 monotonic_scale_response
    \     0.8438   0.0938   0.8906\n  p2 null_sensor_bucket_disagree   0.6562   0.2969
    \  0.6875\n  p4 state_acc                     0.6172   0.5469   0.2656\n  p7 sensor_attention_mass
    \        0.1404   0.1315   0.1105\nSEED 2 IS GENUINELY DEGENERATE and the p2 family
    says so independently of the broken gates: monotonic_scale_response collapses
    to 0.0938 (vs 0.84/0.89) and null_sensor_bucket_disagreement to 0.2969 (vs 0.66/0.69),
    with hr_acc 0.0234. So the pipeline's documented bistability is REAL -- this is
    what a collapsed seed looks like, and the honest metrics detect it. SEEDS 1 AND
    3 do not look like that: both hold output_diff_rate_zero at 1.0 and monotonic
    response at 0.84/0.89. Seed 1 was failed SOLELY by the vacuous swap gate; seed
    3 fails an honest tripwire (hr_acc 0.3516 < 0.40) but only narrowly, and its swap
    failure is provably vacuous. CONCLUSION: the verdict 'all 3 collapsed' is not
    supported -- one seed is collapsed, one clears the valid tripwires, one narrowly
    misses on a real metric."
  gate_id: gate-verdict-consistent-with-sibling-metrics
  passed: false
git_ref: ''
hypothesis_id: the-stage-c-collapse-tripwires-are-defective-and-seed-1-s-collapsed-yes-swap-0-3
id: exp-e33g-swap-vacuity-confirmed-20260806
observed_effects:
- delta: -0.3
  diagnostic_id: eiv_lm_hidden_peak_r2
  direction: down
parent_experiment_id: exp-e33f-tripwire-validity-20260806
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[the-stage-c-collapse-tripwires-are-defective-and-seed-1-s-collapsed-yes-swap-0-3]].
Branches from [[exp-e33f-tripwire-validity-20260806]].

## Measurements
- [[eiv_lm_hidden_peak_r2]]: before 0.3 → after 0.0  (Δ -0.3, down)

## Gates
- `gate-swap-metric-not-vacuous`: **FAIL** — DIRECTLY MEASURED, NO LONGER AN INFERENCE. Seed 3's probe ran after the e33f patch to counterfactual.py, so it is the first artifact to persist the denominator. outputs/probes/e33_phase_c.seed_3.json p3_counterfactual reads VERBATIM:
  {"swap_attribution_acc": 0.0, "swap_total": 0, "swap_correct": 0, "swap_measured": false}
swap_total == 0. The gate performed ZERO measurements and returned 0.0, and the driver treated that 0.0 as a failing score. In e33f this was established by source reading plus a data-side contrast argument; it is now an observed fact on a real run. All three seeds report swap_attribution_acc = 0.0000 and all three were failed on it -- seed 1 SOLELY on it ('collapsed=yes:swap<0.3'). Because the gate is one of three tripwires in an OR, and it can never pass when the model's sampled pairs do not contrast, it can veto every seed regardless of model quality. That is exactly what happened: 0/3 non-collapsed, exit 1, ~9 GPU-hours refused.
- `gate-eval-label-matches-training-label`: **FAIL** — CONFIRMED ON ALL THREE SEEDS. bucket_acc_ordered and bucket_acc_shuffled are EXACTLY 0.0000 for seed 1, seed 2 and seed 3 alike, while p4 -- which scores against the trained label -- separates the seeds cleanly (hr_acc 0.4297 / 0.0234 / 0.3516) with parse_failure_rate 0.0000 / 0.0000 / 0.0078. A metric that returns an identical hard zero for three models of visibly different quality is measuring nothing about the model. This is the predicted consequence of the label-function mismatch quantified in e33f: 19.40% agreement on n=1546, with the tachycardic class (40.88% of the population under the trained label) unreachable under p6's daily-MEAN threshold ladder.
- `gate-verdict-consistent-with-sibling-metrics`: **FAIL** — CONFIRMED, WITH AN IMPORTANT REFINEMENT -- THE VERDICT IS WRONG FOR SEED 1 BUT RIGHT FOR SEED 2. Full comparison across seeds (honest metrics only):
  metric                            seed1    seed2    seed3
  p4 hr_acc                        0.4297   0.0234   0.3516
  p4 parse_failure_rate            0.0000   0.0000   0.0078
  p2 output_diff_rate_zero         1.0000   1.0000   1.0000
  p2 monotonic_scale_response      0.8438   0.0938   0.8906
  p2 null_sensor_bucket_disagree   0.6562   0.2969   0.6875
  p4 state_acc                     0.6172   0.5469   0.2656
  p7 sensor_attention_mass         0.1404   0.1315   0.1105
SEED 2 IS GENUINELY DEGENERATE and the p2 family says so independently of the broken gates: monotonic_scale_response collapses to 0.0938 (vs 0.84/0.89) and null_sensor_bucket_disagreement to 0.2969 (vs 0.66/0.69), with hr_acc 0.0234. So the pipeline's documented bistability is REAL -- this is what a collapsed seed looks like, and the honest metrics detect it. SEEDS 1 AND 3 do not look like that: both hold output_diff_rate_zero at 1.0 and monotonic response at 0.84/0.89. Seed 1 was failed SOLELY by the vacuous swap gate; seed 3 fails an honest tripwire (hr_acc 0.3516 < 0.40) but only narrowly, and its swap failure is provably vacuous. CONCLUSION: the verdict 'all 3 collapsed' is not supported -- one seed is collapsed, one clears the valid tripwires, one narrowly misses on a real metric.

**Verdict:** inconclusive

## Notes
THE VACUITY IS NOW A MEASURED FACT, AND THE WATCHDOG RECOVERED ~9 GPU-HOURS THE PIPELINE HAD REFUSED.

=== WHAT HAPPENED ===
The Stage C 3-seed run completed all three seeds and EXITED 1 at 03:36:37 UTC:
  === Stage C multi-seed summary (0 / 3 non-collapsed) ===
  seed  parse_fail  hr_acc  swap_attr  hr_r2   hr_mae  score   collapsed
  1     0.0000      0.4297  0.0000     0.9254  4.6142  0.4297  yes:swap<0.3
  2     0.0000      0.0234  0.0000     0.9254  4.6142  0.0234  yes:hr_acc<0.4,swap<0.3
  3     0.0078      0.3516  0.0000     0.9254  4.6142  0.3516  yes:hr_acc<0.4,swap<0.3
  FAIL: All 3 Stage C seeds collapsed.
Per run_12ch_pipeline.sh:551-557 it exited without ever populating the canonical adapter dir.

=== THE DECISIVE MEASUREMENT ===
Seed 3's probe ran AFTER the e33f patch, so it is the first artifact to persist the denominator. Verbatim from outputs/probes/e33_phase_c.seed_3.json:
  {"swap_attribution_acc": 0.0, "swap_total": 0, "swap_correct": 0, "swap_measured": false}
swap_total == 0. The gate that vetoed every seed NEVER MADE A SINGLE MEASUREMENT. In e33f this was inferred from source; it is now observed on a real run. Timing was luck worth noting: had I patched an hour later, this evidence would not exist and the vacuity would still be an argument rather than a fact.

=== BUT THE BISTABILITY IS REAL -- SEED 2 IS GENUINELY COLLAPSED ===
This is the part that keeps the finding honest. The p2 ablation family separates the seeds without any help from the broken gates:
  metric                            seed1    seed2    seed3
  p4 hr_acc                        0.4297   0.0234   0.3516
  p2 output_diff_rate_zero         1.0000   1.0000   1.0000
  p2 monotonic_scale_response      0.8438   0.0938   0.8906
  p2 null_sensor_bucket_disagree   0.6562   0.2969   0.6875
Seed 2's monotonic response collapses to 0.0938 against 0.84/0.89, and its bucket disagreement to 0.2969 against 0.66/0.69. That is what a genuinely collapsed seed looks like, and the HONEST metrics catch it. So the pipeline's documented ~50% bistability is NOT a measurement artifact -- I am not overturning that. What is defective is the tripwire set's ability to TELL THE SEEDS APART: all three read swap 0.0000 and p6 0.0000/0.0000 regardless of quality.

=== RECOVERY PERFORMED ===
scratch/watch_stage_c.py (cron 'stage-c-watchdog', no_agent, 15m, silent while healthy) fired once at terminal state, re-scored on honest metrics only, and promoted seed 1 -- the ONLY seed clearing both valid tripwires (parse_fail 0.0000 < 0.5, hr_acc 0.4297 >= 0.40) -- to outputs/adapters/medgemma_12ch_e33. Verified: bundle byte-identical to medgemma_12ch_e33.seed_1 (diff -rq clean), manifest version 2.0, base google/medgemma-1.5-4b-it, git_sha c674cee8912b, final_loss 1.4145, all four sensor_modules plus lora/ present.

=== A PREDICTION OF MINE THAT WAS WRONG ===
I projected the 'timeout 20000' would kill seed 3's probe ~9 minutes short. It finished at 03:36:36, about 22 minutes BEFORE the 03:58:39 kill -- I over-estimated probe duration by roughly 15 minutes. Wrong in the safe direction, and no data was lost, but the projection method (extrapolating one seed's mtime deltas) is evidently not precise enough to bet an artifact on. Correct measured cadence: ~37m36s training + ~1h16m probing for the seed I measured, but seed 3's probe was materially faster.

=== WHAT THIS DOES NOT ESTABLISH ===
Seed 1 is NOT thereby a good model. hr_acc 0.4297 clears the 0.40 bar by 0.03. p1 hr_r2 0.9254 is identical across all three seeds including the collapsed one -- a probe on encoder/projector hidden states that E24-E32 established proves presence and NEVER usability, and its invariance here is a live demonstration of exactly that. p7 sensor_attention_mass 0.1404 sits BELOW the uniform baseline 0.2238. The selected adapter is a starting point for rb-e34's powered native-emission evaluation, not a result.

=== FOLLOW-UP REQUIRED ===
(1) The p3 gate should be replaced in the driver by scratch/diag_swap_gap.py, which the source names as decisive; at minimum the driver must treat swap_measured == false as 'not measured' rather than as a failing score.
(2) p6 must be switched to datasets.hr_label_bucket before its accuracies mean anything.
(3) rb-e34 proceeds against outputs/adapters/medgemma_12ch_e33 with all three seeds reported.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
