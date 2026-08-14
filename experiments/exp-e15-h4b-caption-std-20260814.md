---
artifacts: []
closed: true
config_note: 'E15 (h4b caption-dispersion arm). SINGLE VARIABLE vs control: caption
  gains ''HR variability {hr_std:.1f} bpm SD'' (code already in _build_hr_only_caption
  since commit 97ba82b, never trained). Control = phase_a_12ch_pmdata seed 42 -> outputs/probes/12ch_v3_1min_phase_a.json
  (increment +0.4086, hr_mean 0.9713, hr_peak MAE 12.80 vs trivial 16.97). Arm: 3
  seeds (42/43/44), profiles phase_a_12ch_h4b_s*, byte-identical to control except
  seed + caption (cache key includes caption bytes so fresh CPU text-embedding precompute
  is automatic). Probe: scripts/probe_phase_a_hr.py, data/eval_v3_1min, --num-samples
  6624. GATES (preregistered, from runbook h4-enrich…): g1 BLOCKING eiv_peak_excursion_increment_r2
  >= 0.56 (mean over 3 seeds; seed spread band 0.066); g2 BLOCKING hr_mean subject-grouped
  R2 does NOT fall below 0.90 (capacity-reallocation control: peak up + mean down
  is NOT a win); g3 informational: level-only control quoted alongside; hr_peak MAE
  vs trivial reported. FALSIFICATION on record: if hr_std in the caption does not
  move the increment, the supervision account is wrong.'
crash_reason: ''
created_at: '2026-08-14T13:52:44+00:00'
diagnostics_measured:
- after: 0.313
  before: 0.4086
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.9378
  before: 0.9713
  diagnostic_id: eiv_heldout_hr_r2
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: h4b-supersedes-h4-sharpened-from-add-dispersion-structure-to-one-named-scalar-th
id: exp-e15-h4b-caption-std-20260814
observed_effects:
- delta: -0.0956
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: down
- delta: -0.0335
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_heldout_hr_r2: false
    eiv_peak_excursion_increment_r2: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h4b-supersedes-h4-sharpened-from-add-dispersion-structure-to-one-named-scalar-th]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4086 → after 0.313  (Δ -0.0956, down ✗ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.9713 → after 0.9378  (Δ -0.0335, down ✗ predicted none)

**Verdict:** refutes

## Notes
FALSIFICATION TRIGGERED (the preregistered falsification in config_note): hr_std in the caption did NOT raise the peak readout — it LOWERED it. 3-seed mean increment +0.3130 (s42 +0.3225 / s43 +0.3594 / s44 +0.2572) vs bar 0.56 (g1 BLOCKING FAIL by 0.25; best seed 0.20 below bar) and vs control +0.4086 (same-seed paired delta -0.086). g2 PASS by letter (hr_mean 0.9357/0.9332/0.9445 all >=0.90) but hr_mean ALSO fell from 0.9713 — both metrics down = NOT capacity reallocation; the extra clause diluted contrastive alignment overall. g3 info: level-only -0.0062 (control +0.0123); peak MAE 13.45-14.63 vs trivial 17.04 (control 12.80/16.97). The h4b supervision-deficit account is wrong at Phase A: naming dispersion in the caption does not teach the encoder more peak structure. Ladder implication: caption-scalar shallow probes (0.575 with hr_std) do NOT transfer to the contrastive pipeline; the 0.4086->0.31 move shows caption enrichment can even hurt. Surviving hr_peak lever: e4-encoder-excursion-avgmax (architecture-side). MAJOR SIDE FINDING: first launch trained 3 BYTE-IDENTICAL checkpoints (sha 2f721a8d) — `eiv train phase-a` never read profile `seed:` (only lora-proxy did); every historical phase-a profile-seed run silently used 42. Fixed commit 702acb1, reran s43/44 (shas ebe76671/0f8a4948). First REAL phase-a seed band on increment: 0.1022 (prior 0.066 band is suspect — predates fix). Historical phase-a 'multi-seed' claims via CLI profiles need provenance audit (e.g. phase_a_12ch_pmdata '3/3 seeds good basin'). Caveats: (1) control checkpoint trained 2026-08-08 code — a same-code EIV_CAPTION_NO_STD=1 control would cleanly separate caption effect from code drift; g1 fail stands regardless (bar-relative). (2) 3 seeds share the one-time text-embedding cache (caption deterministic — by design). Artifacts: outputs/probes/e15h4b_s{42,43,44}_phase_a.json, checkpoints outputs/checkpoints/e15h4b_s*/phase_a, logs/e15_h4b*.log, commits 778429c/702acb1.

## Postmortem addendum (2026-08-14, after close — caveat (1) RESOLVED by same-code control)
The queued same-code control was run and it CORRECTS the mechanism story in the Notes above
(verdict unchanged):
- **e15ctrl_s42** (`EIV_CAPTION_NO_STD=1`, seed 42, today's code, profile phase_a_12ch_h4b_s42):
  increment **+0.3390**, hr_mean sg R² 0.8991 → `outputs/probes/e15ctrl_s42_phase_a.json`.
- **rep2 replicate byte-identical** (all 4 module shas equal, probe Δ 0.0000): phase-a training is
  fully deterministic; same-seed run variance is ZERO.
- **08-08 control re-probed on today's pool**: +0.3905 (`12ch_v3_phase_a_REPROBE_newpool.json`).
  Old pool was 6552 rows / 123 subjects; today's probe code (84b145f/7217f93) selects 5955 / 131.
- **Decomposition of the 0.4086→0.3390 same-seed gap**: probe-pool change −0.0181; training
  code/corpus drift 08-08→08-14 −0.0515 (suspect: a71034c changed augment caption draws —
  caption-equivalent directed pairs collapsed 824→60, InfoNCE 1.41→1.02; observation only, not
  chased); **caption hr_std clause −0.0165 = NOISE inside the 0.1022 seed band**.
- **CORRECTED mechanism**: the "extra clause diluted contrastive alignment" claim above is WRONG —
  the no-std control's hr_mean (0.8991) is BELOW all three +std arms (0.933–0.945), so the clause
  did not dilute alignment; the apparent dilution was code-drift + pool-change confounds.
- **Refutation STANDS and is now cleaner**: correct control is +0.3390, all arms 0.2572–0.3594,
  bar 0.56 — h4b's supervision-deficit account fails with the confounds removed. Do-not-retry:
  caption enrichment for hr_peak (unchanged).
- Rule for future A/B against historical checkpoints: NEVER compare probe JSONs across probe-code
  versions — re-probe the old checkpoint with current code first (pool selection changed silently).
Artifacts: `outputs/probes/e15ctrl_s42_phase_a.json`, `e15ctrl_rep2_phase_a.json`,
`12ch_v3_phase_a_REPROBE_newpool.json`; commits 367743b, 9b56c7c.

#cross-modal #sensor-text #contrastive #infonce #baseline #epoch-resolution #dataset-whitelist #wearables
