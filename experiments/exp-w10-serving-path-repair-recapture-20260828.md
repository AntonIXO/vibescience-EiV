---
artifacts: []
closed: true
config_note: 'Re-captures the THREE W6 masked arms (w6msk_s43/s44/s45) on optihealth_supabase
  through the REPAIRED serving path, and re-captures the three avg controls as a same-code-path
  control. Everything except the serving path is held fixed: same adapters (weights
  untouched -- only manifest.json gained a patch_pool_mode field recording what the
  training profile already declared), same prompt (prompts/json_slots_prompt.md),
  same greedy decode, same --n 200, same dataset filter, same seeded draw (np.random.default_rng(0)
  inside emission_diversity_audit.py). REFERENCE CAPTURES for the paired comparison
  already exist from 2026-08-21 and are NOT re-run: logs/emission_w6{msk,avg}_s4{3,4,5}_supa.json.
  Code changes under test: (a) src/training/adapter_persistence.py persists+restores
  patch_pool_mode; (b) scratch/emission_diversity_audit.py and src/inference/sensor_session.py
  now pass patch_presence=_patch_presence_from_mask(...). Guarded by tests/test_patch_pool_serving_path.py
  (6/6 pass pre-data). NOTE ON THE AVG ARMS: for them the repair is expected to be
  a NO-OP (patch_presence is ignored by mode ''avg''), which makes them a built-in
  negative control on the whole change -- if an avg arm moves, the repair leaked into
  the default path and g1 is violated in production even though the unit test passed.'
cost: null
crash_reason: ''
created_at: '2026-08-27T21:37:19+00:00'
diagnostics_measured:
- after: 3.261
  before: 3.244
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
do_not_retry: []
external_run: logs/w10_recapture_scoring.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g3-mode-roundtrips
  passed: true
- evidence: ''
  gate_id: g2-masked-actually-differs
  passed: true
- evidence: ''
  gate_id: g1-default-path-bit-identical
  passed: false
- evidence: ''
  gate_id: g4-seed-band-honesty
  passed: true
git_ref: ''
hypothesis_id: h-w10-train-serve-pooling-mismatch-the-w6-f512a3
id: exp-w10-serving-path-repair-recapture-20260828
interpretation:
  at: '2026-08-27T23:46:24+00:00'
  failure_mode: noise
  lesson: 'The DEFECT was real and is fixed, but my PREDICTED DIRECTION was wrong
    and the honest reading is ''no measurable effect'', not ''repair helps''. The
    W6 masked arms really were trained masked and served avg (patch_pool_mode not
    persisted + patch_presence never passed + a silent projector fallback), and repairing
    it changes 93.5% of emitted texts -- so it is unambiguously live. But on the clean
    same-corpus ablation the hr_raw MAE moved +0.017/+0.032/+0.164 bpm, all POSITIVE
    and all ~38x inside the 6.29 bpm supabase seed band, and hr_peak skill straddles
    zero. So: reclassify W6''s supabase regression as UNINTERPRETABLE (broken instrument)
    rather than as evidence against masked pooling, and do NOT expect serving-path
    parity alone to move deployment numbers -- its value is that comparisons become
    valid again, not that they improve. W6''s PRIMARY variance-compression result
    stands untouched, because that gate always supplied patch_presence. Two reusable
    rules: (1) when two instruments measuring one intervention disagree in SIGN and
    differ in exactly one argument, suspect the argument -- the instrument that PASSED
    is usually the one that supplied it, and a consistent-sign regression across seeds
    is systematic rather than noise; (2) a before/after capture separated in TIME
    is only valid if the corpus is pinned -- here the rebuild ([12547]->[13518], normalisation
    sha 8498adeb->50e93f6c) alone moved an inert avg control by 167/200 emissions,
    so only an in-session ablation isolates the variable.'
  transfers_to:
  - h-w8-cardinality-invariant-attention-pooling-f01250
key_numbers:
  blind_vs_avg_max_abs_diff: 0.0
  delta_hr_peak_skill_s43: -0.04
  delta_hr_peak_skill_s44: 0.472
  delta_hr_peak_skill_s45: -0.313
  delta_hr_raw_mae_s43: 0.017
  delta_hr_raw_mae_s44: 0.032
  delta_hr_raw_mae_s45: 0.164
  frac_emissions_changed_mean: 0.935
  frac_emissions_changed_s43: 0.92
  frac_emissions_changed_s44: 0.965
  frac_emissions_changed_s45: 0.92
  largest_delta_as_fraction_of_seed_band: 0.0261
  rejected_optimisation_max_abs_diff: 4.33
  rejected_optimisation_ms_fast: 10.62
  rejected_optimisation_ms_loop: 7.5
  seed_band_supabase_bpm: 6.29
  stage1_avg_control_emissions_moved_s43: 167.0
  stage1_avg_control_emissions_moved_s44: 193.0
observed_effects:
- delta: 0.017
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-w6b-masked-pooling-scored-20260821
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint:
  interventions: eiv-persist-patch-pool-mode-in-manifest,eiv-thread-patch-presence-through-serving
verdict: refutes
---

## Summary
Tests hypothesis [[h-w10-train-serve-pooling-mismatch-the-w6-f512a3]].
Branches from [[exp-w6b-masked-pooling-scored-20260821]].
External run: logs/w10_recapture_scoring.json

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 3.244 → after 3.261  (Δ +0.017, up ✗ predicted down)

## Gates
- `g3-mode-roundtrips`: PASS
- `g2-masked-actually-differs`: PASS
- `g1-default-path-bit-identical`: **FAIL**
- `g4-seed-band-honesty`: PASS

