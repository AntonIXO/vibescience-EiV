---
artifacts: []
closed: true
config_note: 'Preregistered before full run: fixed rows 1023,10dcc,1181f,11b01,1672d,210e3
  train and 21c3a,3b4ab validation; seed0; 30Hz; 384-frame windows; 400 updates; same
  width/optimizer for full, MuQ-only, Base92-only, no-clock controls; zeroed groups
  are interventions. Raw semantic controls audited before any render. Artifact root
  will be /mnt/lightsem-data/experiments/lightsem/audio_light_feature_ablation_20261015_v1.'
cost: null
crash_reason: ''
created_at: '2026-10-15T21:21:16+00:00'
diagnostics_measured:
- after: 0.3940184414386749
  before: 0.394342839717865
  diagnostic_id: light_audio_rgb_mae
- after: 0.42126837372779846
  before: 0.3946359157562256
  diagnostic_id: light_audio_brightness_mae
- after: 0.21266679465770721
  before: 0.21822045743465424
  diagnostic_id: light_audio_event_bce
- after: 0.3417057991027832
  before: 0.3405691683292389
  diagnostic_id: light_audio_aim_mae
- after: 0.9970410340680511
  before: 0.9961542007707805
  diagnostic_id: light_audio_cross_song_aim_corr
- after: 2591.9521484375
  before: 2552.463623046875
  diagnostic_id: light_audio_raw_aim_jerk_p99
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: All four feature arms completed with seed 0, fixed six/two song split,
    30-Hz 384-frame windows, width 96, AdamW and 400 updates. All saved outputs finite.
    Target-specific metrics and median baselines are in SUMMARY.json; full vs Base92-only
    differences are tiny for RGB/event, worse for brightness/aim/jerk.
  gate_id: g1
  passed: true
- evidence: No render was run. No candidate was promoted. Raw semantic aim velocity/acceleration/jerk
    were measured before any renderer.
  gate_id: g2
  passed: true
git_ref: codex/fixed-c-baseline@b798669+dirty
hypothesis_id: on-the-fixed-eight-song-split-and-one-seed-edec41
id: light-feature-utility-context-20261015-v1
interpretation:
  at: '2026-10-15T21:27:28+00:00'
  failure_mode: recipe_conditional
  lesson: 'On the fixed eight-song, one-seed diagnostic split, full MuQ+Base92 was
    not a clear movement input winner: versus Base92-only it improved RGB MAE by only
    0.00032 and event BCE by 0.00555, while worsening brightness MAE by 0.02663, aim-proxy
    MAE by 0.00114, cross-song aim correlation from 0.99615 to 0.99704, and raw aim
    jerk p99 from 2552 to 2592. MuQ-only changed movement much more but produced 5.9x
    higher event BCE and 5.9x higher jerk than full, so MuQ carries useful non-event
    variation but is not a safe movement signal under this recipe. The no-clock arm
    also retained near-identical cross-song correlation (0.99718), so clock removal
    alone did not repair the shortcut. This supports target-specific redundancy, not
    a product-ready feature selection.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: -0.000324398279
  diagnostic_id: light_audio_rgb_mae
  direction: down
- delta: 0.026632457972
  diagnostic_id: light_audio_brightness_mae
  direction: up
- delta: -0.005553662777
  diagnostic_id: light_audio_event_bce
  direction: down
- delta: 0.001136630774
  diagnostic_id: light_audio_aim_mae
  direction: up
- delta: 0.000886833297
  diagnostic_id: light_audio_cross_song_aim_corr
  direction: up
- delta: 39.488525390625
  diagnostic_id: light_audio_raw_aim_jerk_p99
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    light_audio_aim_mae: false
    light_audio_brightness_mae: false
    light_audio_cross_song_aim_corr: false
    light_audio_event_bce: true
    light_audio_raw_aim_jerk_p99: false
    light_audio_rgb_mae: true
provenance: {}
recipe_fingerprint:
  git_ref: codex/fixed-c-baseline@b798669+dirty
verdict: supports
---

## Summary
Tests hypothesis [[on-the-fixed-eight-song-split-and-one-seed-edec41]].
At commit `codex/fixed-c-baseline@b798669+dirty`.

## Measurements
- [[light_audio_rgb_mae]]: before 0.394342839717865 → after 0.3940184414386749  (Δ -0.000324398, down ✓ predicted down)
- [[light_audio_brightness_mae]]: before 0.3946359157562256 → after 0.42126837372779846  (Δ +0.0266325, up ✗ predicted down)
- [[light_audio_event_bce]]: before 0.21822045743465424 → after 0.21266679465770721  (Δ -0.00555366, down ✓ predicted down)
- [[light_audio_aim_mae]]: before 0.3405691683292389 → after 0.3417057991027832  (Δ +0.00113663, up ✗ predicted down)
- [[light_audio_cross_song_aim_corr]]: before 0.9961542007707805 → after 0.9970410340680511  (Δ +0.000886833, up ✗ predicted down)
- [[light_audio_raw_aim_jerk_p99]]: before 2552.463623046875 → after 2591.9521484375  (Δ +39.4885, up ✗ predicted down)

## Gates
- `g1`: PASS — All four feature arms completed with seed 0, fixed six/two song split, 30-Hz 384-frame windows, width 96, AdamW and 400 updates. All saved outputs finite. Target-specific metrics and median baselines are in SUMMARY.json; full vs Base92-only differences are tiny for RGB/event, worse for brightness/aim/jerk.
- `g2`: PASS — No render was run. No candidate was promoted. Raw semantic aim velocity/acceleration/jerk were measured before any renderer.

**Verdict:** supports

## Interpretation
**Failure mode:** `recipe_conditional`

On the fixed eight-song, one-seed diagnostic split, full MuQ+Base92 was not a clear movement input winner: versus Base92-only it improved RGB MAE by only 0.00032 and event BCE by 0.00555, while worsening brightness MAE by 0.02663, aim-proxy MAE by 0.00114, cross-song aim correlation from 0.99615 to 0.99704, and raw aim jerk p99 from 2552 to 2592. MuQ-only changed movement much more but produced 5.9x higher event BCE and 5.9x higher jerk than full, so MuQ carries useful non-event variation but is not a safe movement signal under this recipe. The no-clock arm also retained near-identical cross-song correlation (0.99718), so clock removal alone did not repair the shortcut. This supports target-specific redundancy, not a product-ready feature selection.

## Notes
The preregistered feature utility comparison completed. Full MuQ+Base92 did not beat Base92-only on the primary movement or brightness metrics and only trivially improved RGB/event; no-clock did not reduce cross-song aim correlation. The bounded bidirectional context result is archived separately and is not silently merged into this four-arm verdict. This is diagnostic evidence on eight songs, one seed, and body-derived motion proxies only.

#cross-modal #control #baseline #conditioning-collapse #matched-baseline
