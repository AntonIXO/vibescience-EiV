---
created_at: '2026-08-13T16:53:30+00:00'
gates:
- blocking: true
  description: 'BLOCKING: dense-128 MAE <= 3.89 (within 1 bpm of v7noatom''s 2.89)
    on EVERY seed.'
  id: g1-dense-recovered-all-seeds
- blocking: true
  description: 'BLOCKING: median supabase MAE over seeds 42/43/44 < 4.5 on the frozen
    200-row draw (holds most of E1''s win).'
  id: g2-supabase-median-below-4.5
- blocking: false
  description: Floor 0 and P(emit<45) < 0.10 on all seeds; seed band reported (v9band
    was 1.31; snap-back toward 6.29 indicts augment intensity as the band stabilizer).
  id: g3-structural-invariants-and-band
id: e1b-prob08-not-band-law-owns-the-dense-cost
interventions:
- eiv-hr-augment-deployment-band-mixture
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'PRIMARY: worst seed 5.65 -> <= 3.89 (within 1 bpm of v7noatom 2.89).
    Mechanism: prob 0.4 restores natural-target share 0.2 -> 0.6, recovering real-data
    high-range calibration lost in E1.'
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
  magnitude_note: 'small: median 3.89 -> in [3.5, 4.5] (band supply drops 0.551 ->
    0.362 but the poison-tail deletion, which is intensity-independent, carries most
    of the deployment win)'
- diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: up
  magnitude_note: may widen 1.31 -> <= 3.0 at lower supply; snap-back toward 6.29
    would mean intensity (not law shape) stabilizes the seed lottery
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
E1's dense-population cost (dense-128 MAE 4.49/5.65/4.46 vs bar 3.89, positive bias on truth>80) is owned by the hr_augment_prob 0.4->0.8 intensity change (natural-target share halved to 0.2), NOT by the reshaped band law. Running the identical band law (aug_min_mean_bpm=45, band_prob=0.5) at the baseline prob 0.4 will recover dense to within 1 bpm of 2.89 while keeping median supabase MAE < 4.5.

## Rationale
E1 changed three things at once; the win (supabase 3.89 median, band 1.31) and the cost (dense 4.49/5.65/4.46, positive bias on truth>80) cannot be attributed within it. The natural-share account predicts dense recovery at prob 0.4 because the dense population IS the natural distribution: at prob 0.8 only 20% of targets were real data. The alternative — the band mixture itself owns the cost — predicts dense stays broken. Supabase side: sub-45 deletion is intensity-independent (P(<45)=0.0018 at prob 0.4), so the floor/attractor fix should persist; only the in-band supply drops (0.551 -> 0.362, still 1.8x the 0.204 baseline).

## Plan
Profiles lora_proxy_12ch_wl_band04{,_s43,_s44} (v9band04, seeds 42/43/44; yaml-diff vs v9band = hr_augment_prob only, verified). Init outputs/checkpoints/v5c/phase_b, Phase C 2600 steps. Same frozen supabase 200 + dense 128 scoring. Pipeline scratch/run_e1b_band04.sh -> logs/e1b_band04.log, *_RC= markers. Measured E1 wall time: ~60 min/seed (39 train + 21 capture) -> ~3h total. Decision rule preregistered in rb-e1b-prob-confound-20260813.

## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Applies [[eiv-hr-augment-deployment-band-mixture]].
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (down).
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (up).
Predicts [[eiv_supabase_seed_band_mae_bpm]] (up).
Gate `g1-dense-recovered-all-seeds` (blocking): BLOCKING: dense-128 MAE <= 3.89 (within 1 bpm of v7noatom's 2.89) on EVERY seed.
Gate `g2-supabase-median-below-4.5` (blocking): BLOCKING: median supabase MAE over seeds 42/43/44 < 4.5 on the frozen 200-row draw (holds most of E1's win).
Gate `g3-structural-invariants-and-band` (advisory): Floor 0 and P(emit<45) < 0.10 on all seeds; seed band reported (v9band was 1.31; snap-back toward 6.29 indicts augment intensity as the band stabilizer).

#deployment-distribution #optihealth #collapse #baseline
