---
created_at: '2026-08-13T21:44:15+00:00'
former_ids:
- the-emitted-state-slot-carries-affect-label-information-on-dense-rows-i-e-state-
id: the-emitted-state-slot-carries-affect-7805e5
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-13T21:44:15+00:00'
topic_tags: []
---

## Claim
The emitted `state` slot carries affect-label information on dense rows (i.e. state could be scored for accuracy, or contributes signal beyond the model's own emitted hr_raw)

## Method
Permutation test (20k shuffles), CPU-only, on existing v9band04 dense-128 captures x 3 seeds (scratch/state_label_probe.py). Between-group sum-of-squares of label across emitted-state groups; test A raw, test B on residuals after least-squares removal of emit_raw (thermometer control). Labels: pmdata stress (ordinal 0-5, list->mean), lifesnaps TENSE/ANXIOUS (binary).

## Evidence
logs/state_label_probe_v9band04.json. Per seed, n=33 pmdata / n=34 lifesnaps labeled rows in dense-128: ALL association p_perm >= 0.29 (raw) and >= 0.29 (residual beyond emit hr_raw); most >= 0.42. s42/s44 emit a single class on all pmdata rows (100% stress) making accuracy undefined. Polarity is a seed lottery replicating the historical v5c/v4w/v5d observation: s42 lifesnaps 31 neutral/3 stress, s43 26 stress/8 neutral, s44 30 neutral/4 stress on the SAME 34 rows. Bounding fact: corr(label, emit_hr_raw) = 0.015..0.086 -- even a perfect hr thermometer would carry ~zero affect signal on these labels, so no state law operating through hr level can fix this. Any state improvement requires new conditioning information, not target-law reshaping.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
