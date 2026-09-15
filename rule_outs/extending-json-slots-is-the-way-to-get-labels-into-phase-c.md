---
created_at: '2026-09-01T09:04:03+00:00'
former_ids: []
id: extending-json-slots-is-the-way-to-get-labels-into-phase-c
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:04:03+00:00'
topic_tags: []
---

## Claim
Extending the json_slots schema is the way to get labels into Phase C, since Phase C is currently blind to 34 of 35 declared label channels.

## Method
Rendered label_summary() for every window on both rosters, counted per-channel window reach separately for Phase A and Phase C, and broke the Phase C reach down by source dataset. No GPU.

## Evidence
THE CEILING IS REAL BUT NARROW, AND IT IS SET BY THE DATA, NOT THE SCHEMA. Measured on the Phase C roster (scratch/_phase_c_label_ceiling.py, 7,592 windows):

  channel            PhaseA win   PhaseC win    C %   sources on the C roster
  stress_level            1,678        1,665   21.9%  pmdata only
  fatigue                 1,665        1,665   21.9%  pmdata only
  sleep_quality           1,665        1,665   21.9%  pmdata only
  mood_ordinal            1,665        1,665   21.9%  pmdata only
  sleep_duration_h        1,665        1,665   21.9%  pmdata only
  readiness               1,665        1,665   21.9%  pmdata only
  stai_state                257          242    3.2%  lifesnaps only
  food/nutrition group      145          145    1.9%  big_ideas_glycemic only

  channels with ANY Phase C window : 26
  channels on Phase A but ABSENT from Phase C : 21

SO THE CLAIM IS HALF RIGHT. A schema change IS the only route -- but it can reach at most 21.9% of Phase C windows, and every one of those six channels comes from a SINGLE dataset, pmdata. The entire affect corpus this campaign has been mapping (valence 1,298 windows, arousal 609, valence_recalled/arousal_recalled 486 each) sits at EXACTLY ZERO on the Phase C roster, because dapper, case, k_emocon and popane_* are not datasets Phase C draws from.

WHAT THAT MEANS FOR A SCHEMA ARM. A new slot fed by a single dataset is the provenance shortcut in its purest form: the slot's PRESENCE alone identifies the corpus, before any value is read. That is worse than the native-units defect measured this morning, where at least the presence was shared and only the scale leaked. And the six pmdata channels would all fire on the same 1,665 windows, so they are one indicator repeated six times, not six independent labels.

THE HONEST STATEMENT OF THE GOAL'S LIMIT: 'utilise all the labels in all three phases' cannot be achieved on the current Phase C roster. Phase A and B see the labels (they draw from 19 datasets); Phase C draws from 6 and the affect labels live in the other 13. The lever that would change this is the ROSTER, not the schema -- and the roster is what the deployment population dictates, since Phase C must train on data resembling optihealth_supabase.

NOT A DEAD END, A DIFFERENT ARM: if a Phase C label slot is wanted, the only defensible candidate is a pmdata-fed slot scored ONLY against a within-dataset null, exactly as CLAUDE.md prescribes for stai_state ('scoring ONLY with a within-dataset null'). Anything pooled across the roster would be measuring presence, not physiology.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
