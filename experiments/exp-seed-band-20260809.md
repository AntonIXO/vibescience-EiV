---
artifacts: []
closed: true
config_note: 'PREREGISTERED BEFORE RESULTS EXIST: opened while seed 43 was at step
  ~920/2600 and seed 44 had not begun, so no outcome was visible when the prediction
  and gates were committed.


  SETUP. seed 42 = REUSED outputs/checkpoints/v5c/phase_c (already probed: hr_acc
  0.5234=67/128 vs majority 0.4453; hr_peak skill -3.260 bpm, 19 distinct emitted
  values, top-1 mass 0.271; hr_raw skill +1.618 bpm). seed 43 = profile lora_proxy_12ch_wl_s43.
  seed 44 = profile lora_proxy_12ch_wl_s44. All three initialise from outputs/checkpoints/v5c/phase_b,
  so the ONLY variable is the Phase-C seed. Driver scratch/run_seed_band.sh. Probe
  p2,p3,p4,p6 at 512 samples on the REBUILT data/eval_v3_1min (row identity 200/200
  vs canonical, norm sha 6f5de9c1b55b both sides). Emission audit scratch/emission_diversity_audit.py
  n=48, rng seed 0, production prompt prompts/json_slots_prompt.md through the exact
  bucket_parser generation path.


  COMPARATOR IS SOUND THIS TIME. All arms carry a PINNED hr_buckets manifest block
  (tachycardic 157.71853625752556 / elevated 126.9999977820953, same source_normalization_sha256)
  and the probe logs ''cut points PINNED in adapter manifest ... (reproducible)''.
  Earlier arms recomputed cut points from a mutable corpus, which is how the v4c/v4w
  A/B was scored at 143/109 while the corpus implied 157.72/127.00.


  SEEDING VERIFIED BEFORE THIS WAS TRUSTED. A/A smoke (scratch/run_seed_aa_smoke.sh):
  same profile+seed twice gave initial_loss 6.36121940612793 both, final_loss 5.125603199005127
  both, adapter_model.safetensors sha256 564ce044b0911435f8467125 BOTH. So a cross-seed
  difference is attributable to the seed, not residual nondeterminism.


  KNOWN LIMITATION STATED UP FRONT. Three seeds give a RANGE, not a variance estimate
  with a usable CI. The range of 3 draws systematically UNDERSTATES the true spread,
  so the measured band is a LOWER BOUND on the minimum detectable effect.


  FALSIFICATION WOULD BE GOOD NEWS: if the band is <1.0 bpm, single-seed arms are
  more trustworthy than feared, and the v4w/v5c gap needs another explanation -- leading
  candidate being that data/canonical/X_canonical.npy was rewritten at 16:36:05, INSIDE
  v4c''s Phase A window (15:40:10-16:45:15), so that A/B may have carried two variables.'
crash_reason: ''
created_at: '2026-08-11T21:19:51+00:00'
diagnostics_measured:
- after: 2.4238
  before: 0.0
  diagnostic_id: eiv_phase_c_seed_band_range_bpm
- after: 0.0781
  before: 0.066
  diagnostic_id: eiv_gen_bucket_acc_over_majority
- after: 7.0
  before: 0.0
  diagnostic_id: eiv_gen_between_sample_std
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'All three seeds completed RC=0 on every stage: v6s43_PHASE_C_RC=0 / v6s43_PROBE_RC=0
    / v6s43_EMIT_RC=0 and v6s44_PHASE_C_RC=0 / v6s44_PROBE_RC=0 / v6s44_EMIT_RC=0;
    seed 42 is the pre-existing v5c/phase_c reused unchanged. Pinned hr_buckets are
    BYTE-IDENTICAL across all three manifests: tachycardic=157.71853625752556, elevated=126.9999977820953,
    bradycardic_mean=55.0, source_tensor=data/canonical/X_canonical.npy, source_tensor_shape=[10344,1440,12],
    source_n_samples=10336, source_normalization_sha256=6f5de9c1b55b5654a9802ee93b36f07cb886d12882a90ebb357e2ce4559c12a8.
    All arms scored on the same eval bundle (data/eval_v3_1min) with num_samples=128
    for p4 and the same 48-sample draw for emission diversity, all off the SAME outputs/checkpoints/v5c/phase_b
    so seed is the only variable.'
  gate_id: gate-seed-band-measured
  passed: true
