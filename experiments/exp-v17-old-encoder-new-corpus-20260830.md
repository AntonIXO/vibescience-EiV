---
artifacts:
- outputs/adapters/v17oldenc_c
- logs/v17_driver.log
- logs/v17_phase_c.log
- logs/emission_v17oldenc_supa_summary.json
- logs/emission_v17oldenc_dense_summary.json
- scratch/run_v17_oldenc.sh
closed: true
config_note: 'v17oldenc: ONE variable vs exp-v16 -- encoder checkpoint swapped to
  outputs/checkpoints/ab_ctrl_s43/phase_b (OLD-corpus healthy encoder, arch-identical
  input_dim 24 / d_model 192 / PL120-ST60). Same byte-untouched lora_proxy_12ch_wl_band04,
  same fixed [14041] ddb7498 bundle, same rebuilt eval sha 03d62723, seed 42. Fills
  the untested 2x2 cell old-enc x new-corpus. Runner scratch/run_v17_oldenc.sh. No
  projector reinit reported at launch; caveat respiratory_rate z-shift (median 14.8->19.0)
  named in the hypothesis plan.'
cost:
  gpu_hours: 1.0
  wall_hours: 0.99
crash_reason: ''
created_at: '2026-08-30T16:23:50+00:00'
diagnostics_measured:
- after: 4.279
  before: 10.058
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.583
  before: 0.231
  diagnostic_id: eiv_gen_peak_corr
- after: 10.238
  before: 24.519
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT count v17 as a healthy adapter or deploy it: gates formally failed (corr
  0.583 < 0.60, skill -0.325 < 0). It is CARRIER EVIDENCE, not a product.'
- Do NOT interpret v17's dense parse_ok drop (128->92) as a defect of the old encoder
  without checking the emitted texts -- more varied emissions parse less often under
  the strict JSON regex, which is itself a de-collapse signature.
external_run: ''
former_ids: []
gate_results:
- evidence: 'MARGINAL FAIL, and the margin matters: corr 0.583 vs the 0.60 bar --
    missed by 0.017, deep inside the 0.1 mde. But the MOVE is enormous and unambiguous:
    0.231 -> 0.583 (+0.352) from swapping ONLY the encoder checkpoint, with top1_mass
    0.590 (vs 0.810) and distinct 13. Dense corr 0.597, distinct 39, top1 0.117 --
    the constant-emitter signature is GONE. This is the largest single-variable move
    recorded on this problem in either direction. The preregistered caveat is live:
    the old encoder reads respiratory_rate ~1 sigma off (corpus median moved 14.8->19.0),
    and dense parse_ok dropped to 92/128, so a marginal miss was the named expectation
    for a carrier-confirming result.'
  gate_id: g1-old-encoder-trains-healthy-on-new-corpus
  passed: false
- evidence: 'FAIL but nearly closed: supabase hr_raw MAE 4.279 vs trivial 3.954 ->
    skill -0.325, against v16''s -6.103 on the same data with the new encoder. The
    remaining 0.33 bpm gap is within the 0.80 C-only seed band (exp-054). hr_peak
    remains weak (22.910, skill -5.5).'
  gate_id: g2-beats-trivial
  passed: false
- evidence: 'Advisory FAIL: dense-128 hr_raw MAE 10.238 vs trivial 6.986 -- but down
    from 24.519 (-14.3 bpm), corr 0.597, distinct 39. parse_ok 92/128 (dropped from
    128) -- the adapter emits more varied, less template-locked text; unparsed rows
    excluded from MAE.'
  gate_id: g3-dense-advisory
  passed: false