**Verdict:** refutes

## Interpretation
**Failure mode:** `noise`

The DEFECT was real and is fixed, but my PREDICTED DIRECTION was wrong and the honest reading is 'no measurable effect', not 'repair helps'. The W6 masked arms really were trained masked and served avg (patch_pool_mode not persisted + patch_presence never passed + a silent projector fallback), and repairing it changes 93.5% of emitted texts -- so it is unambiguously live. But on the clean same-corpus ablation the hr_raw MAE moved +0.017/+0.032/+0.164 bpm, all POSITIVE and all ~38x inside the 6.29 bpm supabase seed band, and hr_peak skill straddles zero. So: reclassify W6's supabase regression as UNINTERPRETABLE (broken instrument) rather than as evidence against masked pooling, and do NOT expect serving-path parity alone to move deployment numbers -- its value is that comparisons become valid again, not that they improve. W6's PRIMARY variance-compression result stands untouched, because that gate always supplied patch_presence. Two reusable rules: (1) when two instruments measuring one intervention disagree in SIGN and differ in exactly one argument, suspect the argument -- the instrument that PASSED is usually the one that supplied it, and a consistent-sign regression across seeds is systematic rather than noise; (2) a before/after capture separated in TIME is only valid if the corpus is pinned -- here the rebuild ([12547]->[13518], normalisation sha 8498adeb->50e93f6c) alone moved an inert avg control by 167/200 emissions, so only an in-session ablation isolates the variable.

Transfers to: [[h-w8-cardinality-invariant-attention-pooling-f01250]]

## Key numbers
- `blind_vs_avg_max_abs_diff`: 0
- `delta_hr_peak_skill_s43`: -0.04
- `delta_hr_peak_skill_s44`: 0.472
- `delta_hr_peak_skill_s45`: -0.313
- `delta_hr_raw_mae_s43`: 0.017
- `delta_hr_raw_mae_s44`: 0.032
- `delta_hr_raw_mae_s45`: 0.164
- `frac_emissions_changed_mean`: 0.935
- `frac_emissions_changed_s43`: 0.92
- `frac_emissions_changed_s44`: 0.965
- `frac_emissions_changed_s45`: 0.92
- `largest_delta_as_fraction_of_seed_band`: 0.0261
- `rejected_optimisation_max_abs_diff`: 4.33
- `rejected_optimisation_ms_fast`: 10.62
- `rejected_optimisation_ms_loop`: 7.5
- `seed_band_supabase_bpm`: 6.29
- `stage1_avg_control_emissions_moved_s43`: 167
- `stage1_avg_control_emissions_moved_s44`: 193

## Notes
TWO-STAGE EXPERIMENT. Stage 1 (6 arms, before/after vs the 2026-08-21 captures) turned out CONFOUNDED, and its own built-in negative control is what caught it: the avg arms, for which patch_presence is provably ignored, still moved 167/200 and 193/200 emissions. Root cause: the corpus was rebuilt between the two capture dates ([12547,1440,12] -> [13518,1440,12]) and population normalisation changed with it (stats sha256 8498adeb -> 50e93f6c), so the same physiological day presents different z-values to the encoder. The supabase block also shifted by exactly 249 rows, which is why raw idx gave 0 common rows; the scorer recovers the offset and PROVES the day-match on true_mean/true_peak -- quantities the serving path cannot influence -- at ~1e-9 relative error against a mismatched-neighbour control of ~4.7 bpm.

Stage 2 (scratch/run_w10_blind_ablation.sh) fixes that by running each masked adapter TWICE on the CURRENT corpus with only patch_presence differing (--presence-blind reproduces the old broken path in-session). That is the quotable comparison and every key_number below comes from it.

ESTABLISHED: the train/serve mismatch was REAL, is now REPAIRED, and the repair is LIVE in production -- 93.5% of emissions change when presence reaches the projector. Before the fix, outputs/adapters/w6msk_s43 (trained masked_avg) loaded back as 'avg', and masked-without-presence was bit-identical to avg (torch.equal True, max abs diff 0.0). The mode now survives persistence: the stage-2 run logs 'patch_pool_mode=masked_avg' restored from the manifest. Test suite went 810 passed/2 failed -> 812 passed/1 failed, and the remaining failure reproduces on a stashed tree.

NOT ESTABLISHED: that the repair buys deployment quality. Per-seed hr_raw MAE deltas are +0.017/+0.032/+0.164 bpm -- all three POSITIVE (nominally slightly worse) and the largest is 0.026x the 6.29 bpm supabase seed band, i.e. ~38x inside it. hr_peak skill straddles zero (-0.040/+0.472/-0.313). No deployment claim in either direction; this instrument cannot resolve an effect this small, exactly as preregistered.

CONSEQUENCE FOR W6: its supabase emission regression should be reclassified as UNINTERPRETABLE (measured through a broken serving path), not as evidence against masked pooling. W6's PRIMARY variance-compression result is unaffected -- that gate supplied patch_presence and was always measuring the real operator.

ALSO RECORDED: a REJECTED optimisation. The masked pool's per-bin Python loop cannot become a fused segment-sum, because adaptive_avg_pool2d bins OVERLAP when patches % k != 0 (at patches=23, k=4 they are [0,6) [5,12) [11,18) [17,23), so patches 5/11/17 sit in two bins each). Measured max abs diff 4.33 against the loop, and the 'optimisation' was SLOWER (10.62 vs 7.50 ms). It is also not the bottleneck: masked vs avg is 284.6 vs 276.2 ms on a full projector forward.

#serving-path-parity #extent-heterogeneity #pooling #deployment-distribution #optihealth #collapse #baseline
