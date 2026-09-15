---
created_at: '2026-08-20T00:33:34+00:00'
former_ids: []
id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
paper_refs: []
problem_tags: []
status: resolved
target: null
title: New-corpus (12,547 @ 0.0) Phase A/B encoders produce deployment-broken Phase
  C adapters; old-corpus encoder healthy on the same draw
topic_tags:
- deployment-distribution
- collapse
- sensor-text
- epoch-resolution
updated_at: '2026-09-01T07:39:56+00:00'
updates:
- at: '2026-08-29T21:10:23+00:00'
  text: "2026-08-29 — THE SYMPTOM PERSISTS ON THE 13,518 CORPUS, measured, not inferred.
    The problem was written against the 12,547 @ 0.0 corpus (sha ac7d2b1c). The corpus
    has since been rebuilt to [13518,1440,12] (merge git_sha f82b082, normalisation
    sha 8498adeb->50e93f6c, +971 windows, dapper and k_emocon newly merged), and a
    full fresh A->B->C chain was run on it on 2026-08-24 (outputs/checkpoints/baseline_sync,
    adapter outputs/adapters/baseline_sync_c, logs/baseline_sync_full_run_report.json,
    all stages status=success).\n\nITS DEPLOYMENT CAPTURE WAS ONLY EVER TAKEN AT n=128,
    which is not the draw any baseline number in this vault is quoted on. Re-captured
    today at the standard n=200 supabase draw, batch 1, rng seed 0, same rebuilt eval_v3_1min
    bundle, RC=0, zero tracebacks, parse_ok 200/200 (logs/emission_baseline_sync_n200_supa.json):\n
    \ hr_raw  MAE 5.021 vs trivial 3.954 -> skill -1.066 bpm, corr 0.307, distinct
    8, top1 0.265\n  hr_peak MAE 23.648 vs trivial 17.410 -> skill -6.238 bpm, corr
    -0.039\nComparators on the IDENTICAL draw (v10conly family, 12,547 corpus): MAE
    2.836 / 3.538 / 2.738, corr 0.739 / 0.765 / 0.792, all skill-POSITIVE.\n\nHONEST
    STATUS: DIRECTIONAL, NOT ESTABLISHED, and it must not be quoted as a regression.
    baseline_sync differs from the family on FOUR variables at once — corpus (13518
    vs 12547), C-stage dataset_filter (6-ds {+big_ideas_glycemic, mmash, dreamt} vs
    the incumbent 3-ds), seed (42 vs 43/44/45), and A/B retrained rather than frozen
    off ab_ctrl_s43. The +2.19 bpm gap against the family median therefore has to
    be priced against the FULL-CHAIN re-seed band of 3.19 bpm (exp-052), not the 0.80
    bpm C-only band (exp-054), and 2.19 sits INSIDE 3.19.\n\nWHAT IS NEVERTHELESS
    NEW: this is the first arm on either corpus to land BELOW the supabase trivial
    baseline while every stage reported success, and corr 0.307 vs the family's 0.739-0.792
    is a collapse on an axis the 3.19 bpm band never described. Consistent with the
    original problem statement (fresh-chain encoders break C while the frozen-A/B
    family stays healthy), now re-observed one corpus later.\n\nDISCRIMINATING NEXT
    STEP, unchanged in spirit and now cheaper: the C-only design already exists. Run
    the band04 C recipe with the incumbent 3-dataset dict off the FROZEN baseline_sync
    phase_b at seeds 43/44/45 and score the same 200-row draw. That collapses four
    variables to one (corpus) against the 0.80 bpm C-only band, which is powered for
    a 2.19 bpm effect where the full-chain band is not. Until that runs, no new-corpus
    C-stage number is interpretable and v10conly (2.74-3.54, median 2.84) remains
    the only deployable recipe."
