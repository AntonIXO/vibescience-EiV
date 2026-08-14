---
created_at: '2026-08-05T21:31:55+00:00'
former_ids:
- the-hr-peak-signal-enters-the-lm-well-aligned-with-the-residual-stream-s-dominan
gates:
- blocking: true
  description: 'PRIMARY LOCALISATION GATE. Report signal-variance alignment (variance-weighted
    mean |corr(PC_i, hr_peak)| over the top 50 components) at every stage: S0_projector,
    S1_layer0_prefix, S2_L8, S2_L17, S2_L25, S3_L34. The verdict is decided by the
    SHAPE of the trajectory, and the branch is fixed in advance: (a) if S0 alignment
    is already LOW (<0.15) the projector emits an off-manifold low-energy signal and
    the fix class is UPSTREAM (whitening / variance-matching / vocab-embedding init);
    (b) if S0 is HIGH (>0.30) and alignment declines monotonically to S3, the LM''s
    own computation swamps the sensor signal and the fix class is CHANGE-WHAT-THE-LM-COMPUTES
    (zero-init gated cross-attention arXiv:2303.16199, or Q-Former two-stage alignment);
    (c) if alignment stays high everywhere while the rank sweep still needs k>=50,
    the signal is genuinely distributed and the framing must be revised rather than
    a sixth lever proposed. Committing to branch (b) before running.'
  diagnostic_id: null
  id: gate-alignment-trajectory
  mde: null
- blocking: true
  description: DONOR NULL AT EVERY k AT EVERY STAGE. The donor-prefix arm must never
    beat always-predict-median at any k at any stage. Donor-null failures produced
    false passes in E25 and E26, and at S0/S1 in particular a donor arm that gains
    skill would mean the pipeline is leaking sample identity rather than reading the
    prefix. Report donor_null_ok per stage.
  diagnostic_id: null
  id: gate-donor-null-all-stages
  mde: null
- blocking: true
  description: SHUFFLED-TARGET NULL AT EVERY k AT EVERY STAGE. Refit on a permuted
    training target; must never beat always-predict-median. Mandatory at high k where
    n_train=700 against d=2560 could overfit and manufacture an apparent trajectory.
    Report shuffled_null_ok per stage.
  diagnostic_id: null
  id: gate-shuffled-null-all-stages
  mde: null
- blocking: true
  description: 'ANCHOR REPRODUCTION. The S3_L34 stage must reproduce exp-e29''s numbers
    at that exact position: full-dimensional Ridge 10.270 bpm and k=3 Ridge 18.407
    bpm, within a tolerance of about 1.5 bpm (a fresh extraction, a possibly different
    retained-sample count and a re-drawn donor permutation all introduce small differences).
    If S3 does NOT reproduce e29, the extraction pipeline differs from e29''s and
    NO cross-stage comparison in this experiment is admissible -- the whole trajectory
    must then be discarded rather than interpreted.'
  diagnostic_id: null
  id: gate-anchor-reproduces-e29
  mde: null
- blocking: false
  description: 'APPARATUS BUG ALREADY CAUGHT AND FIXED -- record it so it is not reintroduced.
    The first n=12 smoke test returned NaN for every S1 spectrum because the sensor-block
    positions were being located with the `slot` tensor from _slot_mask_from_channel_mask,
    which indexes the 96 SOFT TOKENS rather than the assembled context; slicing the
    context with it silently selected constant prompt positions with zero variance
    across samples. Fixed by using token_type_ids (1=sensor, gemma_runtime.py:373)
    as the authoritative index. Verified on a second n=12 run: S1 alignment 0.4248
    now matches S0 alignment 0.4248 almost exactly, which is the expected sanity property
    since layer 0 at the sensor positions is essentially the injected prefix. Any
    future stage-wise probe in this repo must use token_type_ids, never the slot mask,
    to locate sensor positions in the context.'
  diagnostic_id: null
  id: gate-s1-apparatus-verified
  mde: null
- blocking: false
  description: 'SCOPE. Alignment is a summary statistic of a spectrum, not a causal
    measurement: a declining trajectory shows WHERE the signal loses relative energy,
    not that restoring energy would restore generation quality. Any fix proposed on
    the basis of this trajectory inherits the standing caveat that these are FITTED
    readouts on FROZEN features and bound what the representation permits, not what
    cross-entropy training would find. State this explicitly rather than presenting
    the localisation as a prescription.'
  diagnostic_id: null
  id: gate-scope-alignment-is-descriptive
  mde: null
