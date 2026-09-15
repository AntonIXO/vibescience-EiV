---
artifacts:
- logs/a2_affect_eval_13879.json
- logs/a2_affect_13879.log
- scratch/_valence_reachability.py
- scratch/_what_new_data_bought.py
- configs/canonical/labels.yaml
- configs/canonical/privileged.yaml
closed: true
config_note: 'A2 affect probe re-run on the 2026-08-30 corpus [13879,1440,12] (merge
  git_sha 56bb0b5). Runner scratch/a2_affect_eval.py UNCHANGED in its statistics,
  --arms baseline_sync -> logs/a2_affect_eval_13879.json. RC=0, no tracebacks. Probe
  only, no training.


  PRECONDITION EXECUTED AND VERIFIED FIRST (this is what made the run meaningful):
  popane_s5/s6 had no source block in configs/canonical/labels.yaml, so declared_range()
  -> None -> scale_to_declared_range() -> None -> affect_patch_targets() returned
  an all-zero mask. 10,232 valence epochs sat in label_events.parquet invisible to
  the trainer. Measured 0/40 sampled windows carrying any affect target before the
  fix, 40/40 after declaring both blocks with the same [0,10] Response Meter span
  as study 3.


  Encoder: outputs/checkpoints/baseline_sync/phase_a (PL120/ST60), the only trained
  Phase-A on this host built from the current data law. Only ONE arm was available,
  so the arm-vs-sampling-matched-control contrast from the original run could NOT
  be reproduced; this run tests recoverability at the new supply, not the head''s
  effect.


  Nulls are the script''s own 40-permutation within-dataset subject->target reassignment
  (within-subject permutation is the identity at one session per subject).'
cost:
  gpu_hours: 0.03
  wall_hours: 0.06
crash_reason: ''
created_at: '2026-08-29T22:09:22+00:00'
diagnostics_measured:
- after: -0.047
  before: 0.0
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry:
- Do NOT reopen per-patch valence recoverability on a supply argument again. The original
  rule-out was supply-conditional at 167 subjects; this run tripled it to 506 across
  4 datasets and the effect went the WRONG WAY against a within-dataset null (-0.047,
  z -1.19). Two independent supplies now agree. A future reopen needs a different
  MECHANISM (denser within-session labels, a different readout stage, per-dataset
  modelling), not more subjects of the same kind.
- Do NOT run this probe against a GLOBAL shuffle null. The within-dataset null sits
  at +0.169 while the observed r is +0.122 -- a global shuffle would land near 0 and
  turn this powered null into a false positive. The permutation must reassign subject->target
  INSIDE each dataset.
- Do NOT declare dapper or k_emocon valence into affect_patch_targets without first
  showing their scales are commensurable with the 0-10 Response Meter. dapper is 1-5
  and k_emocon 2.08-4.00; pooling different instruments on a shared axis is the exact
  error scale_to_declared_range exists to prevent, and it would silently inflate the
  between-dataset structure that already dominates this null.
- 'Do NOT read the labels.yaml/privileged.yaml fixes as part of the null. They are
  independent repairs: 10,232 valence epochs and 18,783 privileged EDA epochs were
  present in the parquet and invisible to the trainer. Those stand regardless of this
  probe''s verdict.'
external_run: logs/a2_affect_eval_13879.json
former_ids: []
gate_results:
- evidence: FAIL, and it is a POWERED null rather than a near-miss. valence_mean r
    = +0.122 (R2 +0.015, n=1012 cells / 506 subjects) against its own within-dataset
    null of +0.169 +- 0.039 -> over_null -0.047, z -1.19, perm p 0.875. The observed
    correlation is POSITIVE and still BELOW what subject->target reassignment inside
    the same datasets produces by itself. Null sd 0.039 gives an MDE around +0.08
    at this n, and the preregistered bar was +0.05; the measured value is negative,
    so the lever is eliminated at this supply rather than left ambiguous. valence_spread
    is at chance (+0.117 vs null +0.113, z +0.08). arousal_mean stays CASE-only (n=60,
    30 subjects) and is uninformative as before (z +1.00 on a null sd of 0.207).
  gate_id: g1-valence-clears-its-null
  passed: false
