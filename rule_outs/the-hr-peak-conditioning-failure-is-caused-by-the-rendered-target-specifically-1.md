---
created_at: '2026-08-05T22:09:36+00:00'
id: the-hr-peak-conditioning-failure-is-caused-by-the-rendered-target-specifically-1
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T22:09:36+00:00'
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Claim
The hr_peak conditioning failure is caused by the RENDERED TARGET -- specifically: (1) PRIOR-DOMINATION, i.e. hr_peak's target is near-deterministic at the token level so cross-entropy is dominated by a cheap prior that starves the sensor pathway of gradient; (2) VARIABLE-WIDTH / PLACE-VALUE SCRAMBLING, i.e. hr_peak renders at width 4 or 5 so character positions carry sliding place values that independent per-position softmaxes cannot express, fixable by a fixed-width target render. ALSO ELIMINATED, as a separate concern: the worry that the projector's 7.605 bpm decodability is largely daily-mean recovery in disguise.

## Method
exp-e32-target-encoding-20260805, three pure-CPU probes, ~15 s total, no GPU, no model weights, no forward pass.

THE INSTRUMENT IS A BUILT-IN A/B CONTROL. hr_raw and hr_peak are both numeric slots in the SAME JSON target, trained by the SAME objective, through the SAME architecture, from the SAME prefix -- yet hr_raw SUCCEEDS end-to-end (R2 +0.830, skill +4.27 bpm) while hr_peak COLLAPSES (R2 +0.198, skill -6.48 bpm). Any proposed mechanism that applies equally to both slots cannot explain the asymmetry, so every statistic below is computed for BOTH and compared.

probe_e32a_target_entropy.py -- reproduces the exact render path from datasets.py (round(v,1) inside json.dumps) on the strict-clean population n=1546, then measures per-character-position entropy, majority-symbol share, distinct-symbol count and string-length entropy for both slots, plus value-space distribution statistics.

probe_e32b_place_alignment.py -- compares two alignments of the same numbers: (A) POSITION-ALIGNED, the left-indexed character positions the model actually sees today, and (B) PLACE-ALIGNED, re-rendered at fixed width so every slot is a constant place value (hundreds/tens/ones/tenths). Per slot it computes entropy, majority share, and MUTUAL INFORMATION between slot symbol and quantile-binned underlying value; MI is the decisive statistic because it upper-bounds what any per-position softmax could learn from that slot. hr_raw is the validity control: being already fixed-width, its position-to-place delta MUST be ~0 or the instrument is broken.

probe_e32c_mean_confound.py -- turns the standing increment rule on my own headline. Computes MAE(hr_peak | TRUE daily mean) by linear, isotonic and GBT regression on the identical E26-E31 subsample and split (n=1000, 700/300, SEED 0), establishing the confound floor that the projector's 7.605 must beat to represent anything beyond the daily-mean recovery hr_raw already demonstrates.

## Evidence
(1) PRIOR-DOMINATION -- SIGN IS BACKWARDS. Rendered-target statistics, strict-clean n=1546:
  total coverage-weighted character entropy   hr_peak 11.3474 bits   hr_raw 8.4353 bits   (peak-minus-raw +2.9121, WRONG DIRECTION)
  MAE vs own median                           hr_peak 18.703         hr_raw 6.512
  fraction within 1 bpm of median             hr_peak 0.033          hr_raw 0.089
  std                                         hr_peak 23.27          hr_raw 7.90
hr_raw is the MORE concentrated and MORE prior-predictable target, and hr_raw is the slot that SUCCEEDS (+4.27 bpm skill) while hr_peak FAILS (-6.48 bpm) under the identical objective, architecture and prefix. The proposed mechanism predicts the OPPOSITE of the observed outcome.

(2) VARIABLE-WIDTH RENDERING -- PLACE-ALIGNMENT HURTS. The structural asymmetry is real: hr_raw renders at FIXED width 4 with pos2 always '.' (entropy exactly 0.0000); hr_peak renders at width 4 OR 5 (length entropy 0.7032) with pos2 a '.' only 19.1% of the time, so in '97.1' pos1 is the ones digit while in '148.3' it is the tens digit. But re-rendering at fixed width REDUCES per-slot mutual information with the underlying value:
  hr_peak total slot MI   position-aligned 5.2115 -> place-aligned 4.2699   delta -0.9416 bits
  hr_raw  total slot MI   position-aligned 3.7626 -> place-aligned 3.7626   delta +0.0000 bits   <- CONTROL, exactly zero as required since hr_raw is already fixed-width
  DIFFERENTIAL GAIN (peak minus raw) -0.9416 bits, NEGATIVE where the hypothesis required a large positive.
The control landing on exactly 0.0000 shows the instrument is not manufacturing effects, so the negative result is trustworthy.

THE FINDING THAT SURVIVES BOTH REFUTATIONS: hr_peak's target carries MORE per-slot MI than hr_raw's (5.2115 vs 3.7626 bits) and MORE character entropy (11.3474 vs 8.4353), and still fails. Every 'the target does not carry enough information' framing is therefore dead, not just these two.

(3) MEAN-CONFOUND AUDIT -- the 7.605 headline is NOT mean-recovery. Identical E26-E31 subsample and split (n=1000, 700/300, SEED 0):
  always-predict-median              18.492
  peak | TRUE daily mean, linear     18.450
  peak | TRUE daily mean, isotonic   18.440   <- CONFOUND FLOOR
  peak | TRUE daily mean, GBT        18.866
The true daily mean buys only 0.052 bpm over the trivial baseline, so S0's 7.605 is a genuine increment of +10.835 bpm and the emission position's 10.270 is +8.170. corr(daily mean, daily peak) = 0.2986; the mean explains 0.1028 of peak variance; the residual above-baseline excursion has std 21.563 bpm. hr_raw's demonstrated success does not account for the projector's peak decodability.

LIMITATION REPORTED, NOT HIDDEN: the MedGemma tokenizer repo is gated (401 Client Error, google/medgemma-4b-it), so (1) and (2) are CHARACTER-level while the loss is over TOKENS. They transfer directly only if Gemma tokenises digits individually, which was not verifiable in this run. The exactly-zero hr_raw control delta shows the comparison is internally sound; a token-level redo behind HF auth would strengthen it. Finding (3) is pure value-space arithmetic and is unaffected.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#cross-modal #medgemma #collapse #sensor-text
