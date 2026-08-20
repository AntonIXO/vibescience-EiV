---
created_at: '2026-08-15T10:22:58+00:00'
former_ids: []
gates:
- 'g1_head_beats_own_null (BLOCKING): the supervised head''s momentary-state AUC must
  beat its OWN 24-permutation within-window shuffle null with perm p < 0.05 on E26''s
  rows (n=3226, 87 subjects, 660 windows). This is the bar E28''s arms FAILED (0.5237/0.5325
  vs nulls 0.5548/0.5482). Clearing the null is necessary, not sufficient.'
- 'g2_head_reaches_geometry_bound (BLOCKING, MDE committed BEFORE the run): the head
  must reach at least AUC 0.60 -- i.e. recover a majority of the +0.11 over-null headroom
  that raw-on-grid demonstrated (0.6309 vs null 0.5189). If the head clears its null
  but lands under 0.60, record the verdict as a POWERED NULL against ''a supervised
  objective recovers the geometry''s state content'', not as a near-miss. E29 proved
  the information is reachable from this geometry by a shallow model; a trained head
  that cannot approach it is a real negative about the encoder/objective, and that
  is the single most valuable outcome this arm can produce.'
- 'g3_do_no_harm (BLOCKING): hr_mean subject-grouped readout R2 must not regress below
  0.93 (E28 control/intervention both 0.9398/0.9399). Buying state by wrecking the
  HR axis is not a win -- this is the same reallocation trap that refuted the H4b
  caption arm.'
- 'g4_deployment_not_wrecked (BLOCKING): eiv_supabase_hr_raw_mae_bpm on the frozen
  200-row draw must not regress beyond the 0.60 bpm seed band vs the v9band04 baseline
  (2.85 named seed, trivial 4.02), with eiv_dense128_hr_raw_mae_bpm as the companion
  guard. Anton''s ground truth is the production annotation path, not a probe.'
hypothesis_id: analog-state-supervision-a1-on-the-12-547-c5f5eb
id: rb-supervised-state-head-20260815
notes:
- at: '2026-08-15T12:06:29+00:00'
  text: "[2026-08-15] DESIGN CONSTRAINT UPGRADED FROM ARGUED TO MEASURED. Rule-out
    `ro-e22-locality-is-not-an-artefact-20260815` (exp-e31, commit da42ef7).\n\nThis
    runbook already required the head to read EPOCH-LOCAL tokens rather than a window-pooled
    vector. That was an inference from E26's TripletStateDataset defect. It is now
    a measurement, and it is stronger than the argument was.\n\nE22's locality finding
    had been recorded with NO null of any kind. E31 paid that debt with two controls
    on E22's exact design (v5d/phase_b, n=4219 pings, 61 subjects, GroupKFold by subject,
    24 within-subject label permutations per arm):\n\n  lifesnaps_tense            AUC
    \     null             z        perm p\n    enc_global_avg          0.5260   0.4963+-0.0185
    \  +1.60    0.0833   <- FAILS its null\n    enc_local_avg           0.5876   0.4862+-0.0169
    \  +6.01    0.0000\n    enc_PLACEBO_local_avg   0.5154   0.4896+-0.0136   +1.90\n
    \ lifesnaps_tired\n    enc_global_avg          0.5255   0.5189+-0.0110   +0.60
    \   0.2083   <- FAILS its null\n    enc_local_avg           0.5841   0.5123+-0.0116
    \  +6.17    0.0000\n    enc_PLACEBO_local_avg   0.5420   0.5058+-0.0115   +3.15\n
    \   enc_global_avgmax       0.5194   0.5169+-0.0107   +0.23    0.3750   <- FAILS
    its null\n\nTWO CONSEQUENCES FOR THIS ARM.\n\n1. A WINDOW-POOLED STATE HEAD IS
    REFUTED BEFORE IT IS BUILT. The GLOBAL (window-pooled) read of the encoder fails
    its own within-subject shuffle null on both targets, and on tired it fails under
    both pooling operators. A globally-pooled representation is exactly what the projector
    consumes in production. Building the head on the pooled vector would be building
    it on a representation already measured to be indistinguishable from noise on
    affect.\n\n2. THE LOCAL READ IS REAL, NOT AN ARTEFACT, AND NOT A DIMENSIONALITY
    EFFECT. Local arms clear their nulls at z +6.0 to +6.8 on both targets and both
    operators, and beat a PLACEBO-LOCALITY control (same token count, random epoch,
    same day) by +0.042 to +0.072. So there IS ping-aligned signal in the encoder
    tokens for an epoch-local head to read — a different and more encouraging statement
    than E29's.\n\nRECONCILING E29 AND E31 (state this in the closure, do not paper
    over it): the two tasks differ in label source and design. E31's targets are lifesnaps
    EMA mood pings scored cross-subject with a within-subject permutation null; E29's
    is the within-window flow-vs-stress transition pair with a within-window null.
    The encoder carries ping-aligned affect signal (E31) while failing the harder
    trait-free transition contrast (E29). The head should therefore be evaluated on
    BOTH, and a pass on E31-style targets alone must NOT be reported as solving momentary
    state."