- evidence: 'PASS as a procedure, and it is what makes the g1 failure trustworthy.
    The null is the script''s own within-dataset subject->target reassignment, so
    dataset identity is held fixed inside every permutation and cannot manufacture
    the effect. That is exactly why the null sits at +0.169 rather than 0: a large
    part of the apparent pooled correlation IS between-dataset structure, and the
    permutation reproduces it. Had a global shuffle been used, the null would have
    landed near 0 and r=+0.122 would have looked like a clean positive result -- the
    single most likely way this run could have produced a false claim. No separate
    per-dataset breakdown was needed to reach the decision because the pooled effect
    already fails against the stricter null.'
  gate_id: g2-within-dataset-null-not-provenance
  passed: true
- evidence: 'PASS, and the accounting is the reason this run was worth doing. Realised
    supply 1012 valence cells / 506 subjects / 4 datasets (case, popane_s3, popane_s5,
    popane_s6) against the pre-fix baseline of 334 cells / 167 subjects / 2 datasets
    -- 3.0x the cells and 3.0x the subjects. Note the shortfall against the 955-subject
    figure quoted in the hypothesis: dapper (418 subjects) and k_emocon (31) carry
    valence in label_events.parquet but on undeclared ranges (dapper 1-5, k_emocon
    2.08-4.00), so scale_to_declared_range still returns None for them and they remain
    outside the affect target. Declaring those two is a separate, deliberate decision
    -- dapper''s 1-5 scale is a DIFFERENT instrument from the 0-10 dial, and pooling
    them without evidence they are commensurable is precisely the error scale_to_declared_range
    exists to prevent.'
  gate_id: g3-supply-actually-realised
  passed: true
git_ref: ''
hypothesis_id: at-the-rebuilt-corpus-s-valence-supply-21-710-a9a6e0
id: exp-a2-affect-resupply-20260830
interpretation:
  at: '2026-08-29T22:10:28+00:00'
  failure_mode: claim_false
  lesson: "TRIPLING THE SUPPLY DID NOT RESCUE VALENCE, AND THE VAULT'S BASE RATE PREDICTED
    THAT BETTER THAN I DID. I forecast +0.05..+0.12 over null and wrote a calibration
    warning into the hypothesis saying E14, E5 and W8 had all gone the other way on
    'more data rescues a measured null'. They did again: r +0.122 against a within-dataset
    null of +0.169, over_null -0.047, z -1.19, at 3.0x the cells and 3.0x the subjects.
    The retraction of the old rule-out was still correct procedure -- it was explicitly
    supply-conditional and the supply changed -- but the answer did not.\n\nTHE NULL'S
    LOCATION IS THE ACTUAL RESULT. A +0.169 null on a target whose observed r is +0.122
    says most of the apparent pooled correlation is BETWEEN-DATASET structure: four
    corpora with different valence and HR distributions, on an axis where extent alone
    already decodes provenance at 12.03x chance. Anyone scoring this against a global
    shuffle would have gotten a null near 0, reported r=+0.122 as a clean positive,
    and shipped a provenance detector as an affect model. This is the second time
    in one session that the choice of null/aggregation decided a verdict (W12's mean-vs-worst-case
    was the first) -- the statistic is not bookkeeping, it IS the experiment.\n\nTWO
    SILENT-DROP DEFECTS FOUND WHILE SETTING THIS UP, and they outlast the null:\n
    \ (1) labels.yaml had no source block for popane_s5/s6, so declared_range -> None
    -> scale_to_declared_range -> None -> affect_patch_targets returned an all-zero
    mask. 10,232 valence epochs sat in label_events.parquet completely invisible to
    training. Measured 0/40 windows before, 40/40 after. Had I not checked reachability
    first, this experiment would have measured the OLD supply while reporting the
    new one -- a null that meant nothing, recorded as if it meant something.\n  (2)
    privileged.yaml had no blocks for dapper, k_emocon, popane_s5, popane_s6 -- 18,783
    privileged EDA epochs from undeclared datasets, dapper alone being ~63% of the
    corpus's entire EDA supply. tests/test_privileged_channels.py caught this, which
    is exactly what that test exists for.\n\nREUSABLE RULE, and the sharpest thing
    here: AFTER A CORPUS REBUILD, VERIFY THAT NEW LABELS REACH THE TRAINER BEFORE
    MEASURING ANYTHING WITH THEM. Count rows in the parquet, then count windows with
    a non-empty target mask, and require the second number to be non-zero for every
    newly-added corpus. Both registries in this repo are declare-or-silently-drop,
    and neither raises. A label that exists on disk and is invisible downstream produces
    a confident null about a supply that was never actually present.\n\nWHAT REMAINS
    OPEN, stated narrowly: dapper (418 subjects, 1-5 scale) and k_emocon (31, 2.08-4.00)
    still carry valence outside the affect target because their instruments are not
    commensurable with the 0-10 dial. That is a deliberate exclusion, not an oversight,
    and pooling them would inflate exactly the between-dataset structure this null
    is already dominated by. If valence is ever revisited it should be per-dataset
    or within-subject, not pooled harder."
  transfers_to:
  - h-w8-cardinality-invariant-attention-pooling-f01250
