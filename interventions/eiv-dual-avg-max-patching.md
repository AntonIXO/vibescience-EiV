---
id: eiv-dual-avg-max-patching
name: EiV dual avg+max temporal patching
topic_tags:
- cross-modal
- sensor-text
---

## Description
Concatenate MAX-pooled patch features alongside the existing AVERAGE-pooled ones in the temporal patching stage (src/encoders/temporal.py, TemporalPatching, patch_len=8 stride=4 over T=96 -> 23 patches), doubling the per-patch feature width. Optionally also concatenate a max-pooled branch at the projector's adaptive_avg_pool2d stage (src/projectors/cross_modal.py:260).

MOTIVATION (measured, not assumed): averaging 8 timesteps = 2 hours destroys within-patch extremes irreversibly. A max is an order statistic and no downstream linear reader can recover it from means. Ridge probes on n=4010 clean fully-observed days: 23 avg patches give R2->mean 0.9305 but R2->max only 0.4401, against R2(max | true mean alone) = 0.4371 -- an increment of +0.0030. Max-pooled patches raise R2->max to 0.5066; avg+max concat to 0.5153. On the pure excursion target (max-mean, std 19.1 bpm): avg 0.1685, max 0.1273, avg+max concat 0.2968 (+76% over avg alone).

This is the architectural counterpart to the observed slot asymmetry: hr_raw (a MEAN) conditions at corr +0.740 while hr_peak (a MAX) sits at +0.276.

CONTROL ESTABLISHED: projector slot count is NOT the bottleneck -- sweeping 23/12/8/4/2/1 slots moved R2->max only 0.466->0.457, confirming the loss is upstream at patch construction, not at projector compression.

Preferred over learned/gated attention pooling as a FIRST intervention: softmax is a convex mixture and only approaches argmax at sharp temperature, and would have to learn peak selection through a frozen LLM with no direct supervision. CBAM-style dual avg+max statistics are established precedent. Attention pooling remains the fallback if explicit max-pooling proves insufficient.

#cross-modal #sensor-text