git_ref: ''
hypothesis_id: the-collapse-travels-with-the-new-corpus-66b5f8
id: exp-v17-old-encoder-new-corpus-20260830
interpretation:
  at: '2026-08-30T17:22:43+00:00'
  failure_mode: recipe_conditional
  lesson: 'THE ENCODER CHECKPOINT IS THE PRIMARY CARRIER OF THE COLLAPSE. One variable
    -- swapping the frozen encoder from v11full (new-corpus-trained) to ab_ctrl_s43
    (old-corpus-trained) on the SAME fixed bundle -- moved supabase corr +0.352 (0.231->0.583),
    MAE 10.06->4.28, dense 24.5->10.2, and killed the constant-emitter signature (distinct
    10->39, top1 0.81->0.59). Every prior lever moved corr by <0.24; this moved it
    by 0.35 and is the largest single-variable effect on record. The gates still failed
    by small margins (corr -0.017, skill -0.33 -- within the 0.80 seed band), consistent
    with the preregistered caveat that the old encoder reads the new bundle''s z-space
    slightly off (respiratory_rate median 14.8->19.0). CRITICAL REFRAME of exp-v12:
    ''freezing the new encoder does not fix C'' and ''the new encoder''s tokens probe
    fine linearly'' were both true and both MISLEADING -- a linear HR probe is not
    a CLM gradient, and the new-corpus A/B training produces token GEOMETRY that steers
    LoRA toward prefix-ignoring. The next questions: (a) v18 (new enc x old bundle)
    confirms the double dissociation; (b) WHAT about A/B training on the new corpus
    damages the geometry -- suspects: the InfoNCE pool''s post-rebuild composition,
    soft-token norm/anisotropy shifts invisible to R2 probes.'
  transfers_to:
  - the-collapse-travels-with-the-new-corpus-66b5f8
  - the-fresh-chain-collapse-is-not-caused-by-the-37ddda
  - complement-cell-of-the-v17-2x2-the-new-v11full-838f07
key_numbers:
  dense_corr: 0.597
  dense_distinct: 39.0
  dense_mae: 10.238
  dense_parse_ok: 92.0
  dense_top1: 0.117
  supa_corr: 0.583
  supa_delta_corr_vs_v16: 0.352
  supa_mae: 4.279
  supa_skill: -0.325
  supa_top1: 0.59
  train_final_loss: 0.0
