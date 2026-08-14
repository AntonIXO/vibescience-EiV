---
direction: higher_better
id: eiv_gen_peak_donor_gap_bpm
name: EiV native-emission hr_peak real-minus-donor MAE gap
requires_control: null
topic_tags: []
unit: bpm
---

## Description
Control for eiv_gen_peak_skill_bpm. MAE(donor-prefix emission) - MAE(real-prefix emission) on the SAME targets and same n. The donor arm feeds the soft-token prefix of a DIFFERENT sample while scoring against the real sample's truth, so it isolates per-sample conditioning from any constant/prior the adapter has memorised. Near 0 = the prefix is inert at emission time; large positive = conditioning is real and any residual deficit is a calibration/emission problem rather than a dead pathway.

*Unit:* `bpm` — *higher_better*
