---
direction: higher_better
id: eiv_lm_hidden_peak_r2
name: EiV frozen-LM hidden-state peak R2
requires_control: null
topic_tags:
- cross-modal
- sensor-text
- medgemma
unit: r2
---

## Description
R2 of a Ridge probe predicting the TRUE within-day HR peak (bpm) from the FROZEN LM's residual-stream hidden state, read at the exact token position where the model is about to emit the hr_peak value under teacher forcing. Reported per layer, with the final layer as the headline.

WHY THIS EXISTS. The soft-token prefix demonstrably CONTAINS hr_peak: a Ridge probe on the production projector output (the tensor that becomes the prefix) scores R2=0.81 with an incremental-over-mean of +0.72 on a strictly clean population. Yet at generation the hr_peak slot has NEGATIVE skill against an always-predict-median constant (-5.88 bpm full coverage). Every diagnostic so far has measured either the ENCODER SIDE (probe R2, retrieval, excursion increment) or the OUTPUT (generated value, bucket entropy). Nothing has measured the middle: what survives the frozen LM's own forward pass.

This localises the loss to one of two places, which imply OPPOSITE next moves:
  * HIGH R2 at the final layer => the peak reaches the output position intact and the defect is in the READOUT (the frozen lm_head / final projection cannot express it, or the template prior dominates the logits). LoRA on the LM head / final layers is then precisely targeted.
  * LOW R2 at the final layer => the peak is destroyed INSIDE the LM's forward pass, and adapting the head cannot recover what never arrives. Head-targeted LoRA is then predicted to fail and the lever must move earlier (prefix geometry / basis alignment / gated cross-attention).

MANDATORY CONTROL, same rule as eiv_peak_excursion_increment_r2: report the increment over R2(peak | true daily mean alone, 1 feature) alongside the raw value. 'peak ~ level + constant' otherwise scores a high raw R2 while carrying no peak information. Also report the same probe at the hr_raw position as a WITHIN-MODEL POSITIVE CONTROL: hr_raw is the slot that demonstrably conditions at generation (corr +0.740 full coverage, +4.27 bpm skill), so it calibrates what 'enough to drive generation' looks like at this exact measurement point.

Population must be the STRICT clean filter (no zero-bpm present steps, daily mean > 40 bpm), never the legacy coverage>0.9 & mean>20 filter, which is 59.1% zero-contaminated.

*Unit:* `r2` - *higher_better*

#cross-modal #sensor-text #medgemma

*Unit:* `r2` — *higher_better*

#cross-modal #sensor-text #medgemma