id: the-hr-peak-signal-enters-the-lm-well-ba9dd9
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: down
  magnitude_note: 'PRIMARY. Predicting the signal-variance alignment DECLINES monotonically
    across the stack: high at S0_projector / S1_layer0_prefix, progressively lower
    through S2_L8 / S2_L17 / S2_L25, lowest at S3_L34 (the emission position measured
    by e29). Direction ''down'' refers to that stage-wise decline. Committed to branch
    (b) -- the LM''s computation progressively swamps the sensor signal -- BEFORE
    running. Concretely I expect S0 alignment above 0.30 and S3 alignment below 0.30,
    with the intermediate layers ordered between them. Anchor: S3 must reproduce e29''s
    full-dim 10.270 and k=3 18.407 within about 1.5 bpm, otherwise the whole trajectory
    is inadmissible. If instead S0 alignment is already low (<0.15), branch (a) holds,
    the projector is the culprit, and the cheap upstream fixes (whitening, vocab-embedding
    init) become the next arms rather than gated cross-attention.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak signal enters the LM well-aligned with the residual stream's dominant directions and is PROGRESSIVELY SWAMPED by text-driven variance as it passes through the frozen forward pass. Measuring signal-variance alignment -- the variance-weighted mean |corr(PC_i, hr_peak)| over the top principal components -- at the projector output (S0), at layer 0 on the sensor positions (S1), and at the hr_peak emission position across layers 8, 17, 25 and 34, the alignment will DECLINE MONOTONICALLY across the stack: high at S0/S1, lowest at S3_L34.

COMMITTED TO BRANCH (b) BEFORE RUNNING. Concretely: S0 alignment above 0.30 and S3_L34 alignment below 0.30, with intermediate layers ordered between them.

CONSEQUENCE IF CONFIRMED: the loss happens INSIDE the frozen forward pass, not at the projector. The fix class must change what the LM COMPUTES rather than renormalising what it is handed -- zero-init gated cross-attention (LLaMA-Adapter arXiv:2303.16199) or Q-Former-style two-stage contrastive alignment into the text subspace before the frozen LLM (BLIP-2). Cheap upstream fixes (whitening the projector output, initialising soft tokens from vocabulary embeddings) would then be predicted NOT to help, and should not be spent on.

CONSEQUENCE IF REFUTED VIA BRANCH (a) -- S0 alignment already low (<0.15): the projector emits an off-manifold low-energy signal from the start and the frozen LM is not the culprit. The cheap upstream fixes become the next arms and gated cross-attention is deprioritised. This is the outcome I consider less likely but it is the cheaper world, so the measurement is worth running either way.

CONSEQUENCE IF BRANCH (c) -- alignment high at every stage while the rank sweep still needs k>=50: the signal is distributed by design, the 'swamping' framing is wrong, and the problem needs re-framing rather than a sixth lever. I commit to saying so plainly rather than proposing another intervention.

SCOPE LIMITS. (1) Alignment is descriptive, not causal: a declining trajectory shows where the signal loses relative energy, not that restoring that energy would restore generation quality. (2) These remain FITTED readouts on FROZEN features -- they bound what the representation permits, not what end-to-end training would find. (3) S0 is the projector output mean-pooled over slots and S1 is layer 0 mean-pooled over sensor positions, so both are summaries; a per-slot analysis could differ and is not run here.

## Rationale
exp-e29 refuted the low-rank-bias hypothesis and in doing so produced the first structural mechanism in this problem: at the hr_peak emission position the hidden states are globally LOW-rank (PC1 47.82% of variance, participation ratio 3.92) but the peak signal is NOT in those dominant directions (corr(PC1, hr_peak) = +0.042, best of top-10 only -0.358). Ridge on the top 3 PCs scores 18.407 bpm against always-median 18.492 -- no better than a constant -- while full-dimensional Ridge reaches 10.270. The signal is real, per-sample (donor and shuffled arms flat at every k), and spread thinly across LOW-VARIANCE directions.

That one fact retrodicts the entire E24-E28 series: only coarse magnitude decodes; non-linear readouts LOSE to Ridge because they spend capacity along high-variance directions where there is no signal; no non-leading digit position beats its donor; bin accuracy collapses as bins get finer. It also explains the E20 measurement cos(prefix HR direction, LM native numeric direction) = -0.015.

But e29 measured ONE position at ONE layer, and explicitly recorded that limit in its own scope gate. It cannot distinguish two situations with DIFFERENT and differently-priced fixes:
  (a) the projector already emits a low-energy off-manifold signal -> fix is upstream and nearly free (whitening / variance-matching the projector output, or initialising soft tokens from real vocabulary embeddings)
  (b) the signal starts well-aligned and is progressively swamped by text-driven variance inside the forward pass -> fix must change what the LM COMPUTES (zero-init gated cross-attention, LLaMA-Adapter arXiv:2303.16199; or Q-Former-style two-stage contrastive alignment into the text subspace before the frozen LLM, BLIP-2 Li et al. 2023, which the literature review flagged as the most-replicated fix for exactly this orthogonality symptom)
