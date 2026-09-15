---
created_at: '2026-09-01T07:48:34+00:00'
former_ids: []
id: seed-family-arm-to-log-mapping-is-mismatched
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:48:34+00:00'
topic_tags: []
---

## Claim
The seed-family run is mis-attributed: the live process names profile `lora_proxy_12ch_wl_band04_s43` while the log it writes is `v36labels_phase_c_s43`, so the arm-to-log mapping may be wrong and the results would be recorded against the wrong arm.

## Method
Read the full argv of the live compute process via nvidia-smi --query-compute-apps + ps, and compared --profile against --encoder-checkpoint, --adapter-output-dir and --report-path.

## Evidence
NOT MIS-ATTRIBUTED. The two names describe different axes of the same cell, which is the design of a Phase-C-only seed family.

The full command line of the live process:
  --profile                lora_proxy_12ch_wl_band04_s43
  --encoder-checkpoint     outputs/checkpoints/v36labels/phase_b
  --adapter-output-dir     outputs/adapters/v36labels_c_s43
  --report-path            logs/v36labels_phase_c_s43.json

The PROFILE carries the seed (band04 + seed 43, a one-key sibling verified by dict-diff before launch). The ARM is carried by the encoder checkpoint and the output paths -- this cell is v36labels's own Phase B, re-run through Phase C at a different seed. Naming the profile after the arm would be wrong: the same seed profile is deliberately used for BOTH arms in the family (v36labels and v28affect01), which is what keeps the comparison paired.

So the log name and the profile name disagreeing is the CORRECT state here, and a run where they agreed would mean the seed sibling had been forked per arm -- introducing a second variable into a family whose whole purpose is to isolate the seed.

RAISED BY: the heartbeat flagged 'process name band04_s43 and fresh log v36labels_phase_c_s43 do not match; confirm the arm-to-log mapping'. Correct instinct -- an arm reporting under another arm's name is exactly the defect that cost six hours earlier today, when _v35_rescore.py had its arm hardcoded and printed v35's numbers under a v36 heading. The difference is that THAT was a script naming its subject wrongly, while this is two orthogonal identifiers doing their jobs.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
