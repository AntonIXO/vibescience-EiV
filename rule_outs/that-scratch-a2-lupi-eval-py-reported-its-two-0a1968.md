---
created_at: '2026-08-24T09:50:51+00:00'
former_ids: []
id: that-scratch-a2-lupi-eval-py-reported-its-two-0a1968
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-24T09:50:51+00:00'
topic_tags:
- lupi
- wearables
---

## Claim
That `scratch/a2_lupi_eval.py` reported its two privileged channels under the correct names. It did not: its per-channel labels are TRANSPOSED, so every per-channel number in logs/a2_lupi_eval.json and in rule_out `that-per-patch-eda-tonic-level-scr-rate-is-1dadc0` is attributed to the wrong channel.

## Method
Direct comparison of two independent orderings on the 2026-08-24 bundle. `privileged_patch_targets` (src/training/datasets.py:301-357) indexes its output columns by `metadata.privileged_schema`, i.e. the POSITIONAL order of configs/canonical/privileged.yaml, which is [eda_tonic, eda_scr_rate] (the file's own comment states the index is positional and append-only). `a2_lupi_eval.py:78` instead took `ch_names = list(stats.keys())` from privileged_stats.json, a plain JSON dict whose key order is the REVERSE. Confirmed by an assertion independent of both namings: per-channel window counts read straight from privileged_events.parquet by registry index, compared against the realised target-column counts. scratch/a3_lupi_supply_eval.py now derives names from privileged_schema.channel_names and ASSERTS the binding against the parquet before scoring.

## Evidence
Registry order (configs/canonical/privileged.yaml, positional): idx 0 = eda_tonic, idx 1 = eda_scr_rate. privileged_stats.json key order: ['eda_scr_rate', 'eda_tonic'] -- reversed. Independent per-channel window counts from privileged_events.parquet BY REGISTRY INDEX: eda_tonic 1387 windows / 743 subjects, eda_scr_rate 1616 / 911. A2 printed its column-0 cell as 'eda_scr_rate' with n=9555 cells / 743 subjects; 743 is TONIC's subject count, so column 0 is tonic wearing scr_rate's name. A3's assertion on the same bundle prints: target columns {eda_tonic: 1387, eda_scr_rate: 1616} == parquet {eda_tonic: 1387, eda_scr_rate: 1616} -> 'channel binding VERIFIED'. A2's affected figures: the cell reported as 'priv_CONTROL_s42 scr_rate z+2.15' (the single cell that cleared its null, and the reason the run was read as 1/8 multiple-comparisons noise) is in fact the TONIC cell.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#lupi #wearables