- at: '2026-08-15T13:29:40+00:00'
  text: "[2026-08-15, LATER] THE E29/E31 DISAGREEMENT IS RESOLVED, AND THE PRIOR NOTE
    ON THIS RUNBOOK IS PARTLY WRONG. See exp-e32 (commit 47fa199), rule-out `ro-encoder-affect-is-time-of-day-20260815`,
    which RETRACTS `ro-e22-locality-is-not-an-artefact-20260815`.\n\nWHAT CHANGED.
    The previous note said the encoder 'carries ping-aligned affect signal (E31, z
    +6)' and told the closure to reconcile that against E29. There is nothing to reconcile:
    E31 was measuring TIME OF DAY. Its placebo read a RANDOM epoch, destroying ping
    alignment and clock together. Controlled properly (same clock time, DIFFERENT
    day, same subject):\n    tense  local 0.5876  sameclock 0.5733  =>  +0.0143  (placebo
    had said +0.0722)\n    tired  local 0.5841  sameclock 0.5946  =>  -0.0105  (placebo
    had said +0.0421)\nOn 'tired' a different day at the same hour BEATS the true
    ping. An hour-stratified null agrees: enc_local z +6.70 -> +1.81 (tense), +6.10
    -> -0.22 (tired), with clock_only collapsing +2.75 -> +0.37 and +4.90 -> +0.98
    as the positive control.\n\nSO: the trained encoder fails on BOTH designs. E29
    and E31 agree.\n\nTHREE CONSEQUENCES FOR THIS ARM.\n\n1. SCORE THE HEAD ON THE
    TRANSITION TASK (E26/E29 rows), NOT ON EMA PINGS. The within-window design holds
    subject, day and circadian phase constant by construction. `eiv_momentary_state_auc_over_null`
    stays the primary metric and the reference triple is unchanged. The prior note's
    instruction to evaluate on BOTH tasks is superseded: a lifesnaps-ping evaluation
    is not a second opinion, it is a clock detector, and passing it would be a false
    positive.\n\n2. IF ANY PING-STYLE OR CROSS-SUBJECT EVALUATION IS RUN ANYWAY, IT
    MUST CARRY A TIME-OF-DAY CONTROL: a same-clock-different-day donor arm and/or
    an hour-stratified permutation null. A random-epoch placebo is INVALID on circadian
    signals. Note the clock-only AUC of the target pool before trusting any arm on
    it (0.5493/0.5739 on pings vs 0.5129 on transitions).\n\n3. THE EPOCH-LOCAL REQUIREMENT
    STILL HOLDS, but its justification is now only the surviving half of the retracted
    rule-out: global window-pooled reads fail their own within-subject null (z +1.60/+0.60/+0.23),
    and that is what the projector consumes. Do not cite 'E31 showed local reads work'
    as support -- that claim is retracted.\n\nUNTOUCHED: E29's raw_patch_grid gate
    (AUC 0.6309, z +2.66) stands. Its task has no clock confound and its null is within-window.
    This runbook remains JUSTIFIED and its gates are unchanged."
