---
created_at: '2026-08-18T23:03:16+00:00'
former_ids: []
gates:
- 'STEP 0 IS BLOCKING AND CPU-ONLY: if the linear-readout increment from raw HR patches
  to the excursion target does not exceed the untrained-encoder arm by more than the
  recipe band 0.0457 (eiv_peak_excursion_increment_r2 noise_sigma 0.02285, k=2), record
  supply-infeasible and STOP. Do not proceed to GPU.'
- 'Any GPU arm must preregister a mechanism gate on the JEPA side specifically: report
  predictor-output effective rank and the EMA-target/context cosine, because a JEPA
  that collapses (predictor learns a constant) trivially minimises its loss and will
  look converged while carrying nothing. Anti-collapse evidence is mandatory, not
  optional.'
- The comparison baseline is the RECIPE (phase_a on the current allowlist + corpus),
  not a stored checkpoint, and the arm must be scored on eiv_peak_excursion_increment_r2
  at the PRODUCTION stage (post channel_fusion+projector), never at the raw encoder
  output -- ro-dual-avg-max-pooling-downstream-20260805 measured a 4.3x understatement
  at the encoder stage.
- Report eiv_peak_vram_gib (<11.25 GiB on the RTX 5070) and eiv_training_runtime_sec.
hypothesis_id: null
id: jepa-as-a-phase-a-replacement-scoped-to-the-d4b761
notes:
- at: '2026-08-18T23:24:00+00:00'
  text: "[2026-08-18, ~40 min after queueing] THE 'DEAD' TABLE IN THE PROMPT ABOVE
    IS RF-CONDITIONAL. Read this before using that table to argue anything. See exp-e34
    (commit b95c64d), rule-out `the-function-class-account-of-exp-046-that-8-511840`.\n\nVarying
    ONLY the estimator on identical rows/folds/nulls inverts the ordering:\n  arm
    \               readout    AUC     null            over_null    z\n  raw_patch_grid_8
    \  RF      0.6309  0.5189+-0.0422     +0.1120   +2.66\n  raw_patch_grid_8   LINEAR
    \ 0.5855  0.5711+-0.0130     +0.0143   +1.10\n  enc_pca8           RF      0.4950
    \ 0.5442+-0.0293     -0.0492   -1.68\n  enc_pca8           LINEAR  0.5715  0.5310+-0.0143
    \    +0.0405   +2.83\nUnder a matched LINEAR readout the trained encoder CLEARS
    its own within-window null while the handcrafted features do not.\n\nDOES THIS
    REVIVE JEPA-FOR-STATE? NO, and the reasoning is worth stating so it is not re-argued.
    The rule-out `that-a-jepa-latent-prediction-pretraining-7061c5` rested on the
    RANDOM-INIT arm having zero headroom, and rand_pca8 was measured under RF (-0.0022,
    exactly its own null). Under a LINEAR readout the random-init arm was NOT measured,
    so the headroom argument is now formally RF-only. HOWEVER: (a) the linear effect
    on the trained encoder is +0.0405 (z+2.83) against a raw_shallow bound of 0.6430
    -- still weak, and no arm anywhere has closed that gap; (b) the state motivation
    was never this runbook's justification anyway. THIS RUNBOOK'S CASE IS UNCHANGED
    because it was scoped from the start to the caption-pair dependency and hr_peak
    representation, NOT to state.\n\nIF someone wants to reopen JEPA-for-state specifically,
    the honest cheap precondition is now: re-measure rand_pca8 and enc_pca8 under
    a LINEAR readout with their own nulls. If random-init still sits at its null under
    linear while trained clears it at z+2.83, training IS adding something and the
    'no headroom' argument genuinely weakens. That is ~10 minutes of CPU and it should
    be run BEFORE any GPU is spent on that motivation. It does not block this runbook.\n\nUNCHANGED
    HERE: the blocking CPU Step 0 (latent-vs-raw prediction premise test), the hr_peak
    bar (incumbent already reads R2 0.8084, increment +0.7211 at the production stage
    -- a HIGH bar), the anti-collapse instrumentation requirement (predictor effective
    rank + EMA/context cosine), and the recipe-not-checkpoint baseline rule."
- at: '2026-09-01T08:24:47+00:00'
  text: 'ABANDONED 2026-09-01 on a dead precondition, flagged by doctor(): the runbook
    sat `queued` while the problem it serves is `parked`. A queued runbook against
    a parked problem is the worst of both states -- it reads as live work to anyone
    scanning the queue, and it cannot be started without first re-opening a problem
    that was parked for cause.


    No judgement is made here on JEPA as a Phase A replacement. The plan is not refuted;
    it is unreachable from the current state. If the parent problem is ever re-opened
    on new evidence, this runbook should be re-registered against that re-opening
    rather than resurrected from this record -- its cost estimate and its comparators
    were written for a corpus and a Phase A recipe that have both since changed (the
    corpus is now [14041,1440,12] at merge f1b15e9e, and Phase A now carries the stratified-batches
    fix that healed the collapse this plan predates).'
