---
created_at: '2026-08-13T10:50:41+00:00'
former_ids: []
gates:
- blocking: true
  description: 'BLOCKING: median supabase hr_raw MAE over band-arm seeds 42/43/44
    < 9.49 bpm (the BEST v7noatom-family seed) on the frozen 200-row draw — beats
    the whole baseline band, not the 11.54 point.'
  diagnostic_id: null
  id: g1-median-mae-beats-best-baseline-seed
  mde: null
- blocking: true
  description: 'BLOCKING: floor-emission rate (exact-30) < 0.05 on ALL three seeds.'
  diagnostic_id: null
  id: g2-floor-below-0.05-all-seeds
  mde: null
- blocking: false
  description: dense-128 hr_raw MAE within 1.0 bpm of v7noatom's 2.89 on every seed
    (no dense regression).
  diagnostic_id: null
  id: g3-dense128-within-1bpm
  mde: null
- blocking: false
  description: 'P(emit < 45 bpm) < 0.10 on ALL seeds on the frozen supabase draw (v7noatom
    family: 0.575/0.595/0.335; truth is never <45).'
  diagnostic_id: null
  id: g4-sub45-emission-below-0.10-all-seeds
  mde: null
- blocking: false
  description: Seed band (max-min supabase MAE) reported regardless of size; shared-root-cause
    test keys off whether it lands < 6.29.
  diagnostic_id: null
  id: g5-band-reported
  mde: null
id: e1-band-mass-starvation-drives-supabase-mae-and-seed-band
interventions:
- eiv-hr-augment-deployment-band-mixture
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: median over 3 band-arm seeds < 8 bpm (blocking bar at 9.49 = best
    baseline seed; trivial 4.02 remains the target)
- diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
  magnitude_note: 'band < 6.29 bpm (v7noatom family). This is the shared-root-cause
    test: if MAE drops but the band does NOT shrink, crystallization has a cause other
    than band starvation.'
- diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
  magnitude_note: stays 0.000; the atom fix is structural and the band change must
    not resurrect it
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
problem_tags: []
status: confirmed
supersedes: null
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
---

## Statement
Phase-C deployment failure and seed instability share a root in the hr_raw TARGET LAW: augmentation injects a sub-45 bpm poison tail (region deployment truth never occupies) while starving the 45-65 bpm deployment band. Deleting the tail (aug_min_mean_bpm=45) and drawing ~half of augment target means from [45,65] (band_prob=0.5, hr_augment_prob 0.8) will cut median supabase MAE below the best v7noatom-family seed AND shrink the seed band.

## Rationale
E0 (exp-v7noatom-seed-band-20260813) measured: post-fix seeds crystallize arbitrary sub-45 prototypes (38.9/31.1/31.3/31.6 bpm — 43/44 just above the deleted 30 floor) and emit <45 on 33-60% of deployment rows while truth never is. Closed-form supply/demand (744bbd7): P(target in [45,65])=0.2017 vs demand 0.8290; P(target<45)=0.0729, almost entirely augmentation (aug tail 0.1774, natural 0.0032). The intervention edits the augment target law only. CPU supply gate (scratch/e1_supply_gate.py, real corpus + real draw law, commit 14e2ee4): P(band) 0.2039->0.5510, P(<45) 0.0719->0.0007, high tail preserved (P(mean>100) 0.092->0.109). GO.

## Plan
Profiles lora_proxy_12ch_wl_band{,_s43,_s44} (v9band, seeds 42/43/44), init outputs/checkpoints/v5c/phase_b, Phase C 2600 steps each (~2.5h on the 5070). Score each on the frozen 200-row supabase draw + dense 128 via scratch/emission_diversity_audit.py. Pipeline scratch/run_e1_band.sh -> logs/e1_band.log with per-stage *_RC= markers. Compare band-vs-band against v7noatom family {9.49, 11.54, 15.78}.

## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Applies [[eiv-hr-augment-deployment-band-mixture]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_supabase_seed_band_mae_bpm]] (down).
Predicts [[eiv_supabase_floor_emission_rate]] (none).
Gate `g1-median-mae-beats-best-baseline-seed` (blocking): BLOCKING: median supabase hr_raw MAE over band-arm seeds 42/43/44 < 9.49 bpm (the BEST v7noatom-family seed) on the frozen 200-row draw — beats the whole baseline band, not the 11.54 point.
Gate `g2-floor-below-0.05-all-seeds` (blocking): BLOCKING: floor-emission rate (exact-30) < 0.05 on ALL three seeds.
Gate `g3-dense128-within-1bpm` (advisory): dense-128 hr_raw MAE within 1.0 bpm of v7noatom's 2.89 on every seed (no dense regression).
Gate `g4-sub45-emission-below-0.10-all-seeds` (advisory): P(emit < 45 bpm) < 0.10 on ALL seeds on the frozen supabase draw (v7noatom family: 0.575/0.595/0.335; truth is never <45).
Gate `g5-band-reported` (advisory): Seed band (max-min supabase MAE) reported regardless of size; shared-root-cause test keys off whether it lands < 6.29.

#deployment-distribution #optihealth #collapse #baseline