- at: '2026-08-18T23:10:56+00:00'
  text: "[2026-08-18] THIS RUNBOOK'S CENTRAL PREMISE IS SHARPENED AND ONE OF ITS SENTENCES
    IS NOW WRONG. See exp-046 (commit ff14a20, eea90de), rule-out `that-a-jepa-latent-prediction-pretraining-7061c5`.\n\nTHE
    SENTENCE TO STOP QUOTING. The prompt above says, under E29: \"So the PATCH GEOMETRY
    preserves momentary state and the LEARNED WEIGHTS destroy it. The information
    is present at the encoder's input and absent at its output.\" The first half stands.
    The second half is too strong, and the correction MATTERS FOR THIS ARM'S DESIGN.\n\nWHAT
    WAS MEASURED. E29's finding was challenged on two confounds (dimensionality 8-vs-9216,
    and encoder-stage-vs-production-stage) and BOTH challenges died, so E29 survives
    an adversarial re-test. But the run also added the counterfactual E29 never had.
    All arms on E29's exact rows (n=3226 / 87 subjects / 660 windows / W=120), each
    vs its OWN 24-perm within-window null; harness reproduced E29's 0.6309 and 0.5202
    to FOUR DECIMALS first:\n    raw_patch_grid_8    8 feats   AUC 0.6309  null 0.5189+-0.0422
    \ over_null +0.1120  z +2.66\n    enc_pca8            8 feats   AUC 0.4950  null
    0.5442+-0.0293  over_null -0.0492  z -1.68\n    rand_pca8 (RANDOM)  8 feats   AUC
    0.5442  null 0.5464+-0.0290  over_null -0.0022  z -0.08\n    prod_pca8           8
    feats   AUC 0.4747  null 0.5554+-0.0262  over_null -0.0807  z -3.08\n    enc_full
    \        9216 feats   AUC 0.5202  null 0.5495+-0.0351  over_null -0.0293  z -0.84\n\nA
    RANDOMLY INITIALISED encoder sits EXACTLY ON ITS OWN NULL. So the honest statement
    is not \"training destroyed the state signal\" but \"this architecture never surfaced
    momentary state at all -- trained or untrained\". There is no evidence of a state
    signal that training removed; there is evidence of a function class that never
    exposed one.\n\nTHREE CONSEQUENCES FOR THIS ARM (it remains JUSTIFIED, and its
    gates are UNCHANGED).\n1. THE ARGUMENT FOR IT IS NOW WEAKER THAN THE PROMPT CLAIMS,
    AND SHOULD BE STATED THAT WAY IN THE PREREGISTRATION. The prompt's logic is \"E28's
    null was ambiguous; E29 eliminated 'the architecture cannot represent state'\".
    E29 eliminated it only for the RAW GEOMETRY (8 handcrafted features on the patch
    grid), NOT for the encoder's learned function class -- whose random init is measurably
    inert. Preregister accordingly: this arm now tests whether UNFREEZING and supervising
    can make a function class expose something neither its random init nor its contrastive
    training ever exposed. That is a real question, and a harder one than the prompt
    implies.\n2. UNFREEZING IS NO LONGER OPTIONAL. The prompt already said \"encoder
    UNFROZEN for this arm, or the head can only re-read tokens E29 already showed
    are stateless\". rand_pca8 upgrades that from advice to a requirement: a head
    on frozen tokens is reading a representation whose UNTRAINED equivalent is already
    at its null. If VRAM forces a partial unfreeze, say which blocks and treat a null
    as uninformative about the full-unfreeze question.\n3. DO NOT ADD A PRODUCTION-STAGE
    (post-projector) READOUT TO THIS ARM AS A SECOND OPINION. prod_pca8 is the WORST
    arm measured (-0.0807, z-3.08, the only one significantly below its own null).
    The 4.3x production-over-encoder advantage that ro-dual-avg-max-pooling-downstream-20260805
    measured for hr_peak does NOT transport to a momentary target: prototype cross-attention
    plus the unit-RMS tail concentrates daily-aggregate structure and erases a 2-hour
    contrast. GENERAL RULE EARNED: evidence is bound to its TARGET as well as its
    substrate.\n\nSEQUENCING SUGGESTION, NOT A BLOCK. A new CPU runbook `the-function-class-residual-8-handcrafted-21b62f`
    (priority 10, minutes) asks whether the encoder's tokens can even LINEARLY RECONSTRUCT
    min/max/std of their own input patch. If they cannot, that names the function-class
    defect concretely and would likely change how this GPU arm is built (patch construction,
    not just supervision). Cheap enough to run first.\n\nUNTOUCHED: E29's raw_patch_grid
    gate (AUC 0.6309, z +2.66) stands, the reference triple stands, supply stands
    at 77081 state-labelled epochs / 281 subjects, and all four blocking gates are
    unchanged."
- at: '2026-08-18T23:23:42+00:00'
  text: "[2026-08-18, LATER SAME DAY] THE PREMISE OF THIS RUNBOOK IS NOW READOUT-DEPENDENT.
    Third amendment. See exp-e34 (commit b95c64d), rule-out `the-function-class-account-of-exp-046-that-8-511840`.
    The diagnostic eiv_momentary_state_auc_over_null has been amended accordingly.\n\nWHAT
    CHANGED SINCE THE NOTE ABOVE (posted ~20 minutes earlier). That note told you
    a RANDOM-INIT encoder is inert and therefore this arm tests something harder than
    the prompt claims. That still stands under a RandomForest. But varying ONLY the
    estimator on identical rows/folds/nulls INVERTS the arm ordering:\n\n  arm                readout
    \   AUC     null            over_null    z\n  raw_patch_grid_8   RF      0.6309
    \ 0.5189+-0.0422     +0.1120   +2.66\n  raw_patch_grid_8   LINEAR  0.5855  0.5711+-0.0130
    \    +0.0143   +1.10\n  enc_pca8           RF      0.4950  0.5442+-0.0293     -0.0492
    \  -1.68\n  enc_pca8           LINEAR  0.5715  0.5310+-0.0143     +0.0405   +2.83\n\nUnder
    a MATCHED LINEAR readout the TRAINED ENCODER CLEARS its own within-window null
    (z+2.83) while the 8 handcrafted features do NOT (z+1.10). The 'raw features beat
    the encoder' result was carried by the RANDOMFOREST: 8 dense axis-aligned handcrafted
    features suit axis-aligned splits; 8 PCA components of a distributed code do not.\n\nAlso
    measured: the encoder's tokens CAN linearly reconstruct their own input-patch
    order statistics (min/max/std mean R2 0.5182, vs a preregistered <0.50 falsifier),
    and a random-init encoder reconstructs equally well (0.6177 vs 0.5924) because
    the 120->192 patch projection is over-complete by construction.\n\nFOUR CONSEQUENCES
    FOR THIS ARM. It remains JUSTIFIED; its four blocking gates are UNCHANGED.\n1.
    THE MOTIVATING STORY IS WEAKER AGAIN, AND THE PREREGISTRATION MUST SAY SO. Both
    'the learned weights destroy state' (prompt) and 'the architecture never carried
    state' (previous note) are RF-conditional. The defensible statement is: momentary
    state is WEAK in these tokens under every readout tried, and no arm has reached
    the raw_shallow bound of 0.6430.\n2. SCORE THE HEAD UNDER AT LEAST TWO READOUT
    FAMILIES, each with its OWN null recomputed. A supervised head is itself a readout;
    reporting it against an RF-derived reference triple would compare (new head +
    its own estimator) against (old features x RF) and is not a like-for-like contrast.
    Gate g2's AUC 0.60 bar is unchanged, but state which estimator produced any comparison
    number.\n3. DO NOT CITE 'raw features beat the trained encoder' AS THE MOTIVATION.
    Under a linear readout the reverse holds. The honest motivation for this arm is
    now simply that NOTHING has ever optimised the momentary label directly, which
    was always the strongest part of the case and does not depend on the disputed
    comparison.\n4. A NULL IS STILL VALUABLE, but its interpretation changes: the
    fallback recommendation of 'ship the shallow detector instead' is weakened, because
    the shallow detector's advantage is itself estimator-dependent (raw features clear
    their null at z+1.10 under linear, i.e. NOT significantly). Re-measure the shallow
    detector under both readouts before recommending it as a product fallback.\n\nUNCHANGED:
    supply (77081 state-labelled epochs / 281 subjects), the within-window-null-only
    rule, the epoch-local requirement, the batch_size=1 capture pitfall, and all four
    blocking gates."
