---
created_at: '2026-08-13T21:45:21+00:00'
id: hr-peak-s-seed-robust-negative-skill-is-a-target-law-prototype-problem-that-a-pe
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-13T21:45:21+00:00'
topic_tags: []
---

## Claim
hr_peak's seed-robust negative skill is a target-law/prototype problem that a peak analog of the band law could fix (the lever that worked for hr_raw and is being tested for hrv_raw in E3)

## Method
CPU dissociation probe scratch/e4_peak_read_probe.py on existing v9band04 captures (3 seeds x supabase-200/dense-128): corr, MAE vs always-median trivial, std_ratio, attractor mass, per-population. Logic: a target-law fix requires the representation to carry the signal (high dense corr, deployment-only collapse); low dense corr everywhere rules the law lever out.

## Evidence
logs/e4_peak_read_probe.json (all 6 v9band04 captures). DENSE corr(emit_peak, true_peak): s42 0.348, s43 0.168, s44 0.242 -- the model cannot read the peak even on dense rows where hr_raw corr is ~0.9 and hrv_raw corr is ~0.60. Dense skill negative on all seeds (-2.7..-3.2 vs trivial 16.95). Supabase adds the usual attractor collapse on top (s43: 101.1 bpm on 135/200, skill -12.91), but the dense-side failure proves the representation never carried the extreme-value statistic. Contrast is the point: the hrv band law (E3) was justified BY dense corr 0.60 showing the info was present and only the deployment prototype was wrong; for hr_peak there is no such info to unlock. Consistent with the historical caption-probe ladder (level 0.023 -> dispersion features 0.708): mean-based pooling destroys dispersion/extremes. The justified lever is the ENCODER EXCURSION branch (max-pool / extreme-value path), not target-law reshaping.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
