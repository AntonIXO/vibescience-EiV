---
id: eiv-thread-patch-presence-through-serving
name: Thread patch_presence through all serving/inference paths
topic_tags: []
---

## Description
Thread the per-channel, per-patch measured-presence tensor (`_patch_presence_from_mask`) through every INFERENCE site that builds soft tokens, not just the training loop: scratch/emission_diversity_audit.py and src/inference/sensor_session.py (which is the shared chat + annotate path). The projector silently falls back to a presence-blind adaptive_avg_pool2d when patch_presence is None, so omitting it degrades a masked_avg-trained projector to plain avg with no error raised. Ignored by avg/max/avgmax modes, so it is a no-op for every incumbent checkpoint.
