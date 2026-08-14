---
id: eiv-label-consistent-sampler
name: Label-consistent HR sampler with softened balancing
topic_tags: []
---

## Description
Route the Phase C stratified sampler and the json_slots training target through a SINGLE shared bucket rule (datasets.hr_label_bucket), so the sampler can only equalize the exact 4 labels the LM is trained to emit. Previously get_sampler_weights inverse-frequency balanced a 4-way split of the peak axis (<100/100-120/120-140/>=140) while the target collapsed everything >=120 into one 'tachycardic' label — so that label received 2/4 of the sampler mass. Adds two knobs: sampler_balance_strength (0 = natural prior, 1 = fully equalized; set to 0.5) which interpolates via w = (n/(c*k))**strength, and sampler_weight_cap (4.0) which clips any single sample's multiplier. Guarded by tests/test_hr_bucket_marginal.py and a realised-marginal log line emitted by the trainer before the first step.
