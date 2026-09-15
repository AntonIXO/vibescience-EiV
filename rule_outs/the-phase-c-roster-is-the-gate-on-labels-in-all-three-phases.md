---
created_at: '2026-09-01T10:01:51+00:00'
former_ids: []
id: the-phase-c-roster-is-the-gate-on-labels-in-all-three-phases
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:01:51+00:00'
topic_tags: []
---

## Claim
Phase C can carry labels once the roster admits them -- v40roster's wide roster brings valence to 1,298 windows and arousal to 609, so settling the roster settles 'labels in all three phases'.

## Method
Read _build_json_slots_target end to end and enumerated every slots[...] assignment with its data source, rather than trusting the reach scanner's summary.

## Evidence
THE ROSTER IS NECESSARY AND NOWHERE NEAR SUFFICIENT. Read _build_json_slots_target (src/training/datasets.py:2525-2645), which builds every Phase C target, and it emits THIRTEEN slots:

  heart_rate_bucket, hr_pct, hr_raw, hr_peak, hrv_bucket, hrv_pct, hrv_raw,
  deep_pct, rem_pct, awake_pct, rr, temp, state

Twelve of the thirteen are computed FROM THE SENSOR TENSOR -- raw(0), peak(0), raw(1), nanmean of channels 4/5/2, raw(10), raw(11) -- denormalised through population_stats. They are not labels in any sense; they are the tensor restated as text.

The ONLY label-derived slot is `state`, via infer_state_label(metadata). One channel out of the 35 declared in labels.yaml.

SO THE ARITHMETIC OF THE STANDING GOAL IS: Phase A and Phase B carry labels through _label_clauses on the caption path, drawing from 19-20 datasets. Phase C carries exactly one label channel, and it does so through a DIFFERENT mechanism (the state reducer, not the clause renderer). Widening the roster to 20 datasets changes WHICH windows are drawn; it does not add a single slot to the target. valence reaching 1,298 windows means those windows are now sampled -- their valence value still has nowhere to go in the target string.

WHAT THIS COSTS THE PLAN AS WRITTEN: v40roster tests whether the wide roster is admissible on deployment, which is a real and separate question and remains worth its GPU. But it CANNOT deliver 'labels in all three phases', and I had it recorded as the gate on exactly that. The gate is a TARGET change: _build_json_slots_target must render label slots the way _label_clauses renders caption clauses.

METHOD NOTE, because the scanner nearly hid this: scratch/_label_reach_by_phase.py reported 'keys emitted: []' and 'label-derived keys: (none beyond state)'. The first is a scanner failure -- its regex found no slot assignments in a function that makes thirteen. The second was correct. Had I trusted the empty list I would have concluded Phase C emits nothing at all; had I trusted the summary line I would have been right by accident. Read the function.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
