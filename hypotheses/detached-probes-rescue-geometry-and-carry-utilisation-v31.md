---
ambition_class: mechanism
created_at: '2026-08-31T12:03:41+00:00'
former_ids: []
gap_closure_estimate: 0.6
gates:
- blocking: true
  description: 'g0 BLOCKING (auto-enforced by the driver, exit 42): v31detached phase_a
    eff_rank >= 7.0. This is the discriminating cell: the identical ATTACHED supervision
    (v29all01) scored 6.783. Anchors: healthy 8.35, v28affect01 8.599, no-heads 7.44,
    v19 5.19, carrier-collapse 4.47.'
  diagnostic_id: eiv_effective_rank
  id: g0-detach-rescues-geometry
  mde: 1.0
- blocking: true
  description: 'g1 BLOCKING (the goal''s letter): supabase hr_raw MAE <= 2.836 AND
    corr >= 0.60 on the 200-row deployment draw. Comparator v28affect01: 3.030 / 0.793.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g1-beats-baseline-letter
  mde: 0.8
- blocking: true
  description: 'g2 BLOCKING (no-harm): dense 128-row hr_raw MAE inside v28''s band
    [2.23, 3.83]. An arm that wins the sparse supabase draw while destroying the dense
    draw is a calibration artefact, not a better encoder.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-no-harm-dense
  mde: 0.4
id: detached-probes-rescue-geometry-and-carry-utilisation-v31
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'If the heads'' gradient was the only thing poisoning geometry,
    detaching should recover v28-like or better calibration while adding diary/context
    signal. Prior: g0 ~65% (mechanism is direct), g1 ~35% (the 2.836 letter is 0.19
    below v28 and label heads'' marginal deployment value is still unproven beyond
    affect).'
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Expected back above 7.0, plausibly toward v28's 8.599, since the
    contrastive term alone then shapes the representation.
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- deployment-distribution
- sensor-text
- contrastive
- control
- baseline
- wearables
- epoch-resolution
---

## Statement
DETACHED PROBES (architectural, not dose): making every auxiliary head read fused.detach() in Phase A removes the label-heads' ability to FORMAT the shared representation while they still learn to PREDICT their labels. On the exact v29all01 supervision set (diary 0.1 + context 0.1 + affect 0.1 + statemask) this restores Phase-A geometry above the 7.0 gate, AND the resulting chain (B@30 -> band04 C) beats the baseline letter on supabase (hr_raw MAE <= 2.836, corr >= 0.60) without harming the dense draw (MAE inside v28's [2.23, 3.83]).

## Rationale
exp-v29-all01-chain-20260831 refuted the dose account: the same three heads at 0.1 cost -1.816 eff_rank against affect-0.1-alone (6.783 vs 8.599), WORSE than the -1.50 exp-v22 measured at 0.3. Lowering the weight is therefore exhausted as a lever, and v29's own interpretation named detached probes as the next move. The mechanism is a stop-gradient at the probe input (src/training/pipeline.py:2434, probe_input = fused.detach() if detached_probes else fused), leaving the contrastive objective as the only thing shaping geometry. One-variable arm: dict-diff vs phase_a_12ch_v29all01 is exactly {detached_probes}, verified by scratch/_verify_v31_diff.py, with tests/test_detached_probes_guard.py asserting the gradient is actually severed.

## Plan
phase_a_12ch_v31detached (= v29all01 + detached_probes) -> geometry gate (exit 42 below 7.0) -> phase_b_12ch_v25bigbatch @30 -> lora_proxy_12ch_wl_band04 -> supabase 200-row + dense 128-row capture. Runner scratch/run_v31_detached.sh. Comparators: v28affect01 (supa 3.030 / corr 0.793; dense 2.851 / corr 0.937), v29all01 (eff_rank 6.783, no adapter).

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_effective_rank]] (up).
Gate `g0-detach-rescues-geometry` (blocking): g0 BLOCKING (auto-enforced by the driver, exit 42): v31detached phase_a eff_rank >= 7.0. This is the discriminating cell: the identical ATTACHED supervision (v29all01) scored 6.783. Anchors: healthy 8.35, v28affect01 8.599, no-heads 7.44, v19 5.19, carrier-collapse 4.47.
Gate `g1-beats-baseline-letter` (blocking): g1 BLOCKING (the goal's letter): supabase hr_raw MAE <= 2.836 AND corr >= 0.60 on the 200-row deployment draw. Comparator v28affect01: 3.030 / 0.793.
Gate `g2-no-harm-dense` (blocking): g2 BLOCKING (no-harm): dense 128-row hr_raw MAE inside v28's band [2.23, 3.83]. An arm that wins the sparse supabase draw while destroying the dense draw is a calibration artefact, not a better encoder.

#deployment-distribution #sensor-text #contrastive #control #baseline #wearables #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
