---
artifacts:
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/E42
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/M42
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/protocol.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/E42-val.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/M42-val.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/E42-supa.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/M42-supa.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/scientific_summary.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/closeout_stage_hashes.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/annotation_parity_verified.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/closeout_tests.log
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/common_parsed_descriptive.json
closed: true
config_note: Pre-full-fit lock revision sha256=eb7bc26b3b0d6e00bbe3fa5347813649d91dfab1315158e247310a32bc1ba651,
  sourcegit=2f2b3abbd61b2531014d66e0cdc0e103ff696479. No full fit or comparison performed
  under predecessor. Same cohorts, data, A/B teacher recipe, C recipes and thresholds.
  Only research serving sensor dtype is preserved to match training (legacy unchanged)
  and real two-process verifier added. Infrastructure stages at03b4671 reused because
  cache/training code is bit-unchanged; original started=1789732708.031924 deadline
  preserved. Raw evidence and v1 snapshots under /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/preflight-history/v1.
  Controlled scientific fits start only after verified real GPU roundtrip. No existing
  outcome used to choose this fix.
cost:
  gpu_hours: 0.0
  wall_hours: 2.027321055928866
crash_reason: ''
created_at: '2026-09-18T12:25:14+00:00'
diagnostics_measured:
- after: 18.15737693630576
  before: 24.06539743540287
  diagnostic_id: eiv_transfer_fimae_hr_val
- after: 1.0
  before: 0.859375
  diagnostic_id: eiv_transfer_parse_rate
- after: 12.244047648260931
  before: 16.548601184429845
  diagnostic_id: eiv_transfer_fimae_hr_supa
- after: -1.8406250000000002
  before: -1.4341479619512345
  diagnostic_id: eiv_transfer_content_gap
do_not_retry:
- 'Do not repeat the same locked seed42 recipe expecting a quality win: moving memory
  0→17 failed content and deployment gates despite a better failure-inclusive score.'
- Do not generalize this allocation/depth contrast to other source encoders, modalities,
  budgets or target laws.
- Do not interpret HR-mean readout or whole-latent interventions as clinical stress
  inference or channel-specific physiology.
- Do not choose a best seed or infer a stable seed variance from two C draws.
external_run: local:/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/wandb (offline/unsynced)
former_ids: []
gate_results:
- evidence: Actual A/B-pair/B-triplet/C rosters and full weights verified against
    /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/lock.json (eb7bc26b3b0d6e00bbe3fa5347813649d91dfab1315158e247310a32bc1ba651);
    same subject-grouped cohorts and upstream source. Fixed test not used for recipe
    selection.
  gate_id: g0-integrity
  passed: true
- evidence: '{"delta": 5.908020499097111, "ci": [0.16554261270383308, 15.651756399443933],
    "ci_level": 0.95, "n": 128, "groups": 22}'
  gate_id: g1-worthwhile
  passed: true
- evidence: '{"n": 128, "parsed": 128, "parse_rate": 1.0, "fimae": 18.15737693630576,
    "valid_mae": 18.15737693630576, "uncapped_errors": [13.858550528616426, 14.454115124914033,
    28.467186154183572, 18.22717701562835, 25.50025797334917, 38.6857441341135, 27.95007179735034,
    27.705603407934113, 14.069236901067704, 14.80419686692455, 16.14364612283599,
    17.79668495760597, 27.91159141116949, 16.857286433225944, 5.081490041458217, 29.82256088424588,
    5.3660259696072, 24.11355294447592, 24.48103353450319, 18.27028593486518, 19.58102235612717,
    4.269249568559118, 11.689703698226744, 13.631562357667029, 22.352709116871615,
    21.898102792333766, 20.073408943249383, 14.08635482984149, 12.812233402638405,
    16.02680731268766, 22.38433264607304, 17.849549547865983, 7.305510723257086, 26.76647622366192,
    13.887956496000875, 12.362395722278315, 21.97407565666871, 27.13861475662562,
    8.944860600997288, 20.913585823299677, 1.7901512910805124, 25.21723854128276,
    18.668206088727565, 44.29746978612446, 19.039121914150854, 13.16446055945893,
    16.45435924954863, 27.911467465800946, 30.319927229289938, 18.05984937419459,
    14.402178472985462, 11.231520028179851, 13.535123174734743, 13.414255737487935,
    8.038555423467969, 15.722369944445497, 16.007155125793126, 30.781595904412633,
    15.04541284827529, 14.983650326799982, 18.330331462424788, 17.851048518561818,
    18.58632613922243, 14.844075455573694, 38.21532078795217, 21.62115634306786, 23.724207926976497,
    9.386165769602165, 19.856039781540836, 16.148677954643738, 43.58837231218926,
    12.678788357597618, 17.160929753020746, 32.71177120002868, 29.329270885288082,
    13.28220000603529, 13.561118332085591, 38.959689506485, 22.464904875263777, 6.727335464004035,
    29.66022178751387, 24.91105851814526, 14.079214749257694, 11.13813503627221, 9.03528428549594,
    4.9119323389315355, 6.622542004498989, 16.860187438414293, 18.07780095098564,
    12.702270325120061, 12.596144141733767, 9.3910018918831, 16.335131112325747, 13.1733444923453,
    10.50791893910256, 10.277142488491009, 2.5468303381060053, 12.715898540900135,
    13.580034684600157, 5.032661779936838, 14.784848422234042, 15.719178922220884,
    20.867829057281163, 9.456551120385164, 11.582195118311994, 9.837268262307653,
    14.954176946700365, 19.638323466860278, 15.955575426393274, 31.323550510375405,
    28.097394084484442, 20.40218914008529, 34.207265217560476, 12.773797775298938,
    20.32125300022728, 13.155744345479313, 22.11799847574556, 23.544296362817533,
    13.010147501297318, 6.072229588279193, 4.140415456802096, 24.077716912860687,
    25.38800416372377, 33.53676105355411, 23.006753047652914, 18.922928027513642,
    19.083532135497386, 21.3849584303154], "uncapped_max": 44.29746978612446, "corr":
    -0.07694482679637271, "calibration_slope": -0.06375199249653958, "calibration_intercept":
    61.04077653680373, "prediction_sd": 6.037808883063328, "truth_sd": 7.287272766606084,
    "std_ratio": 0.82854163367283, "distinct": 9, "top1_mass": 0.3828125}'
  gate_id: g2-coverage
  passed: true
