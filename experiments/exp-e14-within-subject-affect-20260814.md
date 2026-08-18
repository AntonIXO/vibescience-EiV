---
artifacts: []
closed: true
config_note: 'E14 within-subject affect supply probe. CPU-only, no model — raw canonical
  daily features. Cohorts: powered subjects only (g0 screen: lifesnaps n>=30 labeled
  days, pos_rate [0.15,0.85], label_std>0; pmdata n>=60, >=3 distinct labels). Per-subject
  expanding walk-forward, chronological by start_time; min_train 20 (lifesnaps) /
  40 (pmdata); refit every step. Feature sets: rich-HR-only (mean/std/p10/p25/p75/p90/min/max/iqr/fr75/cov)
  vs HR+{sleep,rr,temp,hrv,time} per-family vs multichannel — reuses E5''s day_features
  extractor verbatim (present-only, M_mask-aware, MIN_PRESENT). Models: RF(400, min_samples_leaf=5)
  primary + logistic/ridge linear check. Scoring: lifesnaps pooled OOS AUC on tense>0
  (PRIMARY, eiv_affect_multichannel_increment); pmdata pooled OOS Spearman on ordinal
  stress. Trivials: train-label median-forward + day-persistence. Null: within-subject
  permutation of the pooled delta, >=50 perms, identical permuted labels both arms,
  permute only the TRAIN-visible portion consistently (labels permuted within subject
  before folding). Gates preregistered on the hypothesis: g1 blocking pooled delta>0
  perm p<0.05; g2 blocking absolute skill beats both trivials CI95; g3 family attribution;
  g0 power precondition. Script scratch/e14_within_subject_affect.py -> logs/e14_within_subject_affect.json,
  systemd-run scoped MemoryMax=16G.'
cost: null
crash_reason: ''
created_at: '2026-08-14T17:12:18+00:00'
diagnostics_measured:
- after: 0.0003
  before: 0.0
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL. PRIMARY lifesnaps pooled delta = 0.6136 - 0.6133 = +0.0003, perm
    p = 0.6471 (50 perms, null mean +0.0048, std 0.0168) — indistinguishable from
    0. Secondary pmdata delta +0.0286, perm p = 0.098 — also not significant. Linear
    check (logistic/ridge, same walk-forward, logs/e14_linear_check.json): lifesnaps
    delta -0.025, pmdata -0.0076 — NEGATIVE. No model class shows a within-subject
    multichannel increment.'
  gate_id: g1-pooled-delta-perm
  passed: false
- evidence: 'MIXED -> FAIL as preregistered (gate requires beating BOTH trivials on
    the pooled OOS folds; scoring both cohorts). lifesnaps PASS: multichannel AUC
    0.6136; pointwise loss-delta vs median-forward +0.0733 CI95[0.0453,0.1027], vs
    persistence +0.1200 CI95[0.0838,0.1581] — both CI-positive. pmdata FAIL: multichannel
    Spearman 0.2761 vs persistence trivial 0.3522; loss-delta vs median-forward -0.0837
    CI95[-0.1124,-0.0561], vs persistence -0.0741 CI95[-0.1234,-0.0253] — multichannel
    is CI-WORSE than both trivials. Note: real within-subject absolute skill exists
    in lifesnaps but it lives in the RICH-HR features (hr_only 0.6133), not in the
    added channels.'
  gate_id: g2-absolute-skill-vs-trivials
  passed: false
- evidence: 'Informational; no family carries a real delta. lifesnaps family deltas:
    temp +0.0123 (largest, below perm-null 95th pct ~0.028), rr +0.0002, hrv -0.0023,
    time -0.0022, sleep -0.0168. pmdata: sleep +0.0293 accounts for essentially the
    whole multichannel +0.0286, but the pooled delta is sub-significant (p=0.098)
    and absolute skill is below the persistence trivial, so no captioning lever is
    named. Marked not-passed to signal ''no family nominated'', not a scoring failure.'
  gate_id: g3-family-attribution
  passed: false
- evidence: 'PASS. Perm-null std of the pooled delta = 0.0168 => MDE(95%, one-sided)
    ~ 0.028 < 0.05 preregistered bar; pooled n_oos = 493 (lifesnaps) / 725 (pmdata).
    Powered cohort after in-script screen: 15 lifesnaps subjects / 793 labeled days
    (census said 26 usable — attrition from label-row availability in the canonical
    pool at feature-extraction time) and 11 pmdata / 1165 days (census 14). The null
    is POWERED: an effect of the preregistered 0.05 magnitude would have been seen.'
  gate_id: g0-power-precondition
  passed: true