- at: '2026-08-30T07:21:45+00:00'
  text: '2026-08-30 — A FIFTH VARIABLE, MEASURED, THAT EVERY PRIOR FULL-CHAIN FAILURE
    ON THIS PROBLEM WAS CONFOUNDED BY: the C-stage ROSTER could not see most of the
    corpus it was blamed for.


    On the synced bundle [14092,1440,12] (merge git_sha b659855, 21 datasets), the
    designated baseline C profile `lora_proxy_12ch_wl_band04` admits only 7,592 of
    14,092 windows (53.9%) and drops 48.4% of ALL label-epochs. SIX label channels
    sit at EXACTLY ZERO reach under it: valence 0/40,893, arousal 0/6,457, protocol_condition
    0/10,650, exam_session_code 0/7,420, stress_raw 0/4,373, stimulus_class 0/1,230.
    Privileged EDA reaches 33.5%. Its 6-dataset roster predates POPANE s5/s6/s7, DAPPER
    and K-EmoCon entirely.


    WHY THIS MATTERS FOR THE PROBLEM AS STATED: every fresh chain recorded here (ab_ctrl,
    ab_wl, c_patch120, baseline_sync) was trained through a roster in that family.
    So ''the encoder trained on the new corpus is the variable'' was never cleanly
    isolated — the C stage was simultaneously training on roughly the OLD half of
    the corpus while its manifest advertised the new one. That is the same silent-mismatch
    class this vault has now paid for four times (W10 patch_presence fallback, E22
    mask inversion, and the two 2026-08-30 registry drops in labels.yaml / privileged.yaml).


    MEASUREMENT, not inference: scratch/_phasec_coverage_audit.py and scratch/_phasec_supervision_audit.py
    print the per-channel reach for any profile against the live bundle.


    NEW ARM UNDER TEST: hypothesis `a-full-a-b-c-retrain-whose-roster-actually-052f9a`,
    chain scratch/run_v11_chain.sh, profiles phase_a_12ch_v11full -> phase_b_12ch_v11full
    -> lora_proxy_12ch_v11full. Roster 19 datasets for A/B, 20 for C. Reach verified
    BEFORE launch: label-epochs 44.2% -> 94.7%, EDA 33.5% -> 100%, the six zero-reach
    channels all restored. dryad_ifh_affect stays excluded on its raw-text hr_peak
    mode collapse (19 -> 6 distinct values, top-1 0.271 -> 0.604), nurse_stress excluded
    from A/B only on its shift-schedule state labels.


    UNTIL THAT ARM CLOSES, the problem''s own framing should be read with this caveat
    attached: the roster confound is a live alternative explanation for every fresh-chain
    failure recorded on this problem, and it was removable all along.'
- at: '2026-09-01T07:39:56+00:00'
  text: 'CLOSED 2026-09-01. The synthesis has said RESOLVED since 2026-08-31 05:37
    and the record stayed open for a day; leaving it so hands a future session an
    active-mystery framing for a question that has a confirmed answer (3 confirmed
    hypotheses, exp-v25r30 SUPPORTS with both gates passing).


    WHAT THE FOLLOW-ON CAMPAIGN ADDED, and none of it reopens the diagnosis:


    1. THE eff_rank GATE NOW HAS A MEASURED NOISE FLOOR. It ran this whole campaign
    unfloored. Measured 2026-09-01 with the same probe core, 8 draw seeds on 5 checkpoints:
    pooled finite-draw sigma 0.076, k=2 band 0.153 (scratch/_eff_rank_noise_floor.py,
    logs/eff_rank_noise_floor.json). Re-reading the campaign''s own decisions against
    it: v29 was killed at 6.783 against a 7.0 gate, a margin of 0.217 = 2.84 sigma,
    so that kill CLEARS k=2 and stands. The diagnostic is registered with the floor.


    2. LAW (a) IS NARROWER THAN IT READS. ''The geometry probe gates Phase A'' is
    sound as a VETO -- v29 at 6.783 was genuinely broken -- but rank is now disconfirmed
    five ways as a DIRECTION: v31 posted the highest rank ever measured here (10.106)
    and lost deployment outright, while v34 posted the campaign''s best supabase numbers
    at 5.414, BELOW the very threshold that condemned v29. Anyone reading law (a)
    as ''raise rank to improve the model'' will spend GPU for nothing.


    3. THE OPEN THREAD LISTED HERE AS ''hr_peak weak everywhere (corr ~0.11)'' IS
    NO LONGER OPEN, and it was not solved by anything in this problem''s space. It
    was solved by changing what the CAPTION says: v33/v34 (caption_style hr_dispersion)
    took supabase hr_peak from skill -4.422 to +1.011 with eff_rank flat, and v36
    (labels in the caption) reached hr_raw 1.862 vs the incumbent''s 3.030. The mechanism
    is that InfoNCE keeps only what the two views share, so a level-only text view
    teaches level and discards spread by construction. That work lives on the deployment
    problem.


    4. THE OTHER OPEN THREAD, ''label-head supervision costs rank at 0.3 weights --
    detached probes or per-head bisection'', IS ALSO ANSWERED AND NEGATIVELY. Detached
    probes were built and run: v31 (all heads detached) and v32 (incumbent + detach
    only, one key) both RESCUED geometry (10.106 / 9.358) and both DESTROYED deployment
    (dense MAE 7.312 / 9.006 against the incumbent''s 2.851). Two independent arms,
    same failure, so it is the detach and not the head set. The head route for utilisation
    is closed.


    The healed recipe and its guards (tests/test_stratified_batches_guard.py, the
    batch-30 cap) are unchanged and still in force.'
