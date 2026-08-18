---
created_at: '2026-08-08T04:28:13+00:00'
former_ids:
- phase-b-triplet-orthogonalization-is-close-to-vacuous-99-1-of-its-training-samples-carry-a-single-state-label-and-every-non-none-label-is-a-100-pure-dataset-identifier
id: phase-b-triplet-orthogonalization-is-f6d3c5
paper_refs: []
problem_tags: []
status: parked
title: 'Phase B triplet orthogonalization is close to vacuous: 99.1% of its training
  samples carry a single state label, and every non-NONE label is a 100%-pure dataset
  identifier'
topic_tags:
- dataset-whitelist
- contrastive
- collapse
- baseline
updated_at: '2026-08-15T09:37:37+00:00'
updates:
- at: '2026-08-14T20:23:20+00:00'
  text: 'Parked 2026-08-14: real finding, but no hypothesis, runbook or rule-out is
    attached and nothing is queued against it. Parking is a statement about attention,
    not evidence — the measurement stands and this can be reopened.'
- at: '2026-08-15T07:47:15+00:00'
  text: "[2026-08-15 BOTH BLOCKING PREMISES OF THIS PROBLEM ARE OBSOLETE — unpark
    candidate]\n\nexp-e25-triplet-degeneracy-premise-20260815 re-measured this problem's
    two founding claims on the current corpus (git_sha e849eb7, all 11318 samples,
    read through the production label path: load_sample_metadata / state_series /
    infer_state_label).\n\nCLAIM 1 '99.1% of training samples carry a single state
    label' -> MEASURED 0.8194.\n  Of 7054 labelled windows: 5780 one state, 1175 two,
    99 three.\n  1274 TRANSITION-BEARING windows = 18.06%, against the ~0.9% implied.
    A 20x increase.\n\nCLAIM 2 'every non-NONE label is a 100%-pure dataset identifier'
    -> MEASURED 0.4751-0.5455.\n  flow    n=2858, spans 6 datasets, top-dataset purity
    0.5455\n  stress  n=2387, spans 8 datasets, top-dataset purity 0.4751\n  Roughly
    half of all same-state positive pairs are CROSS-DATASET.\n\nBoth were properties
    of the OLD label supply. Commit a7fc419 placed ground truth on the epoch it was
    recorded in; the per-epoch series is also strictly broader than the day reduction
    (flow 4->6 datasets, stress 7->8), because infer_state_label's majority collapse
    was DISCARDING minority states contributed by other cohorts. So the migration
    did not merely re-time labels — it recovered supply.\n\nCONSEQUENCE. The load-bearing
    objection to Phase B — that the triplet margin is spent separating provenance
    rather than physiology — no longer holds. Triplet mining can now draw same-state
    positives across datasets, and 1274 windows offer WITHIN-subject, WITHIN-day state
    transitions, which is exactly the design that removes the trait dominance E16/E17/E18
    measured (per-user constant AUC 0.7381 vs best physiological 0.5818).\n\nThis
    pairs with exp-e24-lab-cohorts-20260815 from the same session, which found real
    subject-grouped state signal where labels are dense: WESAD AUC 0.8497 (clock 0.5640),
    neuro_stress 0.6806 (clock 0.4930). Phase B now has BOTH a non-degenerate objective
    AND cohorts where the target carries a large effect. Those two conditions have
    never previously coexisted.\n\nSTATUS RECOMMENDATION: unpark. The remaining work
    is the actual retrain, which must be preregistered and scored on held-out subjects
    against the clock-only and per-user-constant controls this line has established.\n\nVERDICT-FIELD
    CAVEAT, recorded so the machine-readable layer is not misread: exp-e25 carries
    verdict `refutes` and its hypothesis reads `refuted`, but BOTH ITS BLOCKING GATES
    PASSED. The cause is mine — I registered predicted_effects with direction `up`
    on two diagnostics that are lower-is-better in this context (single-state share
    and provenance purity both FELL, which is the favourable direction). The gate
    results and the recorded numbers are the authoritative reading; the verdict string
    is an artifact of my direction annotation, not a scientific finding."
