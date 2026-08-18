---
artifacts: []
closed: true
config_note: 'CPU-only, zero GPU, on cached features from exp-e26. SAME frozen H[:,0,:]
  at the hr_peak emission position, SAME train/test split (700/300), SAME adapter
  medgemma_12ch_e23v2 and strict-clean population as E26/E27/E28. NEW AXIS: instead
  of varying the readout (E28) this varies the RANK of the feature subspace. PCA fit
  on TRAIN ONLY, then RidgeCV restricted to the top-k principal components, sweeping
  k in {1,2,3,5,10,20,50,100,300}. Three arms at every k: REAL (H), DONOR (Hd, identical
  pipeline), SHUF (real pipeline on a permuted training target). Also reports explained-variance
  ratio, participation ratio, and corr(PC score, hr_peak) for the top 10 PCs. Motivated
  by external theory (Petrov, Torr & Bibi ICLR 2024, arXiv:2310.19698) rather than
  by extending my own prior framing. Script scratch/probe_e29_prefix_rank.py, decision
  rule in docstring, written before the run; committed to the low-rank branch.'
cost: null
crash_reason: ''
created_at: '2026-08-05T21:25:24+00:00'
diagnostics_measured:
- after: 18.407
  before: 10.27
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAILED, decisively, and this refutes my preregistered hypothesis. Ridge
    on the top 3 PCs gives 18.407 bpm against full-dimensional Ridge at 10.270 --
    a gap of 8.14 bpm where the gate allowed 0.5. The k=3 arm barely beats always-predict-median
    (18.492), i.e. a rank-3 subspace of the hidden state carries almost NO usable
    peak information. MAE falls monotonically with rank across the entire sweep: k=1
    19.244, k=3 18.407, k=10 15.223, k=20 13.242, k=50 11.108, k=100 10.796, k=300
    10.304. Paired bootstrap CI95 (best_k=300 minus k=3) = [-9.371, -6.885], entirely
    below zero: more rank DOES help, substantially, at every step. The prefix''s peak-predictive
    influence is NOT a low-rank bias.'
  gate_id: gate-rank3-matches-full
  passed: false
- evidence: 'PASS at every k, which is what makes the refutation trustworthy. Donor
    MAE is flat and skill-free across the whole sweep: 19.294, 19.289, 19.288, 19.289,
    19.344, 19.358, 19.379, 19.380, 19.381 for k = 1,2,3,5,10,20,50,100,300 -- never
    better than always-predict-median (18.492), and slightly WORSE throughout. Critically
    the donor arm does NOT improve with k while the real arm improves by 8.9 bpm over
    the same range. So the rank-dependent gain is genuinely per-sample prefix information,
    not extra components letting the model fit the marginal target distribution.'
  gate_id: gate-donor-no-skill-any-k
  passed: true
- evidence: 'PASS at every k, and this control is what licenses reading the high-k
    arms at all. Shuffled-target MAE: 19.228, 19.247, 19.299, 19.271, 19.319, 19.321,
    19.333, 19.336, 19.339 for k = 1..300. Flat, and never better than always-predict-median
    (18.492), even at k=300 against n_train=700 where overfitting was the specific
    risk. The monotone improvement of the REAL arm with rank is therefore real signal,
    not capacity artefact -- which matters because here the control protects the REFUTING
    branch of my own hypothesis rather than the confirming one.'
  gate_id: gate-shuffled-null-all-k
  passed: true
