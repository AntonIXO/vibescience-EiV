---
created_at: '2026-08-19T13:46:13+00:00'
former_ids: []
id: that-per-patch-dimensional-affect-valence-mean-3ae24a
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-19T13:46:13+00:00'
topic_tags:
- state-supervision
- wearables
- control
---

## Claim
That per-patch dimensional affect (valence mean/spread on the two-instrument declared-range axis; CASE-only arousal) is recoverable from the 12 wrist channels via trained encoder tokens at this corpus's supply - and that affect_weight 0.3 (dimensional head, commit 725ae5e) changes what the encoder tokens carry. Both null. Affect arm and its sampling-matched control are near-IDENTICAL per cell (e.g. valence_mean s42: -0.068 vs -0.070; arousal s43: -0.188 vs -0.188) - the head produced no detectable encoder-side change at all, tighter than the EDA case.

## Method
Post-A2 probe (scratch/a2_affect_eval.py): per-patch targets from affect_patch_targets (declared-range scaling - CASE joystick [0.5,9.5] + POPANE s3 dial [0,10] onto [0,1]), predicted from encoder patch tokens (channel-mean) by subject-grouped OOF RidgeCV; 40-perm subject->target reassignment within dataset (within-subject permutation is the identity at one session per subject). Same instrument family as the EDA probe (rule-out that-per-patch-eda...1dadc0), same caveat: probe reads encoder tokens, the training head reads fused.

## Evidence
All 12 cells (2 arms x 2 seeds x 3 targets): r -0.19..-0.02, R2 <= 0 vs always-median. No cell clears its 40-perm null (best z +0.43); affect_s42 valence_mean is 2sigma BELOW its null. Supply: 334 valence cells / 167 subjects (case + popane_s3), 60 arousal cells / 30 subjects (CASE only), one short session per subject - sessions inside 1-2 patches of a 1440-epoch day-window, so the token features are dominated by the other ~22 empty patches' context. Null sd 0.04-0.20 => MDE on r roughly 0.1-0.4 depending on target. Honest sentence: 'valence recoverability from wrist-channel encoder tokens is below r~0.2 detectable at n=167 subjects x 1 session; the affect head moved nothing the probe can see.' logs/a2_affect_eval.json, runner scratch/a2_affect_eval.py.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#state-supervision #wearables #control
