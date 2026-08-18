---
artifacts: []
closed: true
config_note: 'ZERO GPU training and NO LM forward pass for the main arm -- S0 needs
  only encoder+projector plus one read of the embedding matrix. ~4 min total. Adapter
  medgemma_12ch_e23v2, 4-bit, strict-clean population, SEED 0, n=1000 -> 700/300,
  same split as E26-E30. Vocabulary embedding matrix 262145 x 2560; PCA basis built
  from 60000 sampled rows, top-1024 components (EVR sum 0.5759). Sweep k in {8,16,32,64,128,256,512,1024}:
  VOCAB arm projects mean-pooled soft tokens onto the top-k vocab principal directions;
  RANDOM arm projects onto a random orthonormal subspace of identical rank (QR of
  a Gaussian); DONOR and SHUFFLED arms at every k. Energy retention of soft tokens
  in the top-k vocab subspace measured against 4000 held-out real vocab rows as the
  ''what does ANY vector retain'' control. Scripts scratch/probe_e31_vocab_subspace.py
  and scratch/probe_e31b_norm_check.py. Smoke-tested at n=40 before the real run.'
cost: null
crash_reason: ''
created_at: '2026-08-05T21:55:59+00:00'
diagnostics_measured:
- after: 7.604
  before: 7.605
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS, decisively. Projecting onto the top-256 vocab principal directions
    and DISCARDING everything orthogonal gives MAE 7.604 -- reproducing the unprojected
    full-dimensional 7.605 to within 0.001 bpm. Full vocab arm: k=8 15.178, k=16 13.253,
    k=32 11.266, k=64 10.211, k=128 8.104, k=256 7.604, k=512 7.831, k=1024 7.657.
    Against always-predict-median 18.492 and unprojected 7.605. The entire predictive
    signal is recoverable from a 256-dimensional slice of the LM''s own embedding
    subspace -- 10% of the 2560-dim space. The hr_peak direction at the projector
    output is NOT outside what the LM spans.'
  gate_id: gate-vocab-subspace-retains-signal
  passed: true
- evidence: 'PASS as a control, and it is what makes the primary result interpretable
    -- while also forbidding the stronger claim. The RANDOM orthonormal subspace of
    identical rank tracks the vocab arm within about 1 bpm at every k, sometimes better:
    k=8 16.031 (vocab 15.178), k=16 14.198 (13.253), k=32 12.409 (11.266), k=64 9.470
    (vocab 10.211 -- RANDOM WINS by 0.741), k=128 8.523 (8.104), k=256 8.189 (7.604),
    k=512 7.609 (vocab 7.831 -- RANDOM WINS by 0.222), k=1024 7.691 (7.657). vocab-minus-random
    gaps: -0.853, -0.945, -1.144, +0.741, -0.419, -0.584, +0.222, -0.035 -- SIGN-ALTERNATING
    and small. There is NO vocab-specific obstruction: the modest degradation at small
    k is ordinary dimension reduction, since a random subspace of the same rank loses
    the same amount. NOTE the correct reading: this establishes the absence of a vocab-specific
    penalty, NOT that the vocab basis is special. A signal recoverable from a random
    256-dim projection is simply a signal that is robustly linearly present, which
    is exactly what E29-E30 already showed.'
  gate_id: gate-random-subspace-control
  passed: true
- evidence: PASS at every k in both arms with wide margin. Donor 19.222-19.245 across
    the whole sweep (k=8 19.225, k=16 19.222, k=32 19.224, k=64 19.227, k=128 19.228,
    k=256 19.235, k=512 19.245); shuffled 19.222-19.399. Every value WORSE than always-predict-median
    18.492, and neither arm improves as k grows from 8 to 1024 while the real arm
    improves by 7.5 bpm over the same range. This rules out the specific risk that
    projection to k up to 1024 against n_train=700 manufactures apparent skill. donor_null_ok=True,
    shuffled_null_ok=True.
  gate_id: gate-donor-shuffled-nulls
  passed: true
