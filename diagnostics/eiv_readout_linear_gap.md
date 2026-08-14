---
direction: higher_better
id: eiv_readout_linear_gap
name: eiv_readout_linear_gap
requires_control: eiv_peak_excursion_increment_r2
topic_tags:
- cross-modal
- medgemma
- collapse
unit: accuracy_delta
---

## Description
DEPRECATED AS A PRIMARY METRIC -- DO NOT PREREGISTER AS A BLOCKING GATE. Measured 2026-08-05 in exp-e25-readout-ceiling-20260805 and found DEGENERATE on the hr_peak population. First-digit accuracy gap (best linear readout of the frozen final hidden state minus frozen greedy emission) at the hr_peak emission position. WHY IT FAILS: hr_peak on the strict-clean population has mean 121.6 bpm, so ~89% of values begin with '1'. The best linear readout scored 0.8917, EXACTLY the majority-class baseline -- a constant predictor that adds nothing -- while the frozen head scored 0.5667, BELOW that baseline. The resulting +0.3250 'gap' measures only that the frozen head is broken (already known), and cleared its >+0.15 bar while carrying zero information about linear recoverability. GENERAL LESSON: an accuracy-gap metric on a skewed categorical target is a trap; the majority-class baseline must be preregistered as a companion, and the gap must be scored against MAX(other_arm, majority), never against the other arm alone. USE INSTEAD: eiv_peak_excursion_increment_r2 on the value arm (continuous, not degenerate), which on the same features/split gave R2(peak|h) +0.5107 vs R2(peak|true mean) -0.0176 => increment +0.5283, MAE 11.815 vs always-median 20.094 with a bootstrap CI excluding zero.

*Unit:* `accuracy_delta` — *higher_better*

#cross-modal #medgemma #collapse
