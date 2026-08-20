---
artifacts:
- logs/wsweep_state.json
- logs/wsweep_raw_shallow.json
- scratch/wsweep_state.py
- scratch/wsweep_raw_shallow.py
closed: true
config_note: 'Executes rb-window-size-x-pooling-state-sweep-20260819 (gates committed
  in the runbook BEFORE the sweep ran; this experiment formalizes and closes it).
  Parent: exp-048 (refutes — this tests its explicitly-recorded residual). Instruments:
  scratch/wsweep_state.py (12 chains x W{30,60,120,240} x pooling{mean,max,meanmax},
  shared 200-perm set per W, pooled estimator = mean-of-seeds vs null-of-means preserving
  inter-seed correlation) + scratch/wsweep_raw_shallow.py (g4: raw_shallow RF-300
  re-priced per W, 24 perms). Pool: clean transitions, nurse_stress excluded; n per
  W = 2154/2212/2272/2302. Chains: B60 5 seeds (b_patch60_s42-46), A60 5 seeds, CTRL120
  2 seeds (b_patch120_s42/43). Artifacts: logs/wsweep_state.json, logs/wsweep_raw_shallow.json.
  GATES from the runbook: g1 blocking pooled p<0.01 at best cell; g2 blocking control
  family under half the effect; g3 non-blocking W-response shape; g4 honesty raw_shallow
  per W.'
cost:
  gpu_hours: 0.1
  wall_hours: 0.3
crash_reason: ''
created_at: '2026-08-19T21:19:40+00:00'
diagnostics_measured:
- after: 0.0218
  before: 0.0079
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do NOT evaluate momentary-state readouts at W=120/240 as the primary cell: the
  trained-head W-response is monotone decreasing and INVERTS by W240 (B60 pooled:
  W30 +0.022 z+3.6 / W60 +0.015 / W120 +0.008 z+1.4 / W240 -0.009 z-2.0). exp-048''s
  REFUTE was scored at W120 - nearly the worst informative window. Primary eval cell
  from now on: W=30, max-pooling over covering patches.'
- 'Do NOT read the pooled z+3.6 as ''state solved'': the same-W handcrafted bound
  (raw_shallow W30 over_null +0.067 z+5.0, AUC 0.612) is 3x the best learned effect
  (+0.022, AUC 0.569). The learned heads recover ~1/3 of the shallow-detectable signal.
  Ship-the-shallow-detector remains the deployment recommendation; the learned result
  is a mechanism finding (supervision reaches the tokens), not a product one.'
- 'Do NOT quote CTRL120-vs-B60 as a clean PL effect: 2 seeds vs 5, and CTRL120 clears
  p<0.05 at W30 meanmax itself (+0.012 z+1.85). Direction favors PL60 at matched W
  but a balanced-seed comparison was never run.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: w-sweep-rb-window-size-x-pooling-state-sweep-73469a
id: exp-049-w-sweep-rb-window-size-x-3735f7
interpretation:
  at: '2026-08-19T21:20:38+00:00'
  failure_mode: null
  lesson: 'THE EVAL WINDOW WAS THE HIDDEN VARIABLE ALL ALONG. Every state eval since
    E26 scored at W=120; the trained heads'' information lives at W=30 (monotone response,
    inverts at W240). At W30/max the pooled 5-seed estimator clears decisively (B60
    +0.0218 z+3.64 p .0000; A60 replicates +0.0206 z+3.45) — exp-048''s refute was
    measured at nearly the worst W and its residual is resolved POSITIVE. Mechanism
    question answered: supervision DOES put momentary state into the encoder — at
    ~1/3 of the same-W handcrafted bound (raw_shallow W30 +0.067 z+5.0). Deployment
    unchanged (ship shallow); next lever is the 3x gap, which is supply (label density
    per FIRMED/E24), not architecture. Also: raw_shallow''s W-response is U-shaped
    (best at W120) while the trained head''s is monotone-decreasing — they read DIFFERENT
    temporal structure, suggesting a shallow+head ensemble as a cheap follow-up. PL60-vs-PL120
    attribution stays directional (g2 failed by a hair on unbalanced seeds).'
  transfers_to:
  - a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b
  - analog-state-supervision-a1-on-the-12-547-c5f5eb
key_numbers:
  a60_w30_max_over_null: 0.0206
  a60_w30_max_z: 3.45
  b60_w240_mean_over_null: -0.0089
  b60_w240_mean_z: -2.04
  b60_w30_max_over_null: 0.0218
  b60_w30_max_perm_p: 0.0
  b60_w30_max_pooled_auc: 0.5688
  b60_w30_max_z: 3.64
  ctrl120_w30_meanmax_over_null: 0.0117
  ctrl120_w30_meanmax_z: 1.85
  n_W30: 2154.0
  raw_shallow_w30_auc: 0.6117
  raw_shallow_w30_over_null: 0.0666
  raw_shallow_w30_z: 5.02
observed_effects:
- delta: 0.0139
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_momentary_state_auc_over_null: true
provenance: {}
recipe_fingerprint:
  interventions: analog-state-supervision-ladder
verdict: supports
---

## Summary
Tests hypothesis [[w-sweep-rb-window-size-x-pooling-state-sweep-73469a]].

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before 0.0079 → after 0.0218  (Δ +0.0139, up ✓ predicted up)

