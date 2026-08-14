---
artifacts: []
closed: true
config_note: 'GPU: RTX 5070 (11.5 GiB usable). Corpus data/canonical [10338,1440,12],
  merge git_sha a85d085, mapping_hash 40db892013080d700e4d37149e974c08, normalization
  stats_sha256 5434f2ac872b738c1b024024c323274b337fa3c194ed122d1980123449ccd694. Eval
  pool data/eval_v3_1min REBUILT from this tensor: n=5889, 127 subjects, peak median
  132.56 bpm, tachy(>=120) 75.1%, trivial always-predict-median MAE 16.954 bpm, level-only
  R2(peak|day mean) +0.0040.


  BOTH ARMS RUN INSIDE THIS EXPERIMENT. The control is re-trained and re-measured
  here rather than compared to any stored number, because the e40 / v3 figures were
  produced on the PRE-REBUILD corpus and are not valid comparators.


  CONTROL      : phase_a_12ch_pmdata -> phase_b_12ch -> lora_proxy_12ch_clmbal

  INTERVENTION : phase_a_12ch_1min_wl -> phase_b_12ch_wl -> lora_proxy_12ch_wl

  configs/training.yaml gains &id_wl_12ch, &phase_b12, phase_b_12ch_wl, lora_proxy_12ch_wl,
  lora_proxy_12ch_wl_smoke. All six profiles verified to resolve via yaml.safe_load
  before launch.


  Text-embedding caches are keyed on tensor sha256 + dataset_filter, so the rebuild
  invalidates all of them and each arm recomputes its own - expected cost, not a fault.'
crash_reason: ''
created_at: '2026-08-08T12:39:07+00:00'
diagnostics_measured:
- after: 0.435
  before: 0.4102
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.7941
  before: 0.9945
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
- after: 0.9597
  before: 0.9553
  diagnostic_id: eiv_heldout_hr_r2
- after: -0.1719
  before: -0.2031
  diagnostic_id: eiv_gen_bucket_acc_over_majority
external_run: ''
gate_results:
- evidence: 'FAIL. Subject-grouped GroupKFold OOF on data/eval_v3_1min (n=5889, 127
    subjects), Phase B soft-token prefix, single seed 42 in BOTH arms. eiv_peak_excursion_increment_r2:
    repaired(v4w) +0.4350 vs incumbent(v4c) +0.4102 => delta +0.0249 against a preregistered
    bar of >= +0.03. Level-only baseline identical at +0.0040 in both arms, so the
    increment is measured above the same trivial predictor. Absolute peak R2: v4c
    0.4142, v4w 0.4390. Both arms re-trained A->B->C inside this experiment on the
    rebuilt corpus; no stored pre-rebuild number was used as a comparator. Runner
    scratch/run_hdataset_ab.sh, all 12 stages RC=0, reports outputs/reports/v4{c,w}_phase_b.json,
    gate reader scratch/read_hdataset_gates.py.'
  gate_id: gate-peak-increment-improves
  passed: false
- evidence: 'PASS. eiv_heldout_hr_r2 on the same pool: repaired(v4w) 0.9597 vs incumbent(v4c)
    0.9553 => delta +0.0044, bar >= -0.02. hr_mean MAE 1.290 (v4c) -> 1.178 (v4w)
    bpm against the pool''s trivial always-predict-median MAE of 16.954 bpm. The saturated
    channel did not regress; the repaired allowlist did not break alignment. Note
    this gate cannot rescue the primary gate, which failed on its own terms.'
  gate_id: gate-hr-mean-no-regression
  passed: true
