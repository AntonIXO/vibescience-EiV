---
artifacts: []
closed: true
config_note: 'E6 readout probe on FROZEN v9band04_s42 phase_c sensor modules (encoder+fusion,
  with_presence_v1, d_model 192, patch_len 120/stride 60 -> 23 patches). data/eval_v3_1min
  strict-clean (hr_cov>0.9, raw day mean>40), <=4000 samples. RidgeCV GroupKFold(5)
  by subject. Arms: avg-pool vs avg+max concat over hr-channel post-fusion patch tokens;
  control R2(peak|true mean) same folds; supply ceiling from exact raw per-patch maxima.
  Gates: g1 increment(avg+max)>=0.25 BLOCKING, g2 delta over avg >=+0.10 BLOCKING.'
cost: null
crash_reason: ''
created_at: '2026-08-14T07:58:13+00:00'
diagnostics_measured:
- after: 0.2326
  before: 0.1858
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL. increment(avgmax over hr-channel tokens) = 0.2326 < 0.25 bar. (Level
    control on this pool: r2(peak|true mean) = -0.0113, so the increment is essentially
    the raw R2.)'
  gate_id: g1-avgmax-increment
  passed: false
- evidence: FAIL. increment(avgmax) - increment(avg) = 0.2326 - 0.1858 = +0.0468 <
    +0.10 bar. The max branch adds under half the required delta on hr-channel tokens.
  gate_id: g2-delta-over-avg
  passed: false
- evidence: Measured. Raw-patch-max supply arm increment 0.4847 (MAE 11.45 vs trivial
    16.45). AND the unpreregistered all-channel avg arm reached 0.5004 (MAE 11.64)
    — above the raw-max arm — showing post-fusion supply at the encoder output is
    HIGH; the hr-channel-only view (both preregistered arms) is the wrong readout
    basis, not the encoder.
  gate_id: g3-supply-ceiling
  passed: true
git_ref: ''
hypothesis_id: hyp-e6-peak-maxpool-readout-20260814
id: exp-e6-peak-maxpool-20260814
interpretation:
  at: '2026-08-18T21:08:44+00:00'
  failure_mode: recipe_conditional
  lesson: 'HEADLINE DIFFERS FROM THE MECHANICAL VERDICT: filed directional_only because
    the direction matched, but both blocking gates FAILED and the informative result
    is an unpreregistered control that RELOCATES the problem. The preregistered claim
    (a max-pool branch over HR-CHANNEL post-fusion patch tokens rescues the peak)
    is dead on its own bars: increment(avgmax) = 0.2326 < the 0.25 bar, and the delta
    over avg is +0.0468 (0.2326 - 0.1858) < the +0.10 bar, on frozen v9band04_s42
    phase_c sensor modules, RidgeCV GroupKFold(5) by subject, with the level control
    r2(peak|true mean) = -0.0113 so the increment is essentially the raw R2. The g3
    supply control PASSED and is the reason to keep this record: the ALL-CHANNEL token
    average reaches increment 0.5004 (MAE 11.64 vs trivial 16.45), EXCEEDING even
    the exact raw-per-patch-max supply arm at 0.4847 (MAE 11.45), so the ChannelFusionTransformer
    DISTRIBUTES peak information across channel tokens and the hr-channel-only readout
    basis undercounts encoder supply by ~2.7x - the wrong basis, not a weak encoder.
    Since encoder+fusion demonstrably carries ~0.50 of excursion variance beyond level,
    the loss to generation must occur at or after the projector pooling (-> 96 sensor_slots)
    or in the LM readout; probe the PROJECTOR OUTPUT next. Also recalibrate history:
    the avg arm 0.1858 vs the old 15-min reference 0.0030 means the 1-min migration
    alone improved this readout ~60x, so 15-min-grid numbers must never be cited as
    current.'
  transfers_to:
  - hyp-e7-projector-bottleneck-20260814
  - hyp-e17-unified-readout-operator-20260815
  - hyp-e19-encoder-pooling-transfer-20260815
  - hyp-e19b-pooling-recipe-level-20260815
  - e4-encoder-excursion-avgmax-20260813
key_numbers: {}
observed_effects:
- delta: 0.0468
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e5-affect-supply-20260814
prediction_match:
  overall: true
  per_diagnostic:
    eiv_peak_excursion_increment_r2: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-e6-peak-maxpool-readout-20260814]].
