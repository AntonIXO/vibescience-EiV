---
created_at: '2026-08-12T11:59:05+00:00'
id: supabase-floor-emission-rate-and-deployment-mae-is-monotone-in-phase-c-final-los
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-12T11:59:05+00:00'
topic_tags:
- deployment-distribution
- collapse
- baseline
---

## Claim
Supabase floor-emission rate (and deployment MAE) is monotone in Phase-C final_loss -- i.e. v4w's deployment advantage over v5c/v5d is explained by underfit acting as accidental regularization against the 30-atom.

## Method
Pre-registered interpolation test: captured the 3 never-captured arms (v4c, v6s43, v6s44) on the identical 200-row supabase draw, predicted their floor rates by linear fit on the 3 known (loss, floor) points BEFORE the data existed, falsifier |measured-predicted|>0.15 written into scratch/run_lossfloor_capture.sh header.

## Evidence
Loss-floor curve on all 6 pre-fix arms, identical 200-row default_rng(0) supabase draw (scratch/score_lossfloor.py, commit c62b983): fit on v4w/v5c/v5d predicts v4c floor 0.796, measured 0.275 (off 0.521); v6s44 predicted 0.866, measured 0.210 (off 0.656) -- both past the pre-registered 0.15 falsifier (script header, written before capture). Same final_loss gives opposite behavior: v4c 0.581->floor 0.275 vs v5c 0.582->0.750; v6s43 0.390->0.710 vs v5d 0.390->0.895 vs v6s44 0.371->0.210. Seed-only supabase band (v5c/v6s43/v6s44, identical init+config): MAE 20.83/19.35/9.45 = 11.37 bpm, floor 0.750/0.710/0.210 = 0.540.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].

#deployment-distribution #collapse #baseline