- evidence: 'FAIL - and it fails because the mechanism this gate demanded was FABRICATED,
    which is exactly what the gate was written to catch. Phase B required a ''state_label
    x dataset purity table with the count of non-vacuous triplets''. The table I previously
    reported (99.0% of samples carry NONE; every non-NONE state a 100%-pure dataset
    identifier: exam_stress=wesad, transient=wesad, n_back=brain_wearable, high/medium=nurse_stress)
    was read from meta.get(''state_label'',''NONE''). `state_label` IS NOT A KEY in
    data/canonical/sample_metadata.json - verified over all 10338 records (keys present:
    subject_id, dataset, start_time, end_time, coverage, measured_coverage, labels).
    Every record returned the default and the uniform default was reported as a finding.
    State is DERIVED at load time by infer_state_label() (src/training/datasets.py:139)
    from the per-sample `labels` dict. MEASURED TRUTH (scratch/verify_state_purity.py):
    neutral 47.5% / stress 32.3% / flow 20.2%; 5427 samples (52.5%) carry a usable
    non-neutral triplet anchor, NOT 1%; no state is dataset-pure (stress is 45.3%
    pmdata, flow 70.7% dryad); the six dropped datasets supply 85 of 5427 non-neutral
    anchors = 1.6%, not ''the only non-vacuous triplets''. Phase A mechanism (provenance
    shortcut bal-acc, structural features only, subject-grouped OOF) WAS measured
    honestly: incumbent 10-ds 0.9945 vs chance 0.1250; repaired 5-ds 0.7941 vs chance
    0.2000; repaired+dreamt 0.8252 vs chance 0.1667; incumbent C 3-ds 0.6592 vs chance
    0.3333. Phase C marginal moved barely (tachy 0.295->0.302, medPeak 131.8->132.2)
    while subjects went 81->216. Since Phase B''s mechanism quantity is unfounded
    rather than merely unmoved, the per-phase attribution is INCOMPLETE and this blocking
    gate must be recorded as failed, not waived.'
  gate_id: gate-per-phase-mechanism-attributed
  passed: false
- evidence: 'PASS (honesty guard observed). |+0.0249| < the documented Phase-A seed
    spread of 0.066, so the primary result is written up as ''NO EVIDENCE OF DIFFERENCE
    AT ONE SEED'' - neither a win nor a refutation of the repaired allowlist. Corroborating
    noise signature: linear and nonlinear increments move in OPPOSITE directions at
    both phases (Phase A linear -0.0179 / nonlinear +0.0523; Phase B linear +0.0249
    / nonlinear -0.0477), and Phase A hr_mean R2 delta -0.0031. Every one of these
    sits inside the band. Per the user''s standing constraint only one seed was run
    (seed 42, unset in all six profiles, falling through to the pipeline default),
    which is precisely why this guard exists instead of a significance test.'
  gate_id: gate-seed-band-honesty
  passed: true
- evidence: 'FAIL - BOTH ARMS REMAIN NEGATIVE, stated in the first line as the gate
    requires: the allowlist did NOT fix the deliverable, whatever it did to the encoder.
    eiv_gen_bucket_acc_over_majority with the majority baseline recomputed on the
    scored pool (probe''s own np.random.RandomState(456), capped at min(num_samples,128)):
    incumbent v4c hr_acc 0.5703 (73/128) vs majority ''tachycardic'' 0.7734 => -0.2031;
    repaired v4w hr_acc 0.6016 (77/128) vs the same 0.7734 => -0.1719. v4w improves
    on v4c by +3.1 points and is STILL 17 points below always emitting one constant
    label. state_acc: v4c 0.5625 (72/128) vs majority ''neutral'' 0.5547 = +0.008
    (noise); v4w 0.4297 (55/128) = BELOW its own majority. CONTEXT THAT MAKES THIS
    THE HEADLINE: the p1 linear probe on the same v4w checkpoint reads hr_r2 0.9672
    / MAE 1.134 bpm against the pool''s trivial always-median MAE of 16.954 bpm. Information
    IS present in the soft tokens and is lost on the way to text - a DECODING failure,
    not a representation failure. Consequence: any Phase C delta between arms runs
    through a broken readout and is untrustworthy in EITHER direction, including the
    +3.1 that favours my intervention. DISCARDED as degenerate rather than reported:
    p3 counterfactual (swap_total 0, swap_measured false - undefined, not zero), p6
    shuffle control (ordered 0.0 / shuffled 0.0 / gap 0.0, both arms dead), p4 deep_sleep_acc
    (only 18/128 scorable). hrv_acc 0.8125 vs 0.6897 is NOT comparable across arms:
    those are 13/16 and 20/29, denominators set by emission not by ground-truth availability
    (52.1% of the eval pool carries no HRV at all).'
  gate_id: gate-emission-honesty
  passed: false
