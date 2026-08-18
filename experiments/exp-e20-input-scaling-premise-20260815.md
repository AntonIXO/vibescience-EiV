---
artifacts: []
closed: true
config_note: 'Runner: scratch/e20_input_scaling_premise.py -> logs/e20_input_scaling_premise.json.
  Pure CPU, ~1 min, no GPU, no model. Corpus git_sha e849eb7, normalization mode=population,
  n=3000 windows sampled (seed 0) of 11318.


  PREMISE CHECK ONLY. The hypothesis `unnormalised-encoder-input-not-data-8ef2be`
  (proposed 2026-08-08, never run) rests on a specific ARITHMETIC claim about per-channel
  variance shares. That claim is checkable in one CPU minute against the live corpus,
  so it was checked before spending the ~15 GPU-minutes the hypothesis asked for.


  Measures per-channel first-layer pre-activation variance two ways (present-step
  only, and as-fed including the 0.0 fill), the z-position of the 0.0 fill in both
  stored and raw units, and the max/min channel-std ratio LayerNorm would equalise.'
cost: null
crash_reason: ''
created_at: '2026-08-15T06:56:22+00:00'
diagnostics_measured:
- after: 0.1173
  before: 0.9607
  diagnostic_id: eiv_nonhr_channel_grouped_r2
do_not_retry:
- use_layernorm / input-normalisation arms motivated by the '96% of first-layer variance
  is hr_mean' claim. That claim was measured on a PRE-population-normalisation corpus
  and is 8.2x wrong on git_sha e849eb7 (measured 11.73%). Do not spend GPU on LayerNorm
  to fix a channel-scale imbalance that the merge-time population normalisation already
  fixed.
- Citing 'the 0.0 fill sits at -4.18 SD for hr_mean' as motivation for anything. True
  in RAW BPM, but the encoder is fed POPULATION-NORMALISED values where the fill sits
  at -0.028 SD (the median). Always state which unit space a fill-position claim is
  in.
external_run: logs/e20_input_scaling_premise.json
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: unnormalised-encoder-input-not-data-8ef2be
id: exp-e20-input-scaling-premise-20260815
interpretation:
  at: '2026-08-18T21:08:40+00:00'
  failure_mode: claim_false
  lesson: 'Input-channel scale is NOT the encoder''s problem on corpus git_sha e849eb7:
    the hypothesis'' decisive premise (hr_mean owns 96.07% of first-layer pre-activation
    variance on present steps, 99.77% as-fed, 7.7x std dominance) measures 11.73%
    / 13.47% / 4.4x at n=3000 seed 0 — off by 8.2x and 7.4x — and hr_mean is not even
    the largest channel (the four circadian encodings tie at ~16.5% as-fed). Merge-time
    population normalisation already flattened the physiological channels (hrv_rmssd
    13.37%, respiratory_rate 12.03%, hr_mean 11.73%, skin_temperature 11.64%), so
    do not fund use_layernorm/input-normalisation arms on this motivation; the ''0.0
    fill sits at -4.18 SD'' argument is true only in RAW BPM (measured -3.994) and
    vanishes in the stored units the encoder actually eats (-0.028 SD, i.e. the median).
    The surviving explanation for the bad non-HR channel readout (eiv_nonhr_channel_grouped_r2
    respiratory_rate -0.0757, sleep_prob_deep -0.1847) is PRESENCE/coverage, not conditioning
    — respiratory_rate is 7.3% present and hrv_rmssd 19.3% — so re-aim at supply,
    and always re-measure a premise against the current corpus git_sha before spending
    GPU (this cost one CPU minute and saved ~15 GPU-minutes).'
  transfers_to:
  - h5-the-shortcut-is-the-coverage-2aaaa0
  - hyp-affect-multichannel-supply-20260814
key_numbers:
  hr_share_as_fed_claimed: 0.9977
  hr_share_as_fed_measured: 0.1347
  hr_share_present_claimed: 0.9607
  hr_share_present_measured: 0.1173
  largest_channel_share_as_fed: 0.1663
  n_sampled: 3000.0
  std_ratio_claimed: 7.7
  std_ratio_measured: 4.4
  z_fill_hr_raw_bpm: -3.994
  z_fill_hr_stored_units: -0.028
observed_effects:
- delta: -0.8434
  diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: down
parent_experiment_id: exp-e19b-pooling-recipe-level-20260815
prediction_match:
  overall: false
  per_diagnostic:
    eiv_heldout_hr_r2: false
    eiv_nonhr_channel_grouped_r2: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[unnormalised-encoder-input-not-data-8ef2be]].
Branches from [[exp-e19b-pooling-recipe-level-20260815]].
External run: logs/e20_input_scaling_premise.json

