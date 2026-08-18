---
aliases: []
axis: topic
created_at: '2026-08-18T21:06:01+00:00'
id: prompt-budget
---

## Description
The templated prompt's token length measured against the resolved max_prompt_tokens budget, and the silent truncation that follows when it exceeds it. A prompt longer than the budget loses its TAIL — typically the output-format instruction and the last key/enum definitions — with no error raised, which handicaps an arm on exactly the format-compliance axis a parse-rate gate measures. Distinct from `target-style` (the shape of the TARGET the model is trained to emit) and from `padding`: this is about the INPUT prompt being clipped before the model ever sees it. Verified live in exp-045: the shipped json_slots prompt was 327 templated tokens against a 256 budget, and fitting it to 250 tokens moved parse 0.930 -> 1.000 with grounding unchanged.

*Axis:* `topic`