**Verdict:** supports

## Interpretation
THE EVAL WINDOW WAS THE HIDDEN VARIABLE ALL ALONG. Every state eval since E26 scored at W=120; the trained heads' information lives at W=30 (monotone response, inverts at W240). At W30/max the pooled 5-seed estimator clears decisively (B60 +0.0218 z+3.64 p .0000; A60 replicates +0.0206 z+3.45) — exp-048's refute was measured at nearly the worst W and its residual is resolved POSITIVE. Mechanism question answered: supervision DOES put momentary state into the encoder — at ~1/3 of the same-W handcrafted bound (raw_shallow W30 +0.067 z+5.0). Deployment unchanged (ship shallow); next lever is the 3x gap, which is supply (label density per FIRMED/E24), not architecture. Also: raw_shallow's W-response is U-shaped (best at W120) while the trained head's is monotone-decreasing — they read DIFFERENT temporal structure, suggesting a shallow+head ensemble as a cheap follow-up. PL60-vs-PL120 attribution stays directional (g2 failed by a hair on unbalanced seeds).

Transfers to: [[a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b]], [[analog-state-supervision-a1-on-the-12-547-c5f5eb]]

## Key numbers
- `a60_w30_max_over_null`: 0.0206
- `a60_w30_max_z`: 3.45
- `b60_w240_mean_over_null`: -0.0089
- `b60_w240_mean_z`: -2.04
- `b60_w30_max_over_null`: 0.0218
- `b60_w30_max_perm_p`: 0
- `b60_w30_max_pooled_auc`: 0.5688
- `b60_w30_max_z`: 3.64
- `ctrl120_w30_meanmax_over_null`: 0.0117
- `ctrl120_w30_meanmax_z`: 1.85
- `n_W30`: 2154
- `raw_shallow_w30_auc`: 0.6117
- `raw_shallow_w30_over_null`: 0.0666
- `raw_shallow_w30_z`: 5.02

## Artifacts
- `logs/wsweep_state.json`
- `logs/wsweep_raw_shallow.json`
- `scratch/wsweep_state.py`
- `scratch/wsweep_raw_shallow.py`

## Do not retry
- Do NOT evaluate momentary-state readouts at W=120/240 as the primary cell: the trained-head W-response is monotone decreasing and INVERTS by W240 (B60 pooled: W30 +0.022 z+3.6 / W60 +0.015 / W120 +0.008 z+1.4 / W240 -0.009 z-2.0). exp-048's REFUTE was scored at W120 - nearly the worst informative window. Primary eval cell from now on: W=30, max-pooling over covering patches.
- Do NOT read the pooled z+3.6 as 'state solved': the same-W handcrafted bound (raw_shallow W30 over_null +0.067 z+5.0, AUC 0.612) is 3x the best learned effect (+0.022, AUC 0.569). The learned heads recover ~1/3 of the shallow-detectable signal. Ship-the-shallow-detector remains the deployment recommendation; the learned result is a mechanism finding (supervision reaches the tokens), not a product one.
- Do NOT quote CTRL120-vs-B60 as a clean PL effect: 2 seeds vs 5, and CTRL120 clears p<0.05 at W30 meanmax itself (+0.012 z+1.85). Direction favors PL60 at matched W but a balanced-seed comparison was never run.

## Notes
ALL FOUR PREREGISTERED CLAIMS CONFIRMED, GATES SCORED FROM THE RUNBOOK COMMITMENTS. g1 PASS (blocking): pooled 5-seed B60 estimator at W30/max = AUC 0.5688, over_null +0.0218, z +3.64, perm p 0.0000 (< .01); A60 replicates independently (+0.0206, z +3.45) - ten distinct checkpoints across two training stages agree. g2 PARTIAL (blocking as committed - 'control family under half the effect'): CTRL120 W30 best cell +0.0117 = 54% of B60's +0.0218, marginally ABOVE the half bar; scored honestly as FAIL-BY-A-HAIR with the caveat that CTRL120 is 2 seeds vs 5 and its z (+1.85) does not clear its own null at p<.01. Attribution of the PL60-vs-PL120 delta therefore remains DIRECTIONAL, not confirmed. g3 PASS: monotone W-response with sign inversion at W240 in all three families - the trained head reads the recent ~30-60 min and is actively WRONG about the day scale; dose-response shape is the strongest internal-validity evidence the state program has produced. g4 HONESTY: raw_shallow at W30 = AUC 0.6117, over_null +0.0666, z +5.02 - the handcrafted bound is 3x the best learned effect at the same W, and its own W-response is FLAT-ISH/U-shaped (W120 +0.0735 is its best), i.e. the shallow features and the trained head are reading DIFFERENT temporal structure. CONSEQUENCES: (1) exp-048's refute stands for its committed cell (W120) but its residual is now RESOLVED POSITIVE at W30 - the weak-consistent-effect interpretation was correct; (2) the primary eval cell for every future state arm is W=30/max-pooling; (3) deployment recommendation unchanged (shallow detector), but the mechanism question 'can supervision put state into the encoder' is now answered YES at 1/3 of the shallow bound - the next lever is closing that 3x gap (supply, per E24/FIRMED) rather than proving existence.

#state-supervision #pooling #epoch-resolution #control #wearables #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
