---
aliases: []
axis: problem
created_at: '2026-08-11T21:17:23+00:00'
id: run-to-run-variance
---

## Description
Two runs of a byte-identical configuration land in materially different outcomes because some RNG in the pipeline is unseeded, so any A/B comparing single runs is comparing draws from a basin distribution rather than measuring an intervention. Distinct from training-instability: the run itself converges normally, and nothing in the logs looks wrong — the defect is only visible when the same config is run twice and the manifests are diffed.

*Axis:* `problem`
