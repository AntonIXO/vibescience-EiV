---
artifacts: []
closed: true
config_note: 'E12 coverage-augmentation head arm, zero LM training. Base: v9band04_s42
  phase_c, E11 machinery (scratch/e11_linear_head_probe.py) + train-gen cache scratch/tmp_e11_train_gen.json
  (700 dense self-gen texts reusable). NEW: coverage augmentation applied to X/M BEFORE
  soft-token build — hr channel: contiguous-block dropout to target coverage ~U[0.57,0.95]
  (block length law from supa present-run median ~171min); hrv channel: thin to ~U[0.10,0.41];
  presence channel + zero-fill recompute automatically. Augmented rows need FRESH
  generation (coverage changes the prefix text) — ~300 aug-train + ~150 aug-eval gens
  ≈ 25 min GPU. Heads: RidgeCV linear only (E28). Score: (a) E11 plain heads on aug-eval
  dense (g1 mechanism), (b) mix-trained heads on aug-eval dense (g2) and frozen supa-200
  captures (g3), controls (g4). Script scratch/e12_coverage_aug_heads.py -> logs/e12_coverage_aug_heads.json.'
crash_reason: ''
created_at: '2026-08-14T10:58:44+00:00'
diagnostics_measured:
- after: -1.676
  before: -5.362
  diagnostic_id: eiv_nonhr_channel_grouped_r2
- after: -10.585
  before: -6.967
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 3.018
  before: 4.521
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL. Plain E11-recipe heads on paired eval-150 dense rows: hrv skill
    eval_plain +6.467 CI[3.628,9.336] vs eval_aug +4.110 CI[1.754,6.43] — drop 2.357
    < preregistered 3.0 bar, no sign flip (both solidly positive). hr_raw 5.240→4.401
    (mild). Only hr_peak collapses to ~0 (1.685→0.081) but gate keyed on hrv. Supabase-law
    block masks on dense rows do NOT reproduce the supabase failure: coverage explains
    ~20% of the ~11.8-unit dense→supa hrv skill gap. Coverage covariate shift is a
    MINOR contributor, not the mechanism.'
  gate_id: g1-coverage-shift-mechanism
  passed: false
- evidence: 'PASS (weakly informative given g1 fail): mix heads (700 plain + 300 aug)
    hrv skill on eval_aug +4.159 CI[1.525,6.824] > 0. But plain heads already at +4.110
    on the same rows — augmentation added ~nothing in-distribution because coverage
    degradation never broke the heads there.'
  gate_id: g2-augmented-recovery-dense
  passed: true
- evidence: 'FAIL but with the run''s most interesting number: mix hrv head supa MAE
    9.990 vs plain 13.675 (skill −5.362→−1.676, 69% of gap-to-trivial closed) yet
    still above trivial 8.314. Secondary: hr_peak supa skill WORSE −6.967→−10.585;
    hr_raw supa lost CI-positivity (+1.007 CI[0.516,1.542] → −0.197 CI[−0.758,0.38]).
    Aug training helps hrv transfer more than the dense mechanism test predicts, hurts
    hr slots — heterogeneous, residual is population/device shift beyond coverage.'
  gate_id: g3-supabase-deployment
  passed: false
- evidence: 'FAIL net: donor gaps on aug eval rows all positive (hr_raw +4.047, hr_peak
    +3.018, hrv +5.348) and shuffle refits destroy skill (clean); but hr_raw supa
    CI-positivity control broken by mix training (CI[−0.758,0.38] includes 0; E11
    plain was +1.007 CI-positive).'
  gate_id: g4-controls
  passed: false
git_ref: ''
hypothesis_id: e11-s-supabase-transfer-failure-is-5472c8
id: exp-e12-coverage-aug-heads-20260814
key_numbers: {}
observed_effects:
- delta: 3.686
  diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
- delta: -3.618
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: down
- delta: -1.503
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: down
parent_experiment_id: exp-e11-linear-head-20260814
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_skill_bpm: false
    eiv_nonhr_channel_grouped_r2: true
provenance: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[e11-s-supabase-transfer-failure-is-5472c8]].
Branches from [[exp-e11-linear-head-20260814]].

