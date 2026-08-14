---
created_at: '2026-08-12T11:26:40+00:00'
former_ids:
- v4w-s-genuine-deployment-advantage-paired-supabase-mae-4-20-vs-v7noatom-8-60-on-
gates:
- blocking: true
  description: 'Paired 200-row supabase capture (default_rng(0) draw): v8basin hr_raw
    MAE <= 8.0 (v7noatom 11.54). Stretch: <=6.14 passes the original deployment bar.'
  diagnostic_id: null
  id: gate-v8-mae-beats-v7
  mde: null
- blocking: false
  description: 'Top emitted hr_raw value share on the supabase capture < 0.40 (v7noatom:
    0.555 at 38.9). Tests low-band RESOLUTION, not just level.'
  diagnostic_id: null
  id: gate-v8-attractor-mass
  mde: null
- blocking: true
  description: Dense 128-row capture hr_raw MAE <= 4.5 (v7noatom 2.89, v5c 3.07).
  diagnostic_id: null
  id: gate-v8-dense-no-regress
  mde: null
- blocking: true
  description: Manifest diff vs v7noatom limited to encoder checkpoint provenance
    (+ loss/seed/timestamps); hr_buckets, data_paths, dataset_filter byte-identical.
  diagnostic_id: null
  id: gate-v8-single-variable
  mde: null
id: v4w-s-genuine-deployment-advantage-9d4fbf
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 11.54 -> <=8 predicted; <=6.14 passes the original v4w-level bar.
    Baseline trivial 4.02. Report with floor rate and escaped-floor conditional MAE
    per diagnostic contract.
- diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
  magnitude_note: Must stay ~0 (<0.02); atom fix is in the code, not the init. Any
    exact-30 resurgence indicts the basin, not the draw.
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
problem_tags: []
status: refuted
supersedes: null
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
---

## Statement
v4w's genuine deployment advantage (paired supabase: MAE 4.20 vs v7noatom 8.60 on the 81 rows v4w reads, wins 58/23) lives in its Phase-B basin geometry, not in its Phase-C underfit. Rerunning Phase C with the atom fix (a71034c) initialized from v4w/phase_b instead of v5c/phase_b (v8basin; single variable = encoder checkpoint) transfers the advantage: supabase MAE drops below v7noatom's 11.54 and low-band prototype mass falls, with no dense regression.

## Rationale


## Plan


## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_supabase_floor_emission_rate]] (none).
Gate `gate-v8-mae-beats-v7` (blocking): Paired 200-row supabase capture (default_rng(0) draw): v8basin hr_raw MAE <= 8.0 (v7noatom 11.54). Stretch: <=6.14 passes the original deployment bar.
Gate `gate-v8-attractor-mass` (advisory): Top emitted hr_raw value share on the supabase capture < 0.40 (v7noatom: 0.555 at 38.9). Tests low-band RESOLUTION, not just level.
Gate `gate-v8-dense-no-regress` (blocking): Dense 128-row capture hr_raw MAE <= 4.5 (v7noatom 2.89, v5c 3.07).
Gate `gate-v8-single-variable` (blocking): Manifest diff vs v7noatom limited to encoder checkpoint provenance (+ loss/seed/timestamps); hr_buckets, data_paths, dataset_filter byte-identical.

#deployment-distribution #optihealth #collapse #baseline
