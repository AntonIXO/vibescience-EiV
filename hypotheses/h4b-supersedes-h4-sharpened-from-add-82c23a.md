---
created_at: '2026-08-08T04:30:45+00:00'
former_ids:
- h4b-supersedes-h4-sharpened-from-add-dispersion-structure-to-one-named-scalar-th
gates: []
id: h4b-supersedes-h4-sharpened-from-add-82c23a
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags: []
status: refuted
supersedes: h4-caption-content-not-architecture-the-3306b2
topic_tags:
- cross-modal
- sensor-text
- contrastive
- infonce
- baseline
- epoch-resolution
- dataset-whitelist
- wearables
---

## Statement
H4b (SUPERSEDES H4, sharpened from "add dispersion structure" to ONE NAMED SCALAR).
The hr_peak deficit is a SUPERVISION deficit, and the missing quantity is the within-day
STANDARD DEVIATION of heart rate. Adding hr_std (and secondarily p25/p75) to the caption will raise
hr_peak readout materially, with no architectural change.

THE PREMISE IS NOW VERIFIED AT THE SOURCE, NOT INFERRED. src/training/datasets.py::
_build_hr_only_caption emits exactly:
  "HR {x} bpm, HRV {x} ms, RR {x} breaths/min, temp {x} C, deep {x}, rem {x}."
Every one of those six values comes from _safe_mean() -> _masked_mean(), i.e. all six are channel
MEANS. The caption contains no dispersion, no order statistic, no timing and no conjunction.

THE LADDER, all on the identical strict clean 1-minute pool, subject-grouped GroupKFold(5),
out-of-fold, target = true daily hr_peak, trivial always-predict-median MAE 17.023 bpm:
  arm                                                R2        skill vs trivial
  level only (HR mean, 1 feature)                  +0.0226        +0.03 bpm
  EXACTLY the six caption scalars (RF)             +0.0706        +0.97 bpm
  DEEP encoder->projector->soft tokens, Phase B    +0.4305        +4.32 bpm
  the six caption scalars + hr_std (RF)            +0.5751        +6.37 bpm
  best shallow (std,p25,p75,iqr,skew)              +0.8217       +10.15 bpm
  patch maxes + HistGB (information bound)         +0.9706       +14.72 bpm

TWO CONCLUSIONS, AND THE FIRST ONE EXONERATES THE ARCHITECTURE.
1. The deep stack DECISIVELY beats its own supervision: 0.4305 versus 0.0706, a 6x gap in R2 and
   +4.32 vs +0.97 bpm. The MAS.S60 lecture-4 worry - "a shallow model on the caption scalars will
   match the stack, proving the supervision never contained more" - is REFUTED. The encoder reads
   the raw tensor and demonstrably extracts peak structure the captions never carried.
2. And yet ONE extra number in the caption beats the entire learned pipeline: the six scalars plus
   hr_std reach 0.5751 against the stack's 0.4305. hr_std alone is worth +0.5045 R2 / +5.40 bpm on
   top of the caption's own contents.

MECHANISM. Contrastive alignment captures the information shared between the two views and discards
the rest. The text view is six means, so spread is not shared, so spread is discarded - and hr_MEAN
is correspondingly near-perfect (R2 0.9777, MAE 1.02 bpm vs a trivial 7.60) while hr_PEAK is not.
This also explains why every architectural intervention came back flat: pooling, slot count, window
length and patch construction are all downstream of an objective never asked to preserve spread, and
patch construction is independently proven lossless for the peak (max over patch maxima equals the
true peak to 0.0000 bpm on all 6624 days).

ROBUSTNESS OF THE HEADLINE ARM, per dataset (six + hr_std, RF): lifesnaps R2 +0.4997 (n=4042),
pmdata +0.5966 (n=2015), big_ideas_glycemic -0.5102 (n=93, too small to weigh),
optihealth_supabase +0.8848 (n=452 but only TWO subjects, so this is near-within-subject prediction
and must NOT be read as generalisation). The two cohorts that carry the corpus both land near
0.50-0.60, so no single dataset manufactures the result.

WHY THIS SUPERSEDES H4. H4 named five features, one of which - "fraction of minutes above the day's
own p75" - was a DEFECT in my own feature construction: computed over all 1440 slots instead of
present minutes it equals 0.25*coverage (corr +0.9996 with coverage), and computed correctly it is
the constant 0.25. It carried no information either way. Removing it and adding skew RAISED the
shallow ceiling from 0.708 to 0.8217, so the correction strengthened the finding while invalidating
the attribution. H4b names the one scalar that actually does the work.

FALSIFICATION, UNCHANGED AND STATED UP FRONT: if adding hr_std to the caption does not move hr_peak
readout, the deficit is not the supervision and the whole account is wrong. Report hr_mean in the
same table: hr_peak rising while hr_mean FALLS means caption capacity was reallocated, not that a
ceiling was raised, and that must not be reported as a win.

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv_heldout_hr_r2]] (none).
Supersedes [[h4-caption-content-not-architecture-the-3306b2]].

#cross-modal #sensor-text #contrastive #infonce #baseline #epoch-resolution #dataset-whitelist #wearables