## Measurements
- [[eiv_nonhr_channel_grouped_r2]]: before -5.362 → after -1.676  (Δ +3.686, up ✓ predicted up)
- [[eiv_gen_peak_skill_bpm]]: before -6.967 → after -10.585  (Δ -3.618, down ✗ predicted up)
- [[eiv_gen_peak_donor_gap_bpm]]: before 4.521 → after 3.018  (Δ -1.503, down)

## Gates
- `g1-coverage-shift-mechanism`: **FAIL** — FAIL. Plain E11-recipe heads on paired eval-150 dense rows: hrv skill eval_plain +6.467 CI[3.628,9.336] vs eval_aug +4.110 CI[1.754,6.43] — drop 2.357 < preregistered 3.0 bar, no sign flip (both solidly positive). hr_raw 5.240→4.401 (mild). Only hr_peak collapses to ~0 (1.685→0.081) but gate keyed on hrv. Supabase-law block masks on dense rows do NOT reproduce the supabase failure: coverage explains ~20% of the ~11.8-unit dense→supa hrv skill gap. Coverage covariate shift is a MINOR contributor, not the mechanism.
- `g2-augmented-recovery-dense`: PASS — PASS (weakly informative given g1 fail): mix heads (700 plain + 300 aug) hrv skill on eval_aug +4.159 CI[1.525,6.824] > 0. But plain heads already at +4.110 on the same rows — augmentation added ~nothing in-distribution because coverage degradation never broke the heads there.
- `g3-supabase-deployment`: **FAIL** — FAIL but with the run's most interesting number: mix hrv head supa MAE 9.990 vs plain 13.675 (skill −5.362→−1.676, 69% of gap-to-trivial closed) yet still above trivial 8.314. Secondary: hr_peak supa skill WORSE −6.967→−10.585; hr_raw supa lost CI-positivity (+1.007 CI[0.516,1.542] → −0.197 CI[−0.758,0.38]). Aug training helps hrv transfer more than the dense mechanism test predicts, hurts hr slots — heterogeneous, residual is population/device shift beyond coverage.
- `g4-controls`: **FAIL** — FAIL net: donor gaps on aug eval rows all positive (hr_raw +4.047, hr_peak +3.018, hrv +5.348) and shuffle refits destroy skill (clean); but hr_raw supa CI-positivity control broken by mix training (CI[−0.758,0.38] includes 0; E11 plain was +1.007 CI-positive).

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
COVERAGE HYPOTHESIS LARGELY REFUTED as *the* mechanism; augmentation partially helpful for hrv only. (1) Mechanism test g1 FAILED: supabase-law block masks on paired dense rows cost hrv heads only 2.36 skill-units (6.47→4.11, both CI-positive) — coverage explains ~20% of the ~11.8-unit dense→supa gap. Only hr_peak collapsed under masking (1.69→0.08), consistent with peak deletion by absence blocks. (2) Aug-mix training moved supa hrv MAE 13.675→9.990 (69% of gap-to-trivial closed, still worse than always-median 8.314) — MORE than the dense mechanism test predicts, suggesting the aug helps via feature-scale regularization rather than manifold matching. (3) Costs: supa hr_peak −6.97→−10.59, hr_raw lost CI-positivity — aug training is NOT free. (4) Residual dense→supa failure is population/device/context shift beyond coverage: supabase rows differ in ways synthetic masking of lifesnaps/pmdata days cannot reproduce (different subjects, device (optihealth pipeline), value distributions — e.g. supa hrv trivial 8.31 vs dense 13.4-17.6 implies compressed target variance). IMPLICATION: no head trained purely on public dense corpora will transfer; the lever that remains is supervision ON supabase-adjacent distribution itself — e.g. per-user calibration / few-shot head fitting on the 23 non-frozen supa rows, or collecting labels on deployment. Artifacts: scratch/e12_coverage_aug_heads.py (commit e6c3853), logs/e12_coverage_aug_heads.json, logs/e12_full.log, caches scratch/tmp_e12_*.

#readout #deployment-distribution #medgemma #optihealth #baseline #collapse
