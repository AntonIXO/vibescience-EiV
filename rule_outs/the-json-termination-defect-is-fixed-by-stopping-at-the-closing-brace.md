---
created_at: '2026-09-01T07:27:46+00:00'
former_ids: []
id: the-json-termination-defect-is-fixed-by-stopping-at-the-closing-brace
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:27:46+00:00'
topic_tags:
- deployment-distribution
- optihealth
---

## Claim
The JSON termination defect blocking promotion of the caption branch (v33 47/128, v34 40/128 dense emissions unparseable) can be fixed by stopping generation at the closing brace.

## Method
Iterative decode-time fix with verification at each step, scratch/emission_diversity_audit.py --stop-on-brace: (1) brace-depth criterion, re-captured v33 dense, compared byte-for-byte against the original; (2) inspected the failing rows for the presence of '}' anywhere in the output; (3) replaced with a repeated-key criterion plus _repair_json; (4) 16-row pilot; (5) full 128-row re-capture. Value stability checked per row by scratch/_brace_fix_report.py, which compares recovered hr_raw/hr_peak between the two captures on shared ids.

## Evidence
REFUTED BY DIRECT TEST. The first --stop-on-brace re-capture of v33dispersion came back BYTE-IDENTICAL to the original: 81/128 closed, same text, same MAE 3.076. Inspection of the failing rows shows why: '}' is ABSENT from the entire 128-token output of every looped row (checked on the full text, not a tail sample). The model walks the 13-slot schema, reaches the last pair, and instead of closing re-emits '"temp":"normal","state":"flow"' until the budget ends. There is no brace to stop at, so a brace-only criterion can never fire and is a silent no-op.

WHAT WORKS INSTEAD, MEASURED: stop at the first REPEATED KEY. By that point every schema slot has been emitted exactly once, so nothing is lost; _repair_json then truncates at the last complete "key":"value" pair and appends '}', adding no value the model did not emit.
  16-row pilot on v33dispersion: brace-only 12/16 closed (211 chars) vs key-repeat+repair 16/16 (179 chars), values identical 16/16.
  FULL 128-row dense re-capture of v33dispersion: 81/128 -> 128/128 closed natively, mean text 240 -> 182 chars, and hr_raw AND hr_peak identical on 128/128 shared rows.

So the defect is fixed and the fix provably does not move a single number -- the parser was already first-wins, so the recovered values were always the emitted ones. The promotion blocker on the caption branch is removed; what remains is the seed question, not the emission format.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].

#deployment-distribution #optihealth