observed_effects:
- delta: -5.779
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: 0.352
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: -14.281
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-v16-fixed-bundle-exact-recipe-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[the-collapse-travels-with-the-new-corpus-66b5f8]].
Branches from [[exp-v16-fixed-bundle-exact-recipe-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 10.058 → after 4.279  (Δ -5.779, down)
- [[eiv_gen_peak_corr]]: before 0.231 → after 0.583  (Δ +0.352, up ✓ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 24.519 → after 10.238  (Δ -14.281, down)

## Gates
- `g1-old-encoder-trains-healthy-on-new-corpus`: **FAIL** — MARGINAL FAIL, and the margin matters: corr 0.583 vs the 0.60 bar -- missed by 0.017, deep inside the 0.1 mde. But the MOVE is enormous and unambiguous: 0.231 -> 0.583 (+0.352) from swapping ONLY the encoder checkpoint, with top1_mass 0.590 (vs 0.810) and distinct 13. Dense corr 0.597, distinct 39, top1 0.117 -- the constant-emitter signature is GONE. This is the largest single-variable move recorded on this problem in either direction. The preregistered caveat is live: the old encoder reads respiratory_rate ~1 sigma off (corpus median moved 14.8->19.0), and dense parse_ok dropped to 92/128, so a marginal miss was the named expectation for a carrier-confirming result.
- `g2-beats-trivial`: **FAIL** — FAIL but nearly closed: supabase hr_raw MAE 4.279 vs trivial 3.954 -> skill -0.325, against v16's -6.103 on the same data with the new encoder. The remaining 0.33 bpm gap is within the 0.80 C-only seed band (exp-054). hr_peak remains weak (22.910, skill -5.5).
- `g3-dense-advisory`: **FAIL** — Advisory FAIL: dense-128 hr_raw MAE 10.238 vs trivial 6.986 -- but down from 24.519 (-14.3 bpm), corr 0.597, distinct 39. parse_ok 92/128 (dropped from 128) -- the adapter emits more varied, less template-locked text; unparsed rows excluded from MAE.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

THE ENCODER CHECKPOINT IS THE PRIMARY CARRIER OF THE COLLAPSE. One variable -- swapping the frozen encoder from v11full (new-corpus-trained) to ab_ctrl_s43 (old-corpus-trained) on the SAME fixed bundle -- moved supabase corr +0.352 (0.231->0.583), MAE 10.06->4.28, dense 24.5->10.2, and killed the constant-emitter signature (distinct 10->39, top1 0.81->0.59). Every prior lever moved corr by <0.24; this moved it by 0.35 and is the largest single-variable effect on record. The gates still failed by small margins (corr -0.017, skill -0.33 -- within the 0.80 seed band), consistent with the preregistered caveat that the old encoder reads the new bundle's z-space slightly off (respiratory_rate median 14.8->19.0). CRITICAL REFRAME of exp-v12: 'freezing the new encoder does not fix C' and 'the new encoder's tokens probe fine linearly' were both true and both MISLEADING -- a linear HR probe is not a CLM gradient, and the new-corpus A/B training produces token GEOMETRY that steers LoRA toward prefix-ignoring. The next questions: (a) v18 (new enc x old bundle) confirms the double dissociation; (b) WHAT about A/B training on the new corpus damages the geometry -- suspects: the InfoNCE pool's post-rebuild composition, soft-token norm/anisotropy shifts invisible to R2 probes.

Transfers to: [[the-collapse-travels-with-the-new-corpus-66b5f8]], [[the-fresh-chain-collapse-is-not-caused-by-the-37ddda]], [[complement-cell-of-the-v17-2x2-the-new-v11full-838f07]]

## Key numbers
- `dense_corr`: 0.597
- `dense_distinct`: 39
- `dense_mae`: 10.238
- `dense_parse_ok`: 92
- `dense_top1`: 0.117
- `supa_corr`: 0.583
- `supa_delta_corr_vs_v16`: 0.352
- `supa_mae`: 4.279
- `supa_skill`: -0.325
- `supa_top1`: 0.59
- `train_final_loss`: 0

## Artifacts
- `outputs/adapters/v17oldenc_c`
- `logs/v17_driver.log`
- `logs/v17_phase_c.log`
- `logs/emission_v17oldenc_supa_summary.json`
- `logs/emission_v17oldenc_dense_summary.json`
- `scratch/run_v17_oldenc.sh`

## Do not retry
- Do NOT count v17 as a healthy adapter or deploy it: gates formally failed (corr 0.583 < 0.60, skill -0.325 < 0). It is CARRIER EVIDENCE, not a product.
- Do NOT interpret v17's dense parse_ok drop (128->92) as a defect of the old encoder without checking the emitted texts -- more varied emissions parse less often under the strict JSON regex, which is itself a de-collapse signature.

## Notes
THE SMOKING GUN, even under formally-failed gates. Swapping ONLY the encoder checkpoint (v11full -> ab_ctrl_s43, both frozen, arch-identical) on the SAME fixed bundle moved supabase corr 0.231 -> 0.583, MAE 10.06 -> 4.28, dense 24.5 -> 10.2, and destroyed the constant-emitter signature (top1 0.810 -> 0.590 supa, 0.305 -> 0.117 dense, distinct 10 -> 39). Largest single-variable effect ever recorded on this problem. The residual gap to fully-healthy (corr 0.583 vs 0.76, skill -0.33 vs +1.26) is consistent with the named caveat: the old encoder reads the new bundle's respiratory_rate ~1 sigma off (corpus median 14.8 -> 19.0) and possibly other small z-shifts -- i.e. the encoder is the CARRIER and the bundle contributes a residual z-space mismatch. Verdict semantics: gates failed so the formal verdict is negative, but the DIRECTIONAL evidence convicts the new-corpus-trained ENCODER as the primary carrier. v18 (complement cell: new encoder x old bundle) is queued on the GPU and will confirm or split this.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