priority: 20
problem_id: optihealth-contrastive-loss-20260727
status: abandoned
topic_tags: []
updated_at: '2026-09-01T08:24:47+00:00'
---

## Prompt
JEPA AS A PHASE-A REPLACEMENT, SCOPED TO THE AXIS THAT IS STILL ALIVE: hr_peak / excursion representation quality and the removal of the sensor-text caption dependency. This is a SELF-CONTAINED prompt; a fresh session has no context from the one that queued it.

=== WHAT IS ALREADY DEAD -- DO NOT RE-MOTIVATE THE ARM THIS WAY ===
rule_out `that-a-jepa-latent-prediction-pretraining-7061c5` (2026-08-18, exp-046) eliminated JEPA as a fix for MOMENTARY WITHIN-WINDOW STATE. On E29's transition rows (n=3226, 87 subjects, 660 windows, flow vs stress, W=120), with every arm against its own 24-perm within-window shuffle null:
    raw_patch_grid_8   8 feats   AUC 0.6309  over_null +0.1120  z +2.66
    enc_pca8           8 feats   AUC 0.4950  over_null -0.0492  z -1.68
    rand_pca8          8 feats   AUC 0.5442  over_null -0.0022  z -0.08   <- RANDOM INIT, inert
    prod_pca8          8 feats   AUC 0.4747  over_null -0.0807  z -3.08   <- production stage, WORST
A RANDOM-INIT encoder already sits exactly on its null, so a new pretraining OBJECTIVE has no headroom to win on that target. Do not resurrect the state motivation.

=== WHAT IS STILL GENUINELY OPEN (this runbook) ===
Two claims from JEPA.md that exp-046 did NOT touch:
  (A) DEPENDENCY. Phase A currently needs sensor-text caption pairs and a text encoder (medgemma-1.5-4b-it, text_encoder_pooling input_embeddings). JEPA needs neither -- it trains on signal structure alone. Even at PARITY on downstream metrics this is a real architectural win: it deletes the caption-quality confound that `eiv-dataset-provenance-shortcut-balacc` shows is severe (bal-acc 0.9945 vs chance 0.1250 on the incumbent 10-dataset allowlist).
  (B) REPRESENTATION. Whether latent prediction produces a better hr_peak/excursion representation than InfoNCE-on-HR-mean-captions. Note the incumbent is NOT weak here: at the production stage it already reads hr_peak at R2 0.8084, increment +0.7211 over knowing the true mean (ro-dual-avg-max-pooling-downstream-20260805). Beating that is the actual bar, and it is high.

=== STEP 0, BLOCKING, CPU-ONLY, DO THIS FIRST ===
Before any GPU: JEPA's premise is that latent prediction beats raw reconstruction because it refuses to spend capacity on noise. Test the PREMISE cheaply on this corpus, not the implementation. Fit a linear predictor from the encoder's own patch grid (patch_len=120, stride=60) mapping context patches -> held-out patch STATISTICS (mean/std/min/max), and compare its excursion-relevant increment against (i) predicting raw held-out SAMPLES and (ii) the untrained-encoder arm. If predicting latent-ish summaries carries no more excursion information than predicting raw values, JEPA's core claim does not hold on THIS data and the GPU arm is not justified -- record supply-infeasible and STOP. Cost: minutes.

=== IF STEP 0 PASSES ===
Minimal, surgical implementation (JEPA.md Variant 1): keep the existing TemporalEncoder as the context encoder, add an EMA copy as target encoder (stop-gradient), a small predictor MLP, mask 30-50% of temporal patches, SmoothL1 in latent space. Do NOT also change patching, channels, or the projector in the same arm -- one variable.

PRIOR ART, verified 2026-08-18 (real, do not re-derive): TS-JEPA arXiv:2509.25449 (public impl github.com/Sennadir/TS_JEPA, NO asserted license -- inspect, do not vendor); ECG-JEPA arXiv:2410.13867 (AUC 0.945 PTB-XL, >1M records, no hand-crafted augmentations); CGM-JEPA arXiv:2605.00933 (+6.5pp AUROC cohort generalization, ~389k readings/228 subjects, github.com/cruiseresearchgroup/CGM-JEPA, no asserted license); JETS (Empirical Health, 3M person-days, 63 channels).

MANDATORY ANTI-COLLAPSE INSTRUMENTATION: a JEPA whose predictor learns a constant minimises its loss perfectly and looks converged. Log predictor-output effective rank (eiv_effective_rank exists) and EMA-target/context cosine EVERY eval. A falling loss with collapsing rank is a FAILED run, not a converged one.

PREREGISTER with a mechanism gate before running. Score on eiv_peak_excursion_increment_r2 AT THE PRODUCTION STAGE. Compare against the RECIPE baseline, not a stored checkpoint.

