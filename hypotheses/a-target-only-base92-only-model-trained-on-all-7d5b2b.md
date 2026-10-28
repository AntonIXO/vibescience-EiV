---
ambition_class: null
created_at: '2026-10-15T23:05:58+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: All 300 rows use the same Base92 feature construction and authored-light
    target schema; train/validation split is song/audio-hash grouped and no body or
    MuQ is used.
  diagnostic_id: null
  id: g1
  mde: null
- blocking: true
  description: Validation RGB, brightness, and event metrics are reported against
    trivial held-out baselines; saved outputs are finite and no exact fixture duplicates
    or spatial-collapse claim is made beyond the measured canonical eight-slot diagnostic.
  diagnostic_id: null
  id: g2
  mde: null
id: a-target-only-base92-only-model-trained-on-all-7d5b2b
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
problem_id: lightsem-audio-to-light-feature-utility-and-context-contract
problem_tags:
- conditioning-collapse
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- control
- baseline
---

## Statement
A target-only Base92-only model trained on all 300 authenticated BeatSaver audio+visual+light rows with a song-level held-out split will beat the current eight-song Base92-only diagnostic on held-out authored RGB, brightness, and event targets without increasing output duplicate/collapse diagnostics; it makes no claim about MuQ, dance, physical aim, or DMX.

## Rationale


## Plan


## Links
Tests problem [[lightsem-audio-to-light-feature-utility-and-context-contract]].
Predicts [[light_audio_rgb_mae]] (down).
Predicts [[light_audio_brightness_mae]] (down).
Predicts [[light_audio_event_bce]] (down).
Gate `g1` (blocking): All 300 rows use the same Base92 feature construction and authored-light target schema; train/validation split is song/audio-hash grouped and no body or MuQ is used.
Gate `g2` (blocking): Validation RGB, brightness, and event metrics are reported against trivial held-out baselines; saved outputs are finite and no exact fixture duplicates or spatial-collapse claim is made beyond the measured canonical eight-slot diagnostic.

#cross-modal #control #baseline #conditioning-collapse #matched-baseline
