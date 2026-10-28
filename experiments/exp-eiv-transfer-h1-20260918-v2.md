---
artifacts:
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/L42
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/P42
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/protocol.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/L42-val.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/P42-val.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/L42-supa.json
- /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/P42-supa.json
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
  wall_hours: 2.1732036873367098
crash_reason: ''
created_at: '2026-09-18T12:25:13+00:00'
diagnostics_measured:
- after: 10.149761711812495
  before: 3.991389007640106
  diagnostic_id: eiv_transfer_fimae_hr_val
- after: 1.0
  before: 1.0
  diagnostic_id: eiv_transfer_parse_rate
- after: 14.951148013352778
  before: 4.193206802848424
  diagnostic_id: eiv_transfer_fimae_hr_supa
- after: 1.7392359756628752
  before: 5.320901237802776
  diagnostic_id: eiv_transfer_content_gap
do_not_retry:
- 'Do not repeat the same locked seed42 recipe expecting a quality win: connector-only
  failed accuracy, deployment and donor tracking.'
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
- evidence: '{"delta": -6.158372704172388, "ci": [-9.43316234787005, -4.5482118991816485],
    "ci_level": 0.95, "n": 128, "groups": 22}'
  gate_id: g1-worthwhile
  passed: false
- evidence: '{"n": 128, "parsed": 128, "parse_rate": 1.0, "fimae": 10.149761711812495,
    "valid_mae": 10.149761711812495, "uncapped_errors": [0.7585505286164249, 2.245884875085963,
    27.532813845816435, 0.8271770156283509, 30.499742026650836, 17.31425586588651,
    10.550071797350341, 10.305603407934115, 0.9692369010677027, 32.19580313307546,
    39.85635387716402, 38.20331504239404, 10.51159141116949, 3.757286433225943, 23.91850995854179,
    12.422560884245883, 44.533974030392805, 13.886447055524087, 31.518966465496817,
    28.72971406513483, 2.1810223561271727, 42.73075043144089, 1.2897036982267451,
    22.068437642332967, 4.952709116871617, 4.498102792333768, 2.6734089432493846,
    2.613645170158506, 0.28776659736159615, 0.6731926873123371, 4.984332646073042,
    0.4495495478659848, 10.094489276742912, 39.23352377633809, 0.7879564960008736,
    4.337604277721681, 4.574075656668711, 9.738614756625623, 1.4551393990027108, 4.213585823299681,
    11.309848708919489, 7.817238541282762, 1.2682060887275668, 22.197469786124458,
    9.43912191415086, 26.735539440541075, 10.45435924954863, 10.511467465800948, 8.219927229289937,
    0.659849374194593, 1.3021784729854602, 5.468479971820145, 7.2351231747347455,
    13.414255737487935, 5.061444576532033, 9.4223699444455, 9.707155125793129, 25.218404095587374,
    9.04541284827529, 8.583650326799983, 11.930331462424789, 5.151048518561822, 4.81367386077757,
    5.144075455573699, 17.784679212047834, 11.221156343067861, 14.024207926976501,
    2.9861657696021666, 13.456039781540838, 9.748677954643739, 12.41162768781075,
    3.0212116424023776, 7.46092975302075, 27.188228799971327, 30.570729114711924,
    3.5822000060352934, 4.161118332085593, 17.04031049351501, 9.764904875263781, 0.3273354640040367,
    36.33977821248614, 4.511058518145262, 4.379214749257699, 1.5618649637277855, 2.635284285495942,
    8.111932338931538, 2.7774579955010097, 10.460187438414295, 11.677800950985642,
    0.6977296748799375, 0.103855858266229, 2.9910018918831014, 9.935131112325749,
    3.473344492345305, 4.107918939102561, 13.777142488491009, 3.8531696618939932,
    0.01589854090013887, 3.8800346846001617, 4.667338220063158, 5.084848422234046,
    6.019178922220888, 7.467829057281165, 0.24344887961483153, 1.1178048816880022,
    0.5627317376923457, 4.554176946700366, 3.7616765331397204, 2.5555754263932755,
    13.923550510375406, 11.397394084484446, 3.702189140085295, 25.69273478243953,
    3.0737977752989423, 3.6212530002272842, 9.755744345479314, 5.417998475745563,
    36.35570363718247, 6.61014750129732, 2.672229588279194, 2.259584543197903, 7.377716912860691,
    8.688004163723775, 22.463238946445898, 6.3067530476529186, 2.222928027513646,
    2.38353213549739, 4.684958430315405], "uncapped_max": 44.533974030392805, "corr":
    0.11219079974367704, "calibration_slope": 0.2039737264530246, "calibration_intercept":
    61.23301793816466, "prediction_sd": 13.248966807263162, "truth_sd": 7.287272766606084,
    "std_ratio": 1.818096732700405, "distinct": 11, "top1_mass": 0.2890625}'
  gate_id: g2-coverage
  passed: true