- at: '2026-08-19T21:21:50+00:00'
  text: '2026-08-19: EXECUTED as the A1/B/A4/W-sweep campaign (exp-047 test_invalid
    → exp-048 refutes → exp-049 supports). The supervised state head was built (Phase
    A + Phase B, per-window + per-patch, commit 725ae5e + session fixes), trained
    at 5 seeds, and scored with its OWN logits per this runbook''s design. Outcome:
    supervision puts momentary state into the encoder (pooled W30/max +0.0218 z+3.64),
    at ~1/3 of the handcrafted bound — the runbook''s ''null here is valuable'' branch
    resolved to ''effect exists, supply-limited; ship the shallow detector meanwhile''.
    See exp-049 interpretation for the W=30 eval-cell rule.'
priority: 3
problem_id: peri-ema-window-destroys-momentary-labels-20260815
status: done
topic_tags: []
updated_at: '2026-08-19T21:21:50+00:00'
---

## Prompt
SUPERVISED MOMENTARY-STATE HEAD -- train the state label DIRECTLY, in the encoder, and score it against its own shuffle null. GPU. Repo /root/optiHealth-EiV.

WHY THIS ARM EXISTS (full context for a fresh session).
optiHealth-EiV / SensorLM: a temporal encoder over wearable physiology emits soft-token prefixes for MedGemma. The `state` slot (flow / stress / neutral) has failed every attempt for weeks, and the failures have now been localised precisely. Do not re-litigate any of the following -- each is measured and recorded.

  E16/E17  Cross-subject affect is TRAIT-DOMINATED. A per-user constant scores AUC 0.7381 while the best physiological arm reaches 0.5818. Shortening the window does not help; the delta over a clock-only control RISES with window length and peaks at 24h. Rule-out `shortening-the-analysis-window-to-align-with-0f6d56`.
  E24      State detection WORKS where labels are dense and the stressor is induced (WESAD AUC 0.8497 vs clock 0.5640). The discriminating variable is LABEL QUALITY, not the sensor stack.
  E26      THE CLEAN DESIGN: within-window transitions -- two DIFFERENT states, SAME subject, SAME day, minutes apart, so trait is held constant BY CONSTRUCTION and the 0.7381 per-user constant is exactly 0.5. Shallow peri-epoch HR features score AUC 0.6329 vs a within-window shuffle null of 0.5492 (+3.41 SD). n=3226 epochs, 87 subjects, 660 windows, W=120.
  E28      The trained encoder's own tokens score 0.5237 / 0.5325 -- BELOW their own shuffle nulls (0.5548 / 0.5482). Fixing the triplet miner (segment-level anchors, same-window hard negatives) did NOT help, and the mining demonstrably worked (triplet loss 2.6x, margin driven to its 0.80 ceiling). The encoder carries no state.
  E29      THE GATE FOR THIS ARM. Raw canonical HR read on the ENCODER'S OWN patch grid (patch_len=120, stride=60, avgmax over covering patches) scores AUC 0.6309 vs its own null 0.5189+-0.0422, z +2.66 -- from 8 features and ZERO learned parameters. So the PATCH GEOMETRY preserves momentary state and the LEARNED WEIGHTS destroy it. The information is present at the encoder's input and absent at its output.