---

## Description
Every A→B→C chain whose Phase A/B encoder was trained on the 12,547-window min_coverage-0.0 corpus (sha ac7d2b1c) produces a deployment-broken Phase C, while the SAME C recipe off an old-corpus encoder is healthy on the SAME rebuilt eval draw. Measured 2026-08-19/20, four fresh chains vs one anchor, all supabase-200 batch-1 captures on the rebuilt data/eval_v3_1min (trivial 3.95):
  v9band04_s42 (v5c old-corpus encoder + band04 C): MAE 3.044, skill +0.91, corr 0.68 — HEALTHY
  ab_ctrl   (new-corpus 10-ds A/B + 3-ds C, band04): MAE 6.03, corr 0.357, top1 0.44
  ab_wl     (new-corpus 6-ds wl A/B/C, band04):      MAE 20.28, corr 0.282
  c_patch120_s42/s43 (analog state A/B + wl_noatom C): MAE 23.6/24.6, 35.1/32.5-value attractors
State slot collapses to a prior in every fresh chain (200/200 neutral or 198/200 stress; never flow).
The C recipe is exonerated (band04 law held everywhere); the corpus tensor is exonerated (v9band04 reads the same eval rows fine); the variable is the ENCODER/PROJECTOR TRAINED ON THE NEW CORPUS. Candidate mechanisms, none yet isolated: (a) Phase A/B InfoNCE pool now includes the 974 min_coverage<0.1 sparse windows (analog arms gate at 0.0; ab arms inherited profile floors — CHECK per-profile realized pools); (b) captions regenerated on the new corpus differ numerically → text targets shifted; (c) text-embedding caches rebuilt this session — byte-equivalence with the v5c-era cache never verified; (d) population_stats refit (shifts ≤0.63%) interacting with denormalised caption values; (e) POPANE/CASE windows (255 new) entering the A/B pool with 41-min sessions in 1440-epoch windows. DISCRIMINATING NEXT EXPERIMENT (cheapest first): retrain ONE Phase A with the old corpus's exact profile on the new tensor but min_coverage 0.1 (drops the 974) → B → C; if healthy, (a) is the mechanism; if broken, diff captions/caches next. Until resolved, NO C-stage experiment on a new-corpus encoder is interpretable and v9band04 remains the only deployable adapter.

## Synthesis
RESOLVED 2026-08-31 05:37 (exp-v25r30, verdict SUPPORTS, both gates PASS; 13 experiments closed in the 24h campaign).

THE ANSWER, COMPLETE: the constant-emitter collapse had TWO anchor-starvation defects, one per contrastive stage, both introduced when the v11-era profiles were rewritten around the new-corpus whitelist:
 1. PHASE A lost `stratified_batches` (92 -> 16 anchors/step; 184k -> 32k total). Fix: restore the key (exp-v20, eff_rank 4.47 -> 7.44). Detectable by the geometry probe.
 2. PHASE B ran at batch 25 (50 pairs), below the steep dose-response threshold. Fix: batch 30 = VRAM max on the 5070 (exp-v25r30). NOT detectable by rank (fresh Bs inflate rank while degrading C) -- only a deployment capture catches it.
BOTH fixes necessary, NEITHER sufficient: v21 (A fixed, B@25) collapsed; v23 (A fixed, no B) was only near-healthy; A+B fixed = healed.