Branches from [[exp-e5-affect-supply-20260814]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.1858 → after 0.2326  (Δ +0.0468, up ✓ predicted up)

## Gates
- `g1-avgmax-increment`: **FAIL** — FAIL. increment(avgmax over hr-channel tokens) = 0.2326 < 0.25 bar. (Level control on this pool: r2(peak|true mean) = -0.0113, so the increment is essentially the raw R2.)
- `g2-delta-over-avg`: **FAIL** — FAIL. increment(avgmax) - increment(avg) = 0.2326 - 0.1858 = +0.0468 < +0.10 bar. The max branch adds under half the required delta on hr-channel tokens.
- `g3-supply-ceiling`: PASS — Measured. Raw-patch-max supply arm increment 0.4847 (MAE 11.45 vs trivial 16.45). AND the unpreregistered all-channel avg arm reached 0.5004 (MAE 11.64) — above the raw-max arm — showing post-fusion supply at the encoder output is HIGH; the hr-channel-only view (both preregistered arms) is the wrong readout basis, not the encoder.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

HEADLINE DIFFERS FROM THE MECHANICAL VERDICT: filed directional_only because the direction matched, but both blocking gates FAILED and the informative result is an unpreregistered control that RELOCATES the problem. The preregistered claim (a max-pool branch over HR-CHANNEL post-fusion patch tokens rescues the peak) is dead on its own bars: increment(avgmax) = 0.2326 < the 0.25 bar, and the delta over avg is +0.0468 (0.2326 - 0.1858) < the +0.10 bar, on frozen v9band04_s42 phase_c sensor modules, RidgeCV GroupKFold(5) by subject, with the level control r2(peak|true mean) = -0.0113 so the increment is essentially the raw R2. The g3 supply control PASSED and is the reason to keep this record: the ALL-CHANNEL token average reaches increment 0.5004 (MAE 11.64 vs trivial 16.45), EXCEEDING even the exact raw-per-patch-max supply arm at 0.4847 (MAE 11.45), so the ChannelFusionTransformer DISTRIBUTES peak information across channel tokens and the hr-channel-only readout basis undercounts encoder supply by ~2.7x - the wrong basis, not a weak encoder. Since encoder+fusion demonstrably carries ~0.50 of excursion variance beyond level, the loss to generation must occur at or after the projector pooling (-> 96 sensor_slots) or in the LM readout; probe the PROJECTOR OUTPUT next. Also recalibrate history: the avg arm 0.1858 vs the old 15-min reference 0.0030 means the 1-min migration alone improved this readout ~60x, so 15-min-grid numbers must never be cited as current.

Transfers to: [[hyp-e7-projector-bottleneck-20260814]], [[hyp-e17-unified-readout-operator-20260815]], [[hyp-e19-encoder-pooling-transfer-20260815]], [[hyp-e19b-pooling-recipe-level-20260815]], [[e4-encoder-excursion-avgmax-20260813]]

## Notes
REFUTED on its own gates — but the informative half is the g3 control, which relocates the problem. Preregistered claim (max-pool branch on HR-CHANNEL tokens rescues the peak) fails: increment(avgmax) 0.2326 < 0.25 and delta over avg +0.0468 < +0.10. The prediction's magnitude range (0.15-0.45) technically contained 0.2326, but the preregistered blocking bars decide, and both fail. WHAT THE CONTROLS SHOWED INSTEAD (unpreregistered, needs its own confirmation before any claim): (1) the ALL-CHANNEL token average scores increment 0.5004 (MAE 11.64 vs trivial 16.45), EXCEEDING the exact raw-per-patch-max supply arm (0.4847, MAE 11.45); (2) so post-fusion, peak information is NOT concentrated in the hr-channel token stack — the ChannelFusionTransformer distributes it across channel tokens; the hr-only readout basis undercounts encoder supply ~2.7x. IMPLICATION CHAIN: the encoder+fusion DOES carry substantial peak excursion (0.50 of variance beyond level, on a pool where level predicts nothing, r2(peak|mean) = -0.0113); the loss to generation must therefore occur at or after the projector pooling (pool -> 96 sensor_slots) or in the LM readout — consistent with the old 15-min finding that the learned projector stage was the suspect, now reproduced at 1-min at the readout level. NEXT (E7): same probe on the PROJECTOR OUTPUT (soft tokens, pre- and post-_masked_mean_soft_tokens) to localize whether the 0.50 supply survives the projector; that decides projector-surgery vs LM-side levers. Note the avg arm 0.1858 vs old 15-min reference 0.0030: the 1-min migration alone already improved the hr-channel avg readout ~60x; historical references from the 15-min grid must not be cited as current.

#cross-modal #pooling #epoch-resolution #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
