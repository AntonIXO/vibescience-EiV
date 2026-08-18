---
created_at: '2026-08-14T22:58:02+00:00'
former_ids: []
gates:
- blocking: true
  description: RECIPE-LEVEL REPRODUCIBILITY. The gap(max - avg) on eiv_peak_excursion_increment_r2
    must be >= +0.05 on EVERY Phase-B checkpoint tested (>=4 checkpoints), measured
    on identical rows/split/control. MDE 0.05 inherited from E19. If any checkpoint
    falls below, the effect is basin-dependent and E19's headline must be re-scoped
    from 'the pooling operator is the lever' to 'one checkpoint showed this'.
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: g1_gap_reproduces_on_every_checkpoint
  mde: 0.05
- blocking: true
  description: SHUFFLE CONTROL. Refitting the identical avg and max readouts on ROW-SHUFFLED
    excursion targets must yield |R2| < 0.05 on every checkpoint and both operators.
    This is the control E19 lacked. A max-pooled readout that scores on shuffled labels
    would mean the high-dimensional readout is manufacturing skill rather than reading
    physiology, and would invalidate the whole E19 line.
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: g2_shuffle_control_is_dead
  mde: 0.05
- blocking: false
  description: 'BAND WIDTH. Informational: report min/median/max of gap(max-avg) across
    checkpoints as the recipe-level band, so any future arm claiming to move the pooling
    effect has a measured noise floor to beat — the same discipline as eiv_phase_c_seed_band_range_bpm.'
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: g3_report_gap_band
  mde: null
id: hyp-e19b-pooling-recipe-level-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: gap(max - avg) >= +0.05 on EVERY Phase-B checkpoint tested, with
    the across-checkpoint band narrow relative to the effect. E19 measured +0.3963
    on v5d alone. Partial evidence from the interrupted first run (5 checkpoints)
    showed +0.3963 to +0.4420 with shuffle controls at ~0.000, but that run never
    wrote its JSON and is not citable — this is the clean re-run.
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags:
- optihealth-eiv
- matched-baseline
- run-to-run-variance
status: confirmed
supersedes: null
topic_tags:
- pooling
- cross-modal
- control
- baseline
- epoch-resolution
- dataset-whitelist
- wearables
---

## Statement
H-E19b (recipe-level, not checkpoint-level). The avg->max pooling gap measured in E19 is a property of the ARCHITECTURE + data law, not of the particular v5d basin. It will reproduce on every Phase-B checkpoint on disk, at a magnitude far exceeding the across-checkpoint band; and it is genuine physiological readout rather than a high-dimensional readout artifact, so the identical arms refit on shuffled targets will score ~0.

IF CONFIRMED: E19's headline is safe to act on and rb-w4 (the end-to-end projector swap) is justified.
IF REFUTED (gap swings with checkpoint, or shuffled targets score): E19 was a basin draw, the +0.4042 must be re-scoped to a single checkpoint, and rb-w4 must not be launched on its strength.

## Rationale
Anton's standing rule: a baseline is the RECIPE (code/config + data law), not the checkpoint. This project has been burned precisely here — v4w and v5c had byte-identical training manifests and measured 2.673 bpm apart on emitted hr_peak skill, so single-checkpoint claims are draws from a basin distribution. E19's +0.4042 was measured on ONE checkpoint (v5d).

Before rb-w4 spends GPU-hours retraining on the strength of that number, the cheap check is whether the gap reproduces across every Phase-B checkpoint on disk. It also adds the shuffle control E19 omitted: max-pooling produces a higher-variance feature set, and a high-dimensional ridge readout can in principle manufacture apparent skill — refitting on shuffled targets is what excludes that, and it is the same discipline the donor/shuffled nulls served in exp-e31.

## Plan
scratch/e19b_pooling_across_checkpoints.py — GPU, ~5 min.

Iterate every available Phase-B checkpoint {v5d, v5c, v4w, v4c, 12ch_v3, 12ch_v2}. For each: read encoder_config from its OWN phase_config.json, instantiate TemporalEncoder to match, load temporal_encoder.pt, and SKIP the checkpoint if any key is missing (a partially-initialised encoder would manufacture a result). Assemble input per encoder_format_version with_presence_v1 (12 channels + 12 presence flags when input_dim==24).

IDENTICAL across checkpoints: the pool (n=4000, lifesnaps+pmdata, HR coverage>0.9, mean>20 bpm, peak>=40, seed 0), the subject-grouped GroupKFold(5) split, the RidgeCV readout, and the control R2(excursion | true daily mean) = +0.0649. Only the checkpoint changes.

ARMS per checkpoint: avg, max, avgmax concat — pooled across the patch axis N, exactly where cross_modal.py:260 pools. Scored as increment over the mean-only control.

SHUFFLE CONTROL (new, absent from E19): refit avg and max on row-shuffled targets (rng 123) and report raw R2. Must be ~0.

Output: logs/e19b_pooling_across_checkpoints.json with per-checkpoint arms plus gap_min/gap_median/gap_max.

## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Gate `g1_gap_reproduces_on_every_checkpoint` (blocking): RECIPE-LEVEL REPRODUCIBILITY. The gap(max - avg) on eiv_peak_excursion_increment_r2 must be >= +0.05 on EVERY Phase-B checkpoint tested (>=4 checkpoints), measured on identical rows/split/control. MDE 0.05 inherited from E19. If any checkpoint falls below, the effect is basin-dependent and E19's headline must be re-scoped from 'the pooling operator is the lever' to 'one checkpoint showed this'.
Gate `g2_shuffle_control_is_dead` (blocking): SHUFFLE CONTROL. Refitting the identical avg and max readouts on ROW-SHUFFLED excursion targets must yield |R2| < 0.05 on every checkpoint and both operators. This is the control E19 lacked. A max-pooled readout that scores on shuffled labels would mean the high-dimensional readout is manufacturing skill rather than reading physiology, and would invalidate the whole E19 line.
Gate `g3_report_gap_band` (advisory): BAND WIDTH. Informational: report min/median/max of gap(max-avg) across checkpoints as the recipe-level band, so any future arm claiming to move the pooling effect has a measured noise floor to beat — the same discipline as eiv_phase_c_seed_band_range_bpm.

#pooling #cross-modal #control #baseline #epoch-resolution #dataset-whitelist #wearables #optihealth-eiv #matched-baseline #run-to-run-variance
