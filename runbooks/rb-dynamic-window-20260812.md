---
created_at: '2026-08-12T09:00:56+00:00'
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
priority: 2
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
status: queued
topic_tags:
- deployment-distribution
- padding
- optihealth
updated_at: '2026-08-12T09:19:52+00:00'
---

## Prompt
DYNAMIC CONTEXT WINDOW (Anton, 2026-08-12): make the pipeline handle few-hour recordings instead of assuming 24h/1440. Mechanism already verified: encoder zero-fills masked cells in normalized space where zero = population median 71.8 bpm, so a day with coverage c pools to 71.8 + c*(truth-71.8) — a 3h recording is dragged ~87% toward the median (wearable_exam sample 10313: cov 0.129, predicted 77.7, emitted 82.0, ~91% of error explained). Ladder: T0 (running) present-mean fill at inference on population-normalized channels 0,1,10,11 — scratch/emission_diversity_audit.py --impute present_mean; T1 (main event, ~2h GPU) coverage augmentation in Phase C: crop dense training days to random contiguous 1-24h windows, zero-fill the rest, update M_mask, rebuild targets from the window — forces the with_presence_v1 flags to be read; encoder learns de-dilution instead of hardcoding it; T2 (only if T0/T1 insufficient, ~2-3d) true variable-T: attention-mask padded patches in TemporalPatching, keep projector cross-attention (already pools variable token counts to fixed slots), bucketed batching, annotate CLI accepts partial-day windows. Time encodings already position the window within the day. Constraint: any window/resolution change must be judged on eiv_peak_excursion_increment_r2, never raw R2-to-max (window-shortening trap recorded in that diagnostic).

## Preconditions
exp-atomfix-retrain-20260812 closed first: the floor atom dominates supabase error, so window work scored before the atom fix would be confounded. GPU free. Present-mean counterfactual results (outputs/probes/{v4w,v5c}_emissions_supabase_presentmean.json) read and recorded.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Intended gates: `T1 gate (blocking): after coverage-augmented Phase C retrain, hr_raw MAE on a synthetic sparse eval (dense lifesnaps days cropped to 2-6h contiguous windows, truth = window mean) improves >2x over the same checkpoint without coverage augmentation, AND the supabase 200-row capture does not regress vs the atom-fix arm.`, `T0 gate — MEASURED 2026-08-12, commit 1e2e2b9 (scratch/score_presentmean_counterfactual.py, paired n=200, ids+truth verified): present-mean fill does NOT rescue supabase, exactly as preregistered. v4w MAE 16.72->16.56 (-0.16 bpm), v5c 20.83->21.07 (+0.24); floor rate v4w 0.595->0.585, v5c 0.750->0.760; 133 and 154 of 200 rows byte-identical emissions. Of v5c's 150 floored rows only 8 escaped and 10 new rows floored. Inference-time de-dilution is DEAD as a supabase lever; the atom fix (exp-atomfix-retrain-20260812) carries the load. T1 remains motivated by the ultra-low-coverage regime (sample 10313), not by supabase.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#deployment-distribution #padding #optihealth
