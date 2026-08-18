---
created_at: '2026-08-15T12:05:50+00:00'
former_ids: []
id: ro-e22-locality-is-not-an-artefact-20260815
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: true
retraction_reason: "OVERTURNED BY exp-e32 (commit 47fa199) THE SAME DAY, by a control
  this rule-out did not run. Retracting my own claim.\n\nWHAT THIS RULE-OUT GOT WRONG.
  It asserted that E22's locality effect is genuine because the local arm beat a PLACEBO
  that read the same number of tokens at a RANDOM epoch on the same day. That placebo
  destroys ping alignment AND time-of-day SIMULTANEOUSLY, so it cannot distinguish
  'the tokens carry ping-specific physiology' from 'the tokens carry what time it
  is, and the label is time-dependent'.\n\nTHE TELL I MISSED: clock-only AUC is 0.5493
  (tense) / 0.5739 (tired) on this ping task -- nearly the entire local arm (0.5876
  / 0.5841) -- while on E29's transition task clock-only is only 0.5129. HR is strongly
  circadian, so tokens over [ping-120, ping) encode time-of-day more richly and non-linearly
  than the 3 raw sin/cos/minute features every arm already carried.\n\nTHE DECISIVE
  CONTROL (same clock time, DIFFERENT day, same subject -- time-of-day held exactly
  constant, ping-specific physiology destroyed):\n  tense  local 0.5876  sameclock_otherday
  0.5733  =>  +0.0143  (E31's placebo said +0.0722)\n  tired  local 0.5841  sameclock_otherday
  0.5946  =>  -0.0105  (E31's placebo said +0.0421)\nOn `tired` a DIFFERENT DAY at
  the same hour scores HIGHER than the true ping.\n\nCONFIRMED INDEPENDENTLY BY A
  STRATIFIED NULL (permute within subject x hour-of-day, absorbing time-of-day skill
  into the null):\n  enc_local  z +6.70 -> +1.81 (tense)   z +6.10 -> -0.22 (tired)\n
  \ clock_only z +2.75 -> +0.37 (tense)   z +4.90 -> +0.98 (tired)   <- positive control:
  the null behaves correctly rather than destroying everything\n\nWHAT SURVIVES AND
  IS BEING RE-FILED SEPARATELY: (a) the DIMENSIONALITY half -- local and global arms
  genuinely have identical feature counts (4611 avg / 9219 avgmax), since pooling
  collapses the token axis, so the gain was never a dimension artefact; (b) the GLOBAL-arms-fail-their-null
  observation, which is unaffected and if anything strengthened. Both are recorded
  in the replacement rule-out ro-encoder-affect-is-time-of-day-20260815.\n\nNET EFFECT
  ON THE RECORD: E29 and E31 no longer disagree. The trained encoder fails on BOTH
  designs. E29's within-window null was the honest one all along because it holds
  subject AND day constant by construction."
ruled_out_at: '2026-08-15T12:05:50+00:00'
topic_tags:
- control
- wearables
- n-of-1
- baseline
---

## Claim
E22's 'locality confirmed' finding is uncontrolled and may be an artefact. SPECIFICALLY ELIMINATED AS A WORRY: (a) that the local-vs-global gain is a shuffle artefact, and (b) that it is a DIMENSIONALITY artefact from local arms using fewer tokens. Both are false. Reading the trained encoder's tokens LOCALLY (patches covering [ping-120, ping)) genuinely beats reading them GLOBALLY, the gain is about WHERE the tokens are read rather than how many, and it survives a within-subject shuffle null at z +6.0 to +6.8. E22's headline stands. Do not re-run this audit.

THE COROLLARY THAT MATTERS MORE, and is newly established: the GLOBAL arms are the weak ones. enc_global_avg FAILS its own within-subject shuffle null on both targets (z +1.60 p 0.0833 on tense; z +0.60 p 0.2083 on tired). A globally-pooled read of the encoder is statistically indistinguishable from noise on affect -- and a globally-pooled representation is exactly what the projector consumes in production. So any future state/affect head MUST read epoch-local tokens; a window-pooled head is refuted before it is built.

## Method
Re-ran E22's exact design (same rows, same shallow/clock feature construction, same GroupKFold-by-subject split, same RandomForest hyperparameters, same v5d/phase_b checkpoint with weights loaded under refusal-to-proceed) and added the two controls E22 omitted.

(1) WITHIN-SUBJECT SHUFFLE NULL, 24 permutations per arm. Labels are permuted INSIDE each subject, which preserves every subject's own positive rate. This keeps the trait constant in the null -- the constant that dominates cross-subject affect on this corpus (per-user constant AUC 0.7381 vs 0.5818 for the best physiological arm, exp-e16) -- so what survives the null is ping-level signal rather than between-subject trait structure.

(2) PLACEBO LOCALITY. For each ping, read the SAME NUMBER of covering tokens at a RANDOM epoch on the SAME day, with identical pooling. This holds token count, subject, day and operator fixed and destroys ONLY the temporal alignment to the ping, which is the actual content of the locality claim. 'local vs global' alone cannot distinguish 'reading at the right time' from 'reading fewer things'.

## Evidence
v5d/phase_b, n=4219 pings, 61 subjects, GroupKFold(5) by subject, RandomForest(300, min_samples_leaf=5), 24 within-subject label permutations per arm. Clock block (sin/cos + minute-of-day) concatenated to every arm, as in E22.

lifesnaps_tense (pos rate 0.1228)
  arm                        dim     AUC      null             z       perm p
  enc_global_avg             4611    0.5260   0.4963+-0.0185   +1.60   0.0833   <- FAILS its null
  enc_local_avg              4611    0.5876   0.4862+-0.0169   +6.01   0.0000
  enc_PLACEBO_local_avg      4611    0.5154   0.4896+-0.0136   +1.90   0.0000
  enc_global_avgmax          9219    0.5348   0.4846+-0.0201   +2.50   0.0000
  enc_local_avgmax           9219    0.5955   0.4862+-0.0162   +6.76   0.0000
  enc_PLACEBO_local_avgmax   9219    0.5294   0.4844+-0.0187   +2.41   0.0000
  avg    : local-global +0.0616 | local-PLACEBO +0.0722
  avgmax : local-global +0.0607 | local-PLACEBO +0.0661

lifesnaps_tired (pos rate 0.2214)
  enc_global_avg             4611    0.5255   0.5189+-0.0110   +0.60   0.2083   <- FAILS its null
  enc_local_avg              4611    0.5841   0.5123+-0.0116   +6.17   0.0000
  enc_PLACEBO_local_avg      4611    0.5420   0.5058+-0.0115   +3.15   0.0000
  enc_global_avgmax          9219    0.5194   0.5169+-0.0107   +0.23   0.3750   <- FAILS its null
  enc_local_avgmax           9219    0.5903   0.5126+-0.0115   +6.75   0.0000
  enc_PLACEBO_local_avgmax   9219    0.5287   0.5031+-0.0124   +2.06   0.0417
  avg    : local-global +0.0586 | local-PLACEBO +0.0421
  avgmax : local-global +0.0709 | local-PLACEBO +0.0616

FOUR-FOR-FOUR: on both targets and both pooling operators, the local arm clears its own shuffle null at z >= +6.0 AND beats the placebo (same token count, wrong epoch, same day) by +0.042 to +0.072. E22's recorded local_avg on tense was 0.5941 vs 0.5876 here (n=4208 vs 4219, a row-filter difference); direction and magnitude replicate.

INCIDENTAL METHODOLOGICAL FINDING: the dimensionality confound I set out to test DOES NOT EXIST in this design. Pooling collapses the token axis, so local and global arms have IDENTICAL feature counts (4611 avg / 9219 avgmax). The placebo was still the correct control because it tests ALIGNMENT directly rather than inferring it from dimension bookkeeping -- and it is the control that converts 'local > global' into 'local > same-size-wrong-place'.

Artefacts: scratch/e31_e22_locality_audit.py, logs/e31_e22_locality_audit.json, commit da42ef7.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

**RETRACTED:** OVERTURNED BY exp-e32 (commit 47fa199) THE SAME DAY, by a control this rule-out did not run. Retracting my own claim.

WHAT THIS RULE-OUT GOT WRONG. It asserted that E22's locality effect is genuine because the local arm beat a PLACEBO that read the same number of tokens at a RANDOM epoch on the same day. That placebo destroys ping alignment AND time-of-day SIMULTANEOUSLY, so it cannot distinguish 'the tokens carry ping-specific physiology' from 'the tokens carry what time it is, and the label is time-dependent'.

THE TELL I MISSED: clock-only AUC is 0.5493 (tense) / 0.5739 (tired) on this ping task -- nearly the entire local arm (0.5876 / 0.5841) -- while on E29's transition task clock-only is only 0.5129. HR is strongly circadian, so tokens over [ping-120, ping) encode time-of-day more richly and non-linearly than the 3 raw sin/cos/minute features every arm already carried.

THE DECISIVE CONTROL (same clock time, DIFFERENT day, same subject -- time-of-day held exactly constant, ping-specific physiology destroyed):
  tense  local 0.5876  sameclock_otherday 0.5733  =>  +0.0143  (E31's placebo said +0.0722)
  tired  local 0.5841  sameclock_otherday 0.5946  =>  -0.0105  (E31's placebo said +0.0421)
On `tired` a DIFFERENT DAY at the same hour scores HIGHER than the true ping.

CONFIRMED INDEPENDENTLY BY A STRATIFIED NULL (permute within subject x hour-of-day, absorbing time-of-day skill into the null):
  enc_local  z +6.70 -> +1.81 (tense)   z +6.10 -> -0.22 (tired)
  clock_only z +2.75 -> +0.37 (tense)   z +4.90 -> +0.98 (tired)   <- positive control: the null behaves correctly rather than destroying everything

WHAT SURVIVES AND IS BEING RE-FILED SEPARATELY: (a) the DIMENSIONALITY half -- local and global arms genuinely have identical feature counts (4611 avg / 9219 avgmax), since pooling collapses the token axis, so the gain was never a dimension artefact; (b) the GLOBAL-arms-fail-their-null observation, which is unaffected and if anything strengthened. Both are recorded in the replacement rule-out ro-encoder-affect-is-time-of-day-20260815.

NET EFFECT ON THE RECORD: E29 and E31 no longer disagree. The trained encoder fails on BOTH designs. E29's within-window null was the honest one all along because it holds subject AND day constant by construction.

#control #wearables #n-of-1 #baseline