- evidence: PASS EXACTLY. Unprojected full-dimensional RidgeCV on S0 = 7.605 bpm,
    identical to exp-e30's 7.605 to three decimals, far inside the 0.5 bpm tolerance.
    Same strict-clean population (n=1000), same 700/300 split, same SEED 0. The extraction
    is confirmed identical to E30's, so every comparison in this experiment is admissible.
  gate_id: gate-anchor-reproduces-e30-s0
  passed: true
- evidence: 'MEASUREMENT ERROR FOUND AND CORRECTED -- there is NO norm mismatch. The
    n=40 smoke test reported soft-token RMS 37.96 vs vocab-token RMS 0.9926, an apparent
    38x gap that looked like a cheap standalone defect. It was my error. Gemma3 uses
    Gemma3TextScaledWordEmbedding, which multiplies the raw weight by hidden_size**0.5
    = sqrt(2560) = 50.5964 at FORWARD time (modeling_gemma3.py:113 ''return super().forward(input_ids)
    * self.embed_scale'', :506 ''embed_scale=self.config.hidden_size**0.5''). Verified
    empirically in probe_e31b_norm_check.py: embed_scale attribute reads 50.5. Soft
    tokens are injected as embeddings, i.e. AFTER that scaling is applied to real
    tokens, so the correct comparison is against emb(input_ids), not emb.weight. Measured
    on real text: raw emb.weight row RMS 0.9928; emb(ids) fed-to-stack RMS 50.8363;
    soft-token RMS 29.5202. Effective ratio soft/fed-to-stack = 0.581 -- same order
    of magnitude, NOT a 38x mismatch. Soft tokens are somewhat smaller than real token
    embeddings but well within normal range. THE NORM-SCALE LEVER IS DEAD and the
    38x figure must never be quoted; it compares a pre-scaling quantity against a
    post-scaling one.'
  gate_id: gate-norm-scale-descriptive
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-signal-at-the-projector-4aa472
id: exp-e31-vocab-subspace-20260805
interpretation:
  at: '2026-08-18T21:08:43+00:00'
  failure_mode: claim_false
  lesson: 'The hr_peak signal at the projector output is NOT off-manifold w.r.t. the
    LM, which kills two proposed levers outright. Projecting the S0 soft tokens onto
    the top-256 principal directions of the LM''s own 262145x2560 vocabulary embedding
    matrix and DISCARDING everything orthogonal gives MAE 7.604 vs the unprojected
    7.605 (delta 0.001 bpm), i.e. the entire predictive signal lives in 10% of the
    space; the sweep is k=8 15.178, 16 13.253, 32 11.266, 64 10.211, 128 8.104, 256
    7.604, 512 7.831, 1024 7.657 against always-median 18.492. The RANDOM orthonormal
    control of identical rank tracks it within ~1 bpm at every k and BEATS it at k=64
    (9.470 vs 10.211) and k=512 (7.609 vs 7.831), with sign-alternating gaps (-0.853,-0.945,-1.144,+0.741,-0.419,-0.584,+0.222,-0.035):
    there is no vocab-specific obstruction AND no vocab-specific advantage. DO NOT
    SPEND on projector whitening / variance-matching / vocab-embedding-statistics
    init. The norm-scale lever is also dead and the ''38x mismatch'' figure must never
    be quoted - it compared raw emb.weight RMS 0.9928 (pre-scaling) against soft tokens,
    but Gemma3TextScaledWordEmbedding multiplies by hidden_size**0.5 = 50.5964 at
    forward time, so the correct comparison is emb(ids) fed-to-stack RMS 50.8363 vs
    soft-token RMS 29.5202, an effective ratio of 0.581. With geometry, rank, function
    class and scale all eliminated, the surviving untested target is whether END-TO-END
    CROSS-ENTROPY TRAINING can find the readout at all - every E24-E31 number is a
    fitted probe on frozen features.'
  transfers_to:
  - the-hr-peak-signal-enters-the-lm-well-ba9dd9
  - the-hr-peak-failure-is-an-objective-cb1111
  - hyp-e7-projector-bottleneck-20260814
