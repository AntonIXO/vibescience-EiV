---
artifacts: []
closed: true
config_note: 'Runner: scratch/e21_coverage_shortcut_premise.py -> logs/e21_coverage_shortcut_premise.json.
  Pure CPU, ~4 min. Corpus git_sha e849eb7, tensor [11318,1440,12], n=6000 windows
  sampled (seed 0), subject-grouped GroupKFold(5) so subject memorisation cannot explain
  decodability. RF(300, min_samples_leaf=2). Classes with <10 members dropped and
  the count reported.


  PREMISE RE-CHECK ONLY, no training. H5 was proposed 2026-08-08 and never run; its
  premise numbers were measured on a corpus that has since been REBUILT TWICE, and
  one of those commits (6d13a66) changed how COVERAGE ITSELF is scored — which is
  precisely the quantity H5''s claim is about. E20 had just demonstrated the cost
  of running a hypothesis whose corpus moved under it, so the premise was re-measured
  before any GPU was considered.


  Three feature sets, exactly as H5 specified: per-channel coverage (12), HR hourly
  presence pattern (24), physiology only (HR mean+std, 2). Two populations: incumbent
  all-datasets and the 6-dataset repaired allowlist.'
crash_reason: ''
created_at: '2026-08-15T06:58:17+00:00'
diagnostics_measured:
- after: 0.8278
  before: 0.9675
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
do_not_retry:
- Quoting 'provenance is ~99.7% decodable from per-channel coverage alone' (acc 0.9972
  / bal 0.9675). That figure predates the coverage-denominator change 6d13a66 and
  the day-anchoring rebuild e849eb7. On the live corpus it is acc 0.9381 / bal 0.8278.
  Any coverage-shortcut arm must be preregistered against the CURRENT numbers.
external_run: logs/e21_coverage_shortcut_premise.json
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: h5-the-shortcut-is-the-coverage-2aaaa0
id: exp-e21-coverage-shortcut-premise-20260815
key_numbers:
  incumbent_coverage_acc_now: 0.9381
  incumbent_coverage_bal_now: 0.8278
  incumbent_coverage_bal_then: 0.9675
  incumbent_hr_presence_bal_now: 0.4977
  incumbent_majority: 0.3952
  incumbent_physiology_bal_now: 0.215
  n_sampled: 6000.0
  repaired_coverage_acc_now: 0.9059
  repaired_coverage_bal_now: 0.8055
  repaired_coverage_bal_then: 0.98
  repaired_majority: 0.6179
  repaired_physiology_bal_now: 0.1969
  shortcut_vs_physiology_ratio: 3.85
observed_effects:
- delta: -0.1397
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  direction: down
parent_experiment_id: exp-e20-input-scaling-premise-20260815
prediction_match:
  overall: false
  per_diagnostic:
    eiv_effective_rank: false
    eiv_peak_excursion_increment_r2: false
provenance: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[h5-the-shortcut-is-the-coverage-2aaaa0]].
Branches from [[exp-e20-input-scaling-premise-20260815]].
External run: logs/e21_coverage_shortcut_premise.json

## Measurements
- [[eiv-dataset-provenance-shortcut-balacc]]: before 0.9675 → after 0.8278  (Δ -0.1397, down)

**Verdict:** inconclusive

## Key numbers
- `incumbent_coverage_acc_now`: 0.9381
- `incumbent_coverage_bal_now`: 0.8278
- `incumbent_coverage_bal_then`: 0.9675
- `incumbent_hr_presence_bal_now`: 0.4977
- `incumbent_majority`: 0.3952
- `incumbent_physiology_bal_now`: 0.215
- `n_sampled`: 6000
- `repaired_coverage_acc_now`: 0.9059
- `repaired_coverage_bal_now`: 0.8055
- `repaired_coverage_bal_then`: 0.98
- `repaired_majority`: 0.6179
- `repaired_physiology_bal_now`: 0.1969
- `shortcut_vs_physiology_ratio`: 3.85

## Do not retry
- Quoting 'provenance is ~99.7% decodable from per-channel coverage alone' (acc 0.9972 / bal 0.9675). That figure predates the coverage-denominator change 6d13a66 and the day-anchoring rebuild e849eb7. On the live corpus it is acc 0.9381 / bal 0.8278. Any coverage-shortcut arm must be preregistered against the CURRENT numbers.

## Notes
PREMISE SURVIVES, HEADLINE NUMBER DOES NOT. Recorded as directional rather than a clean pass because the mechanism holds while the magnitude H5 was written against has moved substantially.

WHAT HOLDS. Dataset provenance remains far more decodable from PRESENCE GEOMETRY than from physiology: coverage-only bal-acc 0.8278 vs physiology-only 0.2150 on the incumbent population, a 3.85x gap, subject-grouped so this is not subject memorisation. Coverage-only accuracy 0.9381 against a 0.3952 majority baseline. H5's mechanism claim — that InfoNCE can satisfy a batch by recognising provenance instead of reading physiology, and that encoder_format_version with_presence_v1 hands it that feature explicitly — is intact. H5's secondary observation also survives: the shortcut is essentially UNCHANGED by curating the allowlist (incumbent 0.8278 vs repaired 0.8055), which is what made 'suppress the signature, do not curate the list' the right framing.

WHAT MOVED. Coverage-only bal-acc fell 0.9675 -> 0.8278 on the incumbent list (-0.1397) and 0.9800 -> 0.8055 on the repaired list (-0.1745). HR hourly presence fell 0.6416 -> 0.4977 and 0.6050 -> 0.4411. The near-perfect shortcut H5 described no longer exists; a strong-but-imperfect one does. Most plausible cause: commit 6d13a66 changed the coverage denominator and which channels count toward it — i.e. it altered the exact quantity being decoded — and e849eb7 re-anchored the window grid.

WHY THIS IS RECORDED RATHER THAN ACTED ON. H5 carries NO gates and an EMPTY plan, so there is nothing preregistered to score, and its intervention (suppressing the coverage signature in Phase A) needs a GPU retrain. That arm is now justifiable but must be preregistered against 0.8278/0.8055, not 0.9675/0.9800, and its expected headroom is correspondingly smaller. Writing it up against the stale figures would be the same error E20 just exposed.

PROCESS LESSON, THIRD INSTANCE TODAY. E20 refuted a hypothesis whose premise the corpus had invalidated; E21 finds a premise the corpus has partially invalidated; both were caught in CPU minutes. Any hypothesis proposed before the last corpus rebuild must have its premise re-measured before its intervention is funded. The corpus git_sha belongs in every premise claim.

#dataset-whitelist #infonce #contrastive #collapse #baseline #epoch-resolution #wearables