- at: '2026-08-15T09:37:37+00:00'
  text: "[2026-08-15 PROVENANCE VERIFICATION of exp-e28b, prompted by a late exit
    notification from the SUPERSEDED first attempt]\n\nA background notification arrived
    reporting exit code 1 for the ORIGINAL segtrip launch (the one aborted as exp-e28-segment-triplet-retrain-20260815,
    verdict `crashed`, OOM). Because that crashed process and the later successful
    run wrote to the SAME output paths, the E28b conclusions had to be re-verified
    against artifacts rather than assumed.\n\nVERIFIED:\n  * chronology is coherent
    — segtrip checkpoint+report 12:19:53, winctrl 12:23:30, scoring 12:34:35. Scoring
    read checkpoints that both predate it.\n  * both reports carry steps_completed=1000,
    which a crashed run cannot produce; segtrip triplet_loss 0.4778 / easy_ratio 0.625
    and winctrl 0.1831 / 0.375, matching the values scored.\n  * current train log
    contains 0 OutOfMemory occurrences and ends at the checkpoint save — the successful
    run overwrote the crashed run's log.\n  * no live training processes; GPU idle
    at 210 MiB.\n\nAND ONE CONFOUND NOW EXPLICITLY CLOSED that the original write-up
    only implied: the two arms' temporal_encoder.pt files have DISTINCT sha256 hashes
    (b6f7e4af0aee8d0a… for the segment arm, a6a60a7684e7400b… for the window control).
    So the near-identical state AUCs (0.5325 vs 0.5237) are NOT an artifact of accidentally
    scoring one checkpoint twice — they are two genuinely different encoders that
    both fail to carry state. That strengthens the refutation rather than weakening
    it.\n\nNo conclusion changes. exp-e28b stands as recorded."
---

## Description
Found 2026-08-08 while closing the dataset-whitelist hypothesis. Unpreregistered mechanism audit;
problem evidence, no calibration weight. Script scratch/h1_per_phase_mechanism.py.

Phase B (`phase_b_12ch`) adds an auxiliary TRIPLET orthogonalization term on top of the Phase A
InfoNCE objective, with a dynamic margin. Triplets are mined on `state_label`. Measured over the
7037 samples that survive the Phase A/B gate on the 1-minute corpus:

  state=NONE         n=6973   99.1% of the corpus   (dominant dataset lifesnaps, purity 0.668)
  state=exam_stress  n=  20   purity 1.0000  -> wearable_exam_stress
  state=transient    n=  15   purity 1.0000  -> wesad
  state=neutral      n=  13   purity 1.0000  -> big_ideas_glycemic
  state=n_back       n=   8   purity 1.0000  -> brain_wearable_monitoring
  state=high         n=   6   purity 1.0000  -> nurse_stress
  state=medium       n=   2   purity 1.0000  -> nurse_stress

TWO INDEPENDENT DEFECTS, EITHER OF WHICH IS SUFFICIENT ON ITS OWN.
1. 99.1% of samples share ONE label. A triplet objective needs an anchor, a same-label positive and
   a different-label negative. Within the NONE mass there is no genuine positive/negative structure
   at all -- every pair is nominally "same state".
2. Every one of the 64 labelled samples has a label that is a PERFECT dataset identifier
   (purity exactly 1.0000 in all six cases). So the only triplets carrying real label contrast are
   triplets whose contrast IS dataset provenance. The margin cannot be spent separating
   physiological state, because the labels do not encode physiological state -- they encode which
   study the row came from. Phase B is therefore, at best, orthogonalising dataset identity.

This compounds the Phase A finding recorded on the epoch-resolution problem: dataset provenance is
already ~99.7% decodable from per-channel coverage alone, so the representation entering Phase B
carries a near-perfect provenance axis, and Phase B's labels then ask it to separate exactly that.

NOT EXPLAINED BY THE GATE BUG. Commit e828b9e fixed a separate Phase B defect (no min_hr_coverage,
so 1981 HR-empty days were admitted, 91.5% at exactly zero HR coverage). The label degeneracy above
is measured AFTER that gate and is unaffected by it.

WHAT IS NOT YET KNOWN, STATED HONESTLY: this is a defect in the TRAINING SIGNAL, not yet a
demonstrated harm to any metric. Phase B measurably does no damage on the current instrument -- the
1-minute Phase B checkpoint scores hr_peak increment +0.4182 versus Phase A's +0.4086, and hr_mean
0.9777 versus 0.9713, i.e. slightly BETTER on both. So the honest statement is that Phase B is
running a nearly-empty objective and is roughly neutral, not that it is actively harmful. The open
questions are (a) whether Phase B is worth its ~10 minutes of GPU at all, measured by an ablation
that simply skips it, and (b) whether a genuine state label exists anywhere in the corpus that is
not a dataset proxy -- if not, the triplet arm has nothing to learn from and should be retired or
re-grounded on a label that is not provenance.