- evidence: 'MEASURED BAND EXCEEDS THE LEGACY 0.066 FIGURE, so per the gate the 0.066
    is retired in writing here. p4 hr_acc across seeds 42/43/44 = 0.5234 (67/128)
    / 0.4453 (57/128) / 0.5000 (64/128), RANGE 0.0781 = 10 of 128 samples, versus
    the legacy 0.066. hr_skill_over_majority = +0.0781 / 0.0000 / +0.0547 against
    an identical majority baseline 0.4453 on all three. state_acc = 0.3828 / 0.5156
    / 0.5164, RANGE 0.1336 (17/128); state_skill = -0.1328 / 0.0000 / +0.0082, a band
    that STRADDLES ZERO. Emission hr_peak skill = -3.260 / -1.6723 / -0.8362 bpm,
    RANGE 2.4238 bpm. CONSEQUENCE, stated as the gate requires: every prior single-seed
    Phase-C conclusion is UNDERPOWERED BY CONSTRUCTION rather than refuted. Specifically
    re-labelled as underpowered: H-1 dryad hr_acc delta 0.0078 (band 0.0781, i.e.
    10x smaller than noise); the allowlist A/B result +0.0249 AND the +0.03 bar it
    was scored against (both inside the band); the v4w-vs-v4c rescored hr_acc gap
    0.0781 (EQUALS the band exactly); and the v4w-vs-v5c ''basin gap'' of 2.673 bpm
    hr_peak, which is ~90% of the 2.4238 bpm pure-seed band. Minimum detectable effect
    for all future Phase-C arms must now be derived from THIS band: >0.0781 on p4
    hr_acc and >2.42 bpm on emitted hr_peak skill for a single-seed comparison.'
  gate_id: gate-band-vs-legacy-spread
  passed: true
- evidence: 'No seed is promoted. Seed 42 has the best p4 hr_acc (0.5234) and seed
    44 the best emission hr_peak skill (-0.8362 bpm) and best hr_raw skill (+3.8804
    bpm) — three different ''winners'' on three metrics, which is itself the argument
    against selection. Reported as a distribution throughout: hr_acc {0.5234, 0.4453,
    0.5000}, hr_peak skill {-3.260, -1.6723, -0.8362} bpm, hr_raw skill {+1.618, +2.8542,
    +3.8804} bpm, hr_peak distinct {19, 24, 17}, hr_peak top1 {0.271, 0.2917, 0.25}.
    The v5c checkpoint remains the control by PRE-EXISTING designation (it was the
    seed-42 arm before this experiment opened), not because it scored well.'
  gate_id: gate-no-post-hoc-best-seed
  passed: true
git_ref: ''
hypothesis_id: with-phase-c-now-seeded-0b5aff2-three-fae38a
id: exp-seed-band-20260809
key_numbers: {}
observed_effects:
- delta: 2.4238
  diagnostic_id: eiv_phase_c_seed_band_range_bpm
  direction: up
- delta: 0.0121
  diagnostic_id: eiv_gen_bucket_acc_over_majority
  direction: up
- delta: 7.0
  diagnostic_id: eiv_gen_between_sample_std
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_between_sample_std: false
    eiv_gen_bucket_acc_over_majority: true
    eiv_phase_c_seed_band_range_bpm: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[with-phase-c-now-seeded-0b5aff2-three-fae38a]].

## Measurements
- [[eiv_phase_c_seed_band_range_bpm]]: before 0.0 → after 2.4238  (Δ +2.4238, up ✓ predicted up)
- [[eiv_gen_bucket_acc_over_majority]]: before 0.066 → after 0.0781  (Δ +0.0121, up ✓ predicted up)
- [[eiv_gen_between_sample_std]]: before 0.0 → after 7.0  (Δ +7, up ✗ predicted none)

## Gates
- `gate-seed-band-measured`: PASS — All three seeds completed RC=0 on every stage: v6s43_PHASE_C_RC=0 / v6s43_PROBE_RC=0 / v6s43_EMIT_RC=0 and v6s44_PHASE_C_RC=0 / v6s44_PROBE_RC=0 / v6s44_EMIT_RC=0; seed 42 is the pre-existing v5c/phase_c reused unchanged. Pinned hr_buckets are BYTE-IDENTICAL across all three manifests: tachycardic=157.71853625752556, elevated=126.9999977820953, bradycardic_mean=55.0, source_tensor=data/canonical/X_canonical.npy, source_tensor_shape=[10344,1440,12], source_n_samples=10336, source_normalization_sha256=6f5de9c1b55b5654a9802ee93b36f07cb886d12882a90ebb357e2ce4559c12a8. All arms scored on the same eval bundle (data/eval_v3_1min) with num_samples=128 for p4 and the same 48-sample draw for emission diversity, all off the SAME outputs/checkpoints/v5c/phase_b so seed is the only variable.
- `gate-band-vs-legacy-spread`: PASS — MEASURED BAND EXCEEDS THE LEGACY 0.066 FIGURE, so per the gate the 0.066 is retired in writing here. p4 hr_acc across seeds 42/43/44 = 0.5234 (67/128) / 0.4453 (57/128) / 0.5000 (64/128), RANGE 0.0781 = 10 of 128 samples, versus the legacy 0.066. hr_skill_over_majority = +0.0781 / 0.0000 / +0.0547 against an identical majority baseline 0.4453 on all three. state_acc = 0.3828 / 0.5156 / 0.5164, RANGE 0.1336 (17/128); state_skill = -0.1328 / 0.0000 / +0.0082, a band that STRADDLES ZERO. Emission hr_peak skill = -3.260 / -1.6723 / -0.8362 bpm, RANGE 2.4238 bpm. CONSEQUENCE, stated as the gate requires: every prior single-seed Phase-C conclusion is UNDERPOWERED BY CONSTRUCTION rather than refuted. Specifically re-labelled as underpowered: H-1 dryad hr_acc delta 0.0078 (band 0.0781, i.e. 10x smaller than noise); the allowlist A/B result +0.0249 AND the +0.03 bar it was scored against (both inside the band); the v4w-vs-v4c rescored hr_acc gap 0.0781 (EQUALS the band exactly); and the v4w-vs-v5c 'basin gap' of 2.673 bpm hr_peak, which is ~90% of the 2.4238 bpm pure-seed band. Minimum detectable effect for all future Phase-C arms must now be derived from THIS band: >0.0781 on p4 hr_acc and >2.42 bpm on emitted hr_peak skill for a single-seed comparison.
- `gate-no-post-hoc-best-seed`: PASS — No seed is promoted. Seed 42 has the best p4 hr_acc (0.5234) and seed 44 the best emission hr_peak skill (-0.8362 bpm) and best hr_raw skill (+3.8804 bpm) — three different 'winners' on three metrics, which is itself the argument against selection. Reported as a distribution throughout: hr_acc {0.5234, 0.4453, 0.5000}, hr_peak skill {-3.260, -1.6723, -0.8362} bpm, hr_raw skill {+1.618, +2.8542, +3.8804} bpm, hr_peak distinct {19, 24, 17}, hr_peak top1 {0.271, 0.2917, 0.25}. The v5c checkpoint remains the control by PRE-EXISTING designation (it was the seed-42 arm before this experiment opened), not because it scored well.

