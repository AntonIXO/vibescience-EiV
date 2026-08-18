---
artifacts: []
closed: true
config_note: 'Runner: `eiv train phase-b --profile phase_b_12ch_segtrip_s42` on RTX
  5070 (11.51 GiB). Corpus git_sha e849eb7. Encoder init outputs/checkpoints/12ch_v3_wl/phase_a.


  ATTEMPTED the E28 intervention arm. The segment miner ENGAGED CORRECTLY — the run
  logged ''Phase B segment triplets ON: 49449 segments over 5837 windows, 3 states
  {neutral 43881, flow 3017, stress 2551}, 1004 transition windows (hard-negative
  supply), W=120 min'' — but the process then died loading the MedGemma text encoder
  for the frozen-embedding precompute.


  CRASH, not a result. No checkpoint was produced, so no gate can be evaluated.'
cost: null
crash_reason: 'CUDA OOM loading the MedGemma text encoder on an RTX 5070 (11.51 GiB).
  ''Tried to allocate 100.00 MiB. GPU 0 has a total capacity of 11.51 GiB of which
  34.94 MiB is free ... this process has 11.27 GiB in use'' -> ''RuntimeError: Could
  not load required MedGemma text encoder google/medgemma-1.5-4b-it. No synthetic/hash
  fallback is permitted.''


  NOT CAUSED BY THE SEGMENT-TRIPLET CHANGE. Verified by running the unmodified CONTROL
  profile (phase_b_12ch_wl_dryad, no segment_triplets key): it OOMs identically at
  the same point. This is a pre-existing capacity limit of this host for Phase B on
  this corpus and it blocks BOTH arms equally.'
created_at: '2026-08-15T09:13:41+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: logs/e28_segtrip_train.log + logs/e28_control_probe.log
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: hyp-e28-segment-triplet-retrain-20260815
id: exp-e28-segment-triplet-retrain-20260815
interpretation:
  at: '2026-08-18T21:08:42+00:00'
  failure_mode: test_invalid
  lesson: 'The segment-triplet retrain hypothesis is UNTESTED, not refuted: the process
    died in CUDA OOM loading the MedGemma text encoder on an RTX 5070 (11.51 GiB total,
    34.94 MiB free, 11.27 GiB already held by the sensor stack, tried to allocate
    100.00 MiB) before any checkpoint or gate existed, and the unmodified control
    profile phase_b_12ch_wl_dryad OOMs identically, so the failure is a pre-existing
    host capacity limit that blocks both arms equally rather than anything about segment
    triplets. The plumbing IS confirmed end-to-end — the miner logged ''49449 segments
    over 5837 windows, 3 states {neutral 43881, flow 3017, stress 2551}, 1004 transition
    windows, W=120'' inside the real training entrypoint (differing from the offline
    audit''s 64005/6389/1129 only because the profile applies min_coverage=0.25 and
    min_hr_coverage=0.05). Durable finding worth more than the crash: the frozen-embedding
    cache CANNOT be pre-warmed across processes, because the key is {raw_index}_{sha1(caption)}
    and captions are RNG-augmented, so 68712 warmed files and 58559 hardlinked embeddings
    both still left ''10150 missing''; and reducing batch 25->8 with accum 2->6 cannot
    help because the binding memory is MedGemma''s WEIGHTS, not activations. Unblock
    via deterministic per-sample captions or a 4-bit precompute path (the latter is
    what exp-e28b actually used).'
  transfers_to:
  - hyp-e27-encoder-on-transitions-20260815
  - e29-state-geometry-innocent-weights-guilty-20260815
key_numbers: {}
observed_effects: []
parent_experiment_id: null
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[hyp-e28-segment-triplet-retrain-20260815]].
External run: logs/e28_segtrip_train.log + logs/e28_control_probe.log

**Verdict:** crashed

**Crash:** CUDA OOM loading the MedGemma text encoder on an RTX 5070 (11.51 GiB). 'Tried to allocate 100.00 MiB. GPU 0 has a total capacity of 11.51 GiB of which 34.94 MiB is free ... this process has 11.27 GiB in use' -> 'RuntimeError: Could not load required MedGemma text encoder google/medgemma-1.5-4b-it. No synthetic/hash fallback is permitted.'

NOT CAUSED BY THE SEGMENT-TRIPLET CHANGE. Verified by running the unmodified CONTROL profile (phase_b_12ch_wl_dryad, no segment_triplets key): it OOMs identically at the same point. This is a pre-existing capacity limit of this host for Phase B on this corpus and it blocks BOTH arms equally.