## Preconditions
GPU free. Corpus data/canonical at 1-minute epochs. A Phase-B checkpoint of the CURRENT recipe available for the incumbent comparison. Read ro ro-dual-avg-max-pooling-downstream-20260805 and that-a-jepa-latent-prediction-pretraining-7061c5 BEFORE designing the arm.

## Log
- **2026-08-18T23:24:00+00:00** — [2026-08-18, ~40 min after queueing] THE 'DEAD' TABLE IN THE PROMPT ABOVE IS RF-CONDITIONAL. Read this before using that table to argue anything. See exp-e34 (commit b95c64d), rule-out `the-function-class-account-of-exp-046-that-8-511840`.

Varying ONLY the estimator on identical rows/folds/nulls inverts the ordering:
  arm                readout    AUC     null            over_null    z
  raw_patch_grid_8   RF      0.6309  0.5189+-0.0422     +0.1120   +2.66
  raw_patch_grid_8   LINEAR  0.5855  0.5711+-0.0130     +0.0143   +1.10
  enc_pca8           RF      0.4950  0.5442+-0.0293     -0.0492   -1.68
  enc_pca8           LINEAR  0.5715  0.5310+-0.0143     +0.0405   +2.83
Under a matched LINEAR readout the trained encoder CLEARS its own within-window null while the handcrafted features do not.

DOES THIS REVIVE JEPA-FOR-STATE? NO, and the reasoning is worth stating so it is not re-argued. The rule-out `that-a-jepa-latent-prediction-pretraining-7061c5` rested on the RANDOM-INIT arm having zero headroom, and rand_pca8 was measured under RF (-0.0022, exactly its own null). Under a LINEAR readout the random-init arm was NOT measured, so the headroom argument is now formally RF-only. HOWEVER: (a) the linear effect on the trained encoder is +0.0405 (z+2.83) against a raw_shallow bound of 0.6430 -- still weak, and no arm anywhere has closed that gap; (b) the state motivation was never this runbook's justification anyway. THIS RUNBOOK'S CASE IS UNCHANGED because it was scoped from the start to the caption-pair dependency and hr_peak representation, NOT to state.

IF someone wants to reopen JEPA-for-state specifically, the honest cheap precondition is now: re-measure rand_pca8 and enc_pca8 under a LINEAR readout with their own nulls. If random-init still sits at its null under linear while trained clears it at z+2.83, training IS adding something and the 'no headroom' argument genuinely weakens. That is ~10 minutes of CPU and it should be run BEFORE any GPU is spent on that motivation. It does not block this runbook.

UNCHANGED HERE: the blocking CPU Step 0 (latent-vs-raw prediction premise test), the hr_peak bar (incumbent already reads R2 0.8084, increment +0.7211 at the production stage -- a HIGH bar), the anti-collapse instrumentation requirement (predictor effective rank + EMA/context cosine), and the recipe-not-checkpoint baseline rule.
- **2026-09-01T08:24:47+00:00** — ABANDONED 2026-09-01 on a dead precondition, flagged by doctor(): the runbook sat `queued` while the problem it serves is `parked`. A queued runbook against a parked problem is the worst of both states -- it reads as live work to anyone scanning the queue, and it cannot be started without first re-opening a problem that was parked for cause.

No judgement is made here on JEPA as a Phase A replacement. The plan is not refuted; it is unreachable from the current state. If the parent problem is ever re-opened on new evidence, this runbook should be re-registered against that re-opening rather than resurrected from this record -- its cost estimate and its comparators were written for a corpus and a Phase A recipe that have both since changed (the corpus is now [14041,1440,12] at merge f1b15e9e, and Phase A now carries the stratified-batches fix that healed the collapse this plan predates).

## Links
Queued for problem [[optihealth-contrastive-loss-20260727]].
Intended gates: `STEP 0 IS BLOCKING AND CPU-ONLY: if the linear-readout increment from raw HR patches to the excursion target does not exceed the untrained-encoder arm by more than the recipe band 0.0457 (eiv_peak_excursion_increment_r2 noise_sigma 0.02285, k=2), record supply-infeasible and STOP. Do not proceed to GPU.`, `Any GPU arm must preregister a mechanism gate on the JEPA side specifically: report predictor-output effective rank and the EMA-target/context cosine, because a JEPA that collapses (predictor learns a constant) trivially minimises its loss and will look converged while carrying nothing. Anti-collapse evidence is mandatory, not optional.`, `The comparison baseline is the RECIPE (phase_a on the current allowlist + corpus), not a stored checkpoint, and the arm must be scored on eiv_peak_excursion_increment_r2 at the PRODUCTION stage (post channel_fusion+projector), never at the raw encoder output -- ro-dual-avg-max-pooling-downstream-20260805 measured a 4.3x understatement at the encoder stage.`, `Report eiv_peak_vram_gib (<11.25 GiB on the RTX 5070) and eiv_training_runtime_sec.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