- evidence: '{"delta": -10.757941210504354, "ci": [-12.802108213930515, -8.850988187213567],
    "ci_level": 0.95, "n": 64, "groups": 16}; comparator-minus-treatment; harm upper
    bound is negative CI lower bound; seen subject only'
  gate_id: g3-deployment
  passed: false
- evidence: '{"nulls": {"zero": {"delta": 85.5905180878728, "ci": [80.75458819379558,
    89.17692696068241], "ci_level": 0.95, "n": 32, "groups": 20}, "random": {"delta":
    85.5905180878728, "ci": [80.75458819379558, 89.17692696068241], "ci_level": 0.95,
    "n": 32, "groups": 20}, "metadata_only": {"delta": 1.7392359756628752, "ci": [0.4233974452245403,
    3.7493514715378087], "ci_level": 0.95, "n": 32, "groups": 20}}, "donor_pairs":
    32, "donor_subjects": 20, "donor_direction_accuracy": 0.375, "donor_mae_to_donor":
    15.128871348147664, "donor_mae_to_target": 16.381502384454237}; whole-latent zero/random
    and donor, not pure HR-only interventions; metadata-only preserves recipient clocks/masks/codebook'
  gate_id: g4-content
  passed: false
- evidence: Verified full training artifacts, preserved source, finite loss and original-weight
    audits; four-mode real GPU train-math/serving/reload/generation preflight passed.
  gate_id: g5-executed
  passed: true
git_ref: 2f2b3abbd61b2531014d66e0cdc0e103ff696479
hypothesis_id: eiv-transfer-h1-connector-only
id: exp-eiv-transfer-h1-20260918-v2
interpretation:
  at: '2026-09-22T09:11:55+00:00'
  failure_mode: recipe_conditional
  lesson: 'Under the frozen locked single-seed allocation recipe, connector-only worsens
    HR failure-inclusive MAE from 3.9914 to 10.1498 bpm on validation and 4.1932 to
    14.9511 on sparse Supabase. Validation r falls from 0.8689 to 0.1122 despite 100%
    numeric parsing; donor direction is 0.375 versus the preregistered 0.65 bar. Nonzero
    gradients and correct save/load/annotation parity do not establish semantic compatibility:
    do not substitute this frozen-receiver recipe for LoRA or generalize the negative
    to all interfaces/budgets.'
  transfers_to: []
key_numbers:
  control_supa_fimae: 4.193206802848424
  control_val_fimae: 3.991389007640106
  deployment_harm_ci_upper: 12.802108213930515
  donor_direction_accuracy: 0.375
  metadata_only_minus_native: 1.7392359756628752
  paired_delta: -6.158372704172388
  treatment_supa_fimae: 14.951148013352778
  treatment_val_fimae: 10.149761711812495
observed_effects:
- delta: 6.158372704172
  diagnostic_id: eiv_transfer_fimae_hr_val
  direction: up
- delta: 0.0
  diagnostic_id: eiv_transfer_parse_rate
  direction: none
- delta: 10.757941210504
  diagnostic_id: eiv_transfer_fimae_hr_supa
  direction: up
- delta: -3.58166526214
  diagnostic_id: eiv_transfer_content_gap
  direction: down
parent_experiment_id: exp-eiv-transfer-h1-20260918
prediction_match:
  overall: false
  per_diagnostic:
    eiv_transfer_content_gap: false
    eiv_transfer_fimae_hr_val: false
provenance: {}
recipe_fingerprint:
  git_ref: 2f2b3abbd61b2531014d66e0cdc0e103ff696479
  interventions: eiv-transfer-connector-only
verdict: refutes
---

## Summary
Tests hypothesis [[eiv-transfer-h1-connector-only]].
Branches from [[exp-eiv-transfer-h1-20260918]].
At commit `2f2b3abbd61b2531014d66e0cdc0e103ff696479`.
External run: local:/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/wandb (offline/unsynced)

