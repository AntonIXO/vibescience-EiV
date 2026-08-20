---
id: analog-state-supervision-ladder
name: analog-state-supervision-ladder
topic_tags:
- state-supervision
- wearables
- sensor-text
---

## Description
Per-profile state-objective selection on the Phase A state head (commit 725ae5e, src/training/state_targets.py): `state_target: hard|soft|ordinal` plus `state_patch_weight`. HARD = pre-existing nominal CE on the per-window argmax (control). SOFT = cross-entropy against the empirical per-epoch state distribution — on the 12,547-window corpus 1,432 of 7,849 labelled windows (18.2%) are not one-hot, mean entropy 0.679 nats of a 1.099 ceiling, so the argmax changes the TARGET for ~1 labelled window in 5 (the transitional days). ORDINAL = CORN (Shi/Cao/Raschka 2022) over the arousal-signed rank flow<neutral<stress; soft targets supply P(y>k)/P(y>=k) directly, reducing to hard-label CORN on one-hot rows; also yields a continuous score via corn_score = E[rank]. PATCH = state_patch_weight>0 adds a per-patch head on the encoder's own 23-patch axis (patch_len 120, stride 60): 22,189 supervised (window,patch) cells vs 7,849 window labels = 2.8x more targets from the same annotations, and it is the only variant that supervises state at a sub-window time scale — the E29-localised defect (window-pooled reads fail their own null; information present at encoder input, absent at output) is precisely what per-patch supervision addresses. Arms in configs/training.yaml: phase_a_12ch_analog_{hard,soft,ordinal,patch}, each single-variable against its named control (soft vs hard: state_target; ordinal vs hard: state_target; patch vs soft: state_patch_weight).

#state-supervision #wearables #sensor-text