Measuring the alignment trajectory across the stack discriminates them for one ~40-minute extraction pass and NO training. Doing this BEFORE committing to either fix is the whole point: both are cheap, but they are cheap in different places, and four levers have already died in this problem from acting before measuring.

APPARATUS NOTE: the first n=12 smoke test returned NaN for every S1 spectrum -- sensor positions were being located with the slot mask, which indexes the 96 soft tokens rather than the assembled context, so the slice silently returned constant prompt positions. Fixed to use token_type_ids (1=sensor). Re-verified at n=12: S1 alignment now equals S0 alignment to four decimals, the expected sanity property. Script scratch/probe_e30_variance_localisation.py, decision rule in its docstring, smoke-tested twice before the real run.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_peak_excursion_increment_r2]] (down).
Gate `gate-alignment-trajectory` (blocking): PRIMARY LOCALISATION GATE. Report signal-variance alignment (variance-weighted mean |corr(PC_i, hr_peak)| over the top 50 components) at every stage: S0_projector, S1_layer0_prefix, S2_L8, S2_L17, S2_L25, S3_L34. The verdict is decided by the SHAPE of the trajectory, and the branch is fixed in advance: (a) if S0 alignment is already LOW (<0.15) the projector emits an off-manifold low-energy signal and the fix class is UPSTREAM (whitening / variance-matching / vocab-embedding init); (b) if S0 is HIGH (>0.30) and alignment declines monotonically to S3, the LM's own computation swamps the sensor signal and the fix class is CHANGE-WHAT-THE-LM-COMPUTES (zero-init gated cross-attention arXiv:2303.16199, or Q-Former two-stage alignment); (c) if alignment stays high everywhere while the rank sweep still needs k>=50, the signal is genuinely distributed and the framing must be revised rather than a sixth lever proposed. Committing to branch (b) before running.
Gate `gate-donor-null-all-stages` (blocking): DONOR NULL AT EVERY k AT EVERY STAGE. The donor-prefix arm must never beat always-predict-median at any k at any stage. Donor-null failures produced false passes in E25 and E26, and at S0/S1 in particular a donor arm that gains skill would mean the pipeline is leaking sample identity rather than reading the prefix. Report donor_null_ok per stage.
Gate `gate-shuffled-null-all-stages` (blocking): SHUFFLED-TARGET NULL AT EVERY k AT EVERY STAGE. Refit on a permuted training target; must never beat always-predict-median. Mandatory at high k where n_train=700 against d=2560 could overfit and manufacture an apparent trajectory. Report shuffled_null_ok per stage.
Gate `gate-anchor-reproduces-e29` (blocking): ANCHOR REPRODUCTION. The S3_L34 stage must reproduce exp-e29's numbers at that exact position: full-dimensional Ridge 10.270 bpm and k=3 Ridge 18.407 bpm, within a tolerance of about 1.5 bpm (a fresh extraction, a possibly different retained-sample count and a re-drawn donor permutation all introduce small differences). If S3 does NOT reproduce e29, the extraction pipeline differs from e29's and NO cross-stage comparison in this experiment is admissible -- the whole trajectory must then be discarded rather than interpreted.
Gate `gate-s1-apparatus-verified` (advisory): APPARATUS BUG ALREADY CAUGHT AND FIXED -- record it so it is not reintroduced. The first n=12 smoke test returned NaN for every S1 spectrum because the sensor-block positions were being located with the `slot` tensor from _slot_mask_from_channel_mask, which indexes the 96 SOFT TOKENS rather than the assembled context; slicing the context with it silently selected constant prompt positions with zero variance across samples. Fixed by using token_type_ids (1=sensor, gemma_runtime.py:373) as the authoritative index. Verified on a second n=12 run: S1 alignment 0.4248 now matches S0 alignment 0.4248 almost exactly, which is the expected sanity property since layer 0 at the sensor positions is essentially the injected prefix. Any future stage-wise probe in this repo must use token_type_ids, never the slot mask, to locate sensor positions in the context.
Gate `gate-scope-alignment-is-descriptive` (advisory): SCOPE. Alignment is a summary statistic of a spectrum, not a causal measurement: a declining trajectory shows WHERE the signal loses relative energy, not that restoring energy would restore generation quality. Any fix proposed on the basis of this trajectory inherits the standing caveat that these are FITTED readouts on FROZEN features and bound what the representation permits, not what cross-entropy training would find. State this explicitly rather than presenting the localisation as a prescription.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