- evidence: 'REPORTED, AND IT IS THE MOST INFORMATIVE PART OF THIS EXPERIMENT. The
    two situations this gate was written to distinguish came apart in an unexpected
    way. The hidden states ARE globally low-rank: PC1 alone explains 47.82% of variance,
    PC1-3 63.95%, PC1-10 83.68%, PC1-50 93.88%, participation ratio (effective rank)
    3.92. But the PEAK-PREDICTIVE content is NOT in those dominant directions. corr(PC
    score, hr_peak) for PC1..PC10 = +0.042, -0.358, -0.019, -0.209, -0.215, +0.211,
    +0.219, +0.069, -0.285, -0.010. PC1 -- nearly half the total variance -- is essentially
    UNCORRELATED with hr_peak (+0.042), and the strongest of the top ten reaches only
    -0.358. So the hr_peak signal lives in HIGH-RANK, LOW-VARIANCE directions of the
    residual stream. This is the opposite of the picture in my hypothesis, and it
    explains why variance-ranked truncation destroys the signal while full-dimensional
    Ridge recovers it: Ridge does not care about variance ranking, it weights directions
    by their predictive value.'
  gate_id: gate-spectrum-reported
  passed: true
- evidence: HONOURED. Both preregistered scope limits are stated and both now matter
    more, not less, given the refutation. (1) Petrov et al. (arXiv:2310.19698) concerns
    a FIXED prefix while mine varies per sample, so this experiment tested a CONSEQUENCE
    of the theory rather than the theorem. The consequence FAILED, and I am explicitly
    NOT claiming the theorem is false -- a per-sample prefix is outside its scope,
    and the honest statement is that this system does not exhibit the low-rank signature
    the theory's bias picture would predict. (2) Rank measured at the emission position
    does not localise WHERE the signal became high-rank and low-variance; a layer
    sweep (or a comparison against the projector output's own spectrum) is required
    and was not run here. Any follow-up must not treat this single-position measurement
    as a claim about the whole stack.
  gate_id: gate-theory-scope-stated
  passed: true
git_ref: ''
hypothesis_id: the-soft-token-prefix-influences-the-lm-82bbde
id: exp-e29-prefix-rank-20260805
interpretation:
  at: '2026-08-18T21:08:42+00:00'
  failure_mode: mechanism_wrong
  lesson: 'PREFIX RANK is eliminated as the location of the loss, and the refutation
    delivered the chain''s first structural mechanism. Ridge on the top 3 PCs gives
    18.407 bpm vs full-dimensional Ridge 10.270 (gate allowed 0.5 bpm), barely beating
    always-predict-median 18.492, and MAE falls monotonically with rank all the way
    out (k=1 19.244, k=3 18.407, k=10 15.223, k=20 13.242, k=50 11.108, k=100 10.796,
    k=300 10.304; CI95 k=300 minus k=3 = [-9.371,-6.885]) while donor (19.288-19.381)
    and shuffled-target (19.228-19.339) arms stay flat and skill-free at every k —
    so the rank-dependent gain is real per-sample signal, and the prefix''s influence
    is NOT a low-rank bias. The mechanism: the hidden states ARE globally low-rank
    (PC1 47.82% of variance, PC1-10 83.68%, participation ratio 3.92) but the peak-predictive
    content is not in the dominant directions — corr(PC_i, hr_peak) for i=1..10 is
    +0.042, -0.358, -0.019, -0.209, -0.215, +0.211, +0.219, +0.069, -0.285, -0.010,
    so PC1 carries nearly half the variance and is uncorrelated with the target. hr_peak
    lives in HIGH-RANK, LOW-VARIANCE directions, which single-handedly explains E28''s
    non-linear losses (capacity allocated along high-variance directions with no signal),
    E20''s cos = -0.015 against the LM''s native numeric direction, and why E28''s
    ''information limit'' reading was wrong. Next arms should change what ENTERS the
    stack (projector-output whitening, soft tokens initialised from real vocabulary
    embeddings, an explicit alignment loss to the LM''s numeric subspace, or zero-init
    gated cross-attention), not how it is read out. Scope limits: this tested a CONSEQUENCE
    of Petrov et al. (arXiv:2310.19698), which assumes a FIXED prefix, so the theorem
    is not falsified; and rank measured at ONE position does not localise WHERE the
    signal became low-variance — run the same spectrum on the projector output and
    intermediate layers before any training run.'
  transfers_to:
  - the-hr-peak-signal-enters-the-lm-well-ba9dd9
  - the-hr-peak-signal-at-the-projector-4aa472
  - the-hr-peak-readout-cap-is-an-a61025
  - hyp-e7-projector-bottleneck-20260814
key_numbers: {}
observed_effects:
- delta: 8.137
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: up
parent_experiment_id: exp-e28-function-class-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-soft-token-prefix-influences-the-lm-82bbde]].
Branches from [[exp-e28-function-class-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 10.27 → after 18.407  (Δ +8.137, up ✗ predicted none)

## Gates
- `gate-rank3-matches-full`: **FAIL** — FAILED, decisively, and this refutes my preregistered hypothesis. Ridge on the top 3 PCs gives 18.407 bpm against full-dimensional Ridge at 10.270 -- a gap of 8.14 bpm where the gate allowed 0.5. The k=3 arm barely beats always-predict-median (18.492), i.e. a rank-3 subspace of the hidden state carries almost NO usable peak information. MAE falls monotonically with rank across the entire sweep: k=1 19.244, k=3 18.407, k=10 15.223, k=20 13.242, k=50 11.108, k=100 10.796, k=300 10.304. Paired bootstrap CI95 (best_k=300 minus k=3) = [-9.371, -6.885], entirely below zero: more rank DOES help, substantially, at every step. The prefix's peak-predictive influence is NOT a low-rank bias.
- `gate-donor-no-skill-any-k`: PASS — PASS at every k, which is what makes the refutation trustworthy. Donor MAE is flat and skill-free across the whole sweep: 19.294, 19.289, 19.288, 19.289, 19.344, 19.358, 19.379, 19.380, 19.381 for k = 1,2,3,5,10,20,50,100,300 -- never better than always-predict-median (18.492), and slightly WORSE throughout. Critically the donor arm does NOT improve with k while the real arm improves by 8.9 bpm over the same range. So the rank-dependent gain is genuinely per-sample prefix information, not extra components letting the model fit the marginal target distribution.
- `gate-shuffled-null-all-k`: PASS — PASS at every k, and this control is what licenses reading the high-k arms at all. Shuffled-target MAE: 19.228, 19.247, 19.299, 19.271, 19.319, 19.321, 19.333, 19.336, 19.339 for k = 1..300. Flat, and never better than always-predict-median (18.492), even at k=300 against n_train=700 where overfitting was the specific risk. The monotone improvement of the REAL arm with rank is therefore real signal, not capacity artefact -- which matters because here the control protects the REFUTING branch of my own hypothesis rather than the confirming one.
- `gate-spectrum-reported`: PASS — REPORTED, AND IT IS THE MOST INFORMATIVE PART OF THIS EXPERIMENT. The two situations this gate was written to distinguish came apart in an unexpected way. The hidden states ARE globally low-rank: PC1 alone explains 47.82% of variance, PC1-3 63.95%, PC1-10 83.68%, PC1-50 93.88%, participation ratio (effective rank) 3.92. But the PEAK-PREDICTIVE content is NOT in those dominant directions. corr(PC score, hr_peak) for PC1..PC10 = +0.042, -0.358, -0.019, -0.209, -0.215, +0.211, +0.219, +0.069, -0.285, -0.010. PC1 -- nearly half the total variance -- is essentially UNCORRELATED with hr_peak (+0.042), and the strongest of the top ten reaches only -0.358. So the hr_peak signal lives in HIGH-RANK, LOW-VARIANCE directions of the residual stream. This is the opposite of the picture in my hypothesis, and it explains why variance-ranked truncation destroys the signal while full-dimensional Ridge recovers it: Ridge does not care about variance ranking, it weights directions by their predictive value.
- `gate-theory-scope-stated`: PASS — HONOURED. Both preregistered scope limits are stated and both now matter more, not less, given the refutation. (1) Petrov et al. (arXiv:2310.19698) concerns a FIXED prefix while mine varies per sample, so this experiment tested a CONSEQUENCE of the theory rather than the theorem. The consequence FAILED, and I am explicitly NOT claiming the theorem is false -- a per-sample prefix is outside its scope, and the honest statement is that this system does not exhibit the low-rank signature the theory's bias picture would predict. (2) Rank measured at the emission position does not localise WHERE the signal became high-rank and low-variance; a layer sweep (or a comparison against the projector output's own spectrum) is required and was not run here. Any follow-up must not treat this single-position measurement as a claim about the whole stack.

**Verdict:** refutes

## Interpretation
**Failure mode:** `mechanism_wrong`

PREFIX RANK is eliminated as the location of the loss, and the refutation delivered the chain's first structural mechanism. Ridge on the top 3 PCs gives 18.407 bpm vs full-dimensional Ridge 10.270 (gate allowed 0.5 bpm), barely beating always-predict-median 18.492, and MAE falls monotonically with rank all the way out (k=1 19.244, k=3 18.407, k=10 15.223, k=20 13.242, k=50 11.108, k=100 10.796, k=300 10.304; CI95 k=300 minus k=3 = [-9.371,-6.885]) while donor (19.288-19.381) and shuffled-target (19.228-19.339) arms stay flat and skill-free at every k — so the rank-dependent gain is real per-sample signal, and the prefix's influence is NOT a low-rank bias. The mechanism: the hidden states ARE globally low-rank (PC1 47.82% of variance, PC1-10 83.68%, participation ratio 3.92) but the peak-predictive content is not in the dominant directions — corr(PC_i, hr_peak) for i=1..10 is +0.042, -0.358, -0.019, -0.209, -0.215, +0.211, +0.219, +0.069, -0.285, -0.010, so PC1 carries nearly half the variance and is uncorrelated with the target. hr_peak lives in HIGH-RANK, LOW-VARIANCE directions, which single-handedly explains E28's non-linear losses (capacity allocated along high-variance directions with no signal), E20's cos = -0.015 against the LM's native numeric direction, and why E28's 'information limit' reading was wrong. Next arms should change what ENTERS the stack (projector-output whitening, soft tokens initialised from real vocabulary embeddings, an explicit alignment loss to the LM's numeric subspace, or zero-init gated cross-attention), not how it is read out. Scope limits: this tested a CONSEQUENCE of Petrov et al. (arXiv:2310.19698), which assumes a FIXED prefix, so the theorem is not falsified; and rank measured at ONE position does not localise WHERE the signal became low-variance — run the same spectrum on the projector output and intermediate layers before any training run.

Transfers to: [[the-hr-peak-signal-enters-the-lm-well-ba9dd9]], [[the-hr-peak-signal-at-the-projector-4aa472]], [[the-hr-peak-readout-cap-is-an-a61025]], [[hyp-e7-projector-bottleneck-20260814]]

## Notes
MY LITERATURE-DERIVED HYPOTHESIS WAS REFUTED, AND THE REFUTATION IS MORE USEFUL THAN THE CONFIRMATION WOULD HAVE BEEN. It produced the first structural finding in this problem that no prior experiment could have reached.

=== THE REFUTATION ===
I predicted Ridge on the top 3 PCs would match full-dim Ridge (10.270) within 0.5 bpm. It gives 18.407 -- barely better than always-predict-median (18.492), and 8.14 bpm short. MAE falls monotonically all the way to k=300 (19.244 / 18.407 / 15.223 / 13.242 / 11.108 / 10.796 / 10.304 at k = 1/3/10/20/50/100/300), CI95 (best minus k=3) = [-9.371, -6.885]. Donor and shuffled arms are FLAT and skill-free at every k (both ~19.2-19.4 throughout, never beating 18.492), so the rank-dependent gain is real per-sample signal and not capacity artefact. The prefix's influence is NOT a low-rank bias.

=== THE ACTUAL STRUCTURE (new, and it inverts my previous reading) ===
The hidden states ARE globally low-rank -- PC1 explains 47.82% of variance, PC1-10 explains 83.68%, participation ratio 3.92 -- but the hr_peak signal is NOT in those dominant directions:
  corr(PC_i score, hr_peak), i=1..10: +0.042, -0.358, -0.019, -0.209, -0.215, +0.211, +0.219, +0.069, -0.285, -0.010
PC1 carries nearly half the variance and is essentially UNCORRELATED with the target (+0.042). The peak lives in HIGH-RANK, LOW-VARIANCE directions. Ridge recovers it because Ridge weights directions by predictive value, not by variance; PCA truncation destroys it because it ranks by variance.

=== WHY THIS MATTERS MORE THAN THE HYPOTHESIS DID ===
This is a mechanism, not another dead lever. The sensor signal is written into the residual stream in directions that are geometrically SMALL relative to the stream's dominant text-driven variance. That is a coherent single explanation for the whole E24-E28 series, and it is a DIFFERENT explanation from the one I recorded after E28:
  * E28's 'information limit' verdict stands as measured, but its natural reading -- 'the features simply do not contain more' -- is now WRONG. The features DO contain more (full-dim Ridge reaches 10.270, 8 bpm better than a constant). What is true is that no readout recovers more than ~10.3 bpm, and the reason is now visible: the signal is spread thinly across many low-variance directions.
  * It also explains the measured cos = -0.015 between the prefix's HR direction and the LM's native numeric direction (E20). A signal in low-variance off-manifold directions is exactly a signal the LM's own numeric machinery has no reason to read.
  * And it explains why non-linear readouts LOSE (E28): they allocate capacity along high-variance directions where there is no signal.

=== WHAT THIS PREDICTS FOR THE NEXT ARM ===
The intervention class that follows is one that puts sensor information into directions the LM ALREADY USES, rather than adding capacity to read the directions it currently writes:
  (a) whitening / variance-normalising the projector output before injection, so sensor variance is not dwarfed by text variance;
  (b) initialising soft tokens from real vocabulary embeddings so they start on the LM's own manifold;
  (c) an explicit alignment loss pushing projector output toward the LM's numeric-token subspace (Q-Former two-stage contrastive alignment, BLIP-2 Li et al. 2023, flagged in the literature review as the most-replicated fix for this exact orthogonality symptom);
  (d) zero-init gated cross-attention (LLaMA-Adapter arXiv:2303.16199), which changes what the LM COMPUTES rather than what it is biased by.
All four are cheap on a 5070 and none has been tried. (a) and (b) are nearly free.

=== SCOPE LIMITS, BOTH PREREGISTERED AND BOTH NOW MORE IMPORTANT ===
1. Petrov et al. (arXiv:2310.19698) assumes a FIXED prefix; mine varies per sample, so this tested a CONSEQUENCE of the theory, not the theorem. The consequence failed. I am NOT claiming the theorem is false -- a per-sample prefix is outside its scope. The honest statement: this system does not show the low-rank signature the bias picture predicts.
2. Rank measured at ONE position (the emission token, final layer) does not localise WHERE the signal became high-rank and low-variance. The obvious follow-up is to run the same spectrum analysis on the PROJECTOR OUTPUT itself and at intermediate layers -- if the projector output is already low-variance relative to the token stream, the fix is upstream normalisation; if it starts strong and decays, the fix is injection or forward-pass related. That is another zero-GPU experiment on data I would need to re-extract, and it should be the next step before any training run.

=== BOOKKEEPING ===
The recorded diagnostic delta (10.270 -> 18.407, direction up) is NOT a regression in the system. It is the measurement of a deliberately crippled rank-3 readout, recorded to document how far a low-rank subspace falls short. The deployable number for this system remains E28's full-dimensional 10.270 bpm.

Artifacts: outputs/probes/e29_prefix_rank.json, logs/e29_rank.log. Script scratch/probe_e29_prefix_rank.py. Zero GPU.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