THE HEALED RECIPE (baseline candidate, pending Anton's designation): phase_a_12ch_v20strat -> phase_b_12ch_v25bigbatch(batch 30) -> lora_proxy_12ch_wl_band04, on the ddb7498 [14041] bundle, 19-ds roster. Measured: supabase MAE 3.203, skill +0.751 (first positive since the rebuild), corr 0.717, top1 0.410; dense 7.388, corr 0.657. Inside the v10conly family MAE band (2.74-3.54) at single seed 42, on 2.3x the roster.

GUARDS IN CODE: tests/test_stratified_batches_guard.py (commit 767ebd8). B batch cap 30 documented in do_not_retry; further anchor scaling via DHEL denominator or grad-cache (paper-small-batch-anticollapse-survey-2026).

DIAGNOSTIC LAWS THE CAMPAIGN BOUGHT: (a) eff_rank/cos_p95 geometry probe (3 min, CPU) gates Phase A -- validated across 68 checkpoints; (b) B damage is rank-INVISIBLE, gate B with a deployment capture; (c) faster-than-usual training or lower-than-usual loss on a contrastive stage is a red flag (the broken A was 5.7x faster with lower loss); (d) never read MAE without corr+top1_mass.

OPEN THREADS (moved to the day agenda, not this problem): hr_peak weak everywhere (corr ~0.11, the OLD saturated-prior problem); label-head supervision costs rank at 0.3 weights (exp-v22) -- detached probes or per-head bisection; the dense residual (7.39 vs 6.99 trivial).

## Updates
- **2026-08-29T21:10:23+00:00** — 2026-08-29 — THE SYMPTOM PERSISTS ON THE 13,518 CORPUS, measured, not inferred. The problem was written against the 12,547 @ 0.0 corpus (sha ac7d2b1c). The corpus has since been rebuilt to [13518,1440,12] (merge git_sha f82b082, normalisation sha 8498adeb->50e93f6c, +971 windows, dapper and k_emocon newly merged), and a full fresh A->B->C chain was run on it on 2026-08-24 (outputs/checkpoints/baseline_sync, adapter outputs/adapters/baseline_sync_c, logs/baseline_sync_full_run_report.json, all stages status=success).

ITS DEPLOYMENT CAPTURE WAS ONLY EVER TAKEN AT n=128, which is not the draw any baseline number in this vault is quoted on. Re-captured today at the standard n=200 supabase draw, batch 1, rng seed 0, same rebuilt eval_v3_1min bundle, RC=0, zero tracebacks, parse_ok 200/200 (logs/emission_baseline_sync_n200_supa.json):
  hr_raw  MAE 5.021 vs trivial 3.954 -> skill -1.066 bpm, corr 0.307, distinct 8, top1 0.265
  hr_peak MAE 23.648 vs trivial 17.410 -> skill -6.238 bpm, corr -0.039
Comparators on the IDENTICAL draw (v10conly family, 12,547 corpus): MAE 2.836 / 3.538 / 2.738, corr 0.739 / 0.765 / 0.792, all skill-POSITIVE.

HONEST STATUS: DIRECTIONAL, NOT ESTABLISHED, and it must not be quoted as a regression. baseline_sync differs from the family on FOUR variables at once — corpus (13518 vs 12547), C-stage dataset_filter (6-ds {+big_ideas_glycemic, mmash, dreamt} vs the incumbent 3-ds), seed (42 vs 43/44/45), and A/B retrained rather than frozen off ab_ctrl_s43. The +2.19 bpm gap against the family median therefore has to be priced against the FULL-CHAIN re-seed band of 3.19 bpm (exp-052), not the 0.80 bpm C-only band (exp-054), and 2.19 sits INSIDE 3.19.

WHAT IS NEVERTHELESS NEW: this is the first arm on either corpus to land BELOW the supabase trivial baseline while every stage reported success, and corr 0.307 vs the family's 0.739-0.792 is a collapse on an axis the 3.19 bpm band never described. Consistent with the original problem statement (fresh-chain encoders break C while the frozen-A/B family stays healthy), now re-observed one corpus later.

DISCRIMINATING NEXT STEP, unchanged in spirit and now cheaper: the C-only design already exists. Run the band04 C recipe with the incumbent 3-dataset dict off the FROZEN baseline_sync phase_b at seeds 43/44/45 and score the same 200-row draw. That collapses four variables to one (corpus) against the 0.80 bpm C-only band, which is powered for a 2.19 bpm effect where the full-chain band is not. Until that runs, no new-corpus C-stage number is interpretable and v10conly (2.74-3.54, median 2.84) remains the only deployable recipe.
- **2026-08-30T07:21:45+00:00** — 2026-08-30 — A FIFTH VARIABLE, MEASURED, THAT EVERY PRIOR FULL-CHAIN FAILURE ON THIS PROBLEM WAS CONFOUNDED BY: the C-stage ROSTER could not see most of the corpus it was blamed for.

On the synced bundle [14092,1440,12] (merge git_sha b659855, 21 datasets), the designated baseline C profile `lora_proxy_12ch_wl_band04` admits only 7,592 of 14,092 windows (53.9%) and drops 48.4% of ALL label-epochs. SIX label channels sit at EXACTLY ZERO reach under it: valence 0/40,893, arousal 0/6,457, protocol_condition 0/10,650, exam_session_code 0/7,420, stress_raw 0/4,373, stimulus_class 0/1,230. Privileged EDA reaches 33.5%. Its 6-dataset roster predates POPANE s5/s6/s7, DAPPER and K-EmoCon entirely.

WHY THIS MATTERS FOR THE PROBLEM AS STATED: every fresh chain recorded here (ab_ctrl, ab_wl, c_patch120, baseline_sync) was trained through a roster in that family. So 'the encoder trained on the new corpus is the variable' was never cleanly isolated — the C stage was simultaneously training on roughly the OLD half of the corpus while its manifest advertised the new one. That is the same silent-mismatch class this vault has now paid for four times (W10 patch_presence fallback, E22 mask inversion, and the two 2026-08-30 registry drops in labels.yaml / privileged.yaml).

MEASUREMENT, not inference: scratch/_phasec_coverage_audit.py and scratch/_phasec_supervision_audit.py print the per-channel reach for any profile against the live bundle.

NEW ARM UNDER TEST: hypothesis `a-full-a-b-c-retrain-whose-roster-actually-052f9a`, chain scratch/run_v11_chain.sh, profiles phase_a_12ch_v11full -> phase_b_12ch_v11full -> lora_proxy_12ch_v11full. Roster 19 datasets for A/B, 20 for C. Reach verified BEFORE launch: label-epochs 44.2% -> 94.7%, EDA 33.5% -> 100%, the six zero-reach channels all restored. dryad_ifh_affect stays excluded on its raw-text hr_peak mode collapse (19 -> 6 distinct values, top-1 0.271 -> 0.604), nurse_stress excluded from A/B only on its shift-schedule state labels.

UNTIL THAT ARM CLOSES, the problem's own framing should be read with this caveat attached: the roster confound is a live alternative explanation for every fresh-chain failure recorded on this problem, and it was removable all along.
- **2026-09-01T07:39:56+00:00** — CLOSED 2026-09-01. The synthesis has said RESOLVED since 2026-08-31 05:37 and the record stayed open for a day; leaving it so hands a future session an active-mystery framing for a question that has a confirmed answer (3 confirmed hypotheses, exp-v25r30 SUPPORTS with both gates passing).

WHAT THE FOLLOW-ON CAMPAIGN ADDED, and none of it reopens the diagnosis:

1. THE eff_rank GATE NOW HAS A MEASURED NOISE FLOOR. It ran this whole campaign unfloored. Measured 2026-09-01 with the same probe core, 8 draw seeds on 5 checkpoints: pooled finite-draw sigma 0.076, k=2 band 0.153 (scratch/_eff_rank_noise_floor.py, logs/eff_rank_noise_floor.json). Re-reading the campaign's own decisions against it: v29 was killed at 6.783 against a 7.0 gate, a margin of 0.217 = 2.84 sigma, so that kill CLEARS k=2 and stands. The diagnostic is registered with the floor.

2. LAW (a) IS NARROWER THAN IT READS. 'The geometry probe gates Phase A' is sound as a VETO -- v29 at 6.783 was genuinely broken -- but rank is now disconfirmed five ways as a DIRECTION: v31 posted the highest rank ever measured here (10.106) and lost deployment outright, while v34 posted the campaign's best supabase numbers at 5.414, BELOW the very threshold that condemned v29. Anyone reading law (a) as 'raise rank to improve the model' will spend GPU for nothing.

3. THE OPEN THREAD LISTED HERE AS 'hr_peak weak everywhere (corr ~0.11)' IS NO LONGER OPEN, and it was not solved by anything in this problem's space. It was solved by changing what the CAPTION says: v33/v34 (caption_style hr_dispersion) took supabase hr_peak from skill -4.422 to +1.011 with eff_rank flat, and v36 (labels in the caption) reached hr_raw 1.862 vs the incumbent's 3.030. The mechanism is that InfoNCE keeps only what the two views share, so a level-only text view teaches level and discards spread by construction. That work lives on the deployment problem.

4. THE OTHER OPEN THREAD, 'label-head supervision costs rank at 0.3 weights -- detached probes or per-head bisection', IS ALSO ANSWERED AND NEGATIVELY. Detached probes were built and run: v31 (all heads detached) and v32 (incumbent + detach only, one key) both RESCUED geometry (10.106 / 9.358) and both DESTROYED deployment (dense MAE 7.312 / 9.006 against the incumbent's 2.851). Two independent arms, same failure, so it is the detach and not the head set. The head route for utilisation is closed.

The healed recipe and its guards (tests/test_stratified_batches_guard.py, the batch-30 cap) are unchanged and still in force.

#deployment-distribution #collapse #sensor-text #epoch-resolution