git_ref: ''
hypothesis_id: on-the-rebuilt-canonical-corpus-commits-c948d5c-00787b2-4a59ca5-tensor-10338-144
id: on-the-rebuilt-canonical-corpus-commits-c948d5c-00787b2-4a59ca5-tensor-10338-144-exp-2026-08-08t12-39-07
observed_effects:
- delta: 0.0248
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
- delta: -0.2004
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  direction: down
- delta: 0.0044
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
- delta: 0.0312
  diagnostic_id: eiv_gen_bucket_acc_over_majority
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv-dataset-provenance-shortcut-balacc: true
    eiv_gen_bucket_acc_over_majority: true
    eiv_heldout_hr_r2: false
    eiv_peak_excursion_increment_r2: true
provenance: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[on-the-rebuilt-canonical-corpus-commits-c948d5c-00787b2-4a59ca5-tensor-10338-144]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4102 → after 0.435  (Δ +0.0248, up ✓ predicted up)
- [[eiv-dataset-provenance-shortcut-balacc]]: before 0.9945 → after 0.7941  (Δ -0.2004, down ✓ predicted down)
- [[eiv_heldout_hr_r2]]: before 0.9553 → after 0.9597  (Δ +0.0044, up ✗ predicted none)
- [[eiv_gen_bucket_acc_over_majority]]: before -0.2031 → after -0.1719  (Δ +0.0312, up ✓ predicted up)

