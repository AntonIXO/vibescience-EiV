---
created_at: '2026-08-05T15:47:14+00:00'
id: ro-swap-delta-as-optimisation-target-20260805
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T15:47:14+00:00'
topic_tags: []
---

## Claim
Teacher-forced paired-swap delta can be used to rank slots by conditioning quality, or optimised as a surrogate for generation conditioning.

## Method
Per-slot swap delta vs free-generation quality compared on the e23v2 baseline (60 samples x 6 donors = 360 pairs).

## Evidence
Non-monotone in the property of interest. The slot that demonstrably WORKS in free generation (hr_raw, corr +0.604/+0.909) had the WEAKER delta (+0.161) and a NON-significant dose slope (p=0.165), while the degenerate bucket slot had the stronger, significant one (+0.313, dose r=+0.204 p=9.7e-05). Measured per-slot gap 0.51x baseline / -0.50x arm1. Valid as a SIGN check, invalid as a ranking or optimisation target.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].