## Measurements
- [[eiv_nonhr_channel_grouped_r2]]: before 0.9607 → after 0.1173  (Δ -0.8434, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

Input-channel scale is NOT the encoder's problem on corpus git_sha e849eb7: the hypothesis' decisive premise (hr_mean owns 96.07% of first-layer pre-activation variance on present steps, 99.77% as-fed, 7.7x std dominance) measures 11.73% / 13.47% / 4.4x at n=3000 seed 0 — off by 8.2x and 7.4x — and hr_mean is not even the largest channel (the four circadian encodings tie at ~16.5% as-fed). Merge-time population normalisation already flattened the physiological channels (hrv_rmssd 13.37%, respiratory_rate 12.03%, hr_mean 11.73%, skin_temperature 11.64%), so do not fund use_layernorm/input-normalisation arms on this motivation; the '0.0 fill sits at -4.18 SD' argument is true only in RAW BPM (measured -3.994) and vanishes in the stored units the encoder actually eats (-0.028 SD, i.e. the median). The surviving explanation for the bad non-HR channel readout (eiv_nonhr_channel_grouped_r2 respiratory_rate -0.0757, sleep_prob_deep -0.1847) is PRESENCE/coverage, not conditioning — respiratory_rate is 7.3% present and hrv_rmssd 19.3% — so re-aim at supply, and always re-measure a premise against the current corpus git_sha before spending GPU (this cost one CPU minute and saved ~15 GPU-minutes).

Transfers to: [[h5-the-shortcut-is-the-coverage-2aaaa0]], [[hyp-affect-multichannel-supply-20260814]]

## Key numbers
- `hr_share_as_fed_claimed`: 0.9977
- `hr_share_as_fed_measured`: 0.1347
- `hr_share_present_claimed`: 0.9607
- `hr_share_present_measured`: 0.1173
- `largest_channel_share_as_fed`: 0.1663
- `n_sampled`: 3000
- `std_ratio_claimed`: 7.7
- `std_ratio_measured`: 4.4
- `z_fill_hr_raw_bpm`: -3.994
- `z_fill_hr_stored_units`: -0.028

## Do not retry
- use_layernorm / input-normalisation arms motivated by the '96% of first-layer variance is hr_mean' claim. That claim was measured on a PRE-population-normalisation corpus and is 8.2x wrong on git_sha e849eb7 (measured 11.73%). Do not spend GPU on LayerNorm to fix a channel-scale imbalance that the merge-time population normalisation already fixed.
- Citing 'the 0.0 fill sits at -4.18 SD for hr_mean' as motivation for anything. True in RAW BPM, but the encoder is fed POPULATION-NORMALISED values where the fill sits at -0.028 SD (the median). Always state which unit space a fill-position claim is in.

## Notes
PREMISE REFUTED ON THE LIVE CORPUS — the hypothesis was written against a corpus that no longer exists, and one CPU minute saved the ~15 GPU-minutes it asked for.

THE CLAIM: hr_mean takes 96.07% of first-layer pre-activation variance on present steps, 99.77% as-fed, with a 7.7x std / 59x variance dominance, because FlexMLP has no input normalisation and use_layernorm is False.
THE MEASUREMENT (git_sha e849eb7, n=3000, seed 0): hr_mean takes 11.73% present-step and 13.47% as-fed. Off by 8.2x and 7.4x. hr_mean is NOT the largest channel on either accounting.

Present-step shares are remarkably FLAT across the physiological channels: hrv_rmssd 13.37%, respiratory_rate 12.03%, hr_mean 11.73%, skin_temperature 11.64% — which is exactly what merge-time population normalisation (median/IQR per channel) is designed to produce. As-fed, the four circadian encodings dominate at ~16.5% each, simply because they are ~99% present while physiological channels run 7-66% present.

THE HYPOTHESIS REFUTES ITSELF ON ITS OWN DECISIVE NUMBER. It claims the 0.0 fill sits at -4.18 SD for hr_mean, and states that 'under population norm the same fill 0.0 IS the median'. Measured: -3.994 SD in RAW BPM (so the arithmetic was right) but -0.028 SD in STORED units (essentially the median). The corpus IS population-normalised — merge_manifest normalization.mode == 'population'. The condition the hypothesis names as the fix was already in force when it was written; it was reasoning about raw bpm while the encoder was already being fed normalised values.

WHAT LAYERNORM WOULD ACTUALLY DO: equalise a 4.4x std ratio (19x variance), not 7.7x/59x, and the largest channel is day_of_week_sin — a synthetic circadian encoding whose scale is by construction. There is no physiological-channel scale pathology left to fix.

THE REAL QUESTION THIS LEAVES OPEN, AND IT IS DIFFERENT. eiv_nonhr_channel_grouped_r2 still records respiratory_rate -0.0757 and sleep_prob_deep -0.1847 (worse than their own mean) off the same encoder. This experiment shows input SCALING is not the explanation. The surviving candidate is PRESENCE: respiratory_rate is 7.3% present and hrv_rmssd 19.3%, so those channels are absent from the overwhelming majority of cells and the encoder has little to read regardless of scaling. That is a supply/coverage question, not a conditioning one, and it needs its own hypothesis rather than a LayerNorm flag.

SCOPE: this refutes the VARIANCE-DOMINANCE PREMISE and the LayerNorm intervention motivated by it. It does NOT test whether use_layernorm=True helps for some other reason (e.g. optimisation stability), and it does not speak to the two rule-outs the hypothesis speculated might be conditional on input scaling — ro-sensor-slot-granularity-20260805 and ro-window-shortening-20260805 remain as recorded, and the correspondence it noticed was indeed coincidence.