THAT IS THE WHOLE ARGUMENT FOR THIS ARM. E28's null was ambiguous between 'the objective never asked for state' and 'this architecture cannot represent state'. E29 eliminated the second. Nothing has ever optimised the momentary label directly: state has only ever been a 0.05-weighted auxiliary margin while InfoNCE-on-HR-mean-captions dominated the gradient.

WHAT TO BUILD.
A supervised per-epoch state head on top of the temporal encoder, trained with cross-entropy on the MOMENTARY label at the epoch it was recorded in (commit a7fc419 placed labels on their true epoch). Key design decisions to make explicitly and record in config_note:
  * The head must read EPOCH-LOCAL tokens (the patches covering [epoch-120, epoch)), NOT a window-pooled vector. A window-pooled representation cannot express a momentary label -- that is the defect E26 identified in TripletStateDataset, which mines on infer_state_label and collapses each transition window to ONE majority label, discarding its internal contrast.
  * Encoder UNFROZEN for this arm, or the head can only re-read tokens E29 already showed are stateless. If VRAM binds, unfreeze the last block only and say so.
  * CLASS IMBALANCE is the real design issue, not supply: 77081 labelled epochs over 281 subjects but {neutral 62053, stress 10297, flow 4731} = 80.5% neutral. Use class-balanced sampling or weighted CE, and report the per-class recall, never a bare accuracy.
  * Keep the HR objective alive alongside it (do-no-harm gate g3). The point is to ADD state, not trade HR for it.

ARMS: control = v9band04 recipe unchanged. intervention = identical plus the supervised state head. >=2 seeds; single-seed comparisons are void on this project (post-atomfix supabase seed band 0.60 bpm; the pre-fix band was 11.37).

SCORING -- the primary metric is `eiv_momentary_state_auc_over_null`, which SUBTRACTS the arm's own within-window shuffle null. On this task the null is NOT 0.5 and it RISES with feature dimensionality (8 feats -> 0.5189, 9216 feats -> 0.5495). A bare AUC has already caused one near-miss reading of a negative result in E28. Always report: raw AUC, null mean +- sd over >=24 permutations, z, perm p, n epochs / subjects / windows, and the feature dimensionality.

REFERENCE TRIPLE to beat, all on E26's exact rows:
    raw_shallow     15 feats   0.6430  null 0.5441   z +4.69
    raw_patch_grid   8 feats   0.6309  null 0.5189   z +2.66   <- the geometry bound this head should approach
    enc_token     9216 feats   0.5202  null 0.5495   z -0.84   <- what the incumbent encoder does

PREREGISTER before launching (propose_hypothesis with predicted_effects + gates, then start_experiment). Commit the MDE against the geometry bound.

A NULL HERE IS GENUINELY VALUABLE AND MUST BE REPORTED PLAINLY: if a head trained directly on the label still cannot reach what 8 raw features already achieve on the same geometry, that is strong evidence the encoder/objective family is the wrong vehicle for state, and the recommendation becomes shipping the shallow detector (src/annotation/momentary_state.py, AUC 0.6329) as the state slot rather than continuing to buy encoder arms.

DEPLOYMENT HONESTY: optihealth_supabase currently carries ZERO state labels, so even a passing result cannot ship to the production annotation path until EMA collection starts there. Say so in the closure rather than implying the feature is immediately shippable.

PITFALL: emission captures stay batch_size=1 (rule-out `batched-generate-in-the-capture-f75db4`: 31/32 emissions differ at batch=8 because cuBLAS reduction-order nondeterminism flips greedy argmax on near-ties).

## Preconditions
E29 (exp-e29-state-localisation-20260815) CLOSED with verdict=supports -- this runbook is its follow-up and must not be run without it. GPU free (RTX 5070, 12227 MiB, stay under 11.25 GiB). Baseline recipe v9band04 (docs/BASELINE.md). Read the corpus shape/git_sha from merge_manifest.json rather than assuming. CRITICAL: use src.training.datasets.state_series to read momentary labels -- a hand-rolled reader looking for a literal 'state' key in metadata.observed(epoch) returns ZERO labels corpus-wide (trap hit and recorded in E29).

## Log
- **2026-08-15T12:06:29+00:00** — [2026-08-15] DESIGN CONSTRAINT UPGRADED FROM ARGUED TO MEASURED. Rule-out `ro-e22-locality-is-not-an-artefact-20260815` (exp-e31, commit da42ef7).

This runbook already required the head to read EPOCH-LOCAL tokens rather than a window-pooled vector. That was an inference from E26's TripletStateDataset defect. It is now a measurement, and it is stronger than the argument was.

E22's locality finding had been recorded with NO null of any kind. E31 paid that debt with two controls on E22's exact design (v5d/phase_b, n=4219 pings, 61 subjects, GroupKFold by subject, 24 within-subject label permutations per arm):

  lifesnaps_tense            AUC      null             z        perm p
    enc_global_avg          0.5260   0.4963+-0.0185   +1.60    0.0833   <- FAILS its null
    enc_local_avg           0.5876   0.4862+-0.0169   +6.01    0.0000
    enc_PLACEBO_local_avg   0.5154   0.4896+-0.0136   +1.90
  lifesnaps_tired
    enc_global_avg          0.5255   0.5189+-0.0110   +0.60    0.2083   <- FAILS its null
    enc_local_avg           0.5841   0.5123+-0.0116   +6.17    0.0000
    enc_PLACEBO_local_avg   0.5420   0.5058+-0.0115   +3.15
    enc_global_avgmax       0.5194   0.5169+-0.0107   +0.23    0.3750   <- FAILS its null

