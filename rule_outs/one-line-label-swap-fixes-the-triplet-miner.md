---
created_at: '2026-08-15T08:48:48+00:00'
former_ids: []
id: one-line-label-swap-fixes-the-triplet-miner
problem_id: phase-b-triplet-orthogonalization-is-f6d3c5
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-15T08:48:48+00:00'
topic_tags:
- contrastive
- collapse
- epoch-resolution
---

## Claim
The Phase B triplet miner can be repaired by mining on per-epoch momentary states instead of infer_state_label, while keeping whole-window anchor/positive/negative tensors as they are.

## Method
Direct code reading of src/training/datasets.py:2120-2229 (TripletStateDataset), 2026-08-15, cross-checked against the E25/E26 measurements.

TripletStateDataset.__getitem__ (:2200-2229) returns x_anchor / x_positive / x_negative, each from self.base.get_sample_by_raw_index(raw_idx) — every one a WHOLE [1440, 12] civil-day window. Bucket construction (:2147-2150) maps each raw_idx to exactly ONE state via infer_state_label, and __getitem__ draws the negative from `[arr for key, arr in self._state_buckets_np.items() if key != state]`, i.e. from a DIFFERENT bucket, which under a window-level index means a different WINDOW.

## Evidence
STRUCTURAL, AND THE NUMBERS THAT MAKE IT MATTER:

1274 windows carry 2-3 distinct momentary states (E25: single-state share 0.8194, so 18.06% of the 7054 labelled windows carry a transition). Within-window state pairs: (flow,stress) 681, (neutral,stress) 399, (flow,neutral) 392.

E26 measured that within-window contrast as REAL: flow-vs-stress at W=120 gives subject-grouped OOF AUC 0.6329 against a clock-only control of 0.5129, and against a 40-permutation within-window shuffle null of mean 0.5492 (std 0.0246) — 3.41 SD above, permutation p = 0.0000. n=3248 epochs, 87 subjects, 662 windows.

THE DEGENERACY IS EXACT, NOT APPROXIMATE. For any of those 1274 windows, a window-level anchor for state A and a window-level negative for state B resolve to the SAME raw_idx, hence the same tensor from get_sample_by_raw_index. ||a-p|| - ||a-n|| is evaluated with a == n identically. Under the shipped dynamic-margin triplet that contributes a constant `margin` to the loss with zero useful gradient direction, i.e. the objective asks the encoder to separate a window from itself.

So the one-line swap at datasets.py:2149 changes bucket COUNTS (states span 6-8 datasets at purity 0.475-0.545 per E25) while leaving the 1274-window transition supply exactly as invisible as before. Bucket counts changing is the trap: it looks like the fix worked.

## Links
Rules out for problem [[phase-b-triplet-orthogonalization-is-f6d3c5]].

#contrastive #collapse #epoch-resolution
