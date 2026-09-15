---
created_at: '2026-09-01T07:42:31+00:00'
former_ids: []
id: the-brace-fix-changes-emitted-values-so-old-captures-are-incomparable
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:42:31+00:00'
topic_tags:
- optihealth
- deployment-distribution
---

## Claim
The JSON termination fix might change the emitted values, so re-captured numbers are not comparable with the originals and the caption arms would need re-scoring from scratch.

## Method
Re-captured the dense draw for all three caption arms with --stop-on-brace against the ORIGINAL captures on the same adapters and the same 128 rows, then compared parse health, mean text length, aggregate MAE, and per-row recovered hr_raw/hr_peak on shared ids.

## Evidence
REFUTED ON ALL THREE ARMS, FULL 128-ROW DENSE DRAWS (scratch/_brace_fix_report.py):
  v33dispersion  81/128 -> 128/128 closed, text 240 -> 182 chars, hr_raw MAE 3.076 -> 3.076, hr_peak 15.880 -> 15.880
  v34nopeak      88/128 -> 128/128 closed, text 226 -> 189 chars, hr_raw MAE 3.919 -> 3.919, hr_peak 15.762 -> 15.762
  v36labels     128/128 -> 128/128 closed, text 182 -> 182 chars, hr_raw MAE 4.017 -> 4.017, hr_peak 15.751 -> 15.751
Per-row check on shared ids: hr_raw AND hr_peak identical on 128/128 rows in EVERY arm.

WHY IT CANNOT MOVE THEM, and this is the part that makes the result predictable rather than lucky: the criterion stops at the first REPEATED key, and by then every one of the 13 schema slots has already been emitted exactly once. The audit parser was already first-wins, so it was always reading those same first occurrences; the loop it now truncates contributed nothing but tokens. v36labels is the control -- it never looped, so the flag is a no-op there, and indeed its text is byte-identical at 182 chars.

CONSEQUENCE: every previously recorded emission number for the caption branch stands as measured, and the promotion blocker is removed without any re-scoring debt. What changed is parse health (and a 20-24% shorter generation on the arms that looped), not the measurement.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].

#optihealth #deployment-distribution
