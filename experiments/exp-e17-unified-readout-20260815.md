---
artifacts: []
closed: true
config_note: 'Runner: scratch/e17_unified_readout.py (+ scratch/canon_utils.py). Pure
  CPU, no GPU, no model weights. Corpus git_sha e849eb7, tensor [11318,1440,12]. Seed
  0. Window HELD FIXED at 1440 minutes in every arm; only the pooling operator and
  token count vary.


  Outputs: logs/e17_unified_readout_a.json, logs/e17_unified_readout_b.json, logs/e17_task_a.log,
  logs/e17_task_b.log.


  BUG FOUND AND FIXED BEFORE ANY NUMBER WAS TRUSTED: data/canonical/population_stats.json
  stores median/iqr as per-channel LISTS, not a name-keyed dict. Resolving by name
  via .get() silently returns (0.0, 1.0), leaving HR in normalised space while the
  code believes it is bpm. The first Task-A run filtered its entire pool to n=0 against
  a ''mean HR > 20 bpm'' gate. Fixed via scratch/canon_utils.py (channel_denorm +
  assert_bpm tripwire); correct values median=72.833 iqr=23.622.


  E16 RE-VERIFIED AGAINST THAT BUG (scratch/e16_denorm_invariance_check.py, logs/e16_denorm_invariance_check.json):
  E16 used the same broken helper, so its features were normalised rather than bpm.
  Reran the E16 headline arm both ways: delta +0.022760 (buggy) vs +0.022155 (correct),
  |difference| 0.00060435 — 46x below E16''s preregistered MDE of 0.028. Per-feature
  affine rescaling preserves ranks, so RF splits and AUC are invariant in exact arithmetic;
  the residual is float32 split-threshold noise. E16''s conclusions and its rule-out
  STAND unchanged.'
cost: null
crash_reason: ''
created_at: '2026-08-14T21:47:37+00:00'
diagnostics_measured:
- after: 0.3605
  before: 0.009
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.6051
  before: 0.7381
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: ''
  gate_id: gA_operator_beats_avg
  passed: true
- evidence: ''
  gate_id: gA_tokens_beat_23
  passed: false
- evidence: ''
  gate_id: gB_local_beats_global
  passed: false
- evidence: ''
  gate_id: gB_beats_within_subject_constant
  passed: false
git_ref: ''
hypothesis_id: hyp-e17-unified-readout-operator-20260815
id: exp-e17-unified-readout-20260815
interpretation:
  at: '2026-08-18T21:08:39+00:00'
  failure_mode: mechanism_wrong
  lesson: 'DO NOT READ THIS `null` AS ''nearly worked'' — the run bundled two targets
    and they came apart completely. Task A (excursion readout) CONFIRMED enormously:
    at the incumbent 23-token grid the increment over the daily-mean control (0.0649)
    goes avg +0.0090 -> avgmax concat +0.3605, a +0.3515 operator gain, SEVEN times
    the preregistered +0.05 bar, with window, token count and data all held fixed
    (n=4000, 77 subjects); max alone +0.0378 and lse alone +0.0374 are near-tied and
    far below, localising the mechanism as LEVEL AND EXTREMUM TOGETHER, and the production
    projector''s adaptive_avg_pool2d (cross_modal.py:260) is sitting on the +0.0090
    arm. Task B (affect) nulled and is what set the verdict: best absolute AUC 0.6051
    across all 8 token x operator arms vs a within-subject constant of 0.7381 (shortfall
    -0.1330), and locality is sign-unstable (local-minus-global -0.0226..+0.0376 against
    MDE 0.028) — a second independent feature construction hitting E16''s exact trait
    ceiling. Secondary and actionable: the LSM-2 144-token geometry is uniformly WORSE
    than 23 at every operator (avgmax 0.1470 vs 0.3605, avg -0.0383, max -0.0302,
    lse -0.0311; penalty -0.2135), so token-count stages should be rescoped to OPERATOR.
    Also: population_stats.json stores median/iqr as per-channel LISTS, so name-keyed
    .get() silently returns (0.0,1.0) and leaves HR normalised while code believes
    bpm — always denorm via canon_utils.channel_denorm with the assert_bpm tripwire.'
  transfers_to:
  - hyp-e19-encoder-pooling-transfer-20260815
  - hyp-e19b-pooling-recipe-level-20260815
  - e4-encoder-excursion-avgmax-20260813
  - hyp-e6-peak-maxpool-readout-20260814
  - hyp-e18-within-subject-state-20260815
  - hyp-e22-encoder-representation-affect-20260815