- evidence: '{"delta": 4.304553536168914, "ci": [-1.784100668689777, 12.78879288927031],
    "ci_level": 0.95, "n": 64, "groups": 16}; comparator-minus-treatment; harm upper
    bound is negative CI lower bound; seen subject only'
  gate_id: g3-deployment
  passed: false
- evidence: '{"nulls": {"zero": {"delta": 79.9868894905087, "ci": [76.77126988901668,
    83.24758230603842], "ci_level": 0.95, "n": 32, "groups": 20}, "random": {"delta":
    79.9868894905087, "ci": [76.77126988901668, 83.24758230603842], "ci_level": 0.95,
    "n": 32, "groups": 20}, "metadata_only": {"delta": -1.8406250000000002, "ci":
    [-3.360623167155426, -0.608440476190477], "ci_level": 0.95, "n": 32, "groups":
    20}}, "donor_pairs": 32, "donor_subjects": 20, "donor_direction_accuracy": 0.15625,
    "donor_mae_to_donor": 20.134015629946525, "donor_mae_to_target": 20.331860509491293};
    whole-latent zero/random and donor, not pure HR-only interventions; metadata-only
    preserves recipient clocks/masks/codebook'
  gate_id: g4-content
  passed: false
- evidence: Verified full training artifacts, preserved source, finite loss and original-weight
    audits; four-mode real GPU train-math/serving/reload/generation preflight passed.
  gate_id: g5-executed
  passed: true
git_ref: 2f2b3abbd61b2531014d66e0cdc0e103ff696479
hypothesis_id: eiv-transfer-h2-middle-memory
id: exp-eiv-transfer-h2-20260918-v2
interpretation:
  at: '2026-09-22T09:11:58+00:00'
  failure_mode: recipe_conditional
  lesson: Layer17 improves failure-inclusive MAE over layer0 (24.0654 to 18.1574 validation;
    16.5486 to 12.2440 Supabase), but coverage improves from 110/128 to 128/128 while
    validation r remains -0.0769, and the metadata-only control is better by 1.8406
    bpm. Donor-direction accuracy is 0.15625 and Supabase harm upper95% bound is 1.7841
    bpm >0.5; therefore this is not evidence of useful transferred physiological content.
    Posthoc common-parsed validation subset (110 rows) is worse at layer17 (17.2639
    vs 11.6397), illustrating why format success and numeric fidelity must be separated;
    this biased subset does not replace the preregistered primary.
  transfers_to: []
key_numbers:
  control_supa_fimae: 16.548601184429845
  control_val_fimae: 24.06539743540287
  deployment_harm_ci_upper: 1.784100668689777
  donor_direction_accuracy: 0.15625
  metadata_only_minus_native: -1.8406250000000002
  paired_delta: 5.908020499097111
  treatment_supa_fimae: 12.244047648260931
  treatment_val_fimae: 18.15737693630576
observed_effects:
- delta: -5.908020499097
  diagnostic_id: eiv_transfer_fimae_hr_val
  direction: down
- delta: 0.140625
  diagnostic_id: eiv_transfer_parse_rate
  direction: up
