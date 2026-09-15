---
created_at: '2026-08-12T09:00:56+00:00'
former_ids: []
gates:
- 'T1 gate (blocking): after coverage-augmented Phase C retrain, hr_raw MAE on a synthetic
  sparse eval (dense lifesnaps days cropped to 2-6h contiguous windows, truth = window
  mean) improves >2x over the same checkpoint without coverage augmentation, AND the
  supabase 200-row capture does not regress vs the atom-fix arm.'
- 'T0 gate — MEASURED 2026-08-12, commit 1e2e2b9 (scratch/score_presentmean_counterfactual.py,
  paired n=200, ids+truth verified): present-mean fill does NOT rescue supabase, exactly
  as preregistered. v4w MAE 16.72->16.56 (-0.16 bpm), v5c 20.83->21.07 (+0.24); floor
  rate v4w 0.595->0.585, v5c 0.750->0.760; 133 and 154 of 200 rows byte-identical
  emissions. Of v5c''s 150 floored rows only 8 escaped and 10 new rows floored. Inference-time
  de-dilution is DEAD as a supabase lever; the atom fix (exp-atomfix-retrain-20260812)
  carries the load. T1 remains motivated by the ultra-low-coverage regime (sample
  10313), not by supabase.'
hypothesis_id: null
id: rb-dynamic-window-20260812
notes:
- at: '2026-08-20T21:20:23+00:00'
  text: '[2026-08-20] T1 IS NOW OWNED BY A PROPERLY-GATED HYPOTHESIS AND RUNBOOK.
    Do not execute T1 from this prompt. The extent problem was re-measured on corpus
    ac7d2b1c and the mechanism restated more precisely than this prompt''s original
    framing: the fill value 0.0 IS the corpus median under population normalisation,
    so unmasked pooling computes exactly naive = median + c*(present - median) (slope
    1.0000, intercept 0.00000, R2 1.00000, n=12503). The damage is a CONTRACTION of
    between-subject spread, not the ''~87% toward the median'' OFFSET this prompt
    describes -- and the offset framing is measurably misleading, because cohorts
    sitting near the corpus median (WESAD 74.7, CASE 71.5 vs median 73.0) post tiny
    mean gaps (5.8 and 4.9 bpm) while being MAXIMALLY compressed (variance ratio 0.0825
    and 0.0285). A mean-based instrument is blind here; see rule_out `that-the-mean-gap-naive-pooled-mean-present-5bb2e3`.


    NEW HOME: problem `extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling`;
    diagnostic `eiv_extent_variance_compression_ratio`; hypotheses h-w5 / h-w6 / h-w7
    / h-w8 / h-w9; runbooks w5 (priority 1, CPU-only BLOCKING gate), w6 (2), w7 (3
    -- this runbook''s T1), w8 (4).


    T0 REMAINS DEAD exactly as recorded here (present-mean imputation: v4w 16.72 ->
    16.56 bpm, v5c 20.83 -> 21.07, 133/200 emissions byte-identical). T2 (true variable-T,
    attention-masked padded patches, 2-3 days) is superseded in spirit by w6/w8, which
    obtain presence-awareness WITHOUT changing tensor width or the 23-token geometry
    -- a far smaller blast radius.


    This runbook stays QUEUED only as the historical source of the T0/T1/T2 ladder
    and the verified zero-fill mechanism note. Execute w5 first; it decides whether
    any of the rest is funded.'
- at: '2026-09-03T06:44:13+00:00'
  text: '2026-09-03 — still queued after 21 days, deliberately. The 2026-09-03 results
    moved the binding constraint away from window geometry: the held-out state target
    splits into lab (WESAD AUC 0.97) and free-living (LifeSnaps 0.47), and no encoder
    objective beats 15 handcrafted multi-scale features on held-out subjects. A dynamic
    window is only worth funding if the free-living cell shows any signal at the feature
    level first (hypothesis registered 2026-09-03 on the momentary-state problem).
    Re-prioritise after that reads.'
priority: 2
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
status: queued
topic_tags:
- deployment-distribution
- padding
- optihealth
updated_at: '2026-09-03T06:44:13+00:00'
---

