---
artifacts: []
closed: false
config_note: 'Executes runbook w6-mask-weighted-cross-patch-pooling-end-to-end-51509d.
  Conditional on exp-w5-three-stage-contraction-20260820 (SUPPORTS, all 4 gates passed,
  g3 masked re-pool 0.1822 -> 0.8159 on frozen tokens).


  DESIGN: 6 chains = 3 seeds (43/44/45) x 2 modes (avg control / masked_avg treatment),
  Phase C only, all off the FROZEN ab_ctrl_s43 phase_b. This is the v10conly baseline
  design (C-only seed family), chosen because exp-054 measured that it collapses the
  seed band to 0.80 bpm -- a shared frozen B is what makes 3 seeds interpretable.
  Runner scratch/run_w6_pooling.sh, profiles scratch/w6_pooling.yaml.


  SINGLE VARIABLE PROVEN PROGRAMMATICALLY: for each seed the two profile dicts were
  diffed and differ in exactly {patch_pool_mode, notes}. Everything else -- lora rank/alpha/dropout,
  steps 2600, batch 2, band04 augment law (hr_augment_band_prob 0.5, min_mean 45),
  sampler, dataset_filter, sensor_slots 96, freeze_encoder -- is byte-identical to
  the c_ctrl_s44/s45 baseline recipe.


  IMPLEMENTATION (commit 665fff2): CrossModalProjector gains patch_pool_mode=''masked_avg''
  plus a patch_presence argument; _patch_presence_from_mask derives [B,C_in,N_patches]
  from m_inherited; patch_pool_mode plumbed profile -> cli_train -> run_lora_proxy_adaptation,
  which it never was before (the source comment warned that setting it in a profile
  would silently train avg and report a fake null).


  PRE-FLIGHT ON REAL CORPUS ROWS (scratch/w6_preflight.py) CAUGHT TWO BUGS BEFORE
  ANY GPU SPEND, both of which would have produced a fake result: (1) channel-MEAN
  presence read 0.4155 on a pmdata day whose HR is 98.7% covered, which would have
  diluted the correction on precisely the dense rows the no-harm gate protects --
  fixed to per-channel presence; (2) collapsing to a single global weighted mean broke
  k>1, and the shipped config runs k = sensor_slots/C = 96/24 = 4, so the all-present
  invariant diverged by 1.53 while the k=1-only unit tests passed -- fixed to mirror
  adaptive_avg_pool2d''s bin boundaries, and a k-parametrised invariant test added.
  Post-fix pre-flight: dense row |masked-avg| = 0.000069 (untouched), sparse case
  row = 0.027 (corrected), all-present invariant max diff 3.81e-06, per-channel patch
  alignment 1.99e-09. Full repo suite 770 passed / 1 skipped.


  BASELINES FROZEN PRE-RUN (v10conly family, same frozen s43 B, same 200-row supabase
  draw): supabase MAE median 2.84, band 0.80, per-seed 2.74-2.84; dense guards 6.04
  / 2.64 / 3.57 against a dense trivial of 7.76; supabase trivial 3.95. Comparison
  is treatment-vs-control WITHIN this run at matched seeds, with the v10conly numbers
  as the sanity anchor.'
cost: null
crash_reason: ''
created_at: '2026-08-20T22:12:02+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: logs/w6avg_s43.log
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: h-w6-mask-weighted-pooling-restores-short-9da5b6
id: exp-w6-masked-pooling-20260820
interpretation: null
key_numbers: {}
observed_effects: []
parent_experiment_id: exp-w5-three-stage-contraction-20260820
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: null
---

## Summary
Tests hypothesis [[h-w6-mask-weighted-pooling-restores-short-9da5b6]].
Branches from [[exp-w5-three-stage-contraction-20260820]].
External run: logs/w6avg_s43.log

#extent-heterogeneity #pooling #padding #deployment-distribution #wearables #denominator #state-supervision #optihealth-eiv #data-contract-violation #matched-baseline