key_numbers: {}
observed_effects:
- delta: -0.001
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
parent_experiment_id: exp-e30-variance-localisation-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-signal-at-the-projector-4aa472]].
Branches from [[exp-e30-variance-localisation-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 7.605 → after 7.604  (Δ -0.001, down ✗ predicted none)

## Gates
- `gate-vocab-subspace-retains-signal`: PASS — PASS, decisively. Projecting onto the top-256 vocab principal directions and DISCARDING everything orthogonal gives MAE 7.604 -- reproducing the unprojected full-dimensional 7.605 to within 0.001 bpm. Full vocab arm: k=8 15.178, k=16 13.253, k=32 11.266, k=64 10.211, k=128 8.104, k=256 7.604, k=512 7.831, k=1024 7.657. Against always-predict-median 18.492 and unprojected 7.605. The entire predictive signal is recoverable from a 256-dimensional slice of the LM's own embedding subspace -- 10% of the 2560-dim space. The hr_peak direction at the projector output is NOT outside what the LM spans.
- `gate-random-subspace-control`: PASS — PASS as a control, and it is what makes the primary result interpretable -- while also forbidding the stronger claim. The RANDOM orthonormal subspace of identical rank tracks the vocab arm within about 1 bpm at every k, sometimes better: k=8 16.031 (vocab 15.178), k=16 14.198 (13.253), k=32 12.409 (11.266), k=64 9.470 (vocab 10.211 -- RANDOM WINS by 0.741), k=128 8.523 (8.104), k=256 8.189 (7.604), k=512 7.609 (vocab 7.831 -- RANDOM WINS by 0.222), k=1024 7.691 (7.657). vocab-minus-random gaps: -0.853, -0.945, -1.144, +0.741, -0.419, -0.584, +0.222, -0.035 -- SIGN-ALTERNATING and small. There is NO vocab-specific obstruction: the modest degradation at small k is ordinary dimension reduction, since a random subspace of the same rank loses the same amount. NOTE the correct reading: this establishes the absence of a vocab-specific penalty, NOT that the vocab basis is special. A signal recoverable from a random 256-dim projection is simply a signal that is robustly linearly present, which is exactly what E29-E30 already showed.
- `gate-donor-shuffled-nulls`: PASS — PASS at every k in both arms with wide margin. Donor 19.222-19.245 across the whole sweep (k=8 19.225, k=16 19.222, k=32 19.224, k=64 19.227, k=128 19.228, k=256 19.235, k=512 19.245); shuffled 19.222-19.399. Every value WORSE than always-predict-median 18.492, and neither arm improves as k grows from 8 to 1024 while the real arm improves by 7.5 bpm over the same range. This rules out the specific risk that projection to k up to 1024 against n_train=700 manufactures apparent skill. donor_null_ok=True, shuffled_null_ok=True.
- `gate-anchor-reproduces-e30-s0`: PASS — PASS EXACTLY. Unprojected full-dimensional RidgeCV on S0 = 7.605 bpm, identical to exp-e30's 7.605 to three decimals, far inside the 0.5 bpm tolerance. Same strict-clean population (n=1000), same 700/300 split, same SEED 0. The extraction is confirmed identical to E30's, so every comparison in this experiment is admissible.
- `gate-norm-scale-descriptive`: PASS — MEASUREMENT ERROR FOUND AND CORRECTED -- there is NO norm mismatch. The n=40 smoke test reported soft-token RMS 37.96 vs vocab-token RMS 0.9926, an apparent 38x gap that looked like a cheap standalone defect. It was my error. Gemma3 uses Gemma3TextScaledWordEmbedding, which multiplies the raw weight by hidden_size**0.5 = sqrt(2560) = 50.5964 at FORWARD time (modeling_gemma3.py:113 'return super().forward(input_ids) * self.embed_scale', :506 'embed_scale=self.config.hidden_size**0.5'). Verified empirically in probe_e31b_norm_check.py: embed_scale attribute reads 50.5. Soft tokens are injected as embeddings, i.e. AFTER that scaling is applied to real tokens, so the correct comparison is against emb(input_ids), not emb.weight. Measured on real text: raw emb.weight row RMS 0.9928; emb(ids) fed-to-stack RMS 50.8363; soft-token RMS 29.5202. Effective ratio soft/fed-to-stack = 0.581 -- same order of magnitude, NOT a 38x mismatch. Soft tokens are somewhat smaller than real token embeddings but well within normal range. THE NORM-SCALE LEVER IS DEAD and the 38x figure must never be quoted; it compares a pre-scaling quantity against a post-scaling one.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The hr_peak signal at the projector output is NOT off-manifold w.r.t. the LM, which kills two proposed levers outright. Projecting the S0 soft tokens onto the top-256 principal directions of the LM's own 262145x2560 vocabulary embedding matrix and DISCARDING everything orthogonal gives MAE 7.604 vs the unprojected 7.605 (delta 0.001 bpm), i.e. the entire predictive signal lives in 10% of the space; the sweep is k=8 15.178, 16 13.253, 32 11.266, 64 10.211, 128 8.104, 256 7.604, 512 7.831, 1024 7.657 against always-median 18.492. The RANDOM orthonormal control of identical rank tracks it within ~1 bpm at every k and BEATS it at k=64 (9.470 vs 10.211) and k=512 (7.609 vs 7.831), with sign-alternating gaps (-0.853,-0.945,-1.144,+0.741,-0.419,-0.584,+0.222,-0.035): there is no vocab-specific obstruction AND no vocab-specific advantage. DO NOT SPEND on projector whitening / variance-matching / vocab-embedding-statistics init. The norm-scale lever is also dead and the '38x mismatch' figure must never be quoted - it compared raw emb.weight RMS 0.9928 (pre-scaling) against soft tokens, but Gemma3TextScaledWordEmbedding multiplies by hidden_size**0.5 = 50.5964 at forward time, so the correct comparison is emb(ids) fed-to-stack RMS 50.8363 vs soft-token RMS 29.5202, an effective ratio of 0.581. With geometry, rank, function class and scale all eliminated, the surviving untested target is whether END-TO-END CROSS-ENTROPY TRAINING can find the readout at all - every E24-E31 number is a fitted probe on frozen features.

Transfers to: [[the-hr-peak-signal-enters-the-lm-well-ba9dd9]], [[the-hr-peak-failure-is-an-objective-cb1111]], [[hyp-e7-projector-bottleneck-20260814]]

## Notes
PREDICTION CONFIRMED, AND IT KILLS THE FIX I PROPOSED ONE EXPERIMENT AGO. Two levers die here; one of them dies because I made a measurement error and checked it before reporting it.

=== 1. THE SUBSPACE RESULT ===
Projecting the S0 soft tokens onto the top-256 principal directions of the LM's own vocabulary embedding matrix -- DISCARDING everything orthogonal -- gives MAE 7.604 against the unprojected 7.605. The signal is entirely recoverable from 10% of the space.
  k        8      16      32      64     128     256     512    1024
  vocab  15.178 13.253  11.266  10.211   8.104   7.604   7.831   7.657
  random 16.031 14.198  12.409   9.470   8.523   8.189   7.609   7.691
Always-median 18.492; unprojected 7.605. Anchor reproduced exp-e30 exactly.

THE RANDOM CONTROL IS WHAT MAKES THIS INTERPRETABLE. A random orthonormal subspace of identical rank tracks the vocab arm within ~1 bpm at every k and BEATS it at k=64 and k=512. The vocab-minus-random gaps alternate sign (-0.853, -0.945, -1.144, +0.741, -0.419, -0.584, +0.222, -0.035). No vocab-specific obstruction, and equally no vocab-specific advantage.

=== 2. E30'S 'OFF-MANIFOLD' READING IS REFUTED -- BY ME, ONE EXPERIMENT LATER ===
I closed E30 saying the projector emits an off-manifold signal and named whitening / vocab-embedding init as the larger and cheaper lever. That was an INFERENCE FROM A VARIANCE STATISTIC (alignment 0.1602), not a measurement of the geometry it claimed. Measured directly, the predictive direction sits comfortably inside the LM's native subspace. 'Spread across low-variance directions' (true, E29/E30) and 'outside the subspace the LM can use' (false, here) are different propositions and I conflated them.
DO NOT SPEND ON projector whitening / variance-matching / vocab-embedding-statistics init.

=== 3. THE NORM-SCALE LEVER ALSO DIES -- MY MEASUREMENT ERROR ===
The n=40 smoke test showed soft-token RMS 37.96 vs vocab RMS 0.9926 and I nearly reported a 38x norm mismatch as a cheap defect. It is an artifact. Gemma3TextScaledWordEmbedding multiplies the raw weight by hidden_size**0.5 = 50.5964 at FORWARD time (modeling_gemma3.py:113, :506; embed_scale reads 50.5). Soft tokens are injected AFTER that scaling applies to real tokens, so the correct comparison is emb(input_ids), not emb.weight:
  raw emb.weight row RMS       0.9928   <- what the smoke test used
  emb(ids) fed-to-stack RMS   50.8363   <- the correct comparison
  soft-token RMS              29.5202
  EFFECTIVE RATIO              0.581
Same order of magnitude. NOT a defect. The 38x figure must never be quoted -- it compares a pre-scaling quantity against a post-scaling one.

=== 4. WHAT THIS LEAVES ===
Every upstream-geometry explanation for the hr_peak failure is now eliminated:
  * signal absent from the prefix -- refuted long ago (R2 0.8084, increment +0.7211)
  * signal lost in the forward pass -- only 2.665 bpm of it (exp-e30)
  * signal low-rank / prefix-theorem-limited -- refuted (exp-e29)
  * readout function class too weak -- refuted (exp-e28: non-linear LOSES to Ridge)
  * signal off-manifold vs the LM subspace -- REFUTED HERE
  * soft-token norm mismatch -- REFUTED HERE (my measurement error)
The signal is present, linearly decodable at 7.605 bpm from the projector output, inside the LM's subspace, at a sane scale, and survives the forward pass at 10.270 -- yet the model EMITS a value with NEGATIVE skill (-5.88 bpm vs always-predict-median).

The gap is no longer plausibly geometric or informational. What has NEVER been tested in this problem is whether END-TO-END CROSS-ENTROPY TRAINING ON THIS DATA CAN FIND THE READOUT AT ALL -- every number in E24-E31 is a FITTED probe on FROZEN features, which bounds what the representation permits, not what the training objective selects. hr_peak is centred at 119.5 bpm sitting exactly on the 120 tachycardic boundary (datasets.py:1964, L2035, L2156-2159), so the token-level objective may be dominated by a near-deterministic prior that costs almost nothing to reproduce. That is an OBJECTIVE/OPTIMISATION hypothesis, not a representation hypothesis, and it is where the evidence now points.

=== 5. SCOPE ===
This is a linear-readout projection test. It shows a linear probe still finds the signal after projection; it does NOT show the LM's attention and MLP machinery would use that direction -- a strictly stronger claim no projection test can settle. S0 is mean-pooled over 96 slots; per-slot geometry could differ. The random-subspace result establishes the ABSENCE of a vocab-specific penalty, not that the vocab basis is special.

Artifacts: outputs/probes/e31_vocab_subspace.json, logs/e31_vocab_subspace.log, logs/e31_smoke.log. Scripts scratch/probe_e31_vocab_subspace.py, scratch/probe_e31b_norm_check.py. ~4 min, no GPU training, no LM forward pass in the main arm.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
