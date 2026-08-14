---
created_at: '2026-08-12T08:58:33+00:00'
gates:
- blocking: true
  description: 'eiv_supabase_floor_emission_rate for v7noatom < 0.10 (v5c same-seed
    baseline: 0.750). BLOCKING: if the floor persists after removing the atom, the
    mechanism attribution is wrong.'
  id: gate-floor-collapse
- blocking: true
  description: 'eiv_supabase_hr_raw_mae_bpm for v7noatom <= 6.14 (v4w''s dense-draw
    level, per Anton''s ''restore v4w level and better''). v5c same-seed baseline:
    20.83; trivial supabase-median baseline 4.02 on the same 200 rows.'
  id: gate-mae-v4w-level
- blocking: true
  description: 'Dense 128-row capture hr_raw MAE for v7noatom <= 4.5 (v5c: 3.07; allow
    drift but not a collapse; v4w''s 6.14 would be a fail).'
  id: gate-dense-no-regress
- blocking: true
  description: v7noatom manifest differs from v5c's only in expected fields (created_at,
    final_loss, adapter sha, profile name); same phase_b init sha, same pinned hr_buckets
    cut points 157.71853625752556/126.9999977820953, same dataset filter. Verified
    by manifest diff before any scoring is quoted.
  id: gate-single-variable
id: the-hr-raw-floor-emissions-on-the-deployment-distribution-are-caused-by-the-augm
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_floor_emission_rate
  direction: down
  magnitude_note: v5c same-seed baseline 0.750 -> <0.10. The atom is ~14.6% of training
    targets; removing it should leave no reason to emit exactly 30 on a population
    whose truth is never <45.
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 20.83 -> <=6.14 (gate bar). Escaped-floor conditional MAE is already
    3.65 on v5c, so if floor capture ends, unconditional MAE should approach 3.6-4.2.
    Falling only to ~10 would mean a second mechanism beyond the atom.
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: none
  magnitude_note: hr_peak saturated-prior problem is a separate defect; no strong
    prediction. Report but do not gate.
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
problem_tags: []
status: inconclusive
supersedes: null
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
---

## Statement
The hr_raw floor emissions on the deployment distribution are caused by the augmentation clamp's probability atom at the boundary, not by inability to read sparse supabase physiology. Retraining Phase C with the truncated-uniform draw (commit a71034c, profile lora_proxy_12ch_wl_noatom; seed 42, same v5c/phase_b init, same filter -- single variable is the code fix) will collapse the supabase floor-emission rate from 0.750 to <0.10 and bring supabase hr_raw MAE from 20.83 to <=6.14 (v4w's deployment level or better), without regressing the dense 128-row capture beyond the seed band.

## Rationale
Mechanism evidence: (1) ~14.6% of training hr_raw targets were literally '30.0' (scratch/floor_atom_estimate.py: clamp fires on 36.5% of augmented draws at prob 0.40); (2) deployment truth is never <45 bpm yet 60-90% of emissions are exactly '30'; (3) conditional on escaping the floor, all arms score near the trivial baseline (v5c 3.65 vs trivial 4.39), so the reading pathway is intact; (4) floor-capture rate orders inversely with arm deployment MAE across v4w/v5c/v5d, i.e. the atom explains the ranking reversal.

## Plan
Pipeline scratch/run_atomfix_retrain.sh (proc_9a60b5cd8903, queued behind the present-mean counterfactual): (1) Phase C retrain profile lora_proxy_12ch_wl_noatom (seed 42, init outputs/checkpoints/v5c/phase_b, single variable = commit a71034c truncated-uniform draw) -> outputs/checkpoints/v7noatom/phase_c; (2) p4 probe n=512; (3) 200-row supabase capture (same default_rng(0) draw as the baseline captures) -> outputs/probes/v7noatom_emissions_supabase.json; (4) dense 128-row capture -> outputs/probes/v7noatom_emissions_n128.json. Score with scratch/score_supabase_capture.py extended to v7noatom. Present-mean counterfactual (proc_f6ffac91272f) doubles as a mechanism control: predicted NOT to rescue supabase (floor is decode-side memorization, not pooling).

## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Predicts [[eiv_supabase_floor_emission_rate]] (down).
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_gen_peak_skill_bpm]] (none).
Gate `gate-floor-collapse` (blocking): eiv_supabase_floor_emission_rate for v7noatom < 0.10 (v5c same-seed baseline: 0.750). BLOCKING: if the floor persists after removing the atom, the mechanism attribution is wrong.
Gate `gate-mae-v4w-level` (blocking): eiv_supabase_hr_raw_mae_bpm for v7noatom <= 6.14 (v4w's dense-draw level, per Anton's 'restore v4w level and better'). v5c same-seed baseline: 20.83; trivial supabase-median baseline 4.02 on the same 200 rows.
Gate `gate-dense-no-regress` (blocking): Dense 128-row capture hr_raw MAE for v7noatom <= 4.5 (v5c: 3.07; allow drift but not a collapse; v4w's 6.14 would be a fail).
Gate `gate-single-variable` (blocking): v7noatom manifest differs from v5c's only in expected fields (created_at, final_loss, adapter sha, profile name); same phase_b init sha, same pinned hr_buckets cut points 157.71853625752556/126.9999977820953, same dataset filter. Verified by manifest diff before any scoring is quoted.

#deployment-distribution #optihealth #collapse #baseline
