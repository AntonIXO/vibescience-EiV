---
aliases:
- late-training-divergence
- loss-spike
- sampler-overcorrection
axis: problem
created_at: '2026-08-05T08:57:43+00:00'
id: training-instability
---

## Description
A training run's optimization degrades late in the run rather than converging — diverging auxiliary probe metrics, loss spikes, or drift onto a sampler/reweighting artifact — producing a checkpoint whose failure is caused by the run itself, not by the architecture or the data pipeline.

*Axis:* `problem`

## Aliases
- late-training-divergence
- loss-spike
- sampler-overcorrection