## Measurements
- [[eiv_transfer_fimae_hr_val]]: before 3.991389007640106 → after 10.149761711812495  (Δ +6.15837, up ✗ predicted down)
- [[eiv_transfer_parse_rate]]: before 1.0 → after 1.0  (Δ +0, flat)
- [[eiv_transfer_fimae_hr_supa]]: before 4.193206802848424 → after 14.951148013352778  (Δ +10.7579, up)
- [[eiv_transfer_content_gap]]: before 5.320901237802776 → after 1.7392359756628752  (Δ -3.58167, down ✗ predicted up)

## Gates
- `g0-integrity`: PASS — Actual A/B-pair/B-triplet/C rosters and full weights verified against /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/lock.json (eb7bc26b3b0d6e00bbe3fa5347813649d91dfab1315158e247310a32bc1ba651); same subject-grouped cohorts and upstream source. Fixed test not used for recipe selection.
- `g1-worthwhile`: **FAIL** — {"delta": -6.158372704172388, "ci": [-9.43316234787005, -4.5482118991816485], "ci_level": 0.95, "n": 128, "groups": 22}
- `g2-coverage`: PASS — {"n": 128, "parsed": 128, "parse_rate": 1.0, "fimae": 10.149761711812495, "valid_mae": 10.149761711812495, "uncapped_errors": [0.7585505286164249, 2.245884875085963, 27.532813845816435, 0.8271770156283509, 30.499742026650836, 17.31425586588651, 10.550071797350341, 10.305603407934115, 0.9692369010677027, 32.19580313307546, 39.85635387716402, 38.20331504239404, 10.51159141116949, 3.757286433225943, 23.91850995854179, 12.422560884245883, 44.533974030392805, 13.886447055524087, 31.518966465496817, 28.72971406513483, 2.1810223561271727, 42.73075043144089, 1.2897036982267451, 22.068437642332967, 4.952709116871617, 4.498102792333768, 2.6734089432493846, 2.613645170158506, 0.28776659736159615, 0.6731926873123371, 4.984332646073042, 0.4495495478659848, 10.094489276742912, 39.23352377633809, 0.7879564960008736, 4.337604277721681, 4.574075656668711, 9.738614756625623, 1.4551393990027108, 4.213585823299681, 11.309848708919489, 7.817238541282762, 1.2682060887275668, 22.197469786124458, 9.43912191415086, 26.735539440541075, 10.45435924954863, 10.511467465800948, 8.219927229289937, 0.659849374194593, 1.3021784729854602, 5.468479971820145, 7.2351231747347455, 13.414255737487935, 5.061444576532033, 9.4223699444455, 9.707155125793129, 25.218404095587374, 9.04541284827529, 8.583650326799983, 11.930331462424789, 5.151048518561822, 4.81367386077757, 5.144075455573699, 17.784679212047834, 11.221156343067861, 14.024207926976501, 2.9861657696021666, 13.456039781540838, 9.748677954643739, 12.41162768781075, 3.0212116424023776, 7.46092975302075, 27.188228799971327, 30.570729114711924, 3.5822000060352934, 4.161118332085593, 17.04031049351501, 9.764904875263781, 0.3273354640040367, 36.33977821248614, 4.511058518145262, 4.379214749257699, 1.5618649637277855, 2.635284285495942, 8.111932338931538, 2.7774579955010097, 10.460187438414295, 11.677800950985642, 0.6977296748799375, 0.103855858266229, 2.9910018918831014, 9.935131112325749, 3.473344492345305, 4.107918939102561, 13.777142488491009, 3.8531696618939932, 0.01589854090013887, 3.8800346846001617, 4.667338220063158, 5.084848422234046, 6.019178922220888, 7.467829057281165, 0.24344887961483153, 1.1178048816880022, 0.5627317376923457, 4.554176946700366, 3.7616765331397204, 2.5555754263932755, 13.923550510375406, 11.397394084484446, 3.702189140085295, 25.69273478243953, 3.0737977752989423, 3.6212530002272842, 9.755744345479314, 5.417998475745563, 36.35570363718247, 6.61014750129732, 2.672229588279194, 2.259584543197903, 7.377716912860691, 8.688004163723775, 22.463238946445898, 6.3067530476529186, 2.222928027513646, 2.38353213549739, 4.684958430315405], "uncapped_max": 44.533974030392805, "corr": 0.11219079974367704, "calibration_slope": 0.2039737264530246, "calibration_intercept": 61.23301793816466, "prediction_sd": 13.248966807263162, "truth_sd": 7.287272766606084, "std_ratio": 1.818096732700405, "distinct": 11, "top1_mass": 0.2890625}
- `g3-deployment`: **FAIL** — {"delta": -10.757941210504354, "ci": [-12.802108213930515, -8.850988187213567], "ci_level": 0.95, "n": 64, "groups": 16}; comparator-minus-treatment; harm upper bound is negative CI lower bound; seen subject only
- `g4-content`: **FAIL** — {"nulls": {"zero": {"delta": 85.5905180878728, "ci": [80.75458819379558, 89.17692696068241], "ci_level": 0.95, "n": 32, "groups": 20}, "random": {"delta": 85.5905180878728, "ci": [80.75458819379558, 89.17692696068241], "ci_level": 0.95, "n": 32, "groups": 20}, "metadata_only": {"delta": 1.7392359756628752, "ci": [0.4233974452245403, 3.7493514715378087], "ci_level": 0.95, "n": 32, "groups": 20}}, "donor_pairs": 32, "donor_subjects": 20, "donor_direction_accuracy": 0.375, "donor_mae_to_donor": 15.128871348147664, "donor_mae_to_target": 16.381502384454237}; whole-latent zero/random and donor, not pure HR-only interventions; metadata-only preserves recipient clocks/masks/codebook
- `g5-executed`: PASS — Verified full training artifacts, preserved source, finite loss and original-weight audits; four-mode real GPU train-math/serving/reload/generation preflight passed.

