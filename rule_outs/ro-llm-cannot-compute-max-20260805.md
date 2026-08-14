---
created_at: '2026-08-05T15:47:14+00:00'
former_ids: []
id: ro-llm-cannot-compute-max-20260805
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T15:47:14+00:00'
topic_tags: []
---

## Claim
The hr_peak slot fails because a 4B LLM cannot compute max/min - the same class of limitation as miscounting the r's in 'strawberry'.

## Method
Inspection of the data path plus the existing within-model contrast.

## Evidence
hr_peak is computed UPSTREAM in the target builder and handed to the LM as a soft token; the LM performs no max operation and no character-level counting, so the tokenization analogy does not transfer. Decisive contrast: the SAME LM reads hr_raw from the SAME prefix at generation corr +0.740 full-coverage while hr_peak sits at +0.276. A general inability to read the prefix would degrade both. The bottleneck is upstream encoding, not downstream token reading.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].
