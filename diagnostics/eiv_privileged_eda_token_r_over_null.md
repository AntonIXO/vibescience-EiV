---
direction: higher_better
id: eiv_privileged_eda_token_r_over_null
name: Privileged EDA per-patch recoverability from encoder tokens, over its own permutation
  null
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags:
- wearables
- lupi
- control
- baseline
unit: corr_delta
---

## Description
Subject-grouped out-of-fold correlation r between per-patch privileged EDA targets (eda_tonic, eda_scr_rate; standardised by the NARROWEST fit available, which is by_subject on bundles from 2026-08-24 on) and the prediction of a readout over the trained temporal encoder's patch tokens (channel-mean, [B,C,N,D] -> [rows, D]) — MINUS the mean of that arm's OWN permutation null.

WHY THE NULL IS SUBTRACTED, NOT QUOTED SEPARATELY: same reason as eiv_momentary_state_auc_over_null. With subject-grouped folds and a 192-d feature matrix a flexible readout can exploit window identity, so the null does not sit at 0.

MANDATORY REPORTING (inherits the 2026-08-18 readout amendment on eiv_momentary_state_auc_over_null): (a) raw r AND null mean +- sd, never the difference alone; (b) z and permutation p; (c) n cells, n windows, n subjects, n datasets; (d) THE ESTIMATOR — report at least a linear and a tree family, each with ITS OWN null recomputed on the same rows/folds; (e) R2 against the always-predict-median trivial, because an r without a skill number has already produced one near-miss reading in this project.

THE ADMISSIBLE NULL DEPENDS ON THE TARGET STANDARDISATION, and this changed under the agent's feet: when targets are standardised by_subject the between-subject level is removed by construction, so A2's 'stamp a donor subject's mean onto the recipient' null tests a quantity the target no longer contains. The structure-preserving null for a by_subject target is a WITHIN-SUBJECT permutation of the target across patches: it holds subject, dataset and the target marginal exactly, and destroys only patch-level alignment — which is the claim. This was NOT available at the A2 supply (one session per subject, null sd 0.000, discarded); it is available from 2026-08-24 (473 of 934 EDA subjects carry >1 window).

REFERENCE (A2, 2026-08-19, rule_out that-per-patch-eda-tonic-level-scr-rate-is-1dadc0, LINEAR readout only, supply 250 subjects x 1 session): all 8 cells r -0.103..+0.067, R2 <= 0, MDE on r roughly 0.15-0.25. The honest statement of that result is 'below r~0.2 detectable at n=250 subjects x 1 session', and it is explicitly scoped to that supply.

*Unit:* `corr_delta` — *higher_better*

#wearables #lupi #control #baseline
