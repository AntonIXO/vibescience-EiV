---
artifacts: []
closed: true
config_note: "LOCKED BEFORE FITS: /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/lock.json
  sha256=022c22d565d296c2c9227abb9438f3a926f0eca1723a69c090894eac063d6cbe; protocolsha=9a32ed7ad81440c3b79e102b6d9ee05e198dbceade71445b112cecc0762f8bd9;
  profiles.yaml and all code/model/data files hashed therein. # Common teacher and
  inference recipe — fixed before full training\n\nCampaign: sensor-transfer. Applies
  identically to L/P/E/M and both C seeds.\nNo candidate training/evaluation results
  have been observed when this declaration was written.\n\n## Teacher\n- Local original
  MedGemma `google/medgemma-1.5-4b-it` snapshot `91850547d9f0b2fdd21aa7c5f4f3d1a8a52c243b`.\n-
  Teacher inference is **nonquantized bfloat16 on CUDA, batch size 4**, frozen parameters;
  output cache arrays are stored float32. Actual model/tokenizer/config/shard hashes
  and per-vector receipts are recorded by the warmer.\n- This is an explicit new common
  source-target recipe, NOT an assertion of byte equivalence to historical fp32 targets.
  The historical artifacts are not the comparison controls.\n- Do not mix or reuse
  undocumented historical vectors. An audit found 212 identical caption filenames
  with conflicting vector bytes across compatible v2 caches; no existing vector was
  certified for reuse.\n- Preserve the actual two stage-specific text laws: A `hr_dispersion_labels`,
  corrected final-attended-token pooling, max length96; B `hierarchical`, input-embedding
  mean, max length256. Do not silently make B inherit A's text law.\n- Only train-subject
  captions admitted by the actual source loaders are materialized. Use the established
  canonical corpus and population normalization; no remerge/refit.\n- Keep all original
  batch neighbours on a resumed partially-written teacher batch. Missing, changed
  or unproven byte receipts fail closed.\n\n## Receiver prompt and persistence\n-
  All C arms use explicit `max_prompt_tokens=384`; evaluation uses the same budget.
  The unchanged instruction is318 tokens with the actual tokenizer. At the old256
  cap, training right-truncation and chat left-truncation selected different instructions.
  No candidate-specific prompt tuning is allowed.\n- Training targets sampled for
  the token-budget preflight were80–84 tokens; decode remains the preregistered greedy128
  new-token cap. Full target/prompt token parity is validated before scientific fits.\n-
  Repeat research-time k-bit preparation when loading a research bundle so original
  embeddings/norms have the same precision as at training. This applies to all new
  research controls/treatments; old legacy LoRA bundles retain their old loader path.\n-
  `WEAVE_DISABLED=1` and strict wrapper guard `WEAVE_TRACE_DISABLED=1`; no patient
  vectors, text or rows uploaded. W&B records scalars offline because the local SDK
  currently receives HTTP403; unsynced is reported explicitly.\n\n## Interpretation
  limits\nThe donor intervention swaps the complete matched-slot sensor latent, which
  can change within-window missingness and clock content. It is NOT labeled a pure
  HR-value-only intervention. The metadata-only control separately holds the target's
  clocks and masks fixed while removing varying physiological content. No numerical
  readout result is presented as stress diagnosis or forecasting.\n\nAll differences
  between comparison arms remain the preregistered allocation package (L/P) or insertion
  depth alone (E/M). Source encoder/fusion/projector A/B seed42 is shared; seed43
  only retrains matched C consumers after discovery gates pass.\n Original24h service
  clock survives preflight resume. No scientific output seen. All-stage admitted rows
  independently validated. Evidence lives under /root/optiHealth-EiV/outputs/campaigns/sensor_transfer;
  source B fingerprint is created by the shared A/B and read back in each C artifact,
  not guessed in advance."
cost: null
crash_reason: 'Preflight instrumentation revision before full fits: sensor BF16 parameters
  were promoted to FP32 on serving load. CPU regression reproduced; fix64d3bb8 preserves
  research sensor dtype. No scientific fit/comparison or outcome exists. Infrastructure
  cache/smoke artifacts retained; original24h clock preserved. New v2 execution record
  follows under the same preregistered hypothesis and thresholds.'
created_at: '2026-09-18T11:57:24+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: local:/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/wandb (offline,
  not remotely synced)
former_ids: []
gate_results: []
git_ref: 03b4671196d544e5400a5c888fdc76d2ae6d03ca
hypothesis_id: eiv-transfer-h2-middle-memory
id: exp-eiv-transfer-h2-20260918
interpretation: null
key_numbers: {}
observed_effects: []
parent_experiment_id: exp-eiv-transfer-h1-20260918
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[eiv-transfer-h2-middle-memory]].
Branches from [[exp-eiv-transfer-h1-20260918]].
At commit `03b4671196d544e5400a5c888fdc76d2ae6d03ca`.
External run: local:/root/optiHealth-EiV/outputs/campaigns/sensor_transfer/wandb (offline, not remotely synced)

**Verdict:** crashed

**Crash:** Preflight instrumentation revision before full fits: sensor BF16 parameters were promoted to FP32 on serving load. CPU regression reproduced; fix64d3bb8 preserves research sensor dtype. No scientific fit/comparison or outcome exists. Infrastructure cache/smoke artifacts retained; original24h clock preserved. New v2 execution record follows under the same preregistered hypothesis and thresholds.

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
