---
created_at: '2026-09-18T11:14:28+00:00'
former_ids: []
id: eiv-functional-sensor-interface-transfer
paper_refs:
- rep2text-2511-06571
- xbridge-2608-11676
- c2c-2510-03215
problem_tags:
- optihealth-eiv
- matched-baseline
status: open
target: null
title: Can a trained interface make a fixed MedGemma causally use physiological evidence?
topic_tags:
- cross-modal
- medgemma
- sensor-text
updated_at: '2026-09-22T09:13:24+00:00'
updates:
- at: '2026-09-18T11:57:23+00:00'
  text: 'PRE-FIT EXECUTION RECIPE: common teacher and inference corrections declared
    before any full fitting. # Common teacher and inference recipe — fixed before
    full training


    Campaign: sensor-transfer. Applies identically to L/P/E/M and both C seeds.

    No candidate training/evaluation results have been observed when this declaration
    was written.


    ## Teacher

    - Local original MedGemma `google/medgemma-1.5-4b-it` snapshot `91850547d9f0b2fdd21aa7c5f4f3d1a8a52c243b`.

    - Teacher inference is **nonquantized bfloat16 on CUDA, batch size 4**, frozen
    parameters; output cache arrays are stored float32. Actual model/tokenizer/config/shard
    hashes and per-vector receipts are recorded by the warmer.

    - This is an explicit new common source-target recipe, NOT an assertion of byte
    equivalence to historical fp32 targets. The historical artifacts are not the comparison
    controls.

    - Do not mix or reuse undocumented historical vectors. An audit found 212 identical
    caption filenames with conflicting vector bytes across compatible v2 caches; no
    existing vector was certified for reuse.

    - Preserve the actual two stage-specific text laws: A `hr_dispersion_labels`,
    corrected final-attended-token pooling, max length96; B `hierarchical`, input-embedding
    mean, max length256. Do not silently make B inherit A''s text law.

    - Only train-subject captions admitted by the actual source loaders are materialized.
    Use the established canonical corpus and population normalization; no remerge/refit.

    - Keep all original batch neighbours on a resumed partially-written teacher batch.
    Missing, changed or unproven byte receipts fail closed.


    ## Receiver prompt and persistence

    - All C arms use explicit `max_prompt_tokens=384`; evaluation uses the same budget.
    The unchanged instruction is318 tokens with the actual tokenizer. At the old256
    cap, training right-truncation and chat left-truncation selected different instructions.
    No candidate-specific prompt tuning is allowed.

    - Training targets sampled for the token-budget preflight were80–84 tokens; decode
    remains the preregistered greedy128 new-token cap. Full target/prompt token parity
    is validated before scientific fits.

    - Repeat research-time k-bit preparation when loading a research bundle so original
    embeddings/norms have the same precision as at training. This applies to all new
    research controls/treatments; old legacy LoRA bundles retain their old loader
    path.

    - `WEAVE_DISABLED=1` and strict wrapper guard `WEAVE_TRACE_DISABLED=1`; no patient
    vectors, text or rows uploaded. W&B records scalars offline because the local
    SDK currently receives HTTP403; unsynced is reported explicitly.


    ## Interpretation limits

    The donor intervention swaps the complete matched-slot sensor latent, which can
    change within-window missingness and clock content. It is NOT labeled a pure HR-value-only
    intervention. The metadata-only control separately holds the target''s clocks
    and masks fixed while removing varying physiological content. No numerical readout
    result is presented as stress diagnosis or forecasting.


    All differences between comparison arms remain the preregistered allocation package
    (L/P) or insertion depth alone (E/M). Source encoder/fusion/projector A/B seed42
    is shared; seed43 only retrains matched C consumers after discovery gates pass.


    Final recipe lock: /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/lock.json
    sha256=022c22d565d296c2c9227abb9438f3a926f0eca1723a69c090894eac063d6cbe; git=03b4671196d544e5400a5c888fdc76d2ae6d03ca.
    No new comparison outcomes have been observed.'
---

## Description
Residual research question, distinct from the resolved product formatter question. Existing soft-token + LoRA results do not test an untouched receiver with a task-trained connector, nor matched early-vs-middle residual memory. Historical v41 A/B subject filtering is not established: the documented YAML split was not forwarded by the CLI and Phase A persisted allowed_subject_count=null. This campaign first repairs and verifies all model-training rosters, then shares one fresh A/B tower across controlled C arms. Exact canonical normalization remains the established corpus-wide fit and is disclosed as transductive preprocessing. Numerical readout tests channel use, not stress diagnosis. Sources and ancestry are in this plan; source snapshots and protocol hashes must be attached before launch.

