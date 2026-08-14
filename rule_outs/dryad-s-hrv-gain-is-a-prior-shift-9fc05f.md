---
created_at: '2026-08-12T01:18:08+00:00'
former_ids:
- dryad-s-hrv-gain-is-a-prior-shift-toward-the-modal-class-rather-than-genuine-thr
id: dryad-s-hrv-gain-is-a-prior-shift-9fc05f
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-12T01:18:08+00:00'
topic_tags:
- denominator
- control
- baseline
- cross-modal
---

## Claim
Dryad's hrv gain is a prior shift toward the modal class rather than genuine three-class separation.

## Method
Re-probed outputs/checkpoints/v5c/phase_c and v5d/phase_c with p4 at num_samples=512 against the rebuilt data/eval_v3_1min, using the build from commit ea45a0e which adds pred_* tracking alongside true_* and ships {hr,hrv,state}_confusion, *_per_class_recall and *_predicted_class_counts. Driver scratch/run_hrv_confusion.sh, which states the SEPARATION-vs-PRIOR-SHIFT decision rule in its header comment before any run. Artifacts outputs/probes/v5c_p4_confusion.json and v5d_p4_confusion.json. The instrument gap this closes was structural: the previous scorer recorded only true_hrv_seen and discarded the predictions, so no per-class recall could ever be computed from a shipped artifact.

## Evidence
REFUTED BY DIRECT MEASUREMENT. Both arms re-probed with the ea45a0e build, which persists confusion matrices, per-class recall and predicted-class counts. Both RC=0, same eval bundle, same pinned cut points.

v5c CONTROL (hrv_acc 0.4348, n=69, majority 0.4203)
  true low    -> pred {low 3, normal 24, high 2}   recall 0.1034
  true normal -> pred {low 0, normal 24, high 1}   recall 0.9600
  true high   -> pred {low 0, normal 12, high 3}   recall 0.2000
  EMITTED {low 3, normal 60, high 6}

v5d +DRYAD (hrv_acc 0.7206, n=68, majority 0.4265)
  true low    -> pred {low 25, normal 4, high 0}   recall 0.8621
  true normal -> pred {low 8, normal 15, high 1}   recall 0.6250
  true high   -> pred {low 0, normal 6, high 9}    recall 0.6000
  EMITTED {low 33, normal 25, high 10}

PER-CLASS DELTAS: low +0.7586, high +0.4000, normal -0.3350.

The decision rule was fixed in scratch/run_hrv_confusion.sh BEFORE the numbers existed: SEPARATION requires recall to improve on the MINORITY classes (low, high), not only on the modal class; PRIOR SHIFT would show the gain concentrated on one class with the other two flat or worse. Recall improved on BOTH minority classes, so the prior-shift explanation is eliminated.

The control is a near-constant 'normal' emitter -- 60 of 69 emissions, recall 0.96 on normal against 0.10 and 0.20 on low and high. That is the saturated-prior signature this project has been tracking on hr_peak, present undetected in the hrv slot, and it explains why its accuracy sat within 0.0145 of its own majority baseline.

v5d trades modal recall (-0.3350) for large minority gains and spreads its emissions 33/25/10 against a true composition of 29/24/15. A prior shift toward 'low' would have raised low recall while flattening high; high recall instead TRIPLED (0.2000 -> 0.6000).

SCOPE LIMITS. One seed per arm, n=68/69 scored emissions. This establishes that the hrv gain is class separation rather than a prior artifact; it does NOT establish the magnitude to two decimal places, and it does not transfer to hr_peak, where dryad still collapses emission onto a 4-value lattice (19 -> 6 distinct values, top-1 mass 0.271 -> 0.604, measured on raw text and parser-independent). Dryad remains a MIXED intervention.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#denominator #control #baseline #cross-modal
