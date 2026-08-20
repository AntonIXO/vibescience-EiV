---
ambition_class: null
created_at: '2026-08-19T21:23:55+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: allowlist-a-b-full-chain-executes-runbook-dc0de8
interventions:
- dataset-allowlist-repaired-all-phases
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: ''
- diagnostic_id: eiv_gen_bucket_acc_over_majority
  direction: up
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags: []
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- dataset-whitelist
- deployment-distribution
- sensor-text
- epoch-resolution
- wearables
- baseline
---

## Statement
ALLOWLIST A/B, FULL CHAIN (executes runbook dataset-filtering-is-not-closed-re-open-1050cb, p1, on the 12,547 corpus). The dataset allowlist applied CONSISTENTLY in all three phases changes GENERATION metrics — the thing the retracted single-phase arm never tested. Arms, single variable = allowlist, both on the same corpus/code/eval draw, seed 42, band04 augment law at C: CONTROL = phase_a_12ch_pmdata (10 datasets) → phase_b_12ch → C with the incumbent 3-dataset dict; REPAIRED = phase_a_12ch_1min_wl (6-dataset wl) → phase_b_12ch_wl → c_ab_wl_s42. Context that sharpens the prediction: this session's c_patch120 failure showed a large A/B→C allowlist mismatch can collapse emission entirely (35.1-attractor, MAE 24 vs v9band04's 3.04 on the same draw), which is exactly the consistency defect this runbook names — the REPAIRED arm holds one allowlist end-to-end and should avoid that failure mode; the CONTROL arm carries the historical mismatch (10-dataset A/B → 3-dataset C). PREDICTIONS: (1) REPAIRED beats trivial (3.95) on supabase hr_raw MAE and lands within 0.60 of v9band04's 3.04; (2) CONTROL ≥ REPAIRED on the same metric (mismatch costs); (3) neither arm re-floors (no single emitted value >50% of rows — the attractor check); (4) if BOTH arms land inside the 0.60 band of each other, the allowlist effect is below the family MDE at one seed and is recorded as such (no silent promotion).

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Applies [[dataset-allowlist-repaired-all-phases]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_gen_bucket_acc_over_majority]] (up).

#dataset-whitelist #deployment-distribution #sensor-text #epoch-resolution #wearables #baseline