## Synthesis
Completed the preregistered 24-hour-bounded campaign in 5.98896 wall hours (2026-09-18); closeout and production CLI parity checks were performed separately on 2026-09-22, not another training allocation. Fresh A/B filtering and artifact audits passed; measured validation comprises 128 rows/22 subjects, sparse Supabase 64 rows/16 ordered four-window blocks from one already-seen person. H1 connector-only: refutes, val FI-MAE 3.9914→10.1498 bpm and Supabase 4.1932→14.9511. H2 layer0→17 memory: directional_only, val 24.0654→18.1574 but physiological content and deployment no-harm gates fail; metadata-only is better by 1.8406 bpm and donor direction only 0.15625. No recipe is promoted. Conditional H3 records eiv-transfer-h3-h1-confirmation and eiv-transfer-h3-h2-confirmation remain proposed but NOT REACHED in this campaign: discovery eligibility failed, so neither seed43 nor locked-test captures were generated; do not start them just because they remain proposed. Original v1 attempts were aborted for a pre-full-fit precision instrumentation correction, not scientific nulls. One C seed and one common source tower cannot establish recipe-wide effects; full-corpus normalization and historically exposed test subjects prevent an untouched-population claim. Whole-latent donor/zero/random controls are broad (including positional/time/missingness information), not isolated HR interventions; metadata-only preserves clocks/masks/codebook separately. eiv annotate on four locked sparse rows each for P42/L42 matches all eight saved native generation texts with user heads disabled. Current problem remains OPEN: input-space dimensional compatibility and live gradients did not suffice for functional numeric transfer. A genuinely changed source/interface or a targeted content-vs-format mechanism test would need a new preregistration; repeating the failed locked recipe or a seed lottery is not the next experiment. Artifacts: /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/; only aggregate W&B result published at https://wandb.ai/antonix-itmo/optihealth-eiv/runs/zh2hn6mz, full training histories remain offline/unsynced.

## Updates
- **2026-09-18T11:57:23+00:00** — PRE-FIT EXECUTION RECIPE: common teacher and inference corrections declared before any full fitting. # Common teacher and inference recipe — fixed before full training

Campaign: sensor-transfer. Applies identically to L/P/E/M and both C seeds.
No candidate training/evaluation results have been observed when this declaration was written.

## Teacher
- Local original MedGemma `google/medgemma-1.5-4b-it` snapshot `91850547d9f0b2fdd21aa7c5f4f3d1a8a52c243b`.
- Teacher inference is **nonquantized bfloat16 on CUDA, batch size 4**, frozen parameters; output cache arrays are stored float32. Actual model/tokenizer/config/shard hashes and per-vector receipts are recorded by the warmer.
- This is an explicit new common source-target recipe, NOT an assertion of byte equivalence to historical fp32 targets. The historical artifacts are not the comparison controls.
- Do not mix or reuse undocumented historical vectors. An audit found 212 identical caption filenames with conflicting vector bytes across compatible v2 caches; no existing vector was certified for reuse.
- Preserve the actual two stage-specific text laws: A `hr_dispersion_labels`, corrected final-attended-token pooling, max length96; B `hierarchical`, input-embedding mean, max length256. Do not silently make B inherit A's text law.
- Only train-subject captions admitted by the actual source loaders are materialized. Use the established canonical corpus and population normalization; no remerge/refit.
- Keep all original batch neighbours on a resumed partially-written teacher batch. Missing, changed or unproven byte receipts fail closed.

## Receiver prompt and persistence
- All C arms use explicit `max_prompt_tokens=384`; evaluation uses the same budget. The unchanged instruction is318 tokens with the actual tokenizer. At the old256 cap, training right-truncation and chat left-truncation selected different instructions. No candidate-specific prompt tuning is allowed.
- Training targets sampled for the token-budget preflight were80–84 tokens; decode remains the preregistered greedy128 new-token cap. Full target/prompt token parity is validated before scientific fits.
- Repeat research-time k-bit preparation when loading a research bundle so original embeddings/norms have the same precision as at training. This applies to all new research controls/treatments; old legacy LoRA bundles retain their old loader path.
- `WEAVE_DISABLED=1` and strict wrapper guard `WEAVE_TRACE_DISABLED=1`; no patient vectors, text or rows uploaded. W&B records scalars offline because the local SDK currently receives HTTP403; unsynced is reported explicitly.

## Interpretation limits
The donor intervention swaps the complete matched-slot sensor latent, which can change within-window missingness and clock content. It is NOT labeled a pure HR-value-only intervention. The metadata-only control separately holds the target's clocks and masks fixed while removing varying physiological content. No numerical readout result is presented as stress diagnosis or forecasting.

All differences between comparison arms remain the preregistered allocation package (L/P) or insertion depth alone (E/M). Source encoder/fusion/projector A/B seed42 is shared; seed43 only retrains matched C consumers after discovery gates pass.

Final recipe lock: /root/optiHealth-EiV/outputs/campaigns/sensor_transfer/lock.json sha256=022c22d565d296c2c9227abb9438f3a926f0eca1723a69c090894eac063d6cbe; git=03b4671196d544e5400a5c888fdc76d2ae6d03ca. No new comparison outcomes have been observed.

## Links
Cites [[rep2text-2511-06571]], [[xbridge-2608-11676]], [[c2c-2510-03215]].

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