## Interpretation
**Failure mode:** `test_invalid`

The segment-triplet retrain hypothesis is UNTESTED, not refuted: the process died in CUDA OOM loading the MedGemma text encoder on an RTX 5070 (11.51 GiB total, 34.94 MiB free, 11.27 GiB already held by the sensor stack, tried to allocate 100.00 MiB) before any checkpoint or gate existed, and the unmodified control profile phase_b_12ch_wl_dryad OOMs identically, so the failure is a pre-existing host capacity limit that blocks both arms equally rather than anything about segment triplets. The plumbing IS confirmed end-to-end — the miner logged '49449 segments over 5837 windows, 3 states {neutral 43881, flow 3017, stress 2551}, 1004 transition windows, W=120' inside the real training entrypoint (differing from the offline audit's 64005/6389/1129 only because the profile applies min_coverage=0.25 and min_hr_coverage=0.05). Durable finding worth more than the crash: the frozen-embedding cache CANNOT be pre-warmed across processes, because the key is {raw_index}_{sha1(caption)} and captions are RNG-augmented, so 68712 warmed files and 58559 hardlinked embeddings both still left '10150 missing'; and reducing batch 25->8 with accum 2->6 cannot help because the binding memory is MedGemma's WEIGHTS, not activations. Unblock via deterministic per-sample captions or a 4-bit precompute path (the latter is what exp-e28b actually used).

Transfers to: [[hyp-e27-encoder-on-transitions-20260815]], [[e29-state-geometry-innocent-weights-guilty-20260815]]

## Notes
THE HYPOTHESIS IS UNTESTED, NOT REFUTED. Code, profile pair and preregistered gates all stand and are committed; only the GPU capacity to execute them is missing on this host.

WHAT THE CRASH NEVERTHELESS PROVED — the miner engaged inside the REAL training entrypoint, on the real corpus, under the real profile, and logged its supply before dying:
  'Phase B segment triplets ON: 49449 segments over 5837 windows, 3 states {neutral 43881, flow 3017, stress 2551}, 1004 transition windows (hard-negative supply), W=120 min'
The numbers differ from the offline audit (64005 segments / 6389 windows / 1129 transition windows) purely because the profile applies min_coverage=0.25 and min_hr_coverage=0.05, which the offline audit did not. So the plumbing is confirmed end-to-end; only training could not proceed.

MECHANISM: run_phase_b_orthogonalization builds the sensor stack FIRST, then calls _precompute_missing_text_embeddings (pipeline.py:~2207), which loads MedGemma while the stack already holds ~11.27 GiB. The text encoder never fits.

THREE REMEDIES TRIED, ALL FAILED, EACH INFORMATIVE:
1. Hardlinked 58559 existing embeddings into the new cache key (the documented scratch/link_text_caches.py trick). Run still reported '10150 missing'.
2. Wrote scratch/warm_text_cache.py to precompute with the GPU otherwise EMPTY. It completed and left 68712 files in the cache dir — yet the training run STILL reported '10150 missing'.
   ROOT CAUSE, and this is the durable finding: the cache key is `{raw_index}_{sha1(caption)}` and captions are generated with augmentation (SensorTextPairDataset(augment=True)) driven by an RNG, so caption text differs between processes and the sha1 never matches. THE FROZEN-EMBEDDING CACHE CANNOT BE PRE-WARMED ACROSS PROCESSES FOR AUGMENTED CAPTIONS. Any future 'just warm the cache first' plan on this repo will fail for the same reason unless captions are made deterministic (fixed per-sample seed) or the key drops the caption hash.
3. Reduced batch_size 25->8 with gradient_accumulation_steps 2->6 (effective batch 48 vs 50). Still OOM — the binding constraint is the MedGemma WEIGHTS, not activation memory, so batch reduction cannot help.

WHAT WOULD ACTUALLY UNBLOCK IT, in order of cost:
  (a) make captions deterministic per raw_index so the cache warms across processes, then run the warm script on a free GPU and let training hit a fully warm cache (never loads MedGemma at all);
  (b) load the text encoder in 4-bit for the precompute path (the profile already supports load_in_4bit; Phase B does not set it);
  (c) free the sensor stack before the precompute and rebuild it after — a pipeline ordering change;
  (d) external GPU with >=24 GiB (the CLAUDE.md note that GPU training needs external access applies here).
Option (a) or (b) is a modest code change and would make this arm runnable on the 5070.

#contrastive #collapse #epoch-resolution #baseline #control #dataset-whitelist #optihealth-eiv #matched-baseline #subject-heldout
