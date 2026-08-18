---
artifacts: []
closed: true
config_note: 'Runner: scratch/e19b_pooling_across_checkpoints.py. GPU RTX 5070. Corpus
  git_sha e849eb7. Seed 0.


  Recipe-level replication of exp-e19-encoder-pooling-20260815, which measured the
  avg->max pooling gap on the SINGLE v5d checkpoint. Iterates every Phase-B checkpoint
  on disk {v5d, v5c, v4w, v4c, 12ch_v3, 12ch_v2}, each instantiated from its OWN encoder_config,
  with strict weight loading (checkpoint skipped if any key missing).


  Held identical across checkpoints: pool (n=4000, lifesnaps+pmdata, HR coverage>0.9,
  mean>20 bpm, peak>=40, seed 0), subject-grouped GroupKFold(5), RidgeCV readout,
  control R2(excursion | true daily mean)=+0.0649. Only the checkpoint varies.


  Adds the SHUFFLE CONTROL that E19 lacked: avg and max readouts refit on row-shuffled
  targets (rng 123).


  NOTE: an earlier attempt was interrupted by a gateway restart after 5 of 6 checkpoints
  and never wrote its JSON; those numbers are not citable and this is the clean re-run.'
crash_reason: ''
created_at: '2026-08-14T22:58:13+00:00'
diagnostics_measured:
- after: 0.4059
  before: 0.0
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: logs/e19b_pooling_across_checkpoints.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_gap_reproduces_on_every_checkpoint
  passed: true
- evidence: ''
  gate_id: g2_shuffle_control_is_dead
  passed: true
- evidence: ''
  gate_id: g3_report_gap_band
  passed: true
git_ref: ''
hypothesis_id: hyp-e19b-pooling-recipe-level-20260815
id: exp-e19b-pooling-recipe-level-20260815
key_numbers:
  avg_arm_band_width: 0.0366
  effect_to_band_ratio: 8.9
  gap_12ch_v3: 0.442
  gap_band_width: 0.0457
  gap_max: 0.442
  gap_median: 0.4059
  gap_min: 0.3963
  gap_v4c: 0.3998
  gap_v4w: 0.4059
  gap_v5c: 0.4142
  gap_v5d: 0.3963
  max_arm_band_width: 0.0186
  n: 4000.0
  n_checkpoints_tested: 5.0
  n_subjects: 77.0
  r2_control_mean_only: 0.0649
  shuffled_r2_max_abs: 0.0018
observed_effects:
- delta: 0.4059
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e19-encoder-pooling-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_peak_excursion_increment_r2: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-e19b-pooling-recipe-level-20260815]].
Branches from [[exp-e19-encoder-pooling-20260815]].
External run: logs/e19b_pooling_across_checkpoints.json

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.0 → after 0.4059  (Δ +0.4059, up ✓ predicted up)

## Gates
- `g1_gap_reproduces_on_every_checkpoint`: PASS
- `g2_shuffle_control_is_dead`: PASS
- `g3_report_gap_band`: PASS

**Verdict:** supports

## Key numbers
- `avg_arm_band_width`: 0.0366
- `effect_to_band_ratio`: 8.9
- `gap_12ch_v3`: 0.442
- `gap_band_width`: 0.0457
- `gap_max`: 0.442
- `gap_median`: 0.4059
- `gap_min`: 0.3963
- `gap_v4c`: 0.3998
- `gap_v4w`: 0.4059
- `gap_v5c`: 0.4142
- `gap_v5d`: 0.3963
- `max_arm_band_width`: 0.0186
- `n`: 4000
- `n_checkpoints_tested`: 5
- `n_subjects`: 77
- `r2_control_mean_only`: 0.0649
- `shuffled_r2_max_abs`: 0.0018

## Notes
CONFIRMED AT RECIPE LEVEL. The E19 pooling gap is a property of the architecture + data law, not of the v5d basin, and it is not a readout artifact.

gap(max - avg) on eiv_peak_excursion_increment_r2, identical rows/split/control, only the checkpoint varying:
  v5d +0.3963 | v5c +0.4142 | v4w +0.4059 | v4c +0.3998 | 12ch_v3 +0.4420
  min +0.3963, median +0.4059, max +0.4420 -> BAND WIDTH 0.0457

THE BAND IS 8.9x SMALLER THAN THE EFFECT. That is the inverse of this project's usual predicament — the Phase-C seed band of 11.37 bpm once exceeded every effect anyone wanted to claim, and the 0.0781 p4 hr_acc band retired a whole family of single-seed conclusions. Here the noise floor is comfortably below the signal, on checkpoints spanning different training runs, different basins and different dataset allowlists (v4/v5 *_wl chain plus the older 12ch_v3 line).

THE SHUFFLE CONTROL IS DEAD: refitting the identical avg and max readouts on row-shuffled excursion targets scores -0.0001 to +0.0018 across all 10 fits. This eliminates the serious alternative explanation — that max-pooling yields a higher-variance feature set from which a high-dimensional RidgeCV manufactures apparent skill. It does not. E19 lacked this control; it now exists.

BOTH ARMS ARE INDIVIDUALLY STABLE: avg +0.2441..+0.2807 (band 0.0366), max +0.6674..+0.6860 (band 0.0186). Neither arm is the noisy one; the gap between them is the reproducible quantity.

CONSEQUENCE: rb-w4-projector-maxpool-end-to-end-20260815 is JUSTIFIED. The +0.40 figure may now be cited as a recipe-level property with a measured 0.0457 band, not as a single-checkpoint observation.

SCOPE LIMIT, STATED NOT HIDDEN. (1) 12ch_v2 was SKIPPED, not measured: its legacy geometry (patch_len=8, stride=4) gives 359 tokens over the 1440-min grid, ~24.7 GiB for the token tensor, and it OOM-killed this script twice before the cause was diagnosed. A MAX_TOKEN_GIB guard now skips loudly and records the reason rather than dying, so the table can never silently omit a checkpoint. The claim is therefore verified on 5 of 6, all sharing the 23-token geometry; the gap at 359 tokens is UNTESTED. Note E17 already measured 144 tokens as WORSE than 23 at every operator, so high-token geometries are not the recommended direction regardless. (2) This remains a LINEAR-READOUT PROBE on pooled tokens — it does not establish that the frozen LM will USE the signal at generation (the exp-e31 distinction). That is precisely what rb-w4 must measure end-to-end, on deployment metrics, across >=3 seeds.

#pooling #cross-modal #control #baseline #epoch-resolution #dataset-whitelist #wearables #optihealth-eiv #matched-baseline #run-to-run-variance