## Prompt
DYNAMIC CONTEXT WINDOW (Anton, 2026-08-12): make the pipeline handle few-hour recordings instead of assuming 24h/1440. Mechanism already verified: encoder zero-fills masked cells in normalized space where zero = population median 71.8 bpm, so a day with coverage c pools to 71.8 + c*(truth-71.8) — a 3h recording is dragged ~87% toward the median (wearable_exam sample 10313: cov 0.129, predicted 77.7, emitted 82.0, ~91% of error explained). Ladder: T0 (running) present-mean fill at inference on population-normalized channels 0,1,10,11 — scratch/emission_diversity_audit.py --impute present_mean; T1 (main event, ~2h GPU) coverage augmentation in Phase C: crop dense training days to random contiguous 1-24h windows, zero-fill the rest, update M_mask, rebuild targets from the window — forces the with_presence_v1 flags to be read; encoder learns de-dilution instead of hardcoding it; T2 (only if T0/T1 insufficient, ~2-3d) true variable-T: attention-mask padded patches in TemporalPatching, keep projector cross-attention (already pools variable token counts to fixed slots), bucketed batching, annotate CLI accepts partial-day windows. Time encodings already position the window within the day. Constraint: any window/resolution change must be judged on eiv_peak_excursion_increment_r2, never raw R2-to-max (window-shortening trap recorded in that diagnostic).

## Preconditions
exp-atomfix-retrain-20260812 closed first: the floor atom dominates supabase error, so window work scored before the atom fix would be confounded. GPU free. Present-mean counterfactual results (outputs/probes/{v4w,v5c}_emissions_supabase_presentmean.json) read and recorded.

## Log
- **2026-08-20T21:20:23+00:00** — [2026-08-20] T1 IS NOW OWNED BY A PROPERLY-GATED HYPOTHESIS AND RUNBOOK. Do not execute T1 from this prompt. The extent problem was re-measured on corpus ac7d2b1c and the mechanism restated more precisely than this prompt's original framing: the fill value 0.0 IS the corpus median under population normalisation, so unmasked pooling computes exactly naive = median + c*(present - median) (slope 1.0000, intercept 0.00000, R2 1.00000, n=12503). The damage is a CONTRACTION of between-subject spread, not the '~87% toward the median' OFFSET this prompt describes -- and the offset framing is measurably misleading, because cohorts sitting near the corpus median (WESAD 74.7, CASE 71.5 vs median 73.0) post tiny mean gaps (5.8 and 4.9 bpm) while being MAXIMALLY compressed (variance ratio 0.0825 and 0.0285). A mean-based instrument is blind here; see rule_out `that-the-mean-gap-naive-pooled-mean-present-5bb2e3`.

NEW HOME: problem `extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling`; diagnostic `eiv_extent_variance_compression_ratio`; hypotheses h-w5 / h-w6 / h-w7 / h-w8 / h-w9; runbooks w5 (priority 1, CPU-only BLOCKING gate), w6 (2), w7 (3 -- this runbook's T1), w8 (4).

T0 REMAINS DEAD exactly as recorded here (present-mean imputation: v4w 16.72 -> 16.56 bpm, v5c 20.83 -> 21.07, 133/200 emissions byte-identical). T2 (true variable-T, attention-masked padded patches, 2-3 days) is superseded in spirit by w6/w8, which obtain presence-awareness WITHOUT changing tensor width or the 23-token geometry -- a far smaller blast radius.

This runbook stays QUEUED only as the historical source of the T0/T1/T2 ladder and the verified zero-fill mechanism note. Execute w5 first; it decides whether any of the rest is funded.
- **2026-09-03T06:44:13+00:00** — 2026-09-03 — still queued after 21 days, deliberately. The 2026-09-03 results moved the binding constraint away from window geometry: the held-out state target splits into lab (WESAD AUC 0.97) and free-living (LifeSnaps 0.47), and no encoder objective beats 15 handcrafted multi-scale features on held-out subjects. A dynamic window is only worth funding if the free-living cell shows any signal at the feature level first (hypothesis registered 2026-09-03 on the momentary-state problem). Re-prioritise after that reads.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Intended gates: `T1 gate (blocking): after coverage-augmented Phase C retrain, hr_raw MAE on a synthetic sparse eval (dense lifesnaps days cropped to 2-6h contiguous windows, truth = window mean) improves >2x over the same checkpoint without coverage augmentation, AND the supabase 200-row capture does not regress vs the atom-fix arm.`, `T0 gate — MEASURED 2026-08-12, commit 1e2e2b9 (scratch/score_presentmean_counterfactual.py, paired n=200, ids+truth verified): present-mean fill does NOT rescue supabase, exactly as preregistered. v4w MAE 16.72->16.56 (-0.16 bpm), v5c 20.83->21.07 (+0.24); floor rate v4w 0.595->0.585, v5c 0.750->0.760; 133 and 154 of 200 rows byte-identical emissions. Of v5c's 150 floored rows only 8 escaped and 10 new rows floored. Inference-time de-dilution is DEAD as a supabase lever; the atom fix (exp-atomfix-retrain-20260812) carries the load. T1 remains motivated by the ultra-low-coverage regime (sample 10313), not by supabase.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#deployment-distribution #padding #optihealth