TWO CONSEQUENCES FOR THIS ARM.

1. A WINDOW-POOLED STATE HEAD IS REFUTED BEFORE IT IS BUILT. The GLOBAL (window-pooled) read of the encoder fails its own within-subject shuffle null on both targets, and on tired it fails under both pooling operators. A globally-pooled representation is exactly what the projector consumes in production. Building the head on the pooled vector would be building it on a representation already measured to be indistinguishable from noise on affect.

2. THE LOCAL READ IS REAL, NOT AN ARTEFACT, AND NOT A DIMENSIONALITY EFFECT. Local arms clear their nulls at z +6.0 to +6.8 on both targets and both operators, and beat a PLACEBO-LOCALITY control (same token count, random epoch, same day) by +0.042 to +0.072. So there IS ping-aligned signal in the encoder tokens for an epoch-local head to read — a different and more encouraging statement than E29's.

RECONCILING E29 AND E31 (state this in the closure, do not paper over it): the two tasks differ in label source and design. E31's targets are lifesnaps EMA mood pings scored cross-subject with a within-subject permutation null; E29's is the within-window flow-vs-stress transition pair with a within-window null. The encoder carries ping-aligned affect signal (E31) while failing the harder trait-free transition contrast (E29). The head should therefore be evaluated on BOTH, and a pass on E31-style targets alone must NOT be reported as solving momentary state.
- **2026-08-15T13:29:40+00:00** — [2026-08-15, LATER] THE E29/E31 DISAGREEMENT IS RESOLVED, AND THE PRIOR NOTE ON THIS RUNBOOK IS PARTLY WRONG. See exp-e32 (commit 47fa199), rule-out `ro-encoder-affect-is-time-of-day-20260815`, which RETRACTS `ro-e22-locality-is-not-an-artefact-20260815`.

WHAT CHANGED. The previous note said the encoder 'carries ping-aligned affect signal (E31, z +6)' and told the closure to reconcile that against E29. There is nothing to reconcile: E31 was measuring TIME OF DAY. Its placebo read a RANDOM epoch, destroying ping alignment and clock together. Controlled properly (same clock time, DIFFERENT day, same subject):
    tense  local 0.5876  sameclock 0.5733  =>  +0.0143  (placebo had said +0.0722)
    tired  local 0.5841  sameclock 0.5946  =>  -0.0105  (placebo had said +0.0421)
On 'tired' a different day at the same hour BEATS the true ping. An hour-stratified null agrees: enc_local z +6.70 -> +1.81 (tense), +6.10 -> -0.22 (tired), with clock_only collapsing +2.75 -> +0.37 and +4.90 -> +0.98 as the positive control.

SO: the trained encoder fails on BOTH designs. E29 and E31 agree.

THREE CONSEQUENCES FOR THIS ARM.

1. SCORE THE HEAD ON THE TRANSITION TASK (E26/E29 rows), NOT ON EMA PINGS. The within-window design holds subject, day and circadian phase constant by construction. `eiv_momentary_state_auc_over_null` stays the primary metric and the reference triple is unchanged. The prior note's instruction to evaluate on BOTH tasks is superseded: a lifesnaps-ping evaluation is not a second opinion, it is a clock detector, and passing it would be a false positive.

2. IF ANY PING-STYLE OR CROSS-SUBJECT EVALUATION IS RUN ANYWAY, IT MUST CARRY A TIME-OF-DAY CONTROL: a same-clock-different-day donor arm and/or an hour-stratified permutation null. A random-epoch placebo is INVALID on circadian signals. Note the clock-only AUC of the target pool before trusting any arm on it (0.5493/0.5739 on pings vs 0.5129 on transitions).

3. THE EPOCH-LOCAL REQUIREMENT STILL HOLDS, but its justification is now only the surviving half of the retracted rule-out: global window-pooled reads fail their own within-subject null (z +1.60/+0.60/+0.23), and that is what the projector consumes. Do not cite 'E31 showed local reads work' as support -- that claim is retracted.

UNTOUCHED: E29's raw_patch_grid gate (AUC 0.6309, z +2.66) stands. Its task has no clock confound and its null is within-window. This runbook remains JUSTIFIED and its gates are unchanged.
- **2026-08-18T23:10:56+00:00** — [2026-08-18] THIS RUNBOOK'S CENTRAL PREMISE IS SHARPENED AND ONE OF ITS SENTENCES IS NOW WRONG. See exp-046 (commit ff14a20, eea90de), rule-out `that-a-jepa-latent-prediction-pretraining-7061c5`.

