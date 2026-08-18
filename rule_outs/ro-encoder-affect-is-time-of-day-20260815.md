---
created_at: '2026-08-15T13:29:00+00:00'
former_ids: []
id: ro-encoder-affect-is-time-of-day-20260815
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-15T13:29:00+00:00'
topic_tags:
- control
- wearables
- n-of-1
- baseline
---

## Claim
That the trained temporal encoder carries ping-specific AFFECT signal, as E22 reported ('locality confirmed') and as my own exp-e31 appeared to confirm against a placebo control. ELIMINATED. What the encoder's epoch-local tokens actually carry on the lifesnaps EMA ping task is TIME OF DAY, and the labels are time-dependent (SEMA3 pings at 10/15/20h; 'tired' is strongly diurnal). Reading a DIFFERENT DAY at the SAME HOUR recovers essentially the whole effect, and on 'tired' scores HIGHER than the true ping.

ALSO ELIMINATED, and this is the reusable part: the RANDOM-EPOCH placebo is NOT a valid locality control on any circadian signal. It destroys ping alignment and time-of-day simultaneously, so it cannot distinguish them. Use a SAME-CLOCK-DIFFERENT-DAY donor, or a null stratified on hour-of-day, or both.

WHAT SURVIVES from the retracted rule-out and is preserved here: (a) the local-vs-global gain is NOT a dimensionality artefact -- pooling collapses the token axis so local and global arms have identical feature counts (4611 avg / 9219 avgmax); (b) the GLOBAL (window-pooled) reads fail their own within-subject shuffle null (z +1.60 p 0.0833 tense; z +0.60 p 0.2083 and z +0.23 p 0.3750 tired), which is unaffected by the time-of-day finding and if anything strengthened -- a window-pooled representation is what the projector consumes in production, and it is indistinguishable from noise on affect.

## Method
Two independent controls that the prior audit (exp-e31) omitted, run on its exact rows, features, folds, classifier and checkpoint.

(1) SAME-CLOCK-DIFFERENT-DAY DONOR. For each ping at epoch e on day d for subject s, read the encoder tokens at the SAME epoch e on a DIFFERENT day d' of the SAME subject, using an identical token selection. Time-of-day and subject are held exactly constant; only the day-and-ping-specific physiology is destroyed. This is the control that separates 'reads the ping' from 'reads what o'clock it is', which the random-epoch placebo cannot do because it destroys both at once.

(2) HOUR-STRATIFIED PERMUTATION NULL. Permute labels within (subject x hour-of-day bin) rather than within subject alone, so the null preserves both the subject's positive rate AND the hour-of-day marginal. Any skill attributable to time-of-day is thereby absorbed into the null. clock_only is carried as a positive control: if the stratified null is working, clock_only's z must collapse while the null itself stays near the observed AUC rather than near 0.5.

## Evidence
v5d/phase_b, n=4219 pings, 61 subjects, 2041 donor days, GroupKFold(5) by subject, RandomForest(300, min_samples_leaf=5), 24 permutations per arm, avg pooling (E31 measured avg and avgmax within 0.01 on every cell, so the operator is not the variable).

=== THE TELL, which should have been checked before E31 was filed ===
clock-only AUC by task:
  E31/E22 ping task        0.5493 (tense) / 0.5739 (tired)   <- nearly the whole local arm
  E29 transition task      0.5129                            <- clock is worthless there
HR is strongly circadian, so tokens covering [ping-120, ping) encode time-of-day more richly and non-linearly than the 3 raw sin/cos/minute features every arm already carried. An RF extracts the clock better from tokens than from the clock block itself.

=== CONTROL 1: SAME CLOCK TIME, DIFFERENT DAY (same subject, identical token selection) ===
Time-of-day held EXACTLY constant; ping-specific physiology destroyed.
  target  local    sameclock_otherday   local-sameclock   (E31's randepoch placebo said)
  tense   0.5876   0.5733               +0.0143           (+0.0722)
  tired   0.5841   0.5946               -0.0105           (+0.0421)
On 'tired' a DIFFERENT DAY at the same hour BEATS the true ping. The apparent locality effect shrinks by 5x on tense and inverts on tired.

=== CONTROL 2: STRATIFIED NULL (permute labels within subject x hour-of-day) ===
Absorbs any 'what time is it' skill into the null itself.
  arm          z vs subject-null  ->  z vs stratified-null
  tense  clock_only   +2.75  ->  +0.37     <- POSITIVE CONTROL: null behaves correctly
  tense  enc_local    +6.70  ->  +1.81
  tired  clock_only   +4.90  ->  +0.98     <- POSITIVE CONTROL
  tired  enc_local    +6.10  ->  -0.22
The local arm's significance collapses on both targets and inverts on tired. clock_only collapsing too (rather than everything collapsing) is what shows the stratified null is absorbing the confound rather than destroying all signal.

=== NET EFFECT ON THE RECORD ===
exp-e29 and exp-e31 no longer disagree. The trained encoder fails to carry affect in BOTH designs. E29's within-window null was the honest one all along, because a within-window design holds subject AND day AND (approximately) circadian phase constant by construction.

UNTOUCHED BY THIS: E29's raw_patch_grid result (AUC 0.6309, z +2.66, transition task). That task's clock-only is 0.5129, so there is no time-of-day confound to inherit, and its null is within-WINDOW. The E29 conclusion -- patch GEOMETRY preserves state, learned WEIGHTS destroy it -- stands, and rb-supervised-state-head-20260815 remains justified.

Artefacts: scratch/e32_timeofday_reconciliation.py, logs/e32_timeofday_reconciliation.json, commit 47fa199. Supersedes retracted rule-out ro-e22-locality-is-not-an-artefact-20260815.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#control #wearables #n-of-1 #baseline