**Verdict:** supports

## Notes
RESULT: the prediction holds, and the band is large enough to swallow every effect this project has treated as a finding.

THREE SEEDS, IDENTICAL CONFIG (lora_proxy_12ch_wl, all off outputs/checkpoints/v5c/phase_b, byte-identical pinned cut points 157.71853625752556/126.9999977820953, same eval bundle, same 48-sample emission draw). Only the Phase-C seed differs.

PRIMARY DIAGNOSTIC. Emitted hr_peak skill vs always-predict-median: seed 42 -3.260, seed 43 -1.6723, seed 44 -0.8362 bpm. RANGE 2.4238 bpm, against a predicted >1.0. Prediction met.

SECONDARY. p4 hr_acc 0.5234 (67/128) / 0.4453 (57/128) / 0.5000 (64/128), RANGE 0.0781 = 10 of 128 samples, against a predicted >0.03 and the legacy 0.066. Prediction met; 0.066 is retired. hr_skill_over_majority +0.0781 / 0.0000 / +0.0547 on an identical 0.4453 majority baseline.

COLLAPSE COMPANION (no directional prediction). hr_peak distinct emitted values 19/24/17, top-1 mass 0.271/0.2917/0.25. No seed mode-collapsed the way v5d did (6 distinct, 130.0 on 11/16), so the band is a genuine spread rather than one degenerate draw.

WHAT THIS COSTS. Re-labelled UNDERPOWERED, not refuted: H-1 dryad hr_acc delta 0.0078 (10x smaller than the band); the allowlist A/B +0.0249 and its own +0.03 bar; the v4w-vs-v4c rescored hr_acc gap 0.0781, which EQUALS the band exactly; and my own earlier v4w-vs-v5c 'basin gap' of 2.673 bpm hr_peak, which is ~90% of the 2.4238 bpm pure-seed band and which I should not have flagged as larger than every effect the project had claimed without this measurement in hand.

WHAT SURVIVES, AND IS STRENGTHENED. hr_peak skill is NEGATIVE on all three seeds (-3.260/-1.6723/-0.8362): no seed beats a constant on hr_peak. This is now a three-seed result and is the strongest evidence yet for the saturated-prior problem. Conversely hr_raw skill is POSITIVE on all three (+1.618/+2.8542/+3.8804 bpm, corr 0.780/0.833/0.918), so hr_raw conditioning is real and survives reseeding. The hr_raw/hr_peak asymmetry is seed-robust and is the signal to build on.

state_skill spans -0.1328/0.0000/+0.0082, a band that STRADDLES ZERO: there is no evidence Phase C reads state at all.

NEW MINIMUM DETECTABLE EFFECT for any future single-seed Phase-C comparison: >0.0781 on p4 hr_acc, >2.42 bpm on emitted hr_peak skill. Both are LOWER BOUNDS, since the range of 3 draws understates the true spread. Multi-seed arms, not single draws, are now the only valid design.

Provenance: driver scratch/run_seed_band.sh -> logs/seed_band.log; all stages RC=0 (v6s43/v6s44 PHASE_C, PROBE, EMIT); probes outputs/probes/v6s43_suite.json, v6s44_suite.json; adapters outputs/checkpoints/v6s43/phase_c, outputs/checkpoints/v6s44/phase_c.

#medgemma #cross-modal #baseline #control #optihealth-eiv #run-to-run-variance #matched-baseline