key_numbers:
  arousal_mean_n: 60.0
  arousal_mean_z: 1.0
  n_cells: 1012.0
  n_cells_before: 334.0
  n_datasets: 4.0
  n_subjects: 506.0
  n_subjects_before: 167.0
  preregistered_bar: 0.05
  valence_mean_null_mean: 0.169
  valence_mean_null_sd: 0.039
  valence_mean_over_null: -0.047
  valence_mean_perm_p: 0.875
  valence_mean_r: 0.122
  valence_mean_z: -1.19
  valence_spread_z: 0.08
observed_effects:
- delta: -0.047
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[at-the-rebuilt-corpus-s-valence-supply-21-710-a9a6e0]].
External run: logs/a2_affect_eval_13879.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.0 → after -0.047  (Δ -0.047, down ✗ predicted up)

## Gates
- `g1-valence-clears-its-null`: **FAIL** — FAIL, and it is a POWERED null rather than a near-miss. valence_mean r = +0.122 (R2 +0.015, n=1012 cells / 506 subjects) against its own within-dataset null of +0.169 +- 0.039 -> over_null -0.047, z -1.19, perm p 0.875. The observed correlation is POSITIVE and still BELOW what subject->target reassignment inside the same datasets produces by itself. Null sd 0.039 gives an MDE around +0.08 at this n, and the preregistered bar was +0.05; the measured value is negative, so the lever is eliminated at this supply rather than left ambiguous. valence_spread is at chance (+0.117 vs null +0.113, z +0.08). arousal_mean stays CASE-only (n=60, 30 subjects) and is uninformative as before (z +1.00 on a null sd of 0.207).
- `g2-within-dataset-null-not-provenance`: PASS — PASS as a procedure, and it is what makes the g1 failure trustworthy. The null is the script's own within-dataset subject->target reassignment, so dataset identity is held fixed inside every permutation and cannot manufacture the effect. That is exactly why the null sits at +0.169 rather than 0: a large part of the apparent pooled correlation IS between-dataset structure, and the permutation reproduces it. Had a global shuffle been used, the null would have landed near 0 and r=+0.122 would have looked like a clean positive result -- the single most likely way this run could have produced a false claim. No separate per-dataset breakdown was needed to reach the decision because the pooled effect already fails against the stricter null.
- `g3-supply-actually-realised`: PASS — PASS, and the accounting is the reason this run was worth doing. Realised supply 1012 valence cells / 506 subjects / 4 datasets (case, popane_s3, popane_s5, popane_s6) against the pre-fix baseline of 334 cells / 167 subjects / 2 datasets -- 3.0x the cells and 3.0x the subjects. Note the shortfall against the 955-subject figure quoted in the hypothesis: dapper (418 subjects) and k_emocon (31) carry valence in label_events.parquet but on undeclared ranges (dapper 1-5, k_emocon 2.08-4.00), so scale_to_declared_range still returns None for them and they remain outside the affect target. Declaring those two is a separate, deliberate decision -- dapper's 1-5 scale is a DIFFERENT instrument from the 0-10 dial, and pooling them without evidence they are commensurable is precisely the error scale_to_declared_range exists to prevent.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

