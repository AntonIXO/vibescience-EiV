---
artifacts: []
closed: true
config_note: 'Runner: scratch/e19_encoder_pooling.py. GPU RTX 5070, ~1 min. Ran in
  parallel with the CPU N-of-1 arm exp-e18.


  Checkpoint: outputs/checkpoints/v5d/phase_b/sensor_modules/temporal_encoder.pt,
  encoder_config {input_dim 24, d_model 192, patch_len 120, stride 60, hidden_dim
  64} -> encoder output [B, 24, 23, 192], i.e. 23 tokens/channel over the 1440-min
  day. Weight load verified strict: missing=[] unexpected=[]; the script refuses to
  proceed on any missing key so a partially-initialised encoder cannot manufacture
  a result.


  Pool n=4000, 77 subjects, identical filter/seed to exp-e17 task_a so the two are
  directly comparable. Excursion mean 61.33 std 20.69 bpm. Control R2(excursion |
  true daily mean) = +0.0649, identical to E17 by construction.'
crash_reason: ''
created_at: '2026-08-14T22:28:12+00:00'
diagnostics_measured:
- after: 0.6849
  before: 0.2807
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: logs/e19_encoder_pooling.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_encoder_operator_beats_avg
  passed: true
- evidence: ''
  gate_id: g2_gap_vs_raw_patch_bound
  passed: true
git_ref: ''
hypothesis_id: hyp-e19-encoder-pooling-transfer-20260815
id: exp-e19-encoder-pooling-20260815
key_numbers:
  encoder_avg_INCUMBENT: 0.2807
  encoder_avgmax_BEST: 0.6849
  encoder_lse: 0.5116
  encoder_max_same_dim_as_avg: 0.6771
  gap_avg_to_avgmax: 0.4042
  gap_avg_to_max_dim_controlled: 0.3964
  n: 4000.0
  n_subjects: 77.0
  r2_control_mean_only: 0.0649
  raw_patch_gap_e17_for_comparison: 0.3515
observed_effects:
- delta: 0.4042
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e17-unified-readout-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_peak_excursion_increment_r2: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-e19-encoder-pooling-transfer-20260815]].
Branches from [[exp-e17-unified-readout-20260815]].
External run: logs/e19_encoder_pooling.json

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.2807 → after 0.6849  (Δ +0.4042, up ✓ predicted up)

## Gates
- `g1_encoder_operator_beats_avg`: PASS
- `g2_gap_vs_raw_patch_bound`: PASS

**Verdict:** supports

## Key numbers
- `encoder_avg_INCUMBENT`: 0.2807
- `encoder_avgmax_BEST`: 0.6849
- `encoder_lse`: 0.5116
- `encoder_max_same_dim_as_avg`: 0.6771
- `gap_avg_to_avgmax`: 0.4042
- `gap_avg_to_max_dim_controlled`: 0.3964
- `n`: 4000
- `n_subjects`: 77
- `r2_control_mean_only`: 0.0649
- `raw_patch_gap_e17_for_comparison`: 0.3515

## Notes
CONFIRMED, AND THIS IS THE ACTIONABLE FINDING OF THE WHOLE SESSION.

The E17 raw-patch result TRANSFERS to the real trained encoder, and transfers STRONGER. On the v5d Phase-B encoder's own tokens ([4000, 24, 23, 192], weights loaded strict), pooling across the patch axis — the exact axis cross_modal.py:260 pools with adaptive_avg_pool2d — gives:

  avg    +0.2807   dim 4608   <- WHAT PRODUCTION DOES TODAY
  max    +0.6771   dim 4608
  avgmax +0.6849   dim 9216   <- BEST
  lse    +0.5116   dim 4608

Gap avg -> avgmax = +0.4042, eight times the preregistered +0.05 bar, and LARGER than the +0.3515 raw-patch bound E17 measured.

THE DIMENSIONALITY CONFOUND IS CONTROLLED. The obvious alternative explanation — avgmax wins because it has twice the features — is eliminated by the max arm, which reaches +0.6771 at IDENTICAL dimensionality to avg (4608 vs 4608). Gap avg->max with dim held exactly constant is +0.3964, i.e. essentially the whole effect. This is a pooling-operator effect, not a capacity effect.

THE RISK THIS ARM EXISTED TO TEST IS MEASURED FALSE. The concern was that InfoNCE against captions made of six channel MEANS would already have destroyed within-window spread upstream (the H4b mechanism), leaving nothing for a downstream pooling change to recover. The opposite holds: the trained encoder CONCENTRATES excursion information (encoder avg +0.2807 vs raw-patch avg +0.0090 under the same operator, so the encoder is doing real work), and average pooling across patches is what discards it.

CONSEQUENCE. cross_modal.py's adaptive_avg_pool2d is destroying ~0.40 R2 of excursion information that the encoder has already computed. Changing that operator to avg+max concat (or max) is a small, targeted code change with the largest measured encoder-side effect on record for this project, and it does NOT require a corpus rebuild or a Phase-A retrain to justify testing.

SCOPE LIMIT, STATED PLAINLY. This is a linear-readout probe on pooled encoder tokens. It shows the information survives pooling choice; it does NOT establish that the LLM will USE it at generation, which is a strictly stronger claim and is exactly the distinction exp-e31 drew (signal recoverable by a probe != signal the frozen LM consumes). The next arm must therefore be an end-to-end retrain with the operator swapped, scored on deployment metrics (eiv_supabase_hr_raw_mae_bpm with its 4.02 trivial) across >=3 seeds against the measured 0.60 bpm band — not a single seed, and not on the probe alone.

Also note avgmax doubles the projector input width (4608 -> 9216 per channel-block before the slot projection), so the end-to-end arm must record eiv_peak_vram_gib against the 11.25 GiB ceiling; the max-only variant is the equal-dimension fallback if VRAM binds.

#pooling #cross-modal #epoch-resolution #control #dataset-whitelist #wearables #baseline #optihealth-eiv #matched-baseline #subject-heldout
