---
artifacts: []
closed: true
config_note: 'Executes E4''s own preregistered Stage-0 CPU gate, which has sat UNRUN
  since 2026-08-13. Script scratch/e4_stage0_excursion_gate.py, readings S1-S3 committed
  PRE-DATA. Live substrate: corpus ac7d2b1c, checkpoint outputs/checkpoints/ab_ctrl/phase_b
  (current recipe) -- deliberately NOT the old-corpus v5c/v5d family E19 used, per
  the premise-staleness rule. Strict-clean pool n=4000 over 93 subjects (coverage>0.9,
  HR min>0, mean>20 bpm). Subject-grouped GroupKFold(5) RidgeCV. MANDATORY CONTROL
  reported: increment over R2(peak | true daily mean, 1 feature) = -0.0325, never
  raw R2-to-max.


  TWO SOURCES IN THIS RECORD, KEPT SEPARATE. (1) MEASURED HERE, new, on live substrate:
  the Stage-0 encoder-site numbers. (2) COPIED, NOT re-measured, and carrying no calibration
  weight: the production-site figures from ro-avgmax-pooling-production-site-20260815
  (avg->avgmax gap +0.0235 v5c / +0.0447 v5d, both below the +0.05 decision bar) and
  ro-dual-avg-max-pooling-downstream-20260805 (delta +0.0090). Those two rule-outs
  postdate E4 and eliminate the end-to-end arm E4''s later gates would have scored.'
cost: null
crash_reason: ''
created_at: '2026-08-20T21:51:50+00:00'
diagnostics_measured:
- after: 0.7334
  before: 0.299
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: logs/e4_stage0_excursion_gate.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: e4-g0-cpu-excursion-increment-ge-0-25
  passed: true
- evidence: ''
  gate_id: e4-g1-dense-peak-corr-above-mean-pool-ceiling
  passed: false
- evidence: ''
  gate_id: e4-g2-BLOCKING-hr-axes-within-v9band04-band
  passed: false
- evidence: ''
  gate_id: e4-g3-dense-peak-skill-above-minus-1
  passed: false
git_ref: ''
hypothesis_id: e4-encoder-excursion-avgmax-20260813
id: exp-e4-stage0-excursion-gate-20260820
interpretation:
  at: '2026-08-20T21:52:54+00:00'
  failure_mode: recipe_conditional
  lesson: 'PREMISE CONFIRMED AND CROSS-SUBSTRATE REPLICATED; PRESCRIPTION DEAD. E4''s
    own blocking Stage-0 gate passed at +0.7334 against a >=0.25 bar, and the avg->avgmax
    gap measured +0.4344 against a recipe band of 0.0457 -- 9.5x the band, with the
    shuffle control at -0.0039. The encoder''s mean-pooling really does destroy roughly
    0.43 R2 of daily-excursion information.


    THE REPLICATION IS THE DURABLE PART. E19/E19b measured this gap at +0.4042 across
    five OLD-corpus checkpoints. Measured here at +0.4344 on a current-recipe checkpoint
    two corpus rebuilds later. A headline that survives a substrate change of that
    size is a property of the architecture, not a basin draw -- which is exactly what
    E19b was designed to ask and could not fully answer at the time.


    WHY THE HYPOTHESIS STILL DOES NOT EARN ITS ARM: the information the encoder-site
    pool destroys is LARGELY RE-CREATED by the projector''s cross-attention before
    the LM ever sees it. That is what ro-avgmax-pooling-production-site-20260815 (+0.0235
    / +0.0447) and ro-dual-avg-max-pooling-downstream-20260805 (+0.0090) measured,
    both under the +0.05 decision bar. So a large, real, reproducible encoder-site
    effect shrinks ~9x by the production site. E4''s three downstream gates are recorded
    UNMEASURED rather than failed, because running them would score an intervention
    already eliminated where it acts. g2 is blocking and unmeasured, so this cannot
    read as a win.


    REUSABLE RULE, and this is the third time this vault has paid for it: AN ENCODER-SITE
    MEASUREMENT IS NOT EVIDENCE ABOUT WHAT THE CONSUMER RECEIVES. Measure at the site
    the intervention will act, before funding the arm. The corollary earned here is
    the cheerful one -- a CPU Stage-0 gate that passes 3x over its bar is still not
    a green light if a later rule-out has moved the site.


    NEW, UNPREREGISTERED, AND WORTH TESTING PROPERLY IF ANYONE REVIVES THIS AXIS:
    max-pooling ALONE scores +0.7348, matching avgmax''s +0.7334. The concat''s avg
    half contributes nothing. E4 specified ''avg+max concat''; the data says max-only
    gets the whole effect at half the width. Carries no calibration weight (observed,
    not predicted), but it would halve the cost of any future excursion arm and should
    be the first thing checked.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: 0.4344
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: false
    eiv_peak_excursion_increment_r2: true
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[e4-encoder-excursion-avgmax-20260813]].
External run: logs/e4_stage0_excursion_gate.json

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.299 → after 0.7334  (Δ +0.4344, up ✓ predicted up)

## Gates
- `e4-g0-cpu-excursion-increment-ge-0-25`: PASS
- `e4-g1-dense-peak-corr-above-mean-pool-ceiling`: **FAIL**
- `e4-g2-BLOCKING-hr-axes-within-v9band04-band`: **FAIL**
- `e4-g3-dense-peak-skill-above-minus-1`: **FAIL**

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

