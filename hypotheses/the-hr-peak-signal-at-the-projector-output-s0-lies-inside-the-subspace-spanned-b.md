---
created_at: '2026-08-05T21:55:50+00:00'
gates:
- blocking: true
  description: PRIMARY. Project the S0 soft tokens onto an orthonormal basis for the
    top-k principal directions of the LM's vocabulary embedding matrix, discarding
    everything orthogonal, and refit the SAME RidgeCV readout. If MAE returns to near
    the unprojected 7.605 at moderate k, the predictive signal LIVES INSIDE the LM's
    native subspace and the off-manifold reading of E30's low alignment is wrong.
  id: gate-vocab-subspace-retains-signal
- blocking: true
  description: THE CONTROL THAT MAKES THIS EXPERIMENT MEAN ANYTHING. At every k, repeat
    the projection onto a RANDOM orthonormal subspace of IDENTICAL rank. Any MAE change
    in the vocab arm is interpretable only relative to this. If vocab and random behave
    the same, the only thing being measured is dimension reduction and NOTHING is
    established about the LM's geometry. A vocab-specific collapse (vocab much worse
    than random at equal rank) is required to claim the direction is orthogonal to
    what the LM spans.
  id: gate-random-subspace-control
- blocking: true
  description: DONOR AND SHUFFLED NULLS AT EVERY k IN BOTH ARMS. Donor-prefix features
    and permuted training targets must never beat always-predict-median (18.492).
    Mandatory here because k up to 1024 against n_train=700 is exactly the regime
    where projection could manufacture apparent skill.
  id: gate-donor-shuffled-nulls
- blocking: true
  description: ANCHOR. Unprojected full-dimensional Ridge on S0 must reproduce exp-e30's
    7.605 bpm within ~0.5 bpm, else the extraction differs from E30's and no comparison
    in this experiment is admissible.
  id: gate-anchor-reproduces-e30-s0
- blocking: false
  description: 'NORM/SCALE CHECK, reported separately from the subspace question.
    Compare soft-token norms against real token embeddings. CRITICAL METHOD POINT:
    Gemma3 uses Gemma3TextScaledWordEmbedding which multiplies the raw weight by hidden_size**0.5
    = sqrt(2560) ~ 50.6 at FORWARD time (modeling_gemma3.py:113, :506). Soft tokens
    are injected as embeddings, i.e. after that scaling applies to real tokens, so
    the correct comparison is against emb(input_ids), NOT against emb.weight. Comparing
    against the raw weight overstates the ratio by ~50x. Report the effective ratio
    and state plainly if the apparent mismatch was a measurement error.'
  id: gate-norm-scale-descriptive
id: the-hr-peak-signal-at-the-projector-output-s0-lies-inside-the-subspace-spanned-b
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'PRIMARY PREDICTION: the vocab-subspace projection RETAINS the signal
    -- MAE returns to within ~1 bpm of the unprojected 7.605 at moderate k (k around
    128-512) -- and the RANDOM subspace of equal rank performs comparably, showing
    that any loss at small k is ordinary dimension reduction rather than a vocab-specific
    geometric obstruction. Direction ''none'' refers to the vocab-minus-random gap
    being near zero, i.e. NO vocab-specific degradation. If that holds, the off-manifold
    reading of E30''s low alignment is REFUTED and projector whitening / vocab-embedding
    init is a DEAD LEVER that should not be spent on. I consider this the more likely
    outcome and I am predicting against the fix I proposed one experiment ago. The
    alternative -- vocab arm collapsing toward the 18.492 median while the random
    arm of equal rank does not -- would confirm the predictive direction is specifically
    orthogonal to what the LM spans and would motivate variance-matching / vocab-statistics
    init.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak signal at the projector output (S0) lies INSIDE the subspace spanned by the frozen LM's token embeddings, and therefore E30's low alignment number does NOT mean the projector emits an off-manifold signal.

Operationally: project the S0 soft tokens onto an orthonormal basis for the top-k principal directions of the LM's vocabulary embedding matrix, discard every component orthogonal to that basis, and refit the identical RidgeCV readout. The decoded MAE will return to within about 1 bpm of the unprojected 7.605 at moderate k, and a RANDOM orthonormal subspace of identical rank will behave comparably.

IF CONFIRMED: the predictive direction is not orthogonal to what the LM spans, the 'off-manifold' reading of E30 is wrong, and projector whitening / vocab-embedding-statistics init is a DEAD LEVER that must not be spent on. The bottleneck is then not the geometry of what the projector hands over.