- delta: -4.304553536169
  diagnostic_id: eiv_transfer_fimae_hr_supa
  direction: down
- delta: -0.406477038049
  diagnostic_id: eiv_transfer_content_gap
  direction: down
parent_experiment_id: exp-eiv-transfer-h2-20260918
prediction_match:
  overall: true
  per_diagnostic:
    eiv_transfer_content_gap: false
    eiv_transfer_fimae_hr_val: true
provenance: {}
recipe_fingerprint:
  git_ref: 2f2b3abbd61b2531014d66e0cdc0e103ff696479
  interventions: eiv-transfer-memory-depth
verdict: directional_only
---

## Summary
Tests hypothesis [[eiv-transfer-h2-middle-memory]].
Branches from [[exp-eiv-transfer-h2-20260918]].
At commit `2f2b3abbd61b2531014d66e0cdc0e103ff696479`.
External run: local:/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/wandb (offline/unsynced)

## Measurements
- [[eiv_transfer_fimae_hr_val]]: before 24.06539743540287 → after 18.15737693630576  (Δ -5.90802, down ✓ predicted down)
- [[eiv_transfer_parse_rate]]: before 0.859375 → after 1.0  (Δ +0.140625, up)
- [[eiv_transfer_fimae_hr_supa]]: before 16.548601184429845 → after 12.244047648260931  (Δ -4.30455, down)
- [[eiv_transfer_content_gap]]: before -1.4341479619512345 → after -1.8406250000000002  (Δ -0.406477, down ✗ predicted up)

## Gates
- `g0-integrity`: PASS — Actual A/B-pair/B-triplet/C rosters and full weights verified against /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/lock.json (eb7bc26b3b0d6e00bbe3fa5347813649d91dfab1315158e247310a32bc1ba651); same subject-grouped cohorts and upstream source. Fixed test not used for recipe selection.
- `g1-worthwhile`: PASS — {"delta": 5.908020499097111, "ci": [0.16554261270383308, 15.651756399443933], "ci_level": 0.95, "n": 128, "groups": 22}
- `g2-coverage`: PASS — {"n": 128, "parsed": 128, "parse_rate": 1.0, "fimae": 18.15737693630576, "valid_mae": 18.15737693630576, "uncapped_errors": [13.858550528616426, 14.454115124914033, 28.467186154183572, 18.22717701562835, 25.50025797334917, 38.6857441341135, 27.95007179735034, 27.705603407934113, 14.069236901067704, 14.80419686692455, 16.14364612283599, 17.79668495760597, 27.91159141116949, 16.857286433225944, 5.081490041458217, 29.82256088424588, 5.3660259696072, 24.11355294447592, 24.48103353450319, 18.27028593486518, 19.58102235612717, 4.269249568559118, 11.689703698226744, 13.631562357667029, 22.352709116871615, 21.898102792333766, 20.073408943249383, 14.08635482984149, 12.812233402638405, 16.02680731268766, 22.38433264607304, 17.849549547865983, 7.305510723257086, 26.76647622366192, 13.887956496000875, 12.362395722278315, 21.97407565666871, 27.13861475662562, 8.944860600997288, 20.913585823299677, 1.7901512910805124, 25.21723854128276, 18.668206088727565, 44.29746978612446, 19.039121914150854, 13.16446055945893, 16.45435924954863, 27.911467465800946, 30.319927229289938, 18.05984937419459, 14.402178472985462, 11.231520028179851, 13.535123174734743, 13.414255737487935, 8.038555423467969, 15.722369944445497, 16.007155125793126, 30.781595904412633, 15.04541284827529, 14.983650326799982, 18.330331462424788, 17.851048518561818, 18.58632613922243, 14.844075455573694, 38.21532078795217, 21.62115634306786, 23.724207926976497, 9.386165769602165, 19.856039781540836, 16.148677954643738, 43.58837231218926, 12.678788357597618, 17.160929753020746, 32.71177120002868, 29.329270885288082, 13.28220000603529, 13.561118332085591, 38.959689506485, 22.464904875263777, 6.727335464004035, 29.66022178751387, 24.91105851814526, 14.079214749257694, 11.13813503627221, 9.03528428549594, 4.9119323389315355, 6.622542004498989, 16.860187438414293, 18.07780095098564, 12.702270325120061, 12.596144141733767, 9.3910018918831, 16.335131112325747, 13.1733444923453, 10.50791893910256, 10.277142488491009, 2.5468303381060053, 12.715898540900135, 13.580034684600157, 5.032661779936838, 14.784848422234042, 15.719178922220884, 20.867829057281163, 9.456551120385164, 11.582195118311994, 9.837268262307653, 14.954176946700365, 19.638323466860278, 15.955575426393274, 31.323550510375405, 28.097394084484442, 20.40218914008529, 34.207265217560476, 12.773797775298938, 20.32125300022728, 13.155744345479313, 22.11799847574556, 23.544296362817533, 13.010147501297318, 6.072229588279193, 4.140415456802096, 24.077716912860687, 25.38800416372377, 33.53676105355411, 23.006753047652914, 18.922928027513642, 19.083532135497386, 21.3849584303154], "uncapped_max": 44.29746978612446, "corr": -0.07694482679637271, "calibration_slope": -0.06375199249653958, "calibration_intercept": 61.04077653680373, "prediction_sd": 6.037808883063328, "truth_sd": 7.287272766606084, "std_ratio": 0.82854163367283, "distinct": 9, "top1_mass": 0.3828125}
- `g3-deployment`: **FAIL** — {"delta": 4.304553536168914, "ci": [-1.784100668689777, 12.78879288927031], "ci_level": 0.95, "n": 64, "groups": 16}; comparator-minus-treatment; harm upper bound is negative CI lower bound; seen subject only
- `g4-content`: **FAIL** — {"nulls": {"zero": {"delta": 79.9868894905087, "ci": [76.77126988901668, 83.24758230603842], "ci_level": 0.95, "n": 32, "groups": 20}, "random": {"delta": 79.9868894905087, "ci": [76.77126988901668, 83.24758230603842], "ci_level": 0.95, "n": 32, "groups": 20}, "metadata_only": {"delta": -1.8406250000000002, "ci": [-3.360623167155426, -0.608440476190477], "ci_level": 0.95, "n": 32, "groups": 20}}, "donor_pairs": 32, "donor_subjects": 20, "donor_direction_accuracy": 0.15625, "donor_mae_to_donor": 20.134015629946525, "donor_mae_to_target": 20.331860509491293}; whole-latent zero/random and donor, not pure HR-only interventions; metadata-only preserves recipient clocks/masks/codebook
- `g5-executed`: PASS — Verified full training artifacts, preserved source, finite loss and original-weight audits; four-mode real GPU train-math/serving/reload/generation preflight passed.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

