---
artifacts: []
closed: true
config_note: 'Executes H-W7''s OWN blocking, CPU-only Stage-0 gate (g0-synthetic-crop-power-check)
  before any GPU is spent, exactly as the hypothesis requires. Script scratch/w7_stage0_crop_power.py,
  readings P1-P3 committed PRE-DATA.


  DESIGN. PAIRED by construction, satisfying g1-paired-crop-eval: the SAME dense day
  is read at full extent and at each crop length, so subject and day are held constant
  and only extent varies. Donor pool = lifesnaps/pmdata with HR coverage >=0.75, n=1200
  windows over 81 subjects. Crops are placed to START on a measured minute so a crop
  is never all-fill. Target is the CROPPED span''s own present-only mean HR -- scoring
  a crop against the full day''s mean would measure clairvoyance rather than de-dilution.
  Readout: subject-grouped GroupKFold(5) RidgeCV on production-stage features (encoder
  -> channel_fusion -> projector, RP to 48/ch), identical to W5/W6.


  BOTH POOLING MODES RUN, and that is the point. H-W6 confirmed the day before (exp-w6b)
  that presence-normalised pooling already removes much of the extent penalty, so
  testing crop augmentation against `avg` alone would have measured a problem that
  is partly fixed. Identical crop placement across modes (rng reseeded per cell).


  METRIC CORRECTION MADE MID-RUN AND DECLARED. The first pass scored `skill = trivial
  - MAE` and produced retention of 1.915 at 41 minutes -- i.e. above 1.0, which cannot
  mean ''no loss''. Cause: the target CHANGES with the crop (each crop''s own present-mean)
  and short crops have a far more dispersed target distribution (trivial 12.74 bpm
  at 41min vs 6.42 at full extent), so a crop posts larger raw skill purely from larger
  target spread. Switched to the scale-free FRACTION OF TARGET SPREAD EXPLAINED (1
  - MAE/trivial, in [0,1]); retention is that fraction over its full-extent value.
  Both quantities are stored in the log. The gate reads the scale-free one.'
cost: null
crash_reason: ''
created_at: '2026-08-21T04:19:49+00:00'
diagnostics_measured:
- after: 0.964
  before: 1.0
  diagnostic_id: eiv_extent_variance_compression_ratio
do_not_retry: []
external_run: logs/w7_stage0_crop_power.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g0-synthetic-crop-power-check
  passed: false
- evidence: ''
  gate_id: g1-paired-crop-eval
  passed: true
- evidence: ''
  gate_id: g2-target-rebuild-verified
  passed: true
- evidence: ''
  gate_id: g3-dense-no-harm
  passed: false
- evidence: ''
  gate_id: g4-shortcut-decodability
  passed: false