## Gates
- `gate-peak-increment-improves`: **FAIL** — FAIL. Subject-grouped GroupKFold OOF on data/eval_v3_1min (n=5889, 127 subjects), Phase B soft-token prefix, single seed 42 in BOTH arms. eiv_peak_excursion_increment_r2: repaired(v4w) +0.4350 vs incumbent(v4c) +0.4102 => delta +0.0249 against a preregistered bar of >= +0.03. Level-only baseline identical at +0.0040 in both arms, so the increment is measured above the same trivial predictor. Absolute peak R2: v4c 0.4142, v4w 0.4390. Both arms re-trained A->B->C inside this experiment on the rebuilt corpus; no stored pre-rebuild number was used as a comparator. Runner scratch/run_hdataset_ab.sh, all 12 stages RC=0, reports outputs/reports/v4{c,w}_phase_b.json, gate reader scratch/read_hdataset_gates.py.
- `gate-hr-mean-no-regression`: PASS — PASS. eiv_heldout_hr_r2 on the same pool: repaired(v4w) 0.9597 vs incumbent(v4c) 0.9553 => delta +0.0044, bar >= -0.02. hr_mean MAE 1.290 (v4c) -> 1.178 (v4w) bpm against the pool's trivial always-predict-median MAE of 16.954 bpm. The saturated channel did not regress; the repaired allowlist did not break alignment. Note this gate cannot rescue the primary gate, which failed on its own terms.
- `gate-per-phase-mechanism-attributed`: **FAIL** — FAIL - and it fails because the mechanism this gate demanded was FABRICATED, which is exactly what the gate was written to catch. Phase B required a 'state_label x dataset purity table with the count of non-vacuous triplets'. The table I previously reported (99.0% of samples carry NONE; every non-NONE state a 100%-pure dataset identifier: exam_stress=wesad, transient=wesad, n_back=brain_wearable, high/medium=nurse_stress) was read from meta.get('state_label','NONE'). `state_label` IS NOT A KEY in data/canonical/sample_metadata.json - verified over all 10338 records (keys present: subject_id, dataset, start_time, end_time, coverage, measured_coverage, labels). Every record returned the default and the uniform default was reported as a finding. State is DERIVED at load time by infer_state_label() (src/training/datasets.py:139) from the per-sample `labels` dict. MEASURED TRUTH (scratch/verify_state_purity.py): neutral 47.5% / stress 32.3% / flow 20.2%; 5427 samples (52.5%) carry a usable non-neutral triplet anchor, NOT 1%; no state is dataset-pure (stress is 45.3% pmdata, flow 70.7% dryad); the six dropped datasets supply 85 of 5427 non-neutral anchors = 1.6%, not 'the only non-vacuous triplets'. Phase A mechanism (provenance shortcut bal-acc, structural features only, subject-grouped OOF) WAS measured honestly: incumbent 10-ds 0.9945 vs chance 0.1250; repaired 5-ds 0.7941 vs chance 0.2000; repaired+dreamt 0.8252 vs chance 0.1667; incumbent C 3-ds 0.6592 vs chance 0.3333. Phase C marginal moved barely (tachy 0.295->0.302, medPeak 131.8->132.2) while subjects went 81->216. Since Phase B's mechanism quantity is unfounded rather than merely unmoved, the per-phase attribution is INCOMPLETE and this blocking gate must be recorded as failed, not waived.
- `gate-seed-band-honesty`: PASS — PASS (honesty guard observed). |+0.0249| < the documented Phase-A seed spread of 0.066, so the primary result is written up as 'NO EVIDENCE OF DIFFERENCE AT ONE SEED' - neither a win nor a refutation of the repaired allowlist. Corroborating noise signature: linear and nonlinear increments move in OPPOSITE directions at both phases (Phase A linear -0.0179 / nonlinear +0.0523; Phase B linear +0.0249 / nonlinear -0.0477), and Phase A hr_mean R2 delta -0.0031. Every one of these sits inside the band. Per the user's standing constraint only one seed was run (seed 42, unset in all six profiles, falling through to the pipeline default), which is precisely why this guard exists instead of a significance test.
- `gate-emission-honesty`: **FAIL** — FAIL - BOTH ARMS REMAIN NEGATIVE, stated in the first line as the gate requires: the allowlist did NOT fix the deliverable, whatever it did to the encoder. eiv_gen_bucket_acc_over_majority with the majority baseline recomputed on the scored pool (probe's own np.random.RandomState(456), capped at min(num_samples,128)): incumbent v4c hr_acc 0.5703 (73/128) vs majority 'tachycardic' 0.7734 => -0.2031; repaired v4w hr_acc 0.6016 (77/128) vs the same 0.7734 => -0.1719. v4w improves on v4c by +3.1 points and is STILL 17 points below always emitting one constant label. state_acc: v4c 0.5625 (72/128) vs majority 'neutral' 0.5547 = +0.008 (noise); v4w 0.4297 (55/128) = BELOW its own majority. CONTEXT THAT MAKES THIS THE HEADLINE: the p1 linear probe on the same v4w checkpoint reads hr_r2 0.9672 / MAE 1.134 bpm against the pool's trivial always-median MAE of 16.954 bpm. Information IS present in the soft tokens and is lost on the way to text - a DECODING failure, not a representation failure. Consequence: any Phase C delta between arms runs through a broken readout and is untrustworthy in EITHER direction, including the +3.1 that favours my intervention. DISCARDED as degenerate rather than reported: p3 counterfactual (swap_total 0, swap_measured false - undefined, not zero), p6 shuffle control (ordered 0.0 / shuffled 0.0 / gap 0.0, both arms dead), p4 deep_sleep_acc (only 18/128 scorable). hrv_acc 0.8125 vs 0.6897 is NOT comparable across arms: those are 13/16 and 20/29, denominators set by emission not by ground-truth availability (52.1% of the eval pool carries no HRV at all).

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
CLOSED 2026-08-08. Code: git 32b0ae3 on branch fix/soft-token-norm-scale.

HEADLINE, per gate-emission-honesty's first-line requirement: THE ALLOWLIST DID NOT FIX THE DELIVERABLE. Both arms emit HR buckets BELOW an always-predict-majority constant (v4c -0.2031, v4w -0.1719 against majority 0.7734 on the probe's own RandomState(456) 128-index pool).

PRIMARY GATE FAILED. gate-peak-increment-improves: +0.4350 - +0.4102 = +0.0249 against a preregistered bar of >= +0.03. NOT reworded, NOT rescued by the two gates that passed. 2 of 3 BLOCKING gates failed (peak-increment, per-phase-mechanism); gate-hr-mean-no-regression passed at +0.0044.

AND the honest reading is weaker still than 'failed': |+0.0249| sits INSIDE the documented 0.066 single-seed band, so per gate-seed-band-honesty this is NO EVIDENCE OF DIFFERENCE AT ONE SEED - neither a win nor a refutation of the repaired allowlist. Corroborating noise signature: linear and nonlinear increments move in OPPOSITE directions at both phases (A: -0.0179 / +0.0523; B: +0.0249 / -0.0477). Two ablation probes move AGAINST the intervention (monotonic_scale_response 0.500->0.125, null_sensor_bucket_disagreement 0.484->0.250).

