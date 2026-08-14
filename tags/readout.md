---
aliases: []
axis: topic
created_at: '2026-08-14T09:21:21+00:00'
id: readout
---

## Description
The final decode stage of the sensor->LLM pipeline: frozen lm_head + greedy text emission of numeric slot values. Distinct from supply (encoder/projector/hidden-state content). E6-E10 (2026-08-14) localized all numeric-slot failures here: prefix and layer-34 hidden states carry hr_peak/hrv (R2 0.5-0.97) while emitted text loses them. Levers on this stage: lm_head LoRA (E9), untied head training, scalar regression heads bypassing softmax decode.

*Axis:* `topic`