git_ref: ''
hypothesis_id: h-w7-extent-crop-augmentation-teaches-de-7bc554
id: exp-w7-stage0-crop-power-20260821
interpretation:
  at: '2026-08-21T04:20:57+00:00'
  failure_mode: claim_false
  lesson: "THE ENCODER READS A PARTIAL WINDOW ALMOST AS WELL AS A WHOLE ONE, so crop
    augmentation has no headroom to buy and H-W7 is not funded. Paired on 1200 dense
    days / 81 subjects, scale-free fraction of target spread explained, each crop
    scored against ITS OWN present-mean:\n  avg         full 0.9337 -> 41min 0.9002
    (retention 0.964)\n  masked_avg  full 0.9290 -> 41min 0.8825 (retention 0.950)\nThe
    gate demanded >=20% loss; measured 3.6% and 5.0%. Monotone in crop length in both
    arms, control exact at 1.000.\n\nTHE DISTINCTION THIS RUN BUYS, and it is the
    reason the extent problem does not collapse with it: there are TWO separable defects
    and only one exists. (a) REPRESENTATION CONTRACTION -- presence-blind pooling
    maps a short session onto the corpus median, measured at 2.85% of CASE's between-subject
    spread (W4b), confirmed to survive into the encoder (W5), and fixed end-to-end
    (W6, 0.5630 -> 0.6897). REAL. (b) READOUT FRAGILITY -- the encoder having hardcoded
    a per-provenance expected coverage and therefore failing on partial windows, which
    is what crop augmentation would teach it out of. NOT REAL, measured here. H-W7
    assumed (b) followed from (a). It does not. A representation can be badly scaled
    and still be perfectly readable once the scale is corrected.\n\nMETHOD LESSON,
    and it nearly cost the run: my first metric was `skill = trivial_MAE - model_MAE`,
    which returned retention of 1.915 -- above 1.0, i.e. crops apparently BETTER than
    full days. That is impossible as stated and was the tell. Cause: the target changes
    with the crop, and short crops have a much wider target distribution (trivial
    12.74 bpm at 41min vs 6.42 at full), so raw skill grows with target spread rather
    than with reading ability. Any across-condition comparison whose TARGET DISTRIBUTION
    also changes must use a scale-free score (here 1 - MAE/trivial); a difference-of-MAEs
    silently rewards the harder-looking condition. Same family of error as the earlier
    ratio>1.0 catch in W5 -- when a bounded-looking quantity exceeds its bound, stop
    and find the confound rather than reporting it.\n\nSECOND-ORDER FINDING WORTH
    KEEPING: masked_avg retains slightly LESS than avg at 41 minutes (0.950 vs 0.964).
    Not a contradiction of W6 -- W6's win is on between-subject SPREAD (the scale),
    while this measures within-condition READABILITY (the ranking), and the masked
    pool concentrates on fewer real patches so its estimate is noisier on very short
    spans. The two metrics are not substitutes, and anyone comparing pooling modes
    should say which axis they mean.\n\nSCOPE, so this rule-out is not over-cited:
    HR-mean readout only, frozen encoder only, no state/affect or hr_peak target,
    and g3/g4 UNMEASURED because no augmented model was trained. It joins rather than
    replaces the E11 prior against synthetic-coverage domain matching. Cost: ~4 minutes
    instead of ~6 GPU-hours."
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: -0.036
  diagnostic_id: eiv_extent_variance_compression_ratio
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: false
    eiv_extent_variance_compression_ratio: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h-w7-extent-crop-augmentation-teaches-de-7bc554]].
External run: logs/w7_stage0_crop_power.json

## Measurements
- [[eiv_extent_variance_compression_ratio]]: before 1.0 → after 0.964  (Δ -0.036, down ✗ predicted up)

## Gates
- `g0-synthetic-crop-power-check`: **FAIL**
- `g1-paired-crop-eval`: PASS
- `g2-target-rebuild-verified`: PASS
- `g3-dense-no-harm`: **FAIL**
- `g4-shortcut-decodability`: **FAIL**

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

THE ENCODER READS A PARTIAL WINDOW ALMOST AS WELL AS A WHOLE ONE, so crop augmentation has no headroom to buy and H-W7 is not funded. Paired on 1200 dense days / 81 subjects, scale-free fraction of target spread explained, each crop scored against ITS OWN present-mean:
  avg         full 0.9337 -> 41min 0.9002 (retention 0.964)
  masked_avg  full 0.9290 -> 41min 0.8825 (retention 0.950)
The gate demanded >=20% loss; measured 3.6% and 5.0%. Monotone in crop length in both arms, control exact at 1.000.

THE DISTINCTION THIS RUN BUYS, and it is the reason the extent problem does not collapse with it: there are TWO separable defects and only one exists. (a) REPRESENTATION CONTRACTION -- presence-blind pooling maps a short session onto the corpus median, measured at 2.85% of CASE's between-subject spread (W4b), confirmed to survive into the encoder (W5), and fixed end-to-end (W6, 0.5630 -> 0.6897). REAL. (b) READOUT FRAGILITY -- the encoder having hardcoded a per-provenance expected coverage and therefore failing on partial windows, which is what crop augmentation would teach it out of. NOT REAL, measured here. H-W7 assumed (b) followed from (a). It does not. A representation can be badly scaled and still be perfectly readable once the scale is corrected.

METHOD LESSON, and it nearly cost the run: my first metric was `skill = trivial_MAE - model_MAE`, which returned retention of 1.915 -- above 1.0, i.e. crops apparently BETTER than full days. That is impossible as stated and was the tell. Cause: the target changes with the crop, and short crops have a much wider target distribution (trivial 12.74 bpm at 41min vs 6.42 at full), so raw skill grows with target spread rather than with reading ability. Any across-condition comparison whose TARGET DISTRIBUTION also changes must use a scale-free score (here 1 - MAE/trivial); a difference-of-MAEs silently rewards the harder-looking condition. Same family of error as the earlier ratio>1.0 catch in W5 -- when a bounded-looking quantity exceeds its bound, stop and find the confound rather than reporting it.

