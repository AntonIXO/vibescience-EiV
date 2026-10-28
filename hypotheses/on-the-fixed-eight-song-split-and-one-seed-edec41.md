---
ambition_class: null
created_at: '2026-10-15T21:19:02+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: All arms finite and same split, seed, architecture width and optimizer
    budget; report trivial median/mean target baselines and group-only/full results
    separately by RGB, brightness, event and aim proxy.
  diagnostic_id: null
  id: g1
  mde: null
- blocking: true
  description: No promotion; raw semantic controls are audited before any render.
  diagnostic_id: null
  id: g2
  mde: null
id: on-the-fixed-eight-song-split-and-one-seed-edec41
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: light_audio_rgb_mae
  direction: down
  magnitude_note: ''
- diagnostic_id: light_audio_brightness_mae
  direction: down
  magnitude_note: ''
- diagnostic_id: light_audio_event_bce
  direction: down
  magnitude_note: ''
- diagnostic_id: light_audio_aim_mae
  direction: down
  magnitude_note: ''
- diagnostic_id: light_audio_cross_song_aim_corr
  direction: down
  magnitude_note: ''
- diagnostic_id: light_audio_raw_aim_jerk_p99
  direction: down
  magnitude_note: ''
problem_id: lightsem-audio-to-light-feature-utility-and-context-contract
problem_tags:
- conditioning-collapse
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- control
- baseline
---

## Statement
On the fixed eight-song split and one-seed equal-budget pilot, MuQ+Base92 will beat each group-only arm on RGB/brightness/event targets, while Base92 timing channels will explain most event/rhythm utility and MuQ will explain more color; removing timestamp/phase channels will reduce cross-song aim correlation without worsening held-out target losses, and a bounded bidirectional context arm will reduce fixture-centered aim MAE and raw aim jerk versus the reset-clock causal incumbent.

## Rationale


## Plan


## Links
Tests problem [[lightsem-audio-to-light-feature-utility-and-context-contract]].
Predicts [[light_audio_rgb_mae]] (down).
Predicts [[light_audio_brightness_mae]] (down).
Predicts [[light_audio_event_bce]] (down).
Predicts [[light_audio_aim_mae]] (down).
Predicts [[light_audio_cross_song_aim_corr]] (down).
Predicts [[light_audio_raw_aim_jerk_p99]] (down).
Gate `g1` (blocking): All arms finite and same split, seed, architecture width and optimizer budget; report trivial median/mean target baselines and group-only/full results separately by RGB, brightness, event and aim proxy.
Gate `g2` (blocking): No promotion; raw semantic controls are audited before any render.

#cross-modal #control #baseline #conditioning-collapse #matched-baseline
