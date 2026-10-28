---
artifacts:
- /mnt/lightsem-data/experiments/lightsem/audio_light_all300_base92_targetonly_20261016_v1/RESULTS.json
- /mnt/lightsem-data/experiments/lightsem/audio_light_all300_base92_targetonly_20261016_v1/model.pt
closed: true
config_note: 'Preregistered all-300 target-only Base92 campaign. Inputs are BeatSaver
  audio NPZ Base92-equivalent 92-D construction; targets are clipped authored RGB/intensity/event
  caches; no MuQ, body, dance, aim, speed or acceleration. Song/audio-hash grouped
  split and indexed mini-batches. Artifact root: /mnt/lightsem-data/experiments/lightsem/audio_light_all300_base92_targetonly_20261016_v1.'
cost: null
crash_reason: ''
created_at: '2026-10-15T23:06:05+00:00'
diagnostics_measured:
- after: 0.3001597821712494
  before: 0.394342839717865
  diagnostic_id: light_audio_rgb_mae
- after: 0.41221481561660767
  before: 0.3946359157562256
  diagnostic_id: light_audio_brightness_mae
- after: 0.690925121307373
  before: 0.21822045743465424
  diagnostic_id: light_audio_event_bce
do_not_retry:
- Do not rerun this exact Base92 padded-to-1116 recipe as a presumed improvement;
  it collapses RGB and aim while event remains at prior.
- Do not interpret RGB MAE improvement without output-range/collapse diagnostics.
external_run: ''
former_ids: []
gate_results:
- evidence: 300 paired authored-light rows admitted; 240/60 song-level split; indexed
    mini-batches; Base92-only input padded to the existing 1116-D interface; no MuQ/body/dance
    used.
  gate_id: g1
  passed: true
- evidence: 'Final held-out RGB MAE 0.30016 is essentially the held-out RGB median
    baseline 0.30015 and comes from RGB output collapse: 100% of predicted RGB values
    are below .01. Brightness MAE 0.41221 is worse than median baseline 0.41184; event
    BCE 0.69093 is the entropy/prior baseline 0.69235. Aim spread is collapsed on
    93.9% of validation windows below .2, so no output promotion.'
  gate_id: g2
  passed: false
git_ref: codex/fixed-c-baseline@b798669+dirty
hypothesis_id: a-target-only-base92-only-model-trained-on-all-7d5b2b
id: light-all300-base92-targetonly-20261016-v1
interpretation:
  at: '2026-10-15T23:10:03+00:00'
  failure_mode: execution_bug
  lesson: The 300-row Base92-only expansion did not improve audio-to-light controls.
    Its RGB MAE 0.30016 only matches the held-out median baseline 0.30015 because
    every predicted RGB value is below 0.01; brightness MAE 0.41221 is worse than
    its median baseline 0.41184, and event BCE 0.69093 is the constant-prior entropy.
    The padded-MuQ interface plus this target weighting therefore permits output collapse,
    and more authored rows alone did not supply a valid improvement.
  transfers_to: []
key_numbers:
  aim_spread_below_0.2_fraction: 0.9388954171562868
  brightness_mae: 0.41221481561660767
  brightness_median_baseline: 0.41184359788894653
  event_bce: 0.690925121307373
  event_prior_bce: 0.6923474073410034
  rgb_mae: 0.3001597821712494
  rgb_median_baseline: 0.300153911113739
  rgb_zero_fraction: 1.0
observed_effects:
- delta: -0.094183057547
  diagnostic_id: light_audio_rgb_mae
  direction: down
- delta: 0.01757889986
  diagnostic_id: light_audio_brightness_mae
  direction: up
- delta: 0.472704663873
  diagnostic_id: light_audio_event_bce
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    light_audio_brightness_mae: false
    light_audio_event_bce: false
    light_audio_rgb_mae: true
provenance: {}
recipe_fingerprint:
  git_ref: codex/fixed-c-baseline@b798669+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[a-target-only-base92-only-model-trained-on-all-7d5b2b]].
At commit `codex/fixed-c-baseline@b798669+dirty`.

## Measurements
- [[light_audio_rgb_mae]]: before 0.394342839717865 → after 0.3001597821712494  (Δ -0.0941831, down ✓ predicted down)
- [[light_audio_brightness_mae]]: before 0.3946359157562256 → after 0.41221481561660767  (Δ +0.0175789, up ✗ predicted down)
- [[light_audio_event_bce]]: before 0.21822045743465424 → after 0.690925121307373  (Δ +0.472705, up ✗ predicted down)

## Gates
- `g1`: PASS — 300 paired authored-light rows admitted; 240/60 song-level split; indexed mini-batches; Base92-only input padded to the existing 1116-D interface; no MuQ/body/dance used.
- `g2`: **FAIL** — Final held-out RGB MAE 0.30016 is essentially the held-out RGB median baseline 0.30015 and comes from RGB output collapse: 100% of predicted RGB values are below .01. Brightness MAE 0.41221 is worse than median baseline 0.41184; event BCE 0.69093 is the entropy/prior baseline 0.69235. Aim spread is collapsed on 93.9% of validation windows below .2, so no output promotion.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `execution_bug`

The 300-row Base92-only expansion did not improve audio-to-light controls. Its RGB MAE 0.30016 only matches the held-out median baseline 0.30015 because every predicted RGB value is below 0.01; brightness MAE 0.41221 is worse than its median baseline 0.41184, and event BCE 0.69093 is the constant-prior entropy. The padded-MuQ interface plus this target weighting therefore permits output collapse, and more authored rows alone did not supply a valid improvement.

## Key numbers
- `aim_spread_below_0.2_fraction`: 0.938895
- `brightness_mae`: 0.412215
- `brightness_median_baseline`: 0.411844
- `event_bce`: 0.690925
- `event_prior_bce`: 0.692347
- `rgb_mae`: 0.30016
- `rgb_median_baseline`: 0.300154
- `rgb_zero_fraction`: 1

## Artifacts
- `/mnt/lightsem-data/experiments/lightsem/audio_light_all300_base92_targetonly_20261016_v1/RESULTS.json`
- `/mnt/lightsem-data/experiments/lightsem/audio_light_all300_base92_targetonly_20261016_v1/model.pt`

## Do not retry
- Do not rerun this exact Base92 padded-to-1116 recipe as a presumed improvement; it collapses RGB and aim while event remains at prior.
- Do not interpret RGB MAE improvement without output-range/collapse diagnostics.

## Notes
The expanded 300-row Base92-only target-only run is rejected as a model improvement. RGB MAE appears lower only because the model collapsed RGB to near-zero and matched the held-out RGB median baseline. Brightness worsened and event prediction fell to the constant-prior entropy. Aim diagnostics are not product targets here but also collapse spatially on 93.9% of validation windows. The run is useful as a negative result: more authored rows without a correctly trained consistent representation/loss did not improve the current audio-to-light model.

#cross-modal #control #baseline #conditioning-collapse #matched-baseline
