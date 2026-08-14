---
created_at: '2026-08-11T21:17:58+00:00'
id: phase-c-ran-unseeded-for-the-project-s-entire-history-so-every-phase-c-a-b-compared-two-basin-draws
paper_refs: []
problem_tags:
- optihealth-eiv
- run-to-run-variance
- matched-baseline
status: open
title: Phase C ran UNSEEDED for the project's entire history, so every Phase-C A/B
  compared two basin draws
topic_tags:
- medgemma
- cross-modal
- baseline
- control
updated_at: '2026-08-11T21:17:58+00:00'
---

## Description
=== FOUND 2026-08-09 while answering "what is special about v4w, our best run?" ===
Unpreregistered discovery; problem evidence, no calibration weight. FIX COMMITTED as 0b5aff2.

--- 1. THE OBSERVATION THAT STARTED IT ---
v4w (outputs/checkpoints/v4w/phase_c) and v5c (outputs/checkpoints/v5c/phase_c) have BYTE-IDENTICAL
training manifests. Diffing every key of manifest.json:
  same dataset_filter {lifesnaps, pmdata, optihealth_supabase, big_ideas_glycemic, mmash, dreamt}
  same profile chain  phase_a_12ch_1min_wl -> phase_b_12ch_wl -> lora_proxy_12ch_wl
  same sensor_config  (IDENTICAL on every field)
  same data_paths     data/canonical/X_canonical.npy
The ONLY differing field in the entire manifest is final_loss: 1.2183 vs 0.5821 (2.1x).
They are the same configuration run twice.

--- 2. ROOT CAUSE ---
src/training/pipeline.py::run_lora_proxy_adaptation contained ZERO seeding across 1156 lines:
  manual_seed 0 | np.random.seed 0 | random.seed 0 | set_seed 0 | Generator( 0
Phase A and Phase B BOTH seed at 42 (pipeline.py:1179-1183 and 1314-1318). Phase C never did.
So every Phase-C adapter this project has ever produced is an unseeded draw, and no Phase-C A/B
ever separated its headline difference from basin noise.

--- 3. HOW BIG THE BASIN SPREAD IS ---
Measured on 48 IDENTICAL eval samples with an identical draw, production prompt
(prompts/json_slots_prompt.md) through the exact bucket_parser generation path
(scratch/emission_diversity_audit.py). "pk" = emitted hr_peak, "raw" = emitted hr_raw.

  arm   pk distinct  pk top1  pk std/true  pk corr  pk skill(bpm)  raw corr  raw skill(bpm)
  v4c            18    0.229        0.922    0.358         -1.228     0.647          +0.517
  v4w            24    0.208        0.712    0.351         -0.587     0.886          +0.906
  v5c            19    0.271        0.852    0.380         -3.260     0.780          +1.618
  v5d             6    0.604        0.715    0.325         -2.663     0.842          +1.100

v4w vs v5c, IDENTICAL CONFIG:
  hr_peak emission skill  2.673 bpm apart
  hr_raw  emission skill  0.712 bpm apart
  distinct emitted hr_peak values  24 vs 19

--- 4. WHY THIS INVALIDATES PRIOR COMPARISONS ---
Every effect this project has claimed is SMALLER than that spread:
  H-1 dryad hr_acc delta            0.0078  (one sample in 128)
  v4c-vs-v4w allowlist primary gate +0.0249 against a +0.03 bar
  the documented "seed spread"       0.066
All were measured with NO Phase-C seed, i.e. they are draws from this basin distribution, not
measurements of an intervention. This does NOT prove those interventions are null; it proves the
experiments lacked the resolution to say either way.

--- 5. WHAT v4w ACTUALLY IS ---
v4w IS the best arm on hr_peak: most distinct emitted values (24), lowest top-1 mass (0.208), best
skill (-0.587). But NO arm beats always-predict-median on hr_peak (best is -0.587, i.e. still worse
than a constant), and v5c beats v4w on hr_raw (+1.618 vs +0.906). "Our best run" was a lucky BASIN
of an ordinary configuration, not a better configuration.
Separately, the shipped outputs/probes/v4w_full_suite.json OVERSTATED it: it was scored on the stale
eval bundle at 143/109 cut points (hr_acc 0.6016), and rescoring the same adapter on the repaired
instrument at 157.72/127.00 gives 0.4297 -- below its own 0.4453 majority baseline.

--- 6. THE FIX (commit 0b5aff2) ---
- seed: int = 42 added to run_lora_proxy_adaptation, applied BEFORE model construction, dataset build
  and dataloader, mirroring the Phase A/B block.
- torch.manual_seed ALONE IS NOT ENOUGH: the WeightedRandomSampler and both DataLoader branches now
  carry explicit torch.Generator()s, or batch ORDER stays a per-run draw.
- The seed is recorded in the adapter manifest's training block, so a basin is attributable after
  the fact.
- Exposed as a profile key (seed:) so a multi-seed A/B can vary it per arm.

--- 7. VERIFIED EMPIRICALLY, NOT ASSUMED ---
A/A test: the same profile and seed run end-to-end TWICE (scratch/run_seed_aa_smoke.sh):
  initial_loss  6.36121940612793  == 6.36121940612793
  final_loss    5.125603199005127 == 5.125603199005127
  lora/adapter_model.safetensors sha256 564ce044b0911435f8467125 on BOTH runs
Byte-identical LoRA weights. Phase C is now reproducible.
Guard tests in tests/test_phase_c_seeding.py (5), including one asserting the seed is actually USED
-- a seed parameter that is never called is worse than none, because it LOOKS reproducible.

--- 8. WHAT MUST HAPPEN BEFORE ANY FURTHER PHASE-C CLAIM ---
A seeded baseline BAND: >=3 seeds of the SAME control config, so the spread is known. An arm that
beats the incumbent by less than the band beat nothing. RUNNING as seeds 43/44 off v5c's phase_b
(profiles lora_proxy_12ch_wl_s43/_s44, scratch/run_seed_band.sh); seed 42 == v5c is reused.

--- 9. SCOPE ---
This says nothing about whether Phase A/B are adequately seeded in PRACTICE -- they call the right
functions, but no A/A test has been run on them. Worth doing: the same 20-minute check would settle it.

#optihealth-eiv #run-to-run-variance #matched-baseline

#medgemma #cross-modal #baseline #control #optihealth-eiv #run-to-run-variance #matched-baseline
