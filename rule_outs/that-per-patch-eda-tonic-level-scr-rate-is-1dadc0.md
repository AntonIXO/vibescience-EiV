---
created_at: '2026-08-19T13:34:40+00:00'
former_ids: []
id: that-per-patch-eda-tonic-level-scr-rate-is-1dadc0
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-19T13:34:40+00:00'
topic_tags:
- state-supervision
- wearables
- control
---

## Claim
That per-patch EDA (tonic level, SCR rate) is recoverable from the 12 wrist channels via the trained encoder's tokens at this corpus's privileged supply - and that privileged_weight 0.3 (LUPI head, commit 725ae5e) injects EDA information into the encoder tokens. Both read as null at the current n. This does NOT eliminate LUPI-for-state (the downstream question of whether EDA supervision helps the state slot is separately unresolved - the state instrument cannot resolve objective effects at 2 seeds, see exp-047 interpretation); it eliminates the intermediate mechanism claim 'the tokens now carry EDA' at this supply.

## Method
Post-A2 probe (scratch/a2_lupi_eval.py): per-patch EDA targets from privileged_events.parquet (per-dataset median/IQR standardised via privileged_stats.json by_dataset - the pooled-fit dataset-indicator trap avoided), predicted from encoder patch tokens (channel-mean) by subject-grouped OOF RidgeCV; 40-perm null reassigning subject->target within dataset (within-subject permutation is the IDENTITY here - one session per subject - the first run's null had sd 0.000 and was discarded). Instrument note: probe reads encoder tokens; the training head reads fused - a fused-stage read could differ but the encoder is what downstream consumes.

## Evidence
All four arms (privileged s42/s43, sampling-matched control s42/s43), both channels: r between -0.103 and +0.067, R2 <= 0 everywhere, vs always-median trivial. Only 1 of 8 cells clears its null (priv_CONTROL_s42 scr_rate z+2.15) - the control, not the intervention, and 1/8 at 40 perms is multiple-comparisons noise. Privileged-vs-control contrast: no cell where the privileged arm beats its own control's read. Null sd 0.06-0.13 => MDE on r is roughly 0.15-0.25: the honest statement is 'EDA recoverability from wrist tokens is below r~0.2 detectable at n=250 subjects x 1 session'. The supply is 500 tonic cells / 250 subjects (case + popane_s3 + popane_s4), 224 scr cells / 112 subjects, one short session per subject, CASE's 41-min sessions inside one 120-min patch. logs/a2_lupi_eval.json, runner scratch/a2_lupi_eval.py.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#state-supervision #wearables #control
