---
ambition_class: mechanism
created_at: '2026-08-29T22:08:00+00:00'
former_ids: []
gap_closure_estimate: 0.15
gates:
- blocking: true
  description: 'g1 BLOCKING (existence at the new supply): valence_mean must clear
    its own 40-permutation null by >= +0.05 in r on at least one trained arm, with
    subject-grouped OOF. The old run''s best cell over ALL 12 was +0.43 sigma with
    r in [-0.19, -0.02]; null sd was 0.04-0.20, so at the new n the MDE on r is roughly
    0.05-0.10. A delta under +0.05 with a non-degenerate null is a POWERED NULL and
    re-closes the rule-out at the higher supply.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1-valence-clears-its-null
  mde: 0.05
- blocking: true
  description: 'g2 BLOCKING (the gain is not dataset identity): valence is now pooled
    over up to six corpora on two instrument families, and extent alone decodes dataset
    identity at 12.03x chance (recorded). So the null must be a WITHIN-DATASET permutation
    (subject->target reassignment inside each dataset), never a global shuffle. Additionally
    the per-dataset r must be reported: if the pooled r is positive while every within-dataset
    r is at or below zero, the effect is provenance and the gate FAILS regardless
    of the pooled number.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2-within-dataset-null-not-provenance
  mde: 0.05
- blocking: false
  description: 'g3 (non-blocking, supply accounting): report the realised cell/subject/dataset
    counts against the pre-fix baseline (334 valence cells / 167 subjects / 2 datasets).
    This is the number that makes the reopen legitimate or not, and it must appear
    in the record whatever the verdict.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3-supply-actually-realised
  mde: 0.05
id: at-the-rebuilt-corpus-s-valence-supply-21-710-a9a6e0
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'Primary. The prior run gave r in [-0.19, -0.02] across all 12 cells
    with the best cell at +0.43 sigma over its null, at 334 cells / 167 subjects.
    At 5.7x the subjects I predict valence_mean clears its within-dataset null by
    +0.05 to +0.12 in r. CALIBRATION WARNING, stated because it should discount this:
    I am 6/13 (0.46) on this diagnostic and 0.411 overall, and the honest base rate
    for ''more data rescues a null this project already measured'' in this vault is
    poor -- E14, E5 and W8 all went the other way. The blocking gates, not the prediction,
    decide this.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- matched-baseline
- subject-heldout
- optihealth-eiv
- data-contract-violation
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- state-supervision
- wearables
- control
- baseline
- epoch-resolution
---

## Statement
At the rebuilt corpus's valence supply (21,710 label-epochs over 955 subjects and six corpora, up from 11,478 over 167 and two), per-patch valence IS recoverable from the 12 wrist channels via trained encoder tokens: valence_mean clears its within-dataset permutation null by >= +0.05 in r on at least one trained arm, and the gain is not carried by dataset identity.

## Rationale
TWO THINGS CHANGED, AND ONLY ONE OF THEM IS 'MORE DATA'.

(1) SUPPLY. The rule-out that-per-patch-dimensional-affect-valence-mean-3ae24a closed with an explicitly supply-conditional sentence: 'valence recoverability ... is below r~0.2 detectable at n=167 subjects x 1 session'. The 2026-08-30 rebuild takes valence from 11,478 to 21,710 label-epochs and from 167 to 955 valence-bearing subjects (5.7x), so the stated bound no longer describes the corpus. I retracted it on that basis.

(2) A SILENT DEFECT, found while checking whether the new supply actually reaches the trainer -- and it did not. popane_s5 (5429 valence epochs) and popane_s6 (4803) have no source block in labels.yaml, so declared_range() returns None, scale_to_declared_range() returns None, and affect_patch_targets() returns an all-zero mask. Measured end-to-end BEFORE the fix: 0 of 40 sampled windows per corpus carried any affect target, while the parquet held 10,232 epochs. AFTER declaring both blocks: 40/40. Without that fix this entire re-test would have measured the OLD supply while believing it measured the new one, and reported a null that meant nothing.

That is the same failure family the vault has paid for repeatedly -- an optional argument or missing declaration that degrades silently rather than raising (W10's patch_presence fallback, E22's mask inversion). Recording it here because the fix is the durable result even if the probe comes back null.

## Plan
PROBE ONLY, no training, CPU/GPU ~10 min. Runner is the EXISTING scratch/a2_affect_eval.py, unchanged in its statistics, pointed at the new corpus and at whatever trained Phase-A arms exist on this host; nulls are its own 40-permutation within-dataset subject->target reassignment.

PRECONDITION ALREADY EXECUTED AND VERIFIED, and it is the substantive part of this hypothesis: popane_s5 and popane_s6 had NO source block in configs/canonical/labels.yaml, so declared_range() returned None, scale_to_declared_range() returned None, and affect_patch_targets() emitted an all-zero mask -- 10,232 valence epochs sat in label_events.parquet completely invisible to training. Measured before the fix: 0 of 40 sampled windows carried any affect target for either corpus. After declaring both blocks (same [0,10] Response Meter dial as study 3): 40/40 for both. That is a real repaired defect independent of whatever the probe now returns.

WRITE logs/a2_affect_eval_13879.json. If g1 fails with a non-degenerate null, re-close the rule-out AT THE NEW SUPPLY -- which is a stronger negative than the original, because it eliminates the supply escape hatch the first one explicitly left open.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1-valence-clears-its-null` (blocking): g1 BLOCKING (existence at the new supply): valence_mean must clear its own 40-permutation null by >= +0.05 in r on at least one trained arm, with subject-grouped OOF. The old run's best cell over ALL 12 was +0.43 sigma with r in [-0.19, -0.02]; null sd was 0.04-0.20, so at the new n the MDE on r is roughly 0.05-0.10. A delta under +0.05 with a non-degenerate null is a POWERED NULL and re-closes the rule-out at the higher supply.
Gate `g2-within-dataset-null-not-provenance` (blocking): g2 BLOCKING (the gain is not dataset identity): valence is now pooled over up to six corpora on two instrument families, and extent alone decodes dataset identity at 12.03x chance (recorded). So the null must be a WITHIN-DATASET permutation (subject->target reassignment inside each dataset), never a global shuffle. Additionally the per-dataset r must be reported: if the pooled r is positive while every within-dataset r is at or below zero, the effect is provenance and the gate FAILS regardless of the pooled number.
Gate `g3-supply-actually-realised` (advisory): g3 (non-blocking, supply accounting): report the realised cell/subject/dataset counts against the pre-fix baseline (334 valence cells / 167 subjects / 2 datasets). This is the number that makes the reopen legitimate or not, and it must appear in the record whatever the verdict.

#state-supervision #wearables #control #baseline #epoch-resolution #matched-baseline #subject-heldout #optihealth-eiv #data-contract-violation
