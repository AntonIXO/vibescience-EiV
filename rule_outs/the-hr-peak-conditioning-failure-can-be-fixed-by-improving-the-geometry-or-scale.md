---
created_at: '2026-08-05T21:58:26+00:00'
id: the-hr-peak-conditioning-failure-can-be-fixed-by-improving-the-geometry-or-scale
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T21:58:26+00:00'
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Claim
The hr_peak conditioning failure can be fixed by improving the GEOMETRY OR SCALE of what the projector hands to the frozen LM -- specifically: (1) whitening or variance-matching the projector output; (2) initialising soft tokens from vocabulary-embedding statistics so they lie 'on the LM's manifold'; (3) rescaling soft tokens to match real token-embedding norms. Also eliminated: the reading of exp-e30's low signal-variance alignment (0.1602 at S0) as evidence that the projector emits an 'off-manifold' signal.

## Method
exp-e31-vocab-subspace-20260805 measured the geometric claim directly instead of inferring it from a variance statistic. Zero training and NO LM forward pass in the main arm -- S0 needs only encoder+projector plus one read of the embedding matrix (~4 min total). Strict-clean population n=1000, 700/300 split, SEED 0, adapter medgemma_12ch_e23v2 4-bit, identical to E26-E30 so the anchor must reproduce.

PRIMARY PROBE (scratch/probe_e31_vocab_subspace.py): PCA the LM's vocabulary embedding matrix (262145 x 2560) from 60000 sampled rows to get an orthonormal basis for the top-k directions the frozen LM was actually trained to consume. Project the mean-pooled S0 soft tokens onto that basis, DISCARDING every component orthogonal to it, and refit the identical RidgeCV readout. Sweep k in {8,16,32,64,128,256,512,1024}.

THE DECISIVE CONTROL: at every k, repeat the projection onto a RANDOM orthonormal subspace of IDENTICAL rank (QR of a Gaussian). Without this, any degradation is indistinguishable from ordinary dimension reduction and nothing about the LM's geometry can be claimed. DONOR and SHUFFLED arms run at every k in both arms.

SECONDARY PROBE (scratch/probe_e31b_norm_check.py): compare soft-token norms against real token embeddings, taking care to compare against emb(input_ids) -- the post-scaling quantity actually fed to the stack -- rather than emb.weight, because Gemma3TextScaledWordEmbedding multiplies by hidden_size**0.5 = 50.5964 at forward time (transformers/models/gemma3/modeling_gemma3.py:113, :506).

Both probes smoke-tested at small n before the real run.

## Evidence
SUBSPACE SWEEP (MAE bpm, always-predict-median 18.492, unprojected full-dim 7.605):
  k        8      16      32      64     128     256     512    1024
  vocab  15.178 13.253  11.266  10.211   8.104   7.604   7.831   7.657
  random 16.031 14.198  12.409   9.470   8.523   8.189   7.609   7.691
At k=256 the vocab-projected MAE is 7.604 vs unprojected 7.605 -- the ENTIRE predictive signal survives inside 10% of the space, i.e. inside what the frozen LM natively spans.

The RANDOM-SUBSPACE CONTROL at identical rank licenses the conclusion: it tracks the vocab arm within ~1 bpm at every k and BEATS it at k=64 (9.470 vs 10.211) and k=512 (7.609 vs 7.831). vocab-minus-random gaps alternate sign: -0.853, -0.945, -1.144, +0.741, -0.419, -0.584, +0.222, -0.035. No vocab-specific obstruction exists, so whitening and vocab-statistics init have nothing to fix. Correct reading: this shows the ABSENCE of a vocab-specific penalty, NOT that the vocab basis is special.

NULLS: donor 19.222-19.245 and shuffled 19.222-19.399 at every k in both arms -- always worse than always-median 18.492, never improving with k, while the real arm improves 7.5 bpm over the same range. So this is not projection manufacturing skill at high k against n_train=700.

ANCHOR: unprojected full-dim RidgeCV on S0 = 7.605, reproducing exp-e30 exactly.

NORM/SCALE (claim 3), measured on real text in probe_e31b_norm_check.py:
  raw emb.weight row RMS       0.9928   <- what the smoke test wrongly used
  emb(ids) fed-to-stack RMS   50.8363   <- the correct comparison
  soft-token RMS              29.5202
  EFFECTIVE RATIO              0.581
The apparent 38x mismatch was an artifact of comparing a pre-scaling quantity to a post-scaling one; embed_scale reads 50.5 = sqrt(2560). Effective ratio 0.581 is the same order of magnitude and not a defect. THE 38x FIGURE MUST NEVER BE QUOTED.

WHY THE exp-e30 INFERENCE WAS WRONG: 'the signal is spread across low-variance directions' (true, exp-e29/exp-e30) and 'the signal lies outside the subspace the LM can use' (false, measured here) are different propositions. They were conflated when exp-e30 was closed, and whitening / vocab-init was named the next and larger lever on that basis. Measured directly one experiment later, the predictive direction sits comfortably inside the LM's native subspace.

SCOPE OF THE ELIMINATION: this is a linear-readout projection test. It shows a linear probe still finds the signal after projection; it does NOT establish that the LM's attention and MLP machinery would USE that direction -- a strictly stronger claim no projection test can settle. S0 is mean-pooled over the 96 slots, so per-slot geometry could differ. What is eliminated is the class of fixes premised on the handed-over representation being geometrically or metrically WRONG; that premise is now measured false.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#cross-modal #medgemma #collapse #sensor-text