Layer17 improves failure-inclusive MAE over layer0 (24.0654 to 18.1574 validation; 16.5486 to 12.2440 Supabase), but coverage improves from 110/128 to 128/128 while validation r remains -0.0769, and the metadata-only control is better by 1.8406 bpm. Donor-direction accuracy is 0.15625 and Supabase harm upper95% bound is 1.7841 bpm >0.5; therefore this is not evidence of useful transferred physiological content. Posthoc common-parsed validation subset (110 rows) is worse at layer17 (17.2639 vs 11.6397), illustrating why format success and numeric fidelity must be separated; this biased subset does not replace the preregistered primary.

## Key numbers
- `control_supa_fimae`: 16.5486
- `control_val_fimae`: 24.0654
- `deployment_harm_ci_upper`: 1.7841
- `donor_direction_accuracy`: 0.15625
- `metadata_only_minus_native`: -1.84063
- `paired_delta`: 5.90802
- `treatment_supa_fimae`: 12.244
- `treatment_val_fimae`: 18.1574

## Artifacts
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/E42`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/M42`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/protocol.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/E42-val.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/M42-val.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/E42-supa.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/M42-supa.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/scientific_summary.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/closeout_stage_hashes.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/annotation_parity_verified.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/closeout_tests.log`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/common_parsed_descriptive.json`

## Do not retry
- Do not repeat the same locked seed42 recipe expecting a quality win: moving memory 0→17 failed content and deployment gates despite a better failure-inclusive score.
- Do not generalize this allocation/depth contrast to other source encoders, modalities, budgets or target laws.
- Do not interpret HR-mean readout or whole-latent interventions as clinical stress inference or channel-specific physiology.
- Do not choose a best seed or infer a stable seed variance from two C draws.

## Notes
One-seed discovery E42/M42; all numbers recomputed from complete locked captures. Gate outcomes: {"worthwhile": true, "coverage": true, "content": false, "deployment": false, "content_status": "measured", "deployment_status": "measured", "pass": false}. This is not recipe promotion; confirmation, if reached, is separate. Zero/random also perturb positional bias; donor exchanges full sensor memory. Metadata-only is the separate fixed clock/mask/codebook guard. No clinical, momentary-state, information-theoretic-loss or global-generalization claim. Conditional seed43 and locked-test branches NOT REACHED because discovery failed, not crashes/null measurements. No new training budget was opened. wall_hours records only this comparison's fit+evaluation subprocess time; common A/B source is shared, not double charged. Training curves remain offline and unsynced. Actual offline W&B IDs: {"E42": "pwlqiqio", "M42": "9vpelj00"}. Verified aggregate-only remote analysis: https://wandb.ai/antonix-itmo/optihealth-eiv/runs/zh2hn6mz . This static mcp-analysis run is not a synchronized training history.

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