IF REFUTED -- the vocab arm collapses toward the 18.492 median while the random arm of equal rank does not: the predictive direction IS specifically orthogonal to the LM's native subspace, which is off-manifold in the decision-relevant sense, and variance-matching / vocab-statistics init becomes a measured-motivated fix rather than an analogy.

IF BOTH ARMS COLLAPSE TOGETHER: only dimension reduction is being measured, nothing is established either way, and the experiment must be reported as uninformative rather than read as support for either side.

SCOPE: this measures whether a LINEAR readout can still find the signal after projection. It does not establish that the LM's own attention and MLP machinery would use that direction, which is a strictly stronger claim no projection test can settle. S0 is mean-pooled over the 96 slots, so a per-slot geometry could differ.

## Rationale
exp-e30 measured decodability at six stages and found the projector output decodes hr_peak at 7.605 bpm against always-median 18.492, while the emission position manages only 10.270. It also found S0 alignment already low (0.1602) with the S0 rank sweep needing k>=100, and I concluded from that pair of facts that the projector emits an 'off-manifold' signal, naming whitening / vocab-embedding init as the next and larger lever.

That conclusion is an INFERENCE FROM A VARIANCE STATISTIC, not a measurement of the geometry it claims. 'Spread across low-variance directions' and 'lying outside the subspace the LM can use' are different propositions, and E30 only established the first. Four levers in this problem have already died from acting on an inference before measuring it, and E30 itself demonstrated that a preregistered summary statistic can be the wrong instrument. Before spending training on a geometric fix, the geometric claim gets a direct test.

The test is nearly free: S0 requires no LM forward pass at all, only encoder+projector, plus one read of the embedding matrix. The vocabulary embedding matrix defines empirically which directions the frozen LM's machinery was trained to consume. Projecting the soft tokens onto the top-k principal directions of that matrix, discarding everything orthogonal, and refitting the same readout asks the question directly.

The RANDOM-SUBSPACE CONTROL at identical rank is what makes the result mean anything: without it, any degradation is indistinguishable from ordinary dimension reduction. This is the same discipline that retracted E25 and E26b.

I am predicting AGAINST the fix I proposed one experiment ago. If the signal survives the vocab projection, whitening and vocab-init are dead and I will say so rather than running them.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-vocab-subspace-retains-signal` (blocking): PRIMARY. Project the S0 soft tokens onto an orthonormal basis for the top-k principal directions of the LM's vocabulary embedding matrix, discarding everything orthogonal, and refit the SAME RidgeCV readout. If MAE returns to near the unprojected 7.605 at moderate k, the predictive signal LIVES INSIDE the LM's native subspace and the off-manifold reading of E30's low alignment is wrong.
Gate `gate-random-subspace-control` (blocking): THE CONTROL THAT MAKES THIS EXPERIMENT MEAN ANYTHING. At every k, repeat the projection onto a RANDOM orthonormal subspace of IDENTICAL rank. Any MAE change in the vocab arm is interpretable only relative to this. If vocab and random behave the same, the only thing being measured is dimension reduction and NOTHING is established about the LM's geometry. A vocab-specific collapse (vocab much worse than random at equal rank) is required to claim the direction is orthogonal to what the LM spans.
Gate `gate-donor-shuffled-nulls` (blocking): DONOR AND SHUFFLED NULLS AT EVERY k IN BOTH ARMS. Donor-prefix features and permuted training targets must never beat always-predict-median (18.492). Mandatory here because k up to 1024 against n_train=700 is exactly the regime where projection could manufacture apparent skill.
Gate `gate-anchor-reproduces-e30-s0` (blocking): ANCHOR. Unprojected full-dimensional Ridge on S0 must reproduce exp-e30's 7.605 bpm within ~0.5 bpm, else the extraction differs from E30's and no comparison in this experiment is admissible.
Gate `gate-norm-scale-descriptive` (advisory): NORM/SCALE CHECK, reported separately from the subspace question. Compare soft-token norms against real token embeddings. CRITICAL METHOD POINT: Gemma3 uses Gemma3TextScaledWordEmbedding which multiplies the raw weight by hidden_size**0.5 = sqrt(2560) ~ 50.6 at FORWARD time (modeling_gemma3.py:113, :506). Soft tokens are injected as embeddings, i.e. after that scaling applies to real tokens, so the correct comparison is against emb(input_ids), NOT against emb.weight. Comparing against the raw weight overstates the ratio by ~50x. Report the effective ratio and state plainly if the apparent mismatch was a measurement error.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
