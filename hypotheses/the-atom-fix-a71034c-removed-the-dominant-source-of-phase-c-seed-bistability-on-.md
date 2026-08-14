---
created_at: '2026-08-13T08:04:43+00:00'
gates:
- blocking: true
  description: 'Exact-30 emission rate < 0.05 on the frozen 200-row supabase draw
    for ALL of seeds 42/43/44. Blocking: one re-flooring seed falsifies the structural-fix
    account and revokes the baseline designation.'
  id: g1-all-seeds-floor-rate-below-0.05
- blocking: false
  description: eiv_supabase_seed_band_mae_bpm recorded with per-seed MAEs, floor rates,
    escaped/attractor structure, and the 4.02 trivial baseline, regardless of band
    size.
  id: g2-band-reported-with-per-seed-values
- blocking: false
  description: v7noatom seed 42 remains the named baseline checkpoint even if seed
    43 or 44 scores better; the band is the deliverable, not a winner.
  id: g3-no-best-seed-promotion
id: the-atom-fix-a71034c-removed-the-dominant-source-of-phase-c-seed-bistability-on-
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
  magnitude_note: 'Post-fix band < 6.0 bpm (vs pre-fix 11.37 measured at c62b983).
    Rationale: the 30-atom was the largest single degenerate mass (14.6% of targets)
    and each pre-fix seed differed mainly in how hard it snapped to it (floor rates
    0.21-0.75). With the atom gone, remaining seed variance comes from the low-band
    prototype choice (38.9-style attractors), which moves MAE less than full floor
    capture. If the band comes out >= 11.37 anyway, seed instability is NOT atom-driven
    and the family-band methodology becomes mandatory for every future arm at 3x cost.'
- diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
  magnitude_note: 'All three seeds < 0.05 exact-30 emissions. This is the STRUCTURAL
    claim behind the baseline designation: the fix removed the mechanism, so no seed
    should re-floor. A single seed >= 0.05 falsifies the structural account and revokes
    v7noatom''s baseline designation (blocking gate g1).'
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
problem_tags: []
status: confirmed
supersedes: null
topic_tags:
- deployment-distribution
- baseline
- control
- optihealth
- collapse
---

## Statement
The atom fix (a71034c) removed the dominant source of Phase-C seed bistability on the deployment distribution: the post-fix seed band of supabase hr_raw MAE (seeds 42/43/44 of lora_proxy_12ch_wl_noatom, same v5c/phase_b init) will be materially narrower than the pre-fix family's 11.37 bpm, and the floor-emission rate 0.000 will replicate structurally on all seeds.

## Rationale
v7noatom is designated deployment baseline by structure (floor 0.000, annotate-path panel 7.31 vs v4w 11.14) but its MAE 11.54 is one seed from a family whose pre-fix seed-only band was 11.37 bpm. Every queued arm (deployment-band mass E1, EOS supervision E2, dynamic window) gates against v7noatom; none can state a minimum detectable effect until this band exists.

## Plan
Train lora_proxy_12ch_wl_noatom_s43/_s44 (Phase C only, v5c/phase_b init, ~2.5h each on 5070), score each on the frozen 200-row supabase draw + dense 128, compute band across seeds 42 (existing v7noatom, not retrained) / 43 / 44. Gates: g1 blocking all-seeds floor < 0.05; g2 per-seed values reported; g3 no best-seed promotion.

## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Predicts [[eiv_supabase_seed_band_mae_bpm]] (down).
Predicts [[eiv_supabase_floor_emission_rate]] (none).
Gate `g1-all-seeds-floor-rate-below-0.05` (blocking): Exact-30 emission rate < 0.05 on the frozen 200-row supabase draw for ALL of seeds 42/43/44. Blocking: one re-flooring seed falsifies the structural-fix account and revokes the baseline designation.
Gate `g2-band-reported-with-per-seed-values` (advisory): eiv_supabase_seed_band_mae_bpm recorded with per-seed MAEs, floor rates, escaped/attractor structure, and the 4.02 trivial baseline, regardless of band size.
Gate `g3-no-best-seed-promotion` (advisory): v7noatom seed 42 remains the named baseline checkpoint even if seed 43 or 44 scores better; the band is the deliverable, not a winner.

#deployment-distribution #baseline #control #optihealth #collapse
