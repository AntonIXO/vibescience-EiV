---
created_at: '2026-08-13T20:38:56+00:00'
gates: []
id: hrv-raw-fails-on-deployment-for-the-same-reason-hr-raw-did-pre-band-law-the-trai
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
  magnitude_note: ''
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
hrv_raw fails on deployment for the same reason hr_raw did pre-band-law: the training target law never places mass where supabase hrv truth lives (median 72 ms, std 10.6, tight), so the model collapses to a population-level prototype attractor (69.1 ms on 126-168/197 rows, corr ~0, skill -0.2 to -0.5) despite genuinely reading hrv on dense (corr ~0.60, skill +2.8 to +3.5, std_ratio 0.75-0.92, all 3 seeds). An hrv analog of the E1b band law (target-mass reshaping toward the deployment band, sub-tail handling) will unlock row-level hrv variation on supabase.

## Rationale


## Plan


## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Predicts [[eiv_supabase_seed_band_mae_bpm]] (down).

#deployment-distribution #optihealth #collapse #baseline
