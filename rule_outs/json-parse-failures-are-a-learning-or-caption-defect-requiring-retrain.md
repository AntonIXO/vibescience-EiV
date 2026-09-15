---
created_at: '2026-08-31T22:13:05+00:00'
former_ids: []
id: json-parse-failures-are-a-learning-or-caption-defect-requiring-retrain
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-31T22:13:05+00:00'
topic_tags: []
---

## Claim
The caption arms' JSON parse failures (v33 47/128, v34 40/128 on the dense draw) mean the model failed to LEARN the emission schema, so the fix is a retrain, a caption change, or a target-format change -- and the affected rows' metrics are untrustworthy because information is missing.

## Method
Static forensics on the emission capture JSONs already on disk (no GPU): first-wins prefix parsing of every '"key":"value"' pair, detection of the first duplicated key as the loop start, and a check for a closing brace anywhere in the generated text. Cross-checked across four arms and both draws (supabase 200 rows, dense 128 rows).

## Evidence
scratch/_json_loop_forensics.py over the recorded dense captures:
  v28affect01  127/128 closed natively,  1 looped
  v33dispersion 81/128 closed natively, 47 looped
  v34nopeak     88/128 closed natively, 40 looped
For EVERY looped row in both caption arms the loop begins at the same key -- `temp`, with repeating unit temp->state, which is the LAST pair of the 13-slot schema (v33 47/47, v34 34/40 at temp). Not one looped row contains a '}' anywhere in its 128 generated tokens. So the model walks the schema to its end and then cannot emit the closing brace; it re-emits the final pair until max_new_tokens. Median looped tail is 173 chars (v33) / 158 (v34), max 307.

DECISIVE FOR THE FIX CHOICE (scratch/_json_loop_missing.py): 47/47 of v33's and 40/40 of v34's looped rows already carry BOTH scored slots, hr_raw and hr_peak, on a first-wins prefix parse. The only missing keys are the hrv group (hrv_bucket, hrv_pct, hrv_raw) plus rr, on 11 rows of v33 and 4 of v34 -- windows with no HRV/RR coverage, where the absence is a data fact rather than a generation failure. Nothing that is scored is ever missing.

So the values are complete BEFORE the loop starts, the defect is confined to termination, and the metrics computed on prefix-recovered rows are sound.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