## Updates
- **2026-08-14T20:23:20+00:00** — Parked 2026-08-14: real finding, but no hypothesis, runbook or rule-out is attached and nothing is queued against it. Parking is a statement about attention, not evidence — the measurement stands and this can be reopened.
- **2026-08-15T07:47:15+00:00** — [2026-08-15 BOTH BLOCKING PREMISES OF THIS PROBLEM ARE OBSOLETE — unpark candidate]

exp-e25-triplet-degeneracy-premise-20260815 re-measured this problem's two founding claims on the current corpus (git_sha e849eb7, all 11318 samples, read through the production label path: load_sample_metadata / state_series / infer_state_label).

CLAIM 1 '99.1% of training samples carry a single state label' -> MEASURED 0.8194.
  Of 7054 labelled windows: 5780 one state, 1175 two, 99 three.
  1274 TRANSITION-BEARING windows = 18.06%, against the ~0.9% implied. A 20x increase.

CLAIM 2 'every non-NONE label is a 100%-pure dataset identifier' -> MEASURED 0.4751-0.5455.
  flow    n=2858, spans 6 datasets, top-dataset purity 0.5455
  stress  n=2387, spans 8 datasets, top-dataset purity 0.4751
  Roughly half of all same-state positive pairs are CROSS-DATASET.

Both were properties of the OLD label supply. Commit a7fc419 placed ground truth on the epoch it was recorded in; the per-epoch series is also strictly broader than the day reduction (flow 4->6 datasets, stress 7->8), because infer_state_label's majority collapse was DISCARDING minority states contributed by other cohorts. So the migration did not merely re-time labels — it recovered supply.

CONSEQUENCE. The load-bearing objection to Phase B — that the triplet margin is spent separating provenance rather than physiology — no longer holds. Triplet mining can now draw same-state positives across datasets, and 1274 windows offer WITHIN-subject, WITHIN-day state transitions, which is exactly the design that removes the trait dominance E16/E17/E18 measured (per-user constant AUC 0.7381 vs best physiological 0.5818).

This pairs with exp-e24-lab-cohorts-20260815 from the same session, which found real subject-grouped state signal where labels are dense: WESAD AUC 0.8497 (clock 0.5640), neuro_stress 0.6806 (clock 0.4930). Phase B now has BOTH a non-degenerate objective AND cohorts where the target carries a large effect. Those two conditions have never previously coexisted.

STATUS RECOMMENDATION: unpark. The remaining work is the actual retrain, which must be preregistered and scored on held-out subjects against the clock-only and per-user-constant controls this line has established.

VERDICT-FIELD CAVEAT, recorded so the machine-readable layer is not misread: exp-e25 carries verdict `refutes` and its hypothesis reads `refuted`, but BOTH ITS BLOCKING GATES PASSED. The cause is mine — I registered predicted_effects with direction `up` on two diagnostics that are lower-is-better in this context (single-state share and provenance purity both FELL, which is the favourable direction). The gate results and the recorded numbers are the authoritative reading; the verdict string is an artifact of my direction annotation, not a scientific finding.
- **2026-08-15T09:37:37+00:00** — [2026-08-15 PROVENANCE VERIFICATION of exp-e28b, prompted by a late exit notification from the SUPERSEDED first attempt]

A background notification arrived reporting exit code 1 for the ORIGINAL segtrip launch (the one aborted as exp-e28-segment-triplet-retrain-20260815, verdict `crashed`, OOM). Because that crashed process and the later successful run wrote to the SAME output paths, the E28b conclusions had to be re-verified against artifacts rather than assumed.

VERIFIED:
  * chronology is coherent — segtrip checkpoint+report 12:19:53, winctrl 12:23:30, scoring 12:34:35. Scoring read checkpoints that both predate it.
  * both reports carry steps_completed=1000, which a crashed run cannot produce; segtrip triplet_loss 0.4778 / easy_ratio 0.625 and winctrl 0.1831 / 0.375, matching the values scored.
  * current train log contains 0 OutOfMemory occurrences and ends at the checkpoint save — the successful run overwrote the crashed run's log.
  * no live training processes; GPU idle at 210 MiB.

AND ONE CONFOUND NOW EXPLICITLY CLOSED that the original write-up only implied: the two arms' temporal_encoder.pt files have DISTINCT sha256 hashes (b6f7e4af0aee8d0a… for the segment arm, a6a60a7684e7400b… for the window control). So the near-identical state AUCs (0.5325 vs 0.5237) are NOT an artifact of accidentally scoring one checkpoint twice — they are two genuinely different encoders that both fail to carry state. That strengthens the refutation rather than weakening it.

No conclusion changes. exp-e28b stands as recorded.

#dataset-whitelist #contrastive #collapse #baseline