git_ref: main@d3f379f
hypothesis_id: hyp-e14-within-subject-affect-supply-20260814
id: exp-e14-within-subject-affect-20260814
interpretation:
  at: '2026-08-18T21:08:39+00:00'
  failure_mode: claim_false
  lesson: 'This is a POWERED NULL, not a directional hint — the mechanical ''up''
    comes from a +0.0003 delta that sits BELOW the permutation null mean (+0.0048,
    std 0.0168, perm p=0.6471, 50 perms), and g0 established MDE ~0.028 < the preregistered
    0.05 bar on n_oos 493 lifesnaps / 725 pmdata. Non-HR daily-summary channels (sleep,
    rr, temp, hrv, time) add NOTHING to affect prediction within subject (multichannel
    AUC 0.6136 vs rich-HR-only 0.6133) and the linear check is outright negative (-0.025
    lifesnaps / -0.0076 pmdata), closing E5''s trait-dominance escape hatch: the lever
    is now null cross-subject AND within-subject. Real within-subject affect skill
    in lifesnaps does exist but lives in rich-HR alone (CI-positive over median-forward
    +0.0733[0.0453,0.1027] and persistence +0.1200[0.0838,0.1581]); pmdata is worse
    than trivial (Spearman 0.2761 vs day-persistence 0.3522), i.e. its stress signal
    is autocorrelation. Sub-daily/temporal features were NOT tested by this daily-summary
    design — that carve is a separate question.'
  transfers_to:
  - hyp-affect-multichannel-supply-20260814
  - hyp-e16-peri-ema-window-20260815
  - hyp-e18-within-subject-state-20260815
  - hyp-e24-lab-protocol-subdaily-state-20260815
key_numbers: {}
observed_effects:
- delta: 0.0003
  diagnostic_id: eiv_affect_multichannel_increment
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-e14-within-subject-affect-supply-20260814]].
At commit `main@d3f379f`.

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.0 → after 0.0003  (Δ +0.0003, up ✓ predicted up)

## Gates
- `g1-pooled-delta-perm`: **FAIL** — FAIL. PRIMARY lifesnaps pooled delta = 0.6136 - 0.6133 = +0.0003, perm p = 0.6471 (50 perms, null mean +0.0048, std 0.0168) — indistinguishable from 0. Secondary pmdata delta +0.0286, perm p = 0.098 — also not significant. Linear check (logistic/ridge, same walk-forward, logs/e14_linear_check.json): lifesnaps delta -0.025, pmdata -0.0076 — NEGATIVE. No model class shows a within-subject multichannel increment.
- `g2-absolute-skill-vs-trivials`: **FAIL** — MIXED -> FAIL as preregistered (gate requires beating BOTH trivials on the pooled OOS folds; scoring both cohorts). lifesnaps PASS: multichannel AUC 0.6136; pointwise loss-delta vs median-forward +0.0733 CI95[0.0453,0.1027], vs persistence +0.1200 CI95[0.0838,0.1581] — both CI-positive. pmdata FAIL: multichannel Spearman 0.2761 vs persistence trivial 0.3522; loss-delta vs median-forward -0.0837 CI95[-0.1124,-0.0561], vs persistence -0.0741 CI95[-0.1234,-0.0253] — multichannel is CI-WORSE than both trivials. Note: real within-subject absolute skill exists in lifesnaps but it lives in the RICH-HR features (hr_only 0.6133), not in the added channels.
- `g3-family-attribution`: **FAIL** — Informational; no family carries a real delta. lifesnaps family deltas: temp +0.0123 (largest, below perm-null 95th pct ~0.028), rr +0.0002, hrv -0.0023, time -0.0022, sleep -0.0168. pmdata: sleep +0.0293 accounts for essentially the whole multichannel +0.0286, but the pooled delta is sub-significant (p=0.098) and absolute skill is below the persistence trivial, so no captioning lever is named. Marked not-passed to signal 'no family nominated', not a scoring failure.
- `g0-power-precondition`: PASS — PASS. Perm-null std of the pooled delta = 0.0168 => MDE(95%, one-sided) ~ 0.028 < 0.05 preregistered bar; pooled n_oos = 493 (lifesnaps) / 725 (pmdata). Powered cohort after in-script screen: 15 lifesnaps subjects / 793 labeled days (census said 26 usable — attrition from label-row availability in the canonical pool at feature-extraction time) and 11 pmdata / 1165 days (census 14). The null is POWERED: an effect of the preregistered 0.05 magnitude would have been seen.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `claim_false`

