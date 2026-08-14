---
created_at: '2026-08-12T01:28:17+00:00'
former_ids:
- gating-dryad-on-measured-hr-coverage-isolates-its-held-cell-fraction-from-datase
id: gating-dryad-on-measured-hr-coverage-4016cd
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-12T01:28:17+00:00'
topic_tags:
- control
- baseline
- denominator
- cross-modal
---

## Claim
Gating dryad on measured HR coverage isolates its held-cell fraction from dataset identity, and the held fraction explains the hr_peak emission collapse.

## Method
Four CPU-only probes committed at 91f5fad: scratch/held_fraction_feasibility.py (per-dataset measured vs held HR-cell fractions from M_mask/M_held, survival counts across coverage thresholds 0.00-0.50), scratch/held_cell_values.py (values of held vs measured vs absent cells, denormalized with population_stats median/IQR channel arrays), scratch/dryad_peak_ceiling.py (per-row denormalized HR peak distribution vs cut points read from outputs/checkpoints/v5d/phase_c/manifest.json), scratch/target_lattice_check.py (distinct-value and top-1-mass counts of the hr_peak training targets themselves). No model loaded, no GPU used.

## Evidence
ELIMINATED ON FOUR COUNTS, all CPU, no GPU spent. This was my own proposed next arm; it is dead before launch.

1. NOT RUNNABLE — the isolation is impossible by construction. dryad mean measured-HR fraction 0.1130 vs lifesnaps 0.9234, pmdata 0.9089. Rows surviving a measured-coverage threshold: 3894 @0.05, 2632 @0.10, 2 @0.20, 0 @0.30. Any threshold comparable to the other datasets empties the pool. Decisively, dryad is the ONLY dataset in the corpus with held cells at all (held fraction 0.6872; every other dataset is exactly 0.0000), so 'held fraction' and 'is dryad' are perfectly collinear. The variable cannot be separated from dataset identity by row filtering — this is a structural impossibility, not low power.

2. THE CONFIG PREMISE IS FACTUALLY WRONG. configs/training.yaml:651 describes dryad as '68.7% zero-but-present on the HR channel'. Measured over 400 dryad rows: HELD cells n=150909, frac_exactly_zero 0.0000, 4748 distinct values, mean −0.5444 normalized = 59.1 bpm (population median 71.80, IQR 23.40). The 1.0000 zero-fraction belongs to ABSENT cells (mask=0, n=338344, distinct=1). Carry-forward is functioning exactly as documented. The comment conflates absent with held and must not be cited again.

3. MY OWN REPLACEMENT HYPOTHESIS IS ALSO REFUTED. I predicted dryad's 15-minute native sampling rate would clip within-day peaks below the pinned cut points. Cut points read from the v5d adapter manifest (tachycardic 157.71853625752556, elevated 126.9999977820953), never recomputed from the corpus. Per-row peak distribution: dryad p50 125.0, p90 196.9, p99 216.8, >=elev 0.4812, >=tach 0.2639; lifesnaps p50 130.1, p90 157.8, p99 182.6, >=elev 0.5704, >=tach 0.1016. dryad peaks are HIGHER in the tail and 2.6x more likely to be tachycardic. There is no sampling ceiling.

4. THE COLLAPSE IS NOT IN THE TARGETS. hr_peak targets are round(hr_peak, 1) — continuous, no lattice (src/training/datasets.py:1510). dryad: 1202 distinct target values, top-1 mass 0.0617. lifesnaps: 811 distinct, top-1 mass 0.0044. dryad targets are MORE diverse than lifesnaps. The model is handed 1202 distinct values and emits 6, with top-1 mass 0.604.

CONSEQUENCE: nothing on the data side explains the hr_peak collapse. It is decode-side — the same saturated-prior failure the confusion matrices just exposed in the hrv slot, where the v5c control emitted 'normal' 60 of 69 times while its accuracy sat within 0.0145 of its own majority baseline and went undetected for months. The hr_peak lattice collapse and the hrv near-constant emitter are one defect with two labels. Dataset composition is not the lever for either.

SCOPE LIMITS: counts are over the current corpus data/canonical [10344, 1440, 12]; held-cell value statistics sampled over the first 400 dryad rows (150909 held cells), peak and target distributions over all 4100 dryad and 3613 lifesnaps rows. This eliminates the data-side explanations; it does not by itself identify which decode-side mechanism (prior saturation, sampling parameters, or objective) produces the lattice.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#control #baseline #denominator #cross-modal
