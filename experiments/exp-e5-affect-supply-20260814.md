---
artifacts: []
closed: true
config_note: 'E5 supply probe, pure CPU. RF(400, min_leaf 5) + linear check, GroupKFold(5)
  by subject. Cohorts from data/canonical (10344x1440x12, current build with live
  sleep block). Features masked present-only per M_mask; missing family features imputed
  with train-fold median + presence flag. Gates preregistered on hypothesis: g1 delta
  AUC >= +0.03 & within-subject perm p<0.05 (blocking), g2 absolute AUC >= 0.58 (blocking).'
crash_reason: ''
created_at: '2026-08-14T07:47:47+00:00'
diagnostics_measured:
- after: -0.0278
  before: 0.0
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL. delta = 0.4538 - 0.4816 = -0.0278 (bar >= +0.03), within-subject
    perm p = 0.84 (24 perms, null mean -0.0038, std 0.0271, z = -0.88). Direction
    is NEGATIVE: adding channels hurt the RF.'
  gate_id: g1-existence-lifesnaps
  passed: false
- evidence: FAIL. Absolute multichannel AUC 0.4538 < 0.58; best arm of ANY composition
    was hr+time 0.5183; linear hr_only 0.5329. Everything is within noise of chance
    0.50 — there is no absolute skill for the delta to sit on.
  gate_id: g2-magnitude-absolute
  passed: false
- evidence: 'Measured. pmdata (hr+sleep+time) 0.1280 vs hr_only 0.1244: delta +0.0036,
    perm p=0.84 (null mean +0.0244 — the null EXCEEDS the observed delta). Sleep adds
    nothing on pmdata.'
  gate_id: g3-pmdata-sleep
  passed: false
- evidence: 'Measured, moot: no family cleared anything. hr+sleep 0.4621, hr+temp
    0.4808, hr+rr 0.5161, hr+hrv 0.4925, hr+time 0.5183 — all vs hr_only 0.4816, all
    within the 0.027 null std of chance. Top RF importances split evenly across hr/temp
    stats (~0.023-0.028 each), i.e. no informative feature dominates because none
    is informative.'
  gate_id: g4-attribution
  passed: false
git_ref: ''
hypothesis_id: hyp-affect-multichannel-supply-20260814
id: exp-e5-affect-supply-20260814
key_numbers: {}
observed_effects:
- delta: -0.0278
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-affect-multichannel-supply-20260814]].

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.0 → after -0.0278  (Δ -0.0278, down ✗ predicted up)

## Gates
- `g1-existence-lifesnaps`: **FAIL** — FAIL. delta = 0.4538 - 0.4816 = -0.0278 (bar >= +0.03), within-subject perm p = 0.84 (24 perms, null mean -0.0038, std 0.0271, z = -0.88). Direction is NEGATIVE: adding channels hurt the RF.
- `g2-magnitude-absolute`: **FAIL** — FAIL. Absolute multichannel AUC 0.4538 < 0.58; best arm of ANY composition was hr+time 0.5183; linear hr_only 0.5329. Everything is within noise of chance 0.50 — there is no absolute skill for the delta to sit on.
- `g3-pmdata-sleep`: **FAIL** — Measured. pmdata (hr+sleep+time) 0.1280 vs hr_only 0.1244: delta +0.0036, perm p=0.84 (null mean +0.0244 — the null EXCEEDS the observed delta). Sleep adds nothing on pmdata.
- `g4-attribution`: **FAIL** — Measured, moot: no family cleared anything. hr+sleep 0.4621, hr+temp 0.4808, hr+rr 0.5161, hr+hrv 0.4925, hr+time 0.5183 — all vs hr_only 0.4816, all within the 0.027 null std of chance. Top RF importances split evenly across hr/temp stats (~0.023-0.028 each), i.e. no informative feature dominates because none is informative.

**Verdict:** refutes

## Notes
REFUTED at the supply level, and more strongly than predicted: I predicted a small positive delta (+0.02..+0.06, sleep-driven); measured -0.0278 (p=0.84). But the decisive finding is g2, not g1: THE HR-ONLY ARM IS ITSELF AT CHANCE (RF AUC 0.4816, linear 0.5329, chance 0.50) on subject-grouped OOF lifesnaps TENSE/ANXIOUS, and NO composition of channels exceeds 0.5183. There is no daily-summary affect signal in ANY channel on the only labeled cohorts — consistent with the earlier bounding fact corr(label, emitted hr) <= 0.09 and with pmdata hr_only Spearman 0.1244 (16 subjects, saturated 3-heavy label). SCOPE OF THE NEGATIVE, stated precisely: this kills daily-summary supply for BETWEEN-DAY affect discrimination generalizing ACROSS SUBJECTS at the population level. It does not measure (a) within-day temporal patterns beyond dispersion/fragmentation summaries — though shallow-at-chance makes deep rescue unlikely per lecture-04 priors — or (b) N-of-1 per-subject models with per-subject baselines, which is the actual product regime and would need within-subject calibration data the platform collects. IMPLICATION FOR ANTON'S STEER: 'sleep/rr/spo2 will improve state detection' is NOT supported by the benchmark cohorts we can score on; the state slot cannot be honestly trained or evaluated on lifesnaps/pmdata labels at all. Any state-detection claim must wait for deployment-side labels (optihealth_supabase labels:{} today). Encoder work on non-HR channels remains justified ONLY for channel-value slots (hrv/rr/temp reporting), not for affect. Artifact logs/e5_affect_supply_probe.json, probe scratch/e5_affect_supply_probe.py, 151s CPU, zero GPU.

#cross-modal #wearables #optihealth #baseline #subject-heldout #representation-learning
