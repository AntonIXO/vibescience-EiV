---
ambition_class: null
created_at: '2026-08-19T09:57:55+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: analog-state-supervision-a1-on-the-12-547-c5f5eb
interventions:
- analog-state-supervision-ladder
override_reason: ''
paper_refs:
- firmed-a-peak-centered-multimodal-dataset-with-fine-grained-annotation-for-emotion-recognition-tang-et-al-2025
- weakly-supervised-learning-for-fine-grained-emotion-recognition-using-physiological-signals-edmil-zhang-el-ali-wang-hanjalic-cesar-ieee-taffc-2023
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: ''
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- state-supervision
- wearables
- control
- epoch-resolution
- baseline
- sensor-text
---

## Statement
ANALOG STATE SUPERVISION (A1). On the 12,547-window min_coverage-0.0 corpus (manifest sha ac7d2b1c, 7,849 state-labelled windows / 88,323 state epochs / 539 subjects), training Phase A with the analog-state ladder (state_target soft | ordinal | patch vs the hard control) will put momentary within-window state INTO the encoder's tokens for the first time — because for the first time the objective asks for it: E29 established the information is present at the encoder input (raw_patch_grid AUC 0.6309 from 8 features, zero learned params) and E28/E29 established the InfoNCE-dominated objective family never optimised the momentary label (state was a 0.05-weighted whole-window argmax auxiliary). The per-patch arm (state_patch_weight 0.5, 22,189 supervised (window,patch) cells) is the sharpest test: it is the only arm that supervises state at a sub-window scale, directly addressing the E31/E22 corollary that window-pooled reads fail their own null. PREDICTIONS, committed before any arm trains: (1) on eiv_momentary_state_auc_over_null (E26 transition rows recomputed on this corpus, both RF and linear readouts, each vs its own >=24-perm within-window null), the PATCH arm's enc_token read scores over_null >= +0.04 with z >= +2.0 under at least one readout family and over_null > 0 under BOTH families, on both seeds; (2) the HARD control's enc_token stays where E29/E34 put the incumbent — over_null <= +0.02 under RF (readout-dependent under linear per E34); (3) the ordering patch > soft > hard holds on mean over_null (2 seeds) under the RF readout; (4) no arm reaches the raw_shallow bound (0.6430 RF on the old corpus; recomputed here) — supervised heads approach the geometry bound, they do not beat handcrafted percentiles at this label supply. FALSIFIER: if the patch arm's enc_token fails its own null under BOTH readout families on both seeds, the encoder/objective family is the wrong vehicle for state even when asked directly, and the recommendation becomes shipping the shallow detector (src/annotation/momentary_state.py) as the state slot.

## Rationale


## Plan


## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Applies [[analog-state-supervision-ladder]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Cites [[firmed-a-peak-centered-multimodal-dataset-with-fine-grained-annotation-for-emotion-recognition-tang-et-al-2025]], [[weakly-supervised-learning-for-fine-grained-emotion-recognition-using-physiological-signals-edmil-zhang-el-ali-wang-hanjalic-cesar-ieee-taffc-2023]].

#state-supervision #wearables #control #epoch-resolution #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
