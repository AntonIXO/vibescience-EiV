---
created_at: '2026-09-18T11:14:25+00:00'
former_ids: []
id: v41-yaml-split-does-not-filter-phase-a-b
problem_id: is-the-frozen-llm-load-bearing-or-a-formatter
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-18T11:14:25+00:00'
topic_tags:
- cross-modal
- sensor-text
- control
---

## Claim
The presence of split=train in the documented v41 A/B/C YAML proves that all three training stages excluded held-out subjects.

## Method
Read-only inspection of the launch script, saved Phase-A manifest and source at the adapter-recorded commit ad78bc2082d0; compare profile keys against actual CLI and dataset forwarding. No model-result recomputation.

## Evidence
PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh writes split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0 forwards split only for C; Phase A allowed_subject_keys was not forwarded (saved allowed_subject_count=null) and B had no allowlist. This static evidence blocks the WHOLE-PIPELINE subject-held-out claim for the documented path. Stored captures/numerical comparisons are preserved; no historical rerun or unknown launch path is asserted. A new campaign will rebuild a verified train-only common upstream tower. Numerical readout-error differences between fitted heads are not an information-theoretic decomposition.

## Links
Rules out for problem [[is-the-frozen-llm-load-bearing-or-a-formatter]].

#cross-modal #sensor-text #control
