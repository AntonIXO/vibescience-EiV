---
ambition_class: null
created_at: '2026-08-27T21:36:54+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'With patch_presence=None or patch_pool_mode=''avg'', the repaired
    serving path must produce BIT-IDENTICAL soft tokens to the pre-change code (torch.equal,
    not allclose). Any drift invalidates every historical artifact. VERIFIED PRE-DATA:
    tests/test_patch_pool_serving_path.py 6/6 pass.'
  diagnostic_id: null
  id: g1-default-path-bit-identical
  mde: null
- blocking: true
  description: 'With real patch_presence supplied, masked_avg output must MEASURABLY
    differ from avg -- otherwise the repair is a no-op that would pass every equivalence
    test. VERIFIED PRE-DATA: max abs diff 1.382, mean 0.00456 on 16 short rows.'
  diagnostic_id: null
  id: g2-masked-actually-differs
  mde: null
- blocking: true
  description: 'A bundle trained masked_avg must load back as masked_avg. VERIFIED
    PRE-DATA: roundtrip w6msk_s43 -> ''masked_avg'', w6avg_s43 -> ''avg''.'
  diagnostic_id: null
  id: g3-mode-roundtrips
  mde: null
- blocking: false
  description: Report all 3 seeds and state the 6.29 bpm supabase band alongside any
    emission number. A change smaller than the band is NOT a deployment finding and
    must not be reported as one.
  diagnostic_id: null
  id: g4-seed-band-honesty
  mde: null
id: h-w10-train-serve-pooling-mismatch-the-w6-f512a3
interventions:
- eiv-persist-patch-pool-mode-in-manifest
- eiv-thread-patch-presence-through-serving
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'Masked arms'' supabase hr_raw MAE should IMPROVE (fall) once served
    with patch_presence, relative to their own presence-blind capture. Per-seed presence-blind
    values: s43 3.377, s44 3.351, s45 3.737. I predict the repaired capture lands
    closer to the avg controls (3.151/2.861/2.754). CRITICAL HONESTY BOUND: the supabase
    seed band is 6.29 bpm and EXCEEDS its own 4.02 trivial baseline, so ANY per-seed
    movement here is uninterpretable as a deployment claim. This prediction is about
    instrument repair (does the served computation change at all, in the trained direction),
    NOT a deployment effect. I will report it as directional evidence only and will
    NOT claim a deployment win regardless of outcome.'
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
problem_tags: []
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- serving-path-parity
- extent-heterogeneity
- pooling
- deployment-distribution
- optihealth
- collapse
- baseline
---

## Statement
H-W10 (TRAIN/SERVE POOLING MISMATCH). The W6 masked_avg arms' emission null on optihealth_supabase is an INSTRUMENT defect, not a property of masked pooling. Two independent gaps, each individually silent, compose into a train/serve mismatch: (1) `save_adapter_bundle` never persisted `patch_pool_mode` and `load_adapter_bundle` never restored it, so a bundle trained with 'masked_avg' reconstructs as 'avg' (MEASURED: outputs/adapters/w6msk_s43 loads back patch_pool_mode='avg'); (2) every inference site -- scratch/emission_diversity_audit.py:119, src/inference/sensor_session.py:139 -- called projector(fused, channel_mask=cm) with NO patch_presence, and cross_modal.py falls back to a plain adaptive_avg_pool2d when it is None rather than raising (MEASURED: torch.equal True, max abs diff 0.0). So the masked arms were TRAINED masked and SERVED avg. This explains the otherwise contradictory W6 result where the primary variance-compression ratio gate passed 7/7 short cohorts (0.5630 -> 0.6897) while supabase emission MAE regressed on all 3 seeds with a consistent sign (+0.226/+0.490/+0.983): the ratio gate was computed by scratch/w6_primary_ratio.py:101, which DOES pass patch_presence, while the emission scorer did not. Repairing the serving path is expected to move the masked arms' emission toward their avg controls, because the served computation becomes the one the weights were trained for.

## Rationale
Found while auditing what the last dataset load (corpus [13518,1440,12], merge git_sha f82b082) and its follow-on tests actually established. The W6 record is internally inconsistent in a way that a stage-mismatch explains exactly: same intervention, opposite signs, on two instruments that differ in precisely one argument. The repo has paid for this exact class of defect before -- E22's train/eval channel_mask semantics inversion, where training used 1=PRESENT >0.05 and inference used 1=MISSING <0.5, and three investigations missed it because the diagnostic pool was restricted to the half where both rules agree. Same signature here: the gate that passed is the one that supplied the argument.

## Plan


## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Applies [[eiv-persist-patch-pool-mode-in-manifest]], [[eiv-thread-patch-presence-through-serving]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g1-default-path-bit-identical` (blocking): With patch_presence=None or patch_pool_mode='avg', the repaired serving path must produce BIT-IDENTICAL soft tokens to the pre-change code (torch.equal, not allclose). Any drift invalidates every historical artifact. VERIFIED PRE-DATA: tests/test_patch_pool_serving_path.py 6/6 pass.
Gate `g2-masked-actually-differs` (blocking): With real patch_presence supplied, masked_avg output must MEASURABLY differ from avg -- otherwise the repair is a no-op that would pass every equivalence test. VERIFIED PRE-DATA: max abs diff 1.382, mean 0.00456 on 16 short rows.
Gate `g3-mode-roundtrips` (blocking): A bundle trained masked_avg must load back as masked_avg. VERIFIED PRE-DATA: roundtrip w6msk_s43 -> 'masked_avg', w6avg_s43 -> 'avg'.
Gate `g4-seed-band-honesty` (advisory): Report all 3 seeds and state the 6.29 bpm supabase band alongside any emission number. A change smaller than the band is NOT a deployment finding and must not be reported as one.

#serving-path-parity #extent-heterogeneity #pooling #deployment-distribution #optihealth #collapse #baseline