This is a POWERED NULL, not a directional hint — the mechanical 'up' comes from a +0.0003 delta that sits BELOW the permutation null mean (+0.0048, std 0.0168, perm p=0.6471, 50 perms), and g0 established MDE ~0.028 < the preregistered 0.05 bar on n_oos 493 lifesnaps / 725 pmdata. Non-HR daily-summary channels (sleep, rr, temp, hrv, time) add NOTHING to affect prediction within subject (multichannel AUC 0.6136 vs rich-HR-only 0.6133) and the linear check is outright negative (-0.025 lifesnaps / -0.0076 pmdata), closing E5's trait-dominance escape hatch: the lever is now null cross-subject AND within-subject. Real within-subject affect skill in lifesnaps does exist but lives in rich-HR alone (CI-positive over median-forward +0.0733[0.0453,0.1027] and persistence +0.1200[0.0838,0.1581]); pmdata is worse than trivial (Spearman 0.2761 vs day-persistence 0.3522), i.e. its stress signal is autocorrelation. Sub-daily/temporal features were NOT tested by this daily-summary design — that carve is a separate question.

Transfers to: [[hyp-affect-multichannel-supply-20260814]], [[hyp-e16-peri-ema-window-20260815]], [[hyp-e18-within-subject-state-20260815]], [[hyp-e24-lab-protocol-subdaily-state-20260815]]

## Notes
NULL RESULT — read as RULES OUT the multichannel-affect supply lever, now in BOTH regimes. CAUTION on the mechanical verdict: observed delta +0.0003 has direction 'up' only by sign; perm p = 0.6471 (null mean +0.0048 — the observed delta is BELOW the null mean). This is a powered null (g0: MDE ~0.028 < preregistered 0.05 bar), not a directional hint. Substantive findings: (1) PRIMARY lifesnaps within-subject multichannel increment +0.0003, p=0.65; linear check NEGATIVE (-0.025/-0.0076, logs/e14_linear_check.json) — E5's trait-dominance escape hatch is CLOSED: cross-subject (E5, delta -0.028 p=0.84) AND within-subject (E14) both null. The affect information simply is not in the added channels' daily summaries. (2) Genuine within-subject affect skill EXISTS in lifesnaps but lives in RICH-HR alone: hr_only AUC 0.6133, CI-positive over both trivials (median-forward +0.0733 [0.0453,0.1027]; persistence +0.1200 [0.0838,0.1581]) — within-person tension IS predictable from HR daily stats; adding sleep/rr/temp/hrv adds NOTHING (multichannel 0.6136). (3) pmdata: multichannel Spearman 0.2761 is CI-WORSE than day-persistence trivial 0.3522 — stress there is autocorrelation, not sensor-readable state. (4) g3: no family nominated; pmdata sleep +0.0293 carries the whole (sub-significant) delta but sits under the trivial. Cohort attrition vs census (15/26 lifesnaps, 11/14 pmdata) from label-row availability at feature time — power unaffected (g0 PASS). DO-NOT-RETRY: multichannel daily-summary affect supply, any grouping (cross-subject E5 + within-subject E14). SURVIVING levers for state: (a) rich-HR-only within-subject head (the only CI-positive affect instrument measured to date; candidate for PerUserHeadStore once supabase EMA labels accrue ~50-60 days); (b) sub-daily/temporal features — explicitly UNTESTED by this design (daily summaries only), carve as new hypothesis if pursued. Runtime 7017s CPU (1.2 cores eff.; joblib inner-RF parallelism useless at these sizes — parallelize the outer subject×perm loop next time). Artifacts: scratch/e14_within_subject_affect.py, scratch/e14_linear_check.py, logs/e14_within_subject_affect.json, logs/e14_linear_check.json, logs/e14_full.log.

#wearables #n-of-1 #cross-modal #optihealth #subject-heldout #representation-learning
