---
created_at: '2026-08-13T08:02:20+00:00'
gates:
- g1-median-supabase-mae-beats-v7noatom-band-blocking
- g2-floor-rate-below-0.05-all-seeds
- g3-dense-128-mae-within-1bpm-of-v7noatom-2.89
- g4-band-scored-not-point
hypothesis_id: e1-band-mass-starvation-drives-supabase-mae-and-seed-band
id: rb-deployment-band-mass-20260813
priority: 2
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
status: done
topic_tags: []
updated_at: '2026-08-13T13:55:49+00:00'
---

## Prompt
E1 — DEPLOYMENT-BAND TRAINING MASS (sharpened by E0, exp-v7noatom-seed-band-20260813).

CONTEXT (self-contained). Repo /root/optiHealth-EiV. Baseline: v7noatom family (lora_proxy_12ch_wl_noatom, seeds 42/43/44 -> supabase MAE 11.54/15.78/9.49, band 6.29 bpm, floor 0.000 all seeds; trivial 4.02; frozen 200-row draw default_rng(0)/223 pool). Deployment truth: mean 58.3, p50 56.8, NEVER <45; 0.829 of truth in [45,65].

TWO defects in the post-a71034c augment law, both measured (scratch/band_supply_demand.py, commit 744bbd7; E0 forensics commit e8837a8):
(a) BAND STARVATION: P(hr_raw target in [45,65]) = 0.2017 vs deployment demand 0.8290 (supply/demand 0.243). Each seed crystallizes an arbitrary low-band prototype (38.9 / 31.1+31.3 / 31.6).
(b) SUB-45 POISON TAIL: P(hr_raw target < 45) = 0.0729, almost entirely from augmentation (E[P(aug<45)]=0.1774; natural 0.0032), while deployment truth is NEVER <45. E0 seeds emit <45 on 33-60% of rows; seeds 43/44 crystallized attractors at 31.1/31.3/31.6 — just above the deleted floor. The tail is the dominant remaining driver of both MAE and seed instability.

INTERVENTION: modify _sample_hr_augment_peak draw law (src/training/datasets.py:1400) to BOTH (1) raise the effective augmented-mean floor so sub-45 target mass ~ natural rate (<0.01), and (2) add a mixture component (~0.5 prob) drawing target_mean ~ U(45,65). NO new atom: extend tests/test_hr_augment_no_atom.py — no single value >1% mass, verify RED first. Re-run scratch/band_supply_demand.py after the change; require P(target in [45,65]) >= 0.5 and P(target<45) <= 0.01 BEFORE spending GPU.

Profiles: lora_proxy_12ch_wl_band seed 42 + _s43/_s44 (diff bodies before launch). Train 3 seeds Phase C from outputs/checkpoints/v5c/phase_b (~2.5h each), score frozen supabase 200 + dense 128 per seed.

GATES: (blocking) median supabase MAE across band-arm seeds < 9.49 (best v7noatom-family seed — beats the WHOLE baseline band, not the 11.54 point); floor rate <0.05 all seeds; dense-128 MAE within 1 bpm of 2.89; P(emit<45) < 0.10 all seeds (new, from E0 forensics); seed band reported (predicted DOWN vs 6.29 — shared-root-cause test: if filling the band does NOT shrink the seed band, crystallization and starvation are separate problems).

Predicted effects at run time: eiv_supabase_hr_raw_mae_bpm DOWN (median <8), eiv_supabase_floor_emission_rate NONE, eiv_supabase_seed_band_mae_bpm DOWN (<6.29).

## Preconditions
rb-v7noatom-seed-band-20260813 DONE (the band is the yardstick). CPU design probe first: measure current training-target hr_raw density in 45-65 bpm from the production dataset path before writing the augmentation.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Run as [[e1-band-mass-starvation-drives-supabase-mae-and-seed-band]].
Intended gates: `g1-median-supabase-mae-beats-v7noatom-band-blocking`, `g2-floor-rate-below-0.05-all-seeds`, `g3-dense-128-mae-within-1bpm-of-v7noatom-2.89`, `g4-band-scored-not-point`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
