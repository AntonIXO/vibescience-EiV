---
direction: lower_better
id: eiv_false_negative_pairs_per_batch
name: eiv_false_negative_pairs_per_batch
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags: []
unit: pairs
---

## Description
Mean number of off-diagonal frozen canonical-caption-equivalent cross-modal pairs that the training objective places in its negative denominator.

*** 2026-09-01 UNIT DEFECT -- READ BEFORE COMPARING ANY TWO VALUES ON THIS DIAGNOSTIC ***
The recorded evaluations are NOT all the same quantity. Measured across the vault (scratch/_false_negative_unit_audit.py):
  exp-014 / exp-015 (dcl-vs-hard-infonce)        before 230.3108  after 233.5957
  exp-eiv-multipositive-dcl-pilot-v2             before   0.1231  after   0.1560
  exp-eiv-multipositive-spectral-entropy-pilot   before   0.9528  after   0.9254
Three orders of magnitude apart. The declared unit is `pairs` and the description says MEAN NUMBER of pairs, so ~230 is a COUNT -- geometrically plausible, since a 30-anchor batch has 870 off-diagonal cells and 230.31 is 26.5% of them. The values 0.12 and 0.93 cannot be counts of anything; they are FRACTIONS.

CONSEQUENCE FOR ANY COMPARISON: values on the two scales are not comparable, and the deltas are not either. The count-scale delta (+3.28 pairs) and the fraction-scale deltas (+0.0328, -0.0273) happen to look similar in magnitude, which is exactly the trap -- +3.28 pairs out of 870 cells is a 0.38pp move, not a 3.28-unit one.

WHY NO NOISE FLOOR IS REGISTERED. doctor() asks for one, and a pooled sigma over these six values would come out near 100 pairs -- wide enough to mark every conceivable future result as inside noise. That is the precise failure the floor programme exists to prevent, so the honest state is UNFLOORED. This is a unit defect in the diagnostic definition, not a missing sigma; the fix is to separate the two quantities (count vs fraction) into distinct diagnostics, or to restate this one unambiguously as a fraction and re-express the ~230 values against their batch geometry. Until then an unfloored diagnostic is visibly unfloored, whereas a fitted sigma would launder the confusion into an authoritative number.

*Unit:* `pairs` — *lower_better*