**Verdict:** refutes

## Interpretation
**Failure mode:** `recipe_conditional`

Under the frozen locked single-seed allocation recipe, connector-only worsens HR failure-inclusive MAE from 3.9914 to 10.1498 bpm on validation and 4.1932 to 14.9511 on sparse Supabase. Validation r falls from 0.8689 to 0.1122 despite 100% numeric parsing; donor direction is 0.375 versus the preregistered 0.65 bar. Nonzero gradients and correct save/load/annotation parity do not establish semantic compatibility: do not substitute this frozen-receiver recipe for LoRA or generalize the negative to all interfaces/budgets.

## Key numbers
- `control_supa_fimae`: 4.19321
- `control_val_fimae`: 3.99139
- `deployment_harm_ci_upper`: 12.8021
- `donor_direction_accuracy`: 0.375
- `metadata_only_minus_native`: 1.73924
- `paired_delta`: -6.15837
- `treatment_supa_fimae`: 14.9511
- `treatment_val_fimae`: 10.1498

## Artifacts
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/L42`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/bundles/P42`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/protocol.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/L42-val.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/P42-val.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/L42-supa.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/captures/P42-supa.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/scientific_summary.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/closeout_stage_hashes.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/annotation_parity_verified.json`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/closeout_tests.log`
- `/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/common_parsed_descriptive.json`

## Do not retry
- Do not repeat the same locked seed42 recipe expecting a quality win: connector-only failed accuracy, deployment and donor tracking.
- Do not generalize this allocation/depth contrast to other source encoders, modalities, budgets or target laws.
- Do not interpret HR-mean readout or whole-latent interventions as clinical stress inference or channel-specific physiology.
- Do not choose a best seed or infer a stable seed variance from two C draws.

## Notes
One-seed discovery L42/P42; all numbers recomputed from complete locked captures. Gate outcomes: {"worthwhile": false, "coverage": true, "content": false, "deployment": false, "content_status": "measured", "deployment_status": "measured", "pass": false}. This is not recipe promotion; confirmation, if reached, is separate. Zero/random also perturb positional bias; donor exchanges full sensor memory. Metadata-only is the separate fixed clock/mask/codebook guard. No clinical, momentary-state, information-theoretic-loss or global-generalization claim. Conditional seed43 and locked-test branches NOT REACHED because discovery failed, not crashes/null measurements. No new training budget was opened. wall_hours records only this comparison's fit+evaluation subprocess time; common A/B source is shared, not double charged. Training curves remain offline and unsynced. Actual offline W&B IDs: {"L42": "eo0nnz4z", "P42": "zzdfqic1"}. Verified aggregate-only remote analysis: https://wandb.ai/antonix-itmo/optihealth-eiv/runs/zh2hn6mz . This static mcp-analysis run is not a synchronized training history.

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