PREMISE CONFIRMED AND CROSS-SUBSTRATE REPLICATED; PRESCRIPTION DEAD. E4's own blocking Stage-0 gate passed at +0.7334 against a >=0.25 bar, and the avg->avgmax gap measured +0.4344 against a recipe band of 0.0457 -- 9.5x the band, with the shuffle control at -0.0039. The encoder's mean-pooling really does destroy roughly 0.43 R2 of daily-excursion information.

THE REPLICATION IS THE DURABLE PART. E19/E19b measured this gap at +0.4042 across five OLD-corpus checkpoints. Measured here at +0.4344 on a current-recipe checkpoint two corpus rebuilds later. A headline that survives a substrate change of that size is a property of the architecture, not a basin draw -- which is exactly what E19b was designed to ask and could not fully answer at the time.

WHY THE HYPOTHESIS STILL DOES NOT EARN ITS ARM: the information the encoder-site pool destroys is LARGELY RE-CREATED by the projector's cross-attention before the LM ever sees it. That is what ro-avgmax-pooling-production-site-20260815 (+0.0235 / +0.0447) and ro-dual-avg-max-pooling-downstream-20260805 (+0.0090) measured, both under the +0.05 decision bar. So a large, real, reproducible encoder-site effect shrinks ~9x by the production site. E4's three downstream gates are recorded UNMEASURED rather than failed, because running them would score an intervention already eliminated where it acts. g2 is blocking and unmeasured, so this cannot read as a win.

REUSABLE RULE, and this is the third time this vault has paid for it: AN ENCODER-SITE MEASUREMENT IS NOT EVIDENCE ABOUT WHAT THE CONSUMER RECEIVES. Measure at the site the intervention will act, before funding the arm. The corollary earned here is the cheerful one -- a CPU Stage-0 gate that passes 3x over its bar is still not a green light if a later rule-out has moved the site.

NEW, UNPREREGISTERED, AND WORTH TESTING PROPERLY IF ANYONE REVIVES THIS AXIS: max-pooling ALONE scores +0.7348, matching avgmax's +0.7334. The concat's avg half contributes nothing. E4 specified 'avg+max concat'; the data says max-only gets the whole effect at half the width. Carries no calibration weight (observed, not predicted), but it would halve the cost of any future excursion arm and should be the first thing checked.

## Notes
READ THE SPLIT, NOT THE VERDICT COLUMN. E4's OWN blocking Stage-0 gate PASSES by 3x on the live substrate, while the three downstream gates are UNMEASURED because the intervention they would score is already refuted at the production site by two rule-outs that postdate E4. The mechanical verdict is therefore mixed and the useful content is entirely in the split.

MEASURED HERE (new, corpus ac7d2b1c, ab_ctrl encoder, n=4000/93 subjects, strict-clean):
  control R2(peak | true daily mean, 1 feature)  -0.0325
  avg     R2 0.2665  increment +0.2990
  max     R2 0.7023  increment +0.7348
  avgmax  R2 0.7009  increment +0.7334   <- E4 g0 bar was >=0.25
  avg -> avgmax gap +0.4344, vs the recipe band 0.0457 (9.5x the band)
  shuffle control  avg -0.0013 / avgmax -0.0039  (dead)

THE ENCODER-SITE EFFECT REPRODUCES ON A CORPUS TWO REBUILDS NEWER THAN THE ONE E19 MEASURED IT ON. E19/E19b reported the avg->avgmax gap as +0.4042 (band 0.0457 across 5 old-corpus checkpoints); measured here on a current-recipe checkpoint it is +0.4344. That is a genuine cross-substrate replication of a headline that could easily have been a basin artifact, and it is worth recording as such.

NEW AND UNPREREGISTERED, BUT THE MOST ACTIONABLE NUMBER IN THE RUN: MAX ALONE (+0.7348) EQUALS AVGMAX (+0.7334). The concatenation adds nothing -- the avg half is dead weight on this axis. E4 specified the intervention as 'avg+max concat'; the data says max-pooling alone captures the entire effect at half the feature width. Any future excursion work should test max-only first. This is an observation from a run whose primary gate was preregistered, so it carries no calibration weight and should be re-tested if it is ever load-bearing.

WHY THE OTHER THREE GATES ARE UNMEASURED RATHER THAN FAILED. g1/g2/g3 all require a trained end-to-end arm. Two rule-outs filed AFTER E4 was written eliminate exactly that arm at the site where it would act: ro-avgmax-pooling-production-site-20260815 (gap +0.0235 on v5c, +0.0447 on v5d, both under the +0.05 bar) and ro-dual-avg-max-pooling-downstream-20260805 (delta +0.0090). Those figures are COPIED into this record, NOT re-measured here, and carry no calibration weight. Spending ~2h GPU to score gates for an already-refuted intervention would be waste. g2 is BLOCKING and unmeasured, which is precisely why this must not read as a win for E4's full claim.

THE HONEST SYNTHESIS: E4's PREMISE is confirmed and stronger than when written -- the encoder's mean-pooling really does destroy ~0.43 R2 of excursion information, reproducibly, across corpora. E4's PRESCRIPTION (change the pooling operator to fix emission) is dead, because the projector's cross-attention already recovers most of that information before the LM sees it, which is what the production-site rule-outs measured. Premise alive, prescription dead -- the same shape as H4/H4b on this problem.

NOTE FOR THE EXTENT WORK: this run and exp-w5-three-stage-contraction-20260820 are measuring the SAME pooling line from two directions. W5 showed the pool destroys short-session SPREAD (recoverable by re-weighting the denominator); this shows it destroys EXCURSION (recoverable by changing the operator). Neither is a re-run of the other -- different target class (momentary/between-subject vs daily aggregate), different fix (denominator vs operator).

#cross-modal #collapse #deployment-distribution #optihealth #baseline