SELF-CORRECTION - A FABRICATED MECHANISM. gate-per-phase-mechanism-attributed failed because the Phase B mechanism I reported did not exist. The claim '99.0% of samples carry state_label NONE and every non-NONE state is a 100%-pure dataset identifier' was read from meta.get('state_label','NONE'); `state_label` is NOT a key in sample_metadata.json, so all 10338 records returned the default and the uniform default was reported as a finding. State is DERIVED at load time by infer_state_label() (src/training/datasets.py:139). Measured truth: neutral 47.5% / stress 32.3% / flow 20.2%; 52.5% (5427) of the corpus carries a usable non-neutral triplet anchor, not 1%; no state is dataset-pure; the six dropped datasets supply 85 of 5427 non-neutral anchors = 1.6%. The COVERAGE justification for the allowlist survives (HR coverage 0.022-0.157, zero strict-clean days, 93 samples = 0.9% of corpus); the mechanism story for it does not. A second claim, '99.0% carry NONE', died with it. The coverage claim was confirmed at 93 samples, not the 97 I had quoted from a summary.

WHAT THE RUN ACTUALLY BOUGHT (the separable, real finding): Phase A/B representation is excellent while Phase C generation is broken. v4w p1 linear probe reads hr_r2 0.9672 / MAE 1.134 bpm against the eval pool's trivial always-predict-median MAE of 16.954 bpm, yet the same checkpoint emits buckets 17 points below a constant. Information IS present in the soft tokens and is lost on the way to text - a DECODING failure, not a representation failure. Consequence: every Phase C delta in this experiment runs through a broken readout and is untrustworthy in EITHER direction, including the +3.1 points that favour my own intervention. This outranks H-POLL and H-WINDOW as the next hypothesis.

FOLLOW-UP MEASURED AFTER THE ARMS (does 'whitelist MORE datasets' help?): NO for the dropped six - they are empty on every channel (hrv coverage 0.0004-0.072, nurse_stress exactly 0.000). But pmdata, 2045 samples of the CURRENT whitelist, carries hrv_rmssd coverage of EXACTLY 0.0000, and dryad_ifh_affect (excluded, n=4094) carries HRV on 4094/4094 samples at 0.307 coverage - 2x the pooled whitelist rate of 0.155. Re-admitting dryad would grow the HRV-bearing pool +137.9% (3024 -> 7195). BLOCKER: dryad is SPARSE (density 0.422 over span 1155.9 minutes, suspected 15-min->1-min broadcast defect) with a degenerate peak marginal (median 89.0 bpm vs corpus 131.8). Correct order is FIX THE BROADCAST, THEN whitelist it - not 'whitelist more' generally.

Also corrected: 'Phase C is worse than before' is FALSE. Across every historical suite on disk, v4w 0.6016 (77/128) is the HIGHEST Phase C emission accuracy on record (12ch_v1_lora 0/1, v2_arm1 5/96, v2_arm2 1/6, v3_1min_e2e 59/128, v4c 73/128). Two traps in that column: the two v3 rows are the SAME run scored twice (0.047 on mean cutpoints vs 0.461 rescored on peak corpus cutpoints), and early rows have denominators of 1, 6 and 96. The problem is unchanged, not worsened.

TOOLING FIX SHIPPED (32b0ae3): src/probes/bucket_parser.py now returns *_total, *_majority_baseline and *_skill_over_majority for hr/hrv/deep_sleep/state, and returns NaN instead of 0.0 for unscored buckets. This makes the two misreadings that occurred in this review unreachable from the payload: (a) hrv_acc 0.8125 vs 0.6897 as a 'regression' when they are 13/16 and 20/29 on emission-determined subsets (52.1% of the eval pool carries no HRV at all), and (b) any bucket accuracy quoted without its constant-predictor baseline. Locked by 7 tests in tests/test_bucket_baseline_reporting.py. Full suite 13 failed / 292 passed, with the identical 13 failures present on a clean tree - pre-existing, not from this change.

DO NOT RETRY the dataset allowlist as a lever without new evidence. The repaired 6-dataset list remains defensible on coverage grounds and is retained for reproducibility, but it has no demonstrated effect on what the model extracts or emits.

#cross-modal #sensor-text #medgemma #epoch-resolution #dataset-whitelist #wearables #baseline