TRIPLING THE SUPPLY DID NOT RESCUE VALENCE, AND THE VAULT'S BASE RATE PREDICTED THAT BETTER THAN I DID. I forecast +0.05..+0.12 over null and wrote a calibration warning into the hypothesis saying E14, E5 and W8 had all gone the other way on 'more data rescues a measured null'. They did again: r +0.122 against a within-dataset null of +0.169, over_null -0.047, z -1.19, at 3.0x the cells and 3.0x the subjects. The retraction of the old rule-out was still correct procedure -- it was explicitly supply-conditional and the supply changed -- but the answer did not.

THE NULL'S LOCATION IS THE ACTUAL RESULT. A +0.169 null on a target whose observed r is +0.122 says most of the apparent pooled correlation is BETWEEN-DATASET structure: four corpora with different valence and HR distributions, on an axis where extent alone already decodes provenance at 12.03x chance. Anyone scoring this against a global shuffle would have gotten a null near 0, reported r=+0.122 as a clean positive, and shipped a provenance detector as an affect model. This is the second time in one session that the choice of null/aggregation decided a verdict (W12's mean-vs-worst-case was the first) -- the statistic is not bookkeeping, it IS the experiment.

TWO SILENT-DROP DEFECTS FOUND WHILE SETTING THIS UP, and they outlast the null:
  (1) labels.yaml had no source block for popane_s5/s6, so declared_range -> None -> scale_to_declared_range -> None -> affect_patch_targets returned an all-zero mask. 10,232 valence epochs sat in label_events.parquet completely invisible to training. Measured 0/40 windows before, 40/40 after. Had I not checked reachability first, this experiment would have measured the OLD supply while reporting the new one -- a null that meant nothing, recorded as if it meant something.
  (2) privileged.yaml had no blocks for dapper, k_emocon, popane_s5, popane_s6 -- 18,783 privileged EDA epochs from undeclared datasets, dapper alone being ~63% of the corpus's entire EDA supply. tests/test_privileged_channels.py caught this, which is exactly what that test exists for.

REUSABLE RULE, and the sharpest thing here: AFTER A CORPUS REBUILD, VERIFY THAT NEW LABELS REACH THE TRAINER BEFORE MEASURING ANYTHING WITH THEM. Count rows in the parquet, then count windows with a non-empty target mask, and require the second number to be non-zero for every newly-added corpus. Both registries in this repo are declare-or-silently-drop, and neither raises. A label that exists on disk and is invisible downstream produces a confident null about a supply that was never actually present.

WHAT REMAINS OPEN, stated narrowly: dapper (418 subjects, 1-5 scale) and k_emocon (31, 2.08-4.00) still carry valence outside the affect target because their instruments are not commensurable with the 0-10 dial. That is a deliberate exclusion, not an oversight, and pooling them would inflate exactly the between-dataset structure this null is already dominated by. If valence is ever revisited it should be per-dataset or within-subject, not pooled harder.

Transfers to: [[h-w8-cardinality-invariant-attention-pooling-f01250]]