SECOND-ORDER FINDING WORTH KEEPING: masked_avg retains slightly LESS than avg at 41 minutes (0.950 vs 0.964). Not a contradiction of W6 -- W6's win is on between-subject SPREAD (the scale), while this measures within-condition READABILITY (the ranking), and the masked pool concentrates on fewer real patches so its estimate is noisier on very short spans. The two metrics are not substitutes, and anyone comparing pooling modes should say which axis they mean.

SCOPE, so this rule-out is not over-cited: HR-mean readout only, frozen encoder only, no state/affect or hr_peak target, and g3/g4 UNMEASURED because no augmented model was trained. It joins rather than replaces the E11 prior against synthetic-coverage domain matching. Cost: ~4 minutes instead of ~6 GPU-hours.

## Notes
H-W7 IS NOT FUNDED. Its own blocking Stage-0 gate failed, on CPU, before any GPU was spent -- which is exactly what the gate was written to do.

THE NUMBER. Paired on the SAME 1200 dense lifesnaps/pmdata windows (81 subjects), crop vs full extent, target = each crop's OWN present-only mean HR, scale-free fraction of target spread explained:
  pooling=avg         full 0.9337 | 41min 0.9002 -> retention 0.964 | 120min 0.982 | 224min 0.981 | 480min 0.990
  pooling=masked_avg  full 0.9290 | 41min 0.8825 -> retention 0.950 | 120min 0.977 | 224min 0.978 | 480min 0.988
The gate required a loss of at least 20% at 41-minute crops for the arm to be justified. The frozen encoder loses 3.6% (avg) and 5.0% (masked_avg). That is a fifth to a quarter of the required headroom, so crop augmentation has essentially nothing to recover on this axis.

WHY THE INSTRUMENT IS TRUSTWORTHY: the P3 control fires exactly (full-extent retention 1.000 by construction in both modes), the loss is MONOTONE in crop length in both arms, and the absolute fraction explained never drops below 0.88. Those three together are what 'the encoder reads a partial window nearly as well as a whole one' looks like; a broken harness would not produce a smooth monotone curve with a correct control.

A METRIC BUG WAS CAUGHT AND FIXED MID-RUN, DECLARED HERE RATHER THAN HIDDEN. The first pass scored raw skill (trivial MAE minus model MAE) and returned retention of 1.915 at 41 minutes. A retention above 1.0 cannot mean 'no loss', so I stopped and diagnosed it: the target CHANGES with the crop, and short crops have a much more dispersed target (trivial 12.74 bpm at 41min vs 6.42 at full), so a crop earns larger raw skill purely from larger target spread. Switching to the scale-free fraction explained removed the confound. Both quantities are kept in logs/w7_stage0_crop_power.json so the correction is auditable.

THE HONEST READING, AND IT IS A DIFFERENT NEGATIVE FROM THE ONE THE HYPOTHESIS FEARED. H-W7 was written expecting the encoder to hardcode a per-provenance expected coverage and therefore to fail on partial windows. Measured: it does not. Reading a 41-minute span is nearly as easy for the frozen encoder as reading a full day, for BOTH pooling modes. So the extent problem this session opened is real at the POOLING/representation level -- W4b's contraction to 2.85% of between-subject spread, W5's confirmation, W6's confirmed end-to-end fix -- but it is NOT a readout-robustness problem that training-time crop augmentation would address. Those are different defects and only the first one existed.

WHAT THIS DOES NOT CLOSE, stated so the rule-out is not over-cited later: (i) it measures HR-mean readout, not state/affect or hr_peak, so crop augmentation is untested for any other target; (ii) it uses a FROZEN encoder, so it cannot speak to whether augmentation would change what training learns about other axes; (iii) g3 (dense no-harm) and g4 (shortcut decodability) are UNMEASURED, not passed, because no augmented model exists. The E11 prior against synthetic-coverage domain matching therefore stands untouched and is now joined by a direct measurement on this corpus.

COST: ~4 minutes of CPU/GPU-light probing instead of the ~6 GPU-hours a 3-seed augmented family would have taken.

#extent-heterogeneity #wearables #deployment-distribution #dataset-whitelist #pooling #denominator #state-supervision #optihealth-eiv #data-contract-violation #matched-baseline