THE SENTENCE TO STOP QUOTING. The prompt above says, under E29: "So the PATCH GEOMETRY preserves momentary state and the LEARNED WEIGHTS destroy it. The information is present at the encoder's input and absent at its output." The first half stands. The second half is too strong, and the correction MATTERS FOR THIS ARM'S DESIGN.

WHAT WAS MEASURED. E29's finding was challenged on two confounds (dimensionality 8-vs-9216, and encoder-stage-vs-production-stage) and BOTH challenges died, so E29 survives an adversarial re-test. But the run also added the counterfactual E29 never had. All arms on E29's exact rows (n=3226 / 87 subjects / 660 windows / W=120), each vs its OWN 24-perm within-window null; harness reproduced E29's 0.6309 and 0.5202 to FOUR DECIMALS first:
    raw_patch_grid_8    8 feats   AUC 0.6309  null 0.5189+-0.0422  over_null +0.1120  z +2.66
    enc_pca8            8 feats   AUC 0.4950  null 0.5442+-0.0293  over_null -0.0492  z -1.68
    rand_pca8 (RANDOM)  8 feats   AUC 0.5442  null 0.5464+-0.0290  over_null -0.0022  z -0.08
    prod_pca8           8 feats   AUC 0.4747  null 0.5554+-0.0262  over_null -0.0807  z -3.08
    enc_full         9216 feats   AUC 0.5202  null 0.5495+-0.0351  over_null -0.0293  z -0.84

A RANDOMLY INITIALISED encoder sits EXACTLY ON ITS OWN NULL. So the honest statement is not "training destroyed the state signal" but "this architecture never surfaced momentary state at all -- trained or untrained". There is no evidence of a state signal that training removed; there is evidence of a function class that never exposed one.

THREE CONSEQUENCES FOR THIS ARM (it remains JUSTIFIED, and its gates are UNCHANGED).
1. THE ARGUMENT FOR IT IS NOW WEAKER THAN THE PROMPT CLAIMS, AND SHOULD BE STATED THAT WAY IN THE PREREGISTRATION. The prompt's logic is "E28's null was ambiguous; E29 eliminated 'the architecture cannot represent state'". E29 eliminated it only for the RAW GEOMETRY (8 handcrafted features on the patch grid), NOT for the encoder's learned function class -- whose random init is measurably inert. Preregister accordingly: this arm now tests whether UNFREEZING and supervising can make a function class expose something neither its random init nor its contrastive training ever exposed. That is a real question, and a harder one than the prompt implies.
2. UNFREEZING IS NO LONGER OPTIONAL. The prompt already said "encoder UNFROZEN for this arm, or the head can only re-read tokens E29 already showed are stateless". rand_pca8 upgrades that from advice to a requirement: a head on frozen tokens is reading a representation whose UNTRAINED equivalent is already at its null. If VRAM forces a partial unfreeze, say which blocks and treat a null as uninformative about the full-unfreeze question.
3. DO NOT ADD A PRODUCTION-STAGE (post-projector) READOUT TO THIS ARM AS A SECOND OPINION. prod_pca8 is the WORST arm measured (-0.0807, z-3.08, the only one significantly below its own null). The 4.3x production-over-encoder advantage that ro-dual-avg-max-pooling-downstream-20260805 measured for hr_peak does NOT transport to a momentary target: prototype cross-attention plus the unit-RMS tail concentrates daily-aggregate structure and erases a 2-hour contrast. GENERAL RULE EARNED: evidence is bound to its TARGET as well as its substrate.

SEQUENCING SUGGESTION, NOT A BLOCK. A new CPU runbook `the-function-class-residual-8-handcrafted-21b62f` (priority 10, minutes) asks whether the encoder's tokens can even LINEARLY RECONSTRUCT min/max/std of their own input patch. If they cannot, that names the function-class defect concretely and would likely change how this GPU arm is built (patch construction, not just supervision). Cheap enough to run first.

UNTOUCHED: E29's raw_patch_grid gate (AUC 0.6309, z +2.66) stands, the reference triple stands, supply stands at 77081 state-labelled epochs / 281 subjects, and all four blocking gates are unchanged.
- **2026-08-18T23:23:42+00:00** — [2026-08-18, LATER SAME DAY] THE PREMISE OF THIS RUNBOOK IS NOW READOUT-DEPENDENT. Third amendment. See exp-e34 (commit b95c64d), rule-out `the-function-class-account-of-exp-046-that-8-511840`. The diagnostic eiv_momentary_state_auc_over_null has been amended accordingly.

WHAT CHANGED SINCE THE NOTE ABOVE (posted ~20 minutes earlier). That note told you a RANDOM-INIT encoder is inert and therefore this arm tests something harder than the prompt claims. That still stands under a RandomForest. But varying ONLY the estimator on identical rows/folds/nulls INVERTS the arm ordering:

  arm                readout    AUC     null            over_null    z
  raw_patch_grid_8   RF      0.6309  0.5189+-0.0422     +0.1120   +2.66
  raw_patch_grid_8   LINEAR  0.5855  0.5711+-0.0130     +0.0143   +1.10
  enc_pca8           RF      0.4950  0.5442+-0.0293     -0.0492   -1.68
  enc_pca8           LINEAR  0.5715  0.5310+-0.0143     +0.0405   +2.83