## Key numbers
- `arousal_mean_n`: 60
- `arousal_mean_z`: 1
- `n_cells`: 1012
- `n_cells_before`: 334
- `n_datasets`: 4
- `n_subjects`: 506
- `n_subjects_before`: 167
- `preregistered_bar`: 0.05
- `valence_mean_null_mean`: 0.169
- `valence_mean_null_sd`: 0.039
- `valence_mean_over_null`: -0.047
- `valence_mean_perm_p`: 0.875
- `valence_mean_r`: 0.122
- `valence_mean_z`: -1.19
- `valence_spread_z`: 0.08

## Artifacts
- `logs/a2_affect_eval_13879.json`
- `logs/a2_affect_13879.log`
- `scratch/_valence_reachability.py`
- `scratch/_what_new_data_bought.py`
- `configs/canonical/labels.yaml`
- `configs/canonical/privileged.yaml`

## Do not retry
- Do NOT reopen per-patch valence recoverability on a supply argument again. The original rule-out was supply-conditional at 167 subjects; this run tripled it to 506 across 4 datasets and the effect went the WRONG WAY against a within-dataset null (-0.047, z -1.19). Two independent supplies now agree. A future reopen needs a different MECHANISM (denser within-session labels, a different readout stage, per-dataset modelling), not more subjects of the same kind.
- Do NOT run this probe against a GLOBAL shuffle null. The within-dataset null sits at +0.169 while the observed r is +0.122 -- a global shuffle would land near 0 and turn this powered null into a false positive. The permutation must reassign subject->target INSIDE each dataset.
- Do NOT declare dapper or k_emocon valence into affect_patch_targets without first showing their scales are commensurable with the 0-10 Response Meter. dapper is 1-5 and k_emocon 2.08-4.00; pooling different instruments on a shared axis is the exact error scale_to_declared_range exists to prevent, and it would silently inflate the between-dataset structure that already dominates this null.
- Do NOT read the labels.yaml/privileged.yaml fixes as part of the null. They are independent repairs: 10,232 valence epochs and 18,783 privileged EDA epochs were present in the parquet and invisible to the trainer. Those stand regardless of this probe's verdict.

## Notes
THE NULL SURVIVES A TRIPLED SUPPLY, and it is now a stronger negative than the one it replaces.

valence_mean r +0.122 (R2 +0.015) vs its own within-dataset null +0.169 +- 0.039 -> over_null -0.047, z -1.19, perm p 0.875, at n=1012 cells / 506 subjects / 4 datasets (was 334 / 167 / 2). valence_spread at chance (z +0.08). arousal_mean still CASE-only and uninformative (n=60, z +1.00 on null sd 0.207).

THE +0.169 NULL IS THE FINDING. The observed correlation is positive, and someone scoring this against a global shuffle would have reported a clean win. Reassigning subject->target INSIDE each dataset reproduces MORE correlation than the model achieves, i.e. most of the apparent pooled signal is between-dataset structure -- four corpora with different valence distributions and different HR distributions, on an axis where extent alone already decodes provenance at 12.03x chance.

THE DURABLE RESULT OF THIS SESSION IS NOT THIS NULL, IT IS THE TWO SILENT-DROP DEFECTS FOUND WHILE SETTING IT UP:
  labels.yaml     -- popane_s5/s6 valence undeclared -> declared_range None -> all-zero affect mask. 10,232 epochs invisible. Verified 0/40 -> 40/40 windows after the fix.
  privileged.yaml -- dapper, k_emocon, popane_s5, popane_s6 EDA undeclared. 18,783 privileged epochs from undeclared datasets; caught by tests/test_privileged_channels.py, which is the registry's only enforcement and did its job. 23/23 pass after declaring them from measured ranges.
Without the first fix this probe would have measured the OLD supply while reporting the new one.

#state-supervision #wearables #control #baseline #epoch-resolution #matched-baseline #subject-heldout #optihealth-eiv #data-contract-violation