key_numbers:
  taskA_144tok_avg: -0.0383
  taskA_144tok_avgmax: 0.147
  taskA_23tok_avg_INCUMBENT: 0.009
  taskA_23tok_avgmax_BEST: 0.3605
  taskA_23tok_lse: 0.0374
  taskA_23tok_max: 0.0378
  taskA_control_r2_mean_only: 0.0649
  taskA_n: 4000.0
  taskA_operator_gain_at_23tok: 0.3515
  taskA_subjects: 77.0
  taskA_token_penalty_144_vs_23: -0.2135
  taskB_best_absolute_auc: 0.6051
  taskB_clock_only_auc: 0.5606
  taskB_local_minus_global_max: 0.0376
  taskB_local_minus_global_min: -0.0226
  taskB_n_pings: 4418.0
  taskB_within_subject_constant_auc: 0.7381
  taskB_within_subject_shortfall: -0.133
observed_effects:
- delta: 0.3515
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
- delta: -0.133
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: exp-e16-peri-ema-window-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: false
    eiv_peak_excursion_increment_r2: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[hyp-e17-unified-readout-operator-20260815]].
Branches from [[exp-e16-peri-ema-window-20260815]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.009 → after 0.3605  (Δ +0.3515, up ✓ predicted up)
- [[eiv_affect_multichannel_increment]]: before 0.7381 → after 0.6051  (Δ -0.133, down ✗ predicted up)

## Gates
- `gA_operator_beats_avg`: PASS
- `gA_tokens_beat_23`: **FAIL**
- `gB_local_beats_global`: **FAIL**
- `gB_beats_within_subject_constant`: **FAIL**

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `mechanism_wrong`

DO NOT READ THIS `null` AS 'nearly worked' — the run bundled two targets and they came apart completely. Task A (excursion readout) CONFIRMED enormously: at the incumbent 23-token grid the increment over the daily-mean control (0.0649) goes avg +0.0090 -> avgmax concat +0.3605, a +0.3515 operator gain, SEVEN times the preregistered +0.05 bar, with window, token count and data all held fixed (n=4000, 77 subjects); max alone +0.0378 and lse alone +0.0374 are near-tied and far below, localising the mechanism as LEVEL AND EXTREMUM TOGETHER, and the production projector's adaptive_avg_pool2d (cross_modal.py:260) is sitting on the +0.0090 arm. Task B (affect) nulled and is what set the verdict: best absolute AUC 0.6051 across all 8 token x operator arms vs a within-subject constant of 0.7381 (shortfall -0.1330), and locality is sign-unstable (local-minus-global -0.0226..+0.0376 against MDE 0.028) — a second independent feature construction hitting E16's exact trait ceiling. Secondary and actionable: the LSM-2 144-token geometry is uniformly WORSE than 23 at every operator (avgmax 0.1470 vs 0.3605, avg -0.0383, max -0.0302, lse -0.0311; penalty -0.2135), so token-count stages should be rescoped to OPERATOR. Also: population_stats.json stores median/iqr as per-channel LISTS, so name-keyed .get() silently returns (0.0,1.0) and leaves HR normalised while code believes bpm — always denorm via canon_utils.channel_denorm with the assert_bpm tripwire.

Transfers to: [[hyp-e19-encoder-pooling-transfer-20260815]], [[hyp-e19b-pooling-recipe-level-20260815]], [[e4-encoder-excursion-avgmax-20260813]], [[hyp-e6-peak-maxpool-readout-20260814]], [[hyp-e18-within-subject-state-20260815]], [[hyp-e22-encoder-representation-affect-20260815]]

## Key numbers
- `taskA_144tok_avg`: -0.0383
- `taskA_144tok_avgmax`: 0.147
- `taskA_23tok_avg_INCUMBENT`: 0.009
- `taskA_23tok_avgmax_BEST`: 0.3605
- `taskA_23tok_lse`: 0.0374
- `taskA_23tok_max`: 0.0378
- `taskA_control_r2_mean_only`: 0.0649
- `taskA_n`: 4000
- `taskA_operator_gain_at_23tok`: 0.3515
- `taskA_subjects`: 77
- `taskA_token_penalty_144_vs_23`: -0.2135
- `taskB_best_absolute_auc`: 0.6051
- `taskB_clock_only_auc`: 0.5606
- `taskB_local_minus_global_max`: 0.0376
- `taskB_local_minus_global_min`: -0.0226
- `taskB_n_pings`: 4418
- `taskB_within_subject_constant_auc`: 0.7381
- `taskB_within_subject_shortfall`: -0.133

## Notes
SPLIT VERDICT: the unified mechanism is HALF RIGHT, and the half that is right is the biggest encoder-side lever this project has measured.

TASK A — CONFIRMED, LARGE. Average pooling was destroying the excursion signal almost completely. At the incumbent 23-token grid, increment over the daily-mean control: avg +0.0090 -> avgmax concat +0.3605. That is +0.3515, seven times the preregistered +0.05 bar, from changing ONE operator with the window, the token count and the data all held fixed. max alone (+0.0378) and lse alone (+0.0374) are near-tied and far below avgmax, which localises the mechanism precisely: it is not the extremum, it is LEVEL AND EXTREMUM TOGETHER. The incumbent projector does adaptive_avg_pool2d across patches (cross_modal.py:260), so the production path is sitting on the +0.0090 arm.

TASK A SECONDARY — THE TOKEN-COUNT STORY IS REFUTED, IN REVERSE. 144 tokens (LSM-2 geometry) are uniformly WORSE than 23 at every operator: avgmax 0.1470 vs 0.3605, avg -0.0383 vs +0.0090, max -0.0302 vs +0.0378, lse -0.0311 vs +0.0374. At fixed n=4000 with subject-grouped folds this reads as a variance/dimensionality cost, not an information deficit. The LSM-2-geometry motivation behind rb-w3 is not supported on the CPU information bound; W3's GPU stage should be dropped or rescoped from TOKEN COUNT to OPERATOR.

TASK B — THE STATE CEILING IS CONFIRMED BY AN INDEPENDENT ROUTE. Best absolute AUC across all 8 (token x operator) arms is 0.6051 against a within-subject constant of 0.7381 — 0.1330 BELOW a per-user constant using zero physiology and zero clock. E16 reached the same ceiling with peri-ping summary statistics; E17 reaches it with patch-token pooling. Two independent feature constructions, same wall. Locality is sign-unstable (local-minus-global: four arms negative, four positive, range -0.0226 to +0.0376 against MDE 0.028), so 'let the label read its own neighbourhood' is not a lever either. E16 confounded 'shorter window' with 'read locally'; both now measure negative separately.

WHAT THIS MEANS ARCHITECTURALLY. The unified account predicted one operator change would move BOTH tasks. It moved exactly one. Averaging destroys extrema (Task A, huge) but locality was never the state bottleneck (Task B, null) — because cross-subject affect variance is trait, and no aggregation of physiological tokens can manufacture between-subject signal that is not there. The correct reading is that the MIL/attention-pooling framing is right about the OPERATOR and wrong about the TARGET.

NEXT, IN PRIORITY ORDER: (1) change the projector's cross-patch pooling from avg to avg+max concat and re-measure end-to-end — this is now the best-evidenced encoder change available, and it is a code change, not a data change; (2) drop or rescope rb-w3's token-count stage; (3) state work goes N-of-1 only (rb-w2), where the 0.7381 constant is absorbed by construction.

INSTRUMENT WORK DONE HERE: found that population_stats.json stores median/iqr as per-channel LISTS, not a name-keyed dict — a name lookup silently yields (0.0,1.0) and leaves values normalised while the code believes they are bpm, which zeroed the entire Task-A pool against a bpm threshold. Fixed with scratch/canon_utils.py (channel_denorm + an assert_bpm tripwire). Re-verified E16 against the same bug: |delta difference| 0.00060435, 46x below E16's MDE of 0.028, because per-feature affine rescaling preserves ranks and hence AUC. E16 stands.

#pooling #epoch-resolution #cross-modal #control #wearables #baseline #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