Under a MATCHED LINEAR readout the TRAINED ENCODER CLEARS its own within-window null (z+2.83) while the 8 handcrafted features do NOT (z+1.10). The 'raw features beat the encoder' result was carried by the RANDOMFOREST: 8 dense axis-aligned handcrafted features suit axis-aligned splits; 8 PCA components of a distributed code do not.

Also measured: the encoder's tokens CAN linearly reconstruct their own input-patch order statistics (min/max/std mean R2 0.5182, vs a preregistered <0.50 falsifier), and a random-init encoder reconstructs equally well (0.6177 vs 0.5924) because the 120->192 patch projection is over-complete by construction.

FOUR CONSEQUENCES FOR THIS ARM. It remains JUSTIFIED; its four blocking gates are UNCHANGED.
1. THE MOTIVATING STORY IS WEAKER AGAIN, AND THE PREREGISTRATION MUST SAY SO. Both 'the learned weights destroy state' (prompt) and 'the architecture never carried state' (previous note) are RF-conditional. The defensible statement is: momentary state is WEAK in these tokens under every readout tried, and no arm has reached the raw_shallow bound of 0.6430.
2. SCORE THE HEAD UNDER AT LEAST TWO READOUT FAMILIES, each with its OWN null recomputed. A supervised head is itself a readout; reporting it against an RF-derived reference triple would compare (new head + its own estimator) against (old features x RF) and is not a like-for-like contrast. Gate g2's AUC 0.60 bar is unchanged, but state which estimator produced any comparison number.
3. DO NOT CITE 'raw features beat the trained encoder' AS THE MOTIVATION. Under a linear readout the reverse holds. The honest motivation for this arm is now simply that NOTHING has ever optimised the momentary label directly, which was always the strongest part of the case and does not depend on the disputed comparison.
4. A NULL IS STILL VALUABLE, but its interpretation changes: the fallback recommendation of 'ship the shallow detector instead' is weakened, because the shallow detector's advantage is itself estimator-dependent (raw features clear their null at z+1.10 under linear, i.e. NOT significantly). Re-measure the shallow detector under both readouts before recommending it as a product fallback.

UNCHANGED: supply (77081 state-labelled epochs / 281 subjects), the within-window-null-only rule, the epoch-local requirement, the batch_size=1 capture pitfall, and all four blocking gates.
- **2026-08-19T21:21:50+00:00** — 2026-08-19: EXECUTED as the A1/B/A4/W-sweep campaign (exp-047 test_invalid → exp-048 refutes → exp-049 supports). The supervised state head was built (Phase A + Phase B, per-window + per-patch, commit 725ae5e + session fixes), trained at 5 seeds, and scored with its OWN logits per this runbook's design. Outcome: supervision puts momentary state into the encoder (pooled W30/max +0.0218 z+3.64), at ~1/3 of the handcrafted bound — the runbook's 'null here is valuable' branch resolved to 'effect exists, supply-limited; ship the shallow detector meanwhile'. See exp-049 interpretation for the W=30 eval-cell rule.

## Links
Queued for problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Run as [[analog-state-supervision-a1-on-the-12-547-c5f5eb]].
Intended gates: `g1_head_beats_own_null (BLOCKING): the supervised head's momentary-state AUC must beat its OWN 24-permutation within-window shuffle null with perm p < 0.05 on E26's rows (n=3226, 87 subjects, 660 windows). This is the bar E28's arms FAILED (0.5237/0.5325 vs nulls 0.5548/0.5482). Clearing the null is necessary, not sufficient.`, `g2_head_reaches_geometry_bound (BLOCKING, MDE committed BEFORE the run): the head must reach at least AUC 0.60 -- i.e. recover a majority of the +0.11 over-null headroom that raw-on-grid demonstrated (0.6309 vs null 0.5189). If the head clears its null but lands under 0.60, record the verdict as a POWERED NULL against 'a supervised objective recovers the geometry's state content', not as a near-miss. E29 proved the information is reachable from this geometry by a shallow model; a trained head that cannot approach it is a real negative about the encoder/objective, and that is the single most valuable outcome this arm can produce.`, `g3_do_no_harm (BLOCKING): hr_mean subject-grouped readout R2 must not regress below 0.93 (E28 control/intervention both 0.9398/0.9399). Buying state by wrecking the HR axis is not a win -- this is the same reallocation trap that refuted the H4b caption arm.`, `g4_deployment_not_wrecked (BLOCKING): eiv_supabase_hr_raw_mae_bpm on the frozen 200-row draw must not regress beyond the 0.60 bpm seed band vs the v9band04 baseline (2.85 named seed, trivial 4.02), with eiv_dense128_hr_raw_mae_bpm as the companion guard. Anton's ground truth is the production annotation path, not a probe.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
