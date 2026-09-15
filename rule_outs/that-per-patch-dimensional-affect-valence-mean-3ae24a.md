---
created_at: '2026-08-19T13:46:13+00:00'
former_ids: []
id: that-per-patch-dimensional-affect-valence-mean-3ae24a
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: true
retraction_reason: 'SUPPLY GREW 1.89x AND THE RULE-OUT NAMED SUPPLY AS ITS OWN LIMIT.
  The original evidence closed with the explicit sentence: ''valence recoverability
  from wrist-channel encoder tokens is below r~0.2 detectable at n=167 subjects x
  1 session''. That is a bound conditional on supply, not an unconditional null --
  it says the probe could not see an effect smaller than roughly r 0.2 at that n.


  The 2026-08-30 corpus rebuild ([13518] -> [13879], merge git_sha 56bb0b5) added
  POPANE study 5 (199 windows / 199 subjects) and study 6 (140/140), both carrying
  valence on their own declared 0-10 dial. Measured from label_events.parquet on the
  new bundle (scratch/_what_new_data_bought.py): valence goes 11,478 -> 21,710 label-epochs
  (1.89x) and the valence-bearing subject count goes 167 (case + popane_s3) -> 955
  across six corpora (popane_s5 199, popane_s3 137, dapper 418, popane_s6 140, case
  30, k_emocon 31). Subjects, which is the unit the MDE was stated in, rise 5.7x.


  That is a change in exactly the quantity the rule-out declared limiting, so the
  claim is reopened for re-measurement rather than assumed still true. NOT RETRACTED:
  the finding that the affect head produced no detectable encoder-side change (affect
  arm vs sampling-matched control near-identical per cell) -- that was a null about
  the HEAD, measured at a tightness the supply change does not touch.


  CAUTION carried forward into any re-test: dapper''s valence is on a 1-5 scale while
  POPANE/CASE are 0-10 / [0.5,9.5], so a pooled fit MUST use declared-range scaling
  (scale_to_declared_range), not within-subject ranks -- and CASE sessions fit inside
  one 120-minute patch, which is why the ranks path was wrong here before.'
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

**RETRACTED:** SUPPLY GREW 1.89x AND THE RULE-OUT NAMED SUPPLY AS ITS OWN LIMIT. The original evidence closed with the explicit sentence: 'valence recoverability from wrist-channel encoder tokens is below r~0.2 detectable at n=167 subjects x 1 session'. That is a bound conditional on supply, not an unconditional null -- it says the probe could not see an effect smaller than roughly r 0.2 at that n.

The 2026-08-30 corpus rebuild ([13518] -> [13879], merge git_sha 56bb0b5) added POPANE study 5 (199 windows / 199 subjects) and study 6 (140/140), both carrying valence on their own declared 0-10 dial. Measured from label_events.parquet on the new bundle (scratch/_what_new_data_bought.py): valence goes 11,478 -> 21,710 label-epochs (1.89x) and the valence-bearing subject count goes 167 (case + popane_s3) -> 955 across six corpora (popane_s5 199, popane_s3 137, dapper 418, popane_s6 140, case 30, k_emocon 31). Subjects, which is the unit the MDE was stated in, rise 5.7x.

That is a change in exactly the quantity the rule-out declared limiting, so the claim is reopened for re-measurement rather than assumed still true. NOT RETRACTED: the finding that the affect head produced no detectable encoder-side change (affect arm vs sampling-matched control near-identical per cell) -- that was a null about the HEAD, measured at a tightness the supply change does not touch.

CAUTION carried forward into any re-test: dapper's valence is on a 1-5 scale while POPANE/CASE are 0-10 / [0.5,9.5], so a pooled fit MUST use declared-range scaling (scale_to_declared_range), not within-subject ranks -- and CASE sessions fit inside one 120-minute patch, which is why the ranks path was wrong here before.

#state-supervision #wearables #control
