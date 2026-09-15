---
created_at: '2026-09-01T10:05:41+00:00'
former_ids: []
id: the-label-renderer-is-the-constraint-on-labels-in-all-three-phases
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:05:41+00:00'
topic_tags: []
---

## Claim
The standing goal 'utilise all the labels in all three phases' is reachable by extending the label renderer -- more channels rendered means more label supervision.

## Method
Enumerated the 13 channels _label_clauses renders, then measured per-window reach and clauses-per-window on the exact rosters phase_b_12ch_v25bigbatch and lora_proxy_12ch_wl_band04 use, via the repo's own load_sample_metadata and label_summary.

## Evidence
THE RENDERER IS NOT THE BINDING CONSTRAINT, AND THIRTEEN CHANNELS ARE ALREADY WIRED (scratch/_caption_clause_reach.py). _label_clauses renders stress_level, stress_raw, valence, arousal, mood_ordinal, fatigue, readiness, sleep_quality, sleep_duration_h, stai_state, panas_positive, panas_negative, workload_diff. Measured on the rosters the arms actually run:

PHASE B (phase_b_12ch_v25bigbatch, 19 datasets, 9,428 windows)
  stress_level 17.8% | mood_ordinal/fatigue/readiness/sleep_quality/sleep_duration_h 17.7% each (all pmdata, SAME windows)
  valence 13.8% | arousal 6.5% | stai_state 2.7% | panas_pos/neg 0.4% | workload_diff 0.4%
  NEVER RENDERED: stress_raw (declared in the renderer, present on zero windows)
  WINDOWS WITH NO CLAUSE AT ALL: 6,118 of 9,428 = 64.9%
  clauses per window: mean 1.30, max 6, p90 6

PHASE C (band04, 6 datasets, 7,592 windows)
  the six pmdata channels at 21.9%, stai_state 3.2%, panas 0.6%
  NEVER RENDERED: stress_raw, valence, arousal, workload_diff
  WINDOWS WITH NO CLAUSE: 5,643 of 7,592 = 74.3%
  clauses per window: mean 1.36, max 6, p90 6

THE DISTRIBUTION IS BIMODAL, WHICH IS THE WHOLE POINT. Mean 1.30 clauses per window with p90 = 6 and max = 6 means a window has either ZERO clauses or SIX -- the six pmdata channels are one EMA questionnaire answered together, so they arrive as a block. There is no window carrying two or three. Adding a 14th channel to the renderer would extend that block for the datasets that already have it and change nothing for the 64.9% carrying none.

SO THE CEILING ON ALL CAPTION-SIDE LABEL WORK IS 35.1% OF PHASE B ROWS AND 25.7% OF PHASE C ROWS. The other two thirds see a caption that is identical no matter what the renderer does. That is a property of the CORPUS -- which datasets carry annotations at all -- and no amount of renderer work moves it.

WHAT THIS DOES TO THE GOAL AS STATED: 'all labels in all three phases' cannot be delivered by rendering more channels, because the channels are already rendered and the rows are not there. stress_raw is the proof in miniature: it is declared in the renderer, has a format string, and reaches ZERO windows.

WHAT IS STILL WORTH DOING, narrowly: valence and arousal reach 13.8%/6.5% of Phase B and 0% of Phase C, and their axis problem is real (source medians 3.00-6.44) -- that is v39scaled, in flight. Beyond it, the lever is the CORPUS (which annotated datasets are admitted), not the renderer.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
