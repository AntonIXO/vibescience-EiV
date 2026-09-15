---
created_at: '2026-08-14T21:23:54+00:00'
former_ids: []
id: peri-ema-window-destroys-momentary-labels-20260815
paper_refs: []
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
status: resolved
target: null
title: 'The 24h window destroys the momentary EMA labels it is supposed to supervise:
  77.6% of daily LifeSnaps mood windows contain contradicting pings, and peri-ping
  sub-daily alignment has never been tested'
topic_tags:
- wearables
- epoch-resolution
- baseline
- control
updated_at: '2026-08-30T11:46:58+00:00'
updates:
- at: '2026-08-14T21:41:57+00:00'
  text: "[2026-08-15 UNIFICATION — the window question and the state question are
    ONE question about the READOUT, not two questions about the window]\n\nAnton's
    steer: unite the window-size arm and the EMA/state arm. Doing so exposes a shared
    mechanism neither arm could see alone.\n\nTHE REFRAME. A 24h window carrying 1-2
    momentary EMA pings is EXACTLY a multiple-instance-learning bag: 1440 instances,
    a label attached to one of them, and the pipeline answers by MEAN-POOLING the
    whole bag (TemporalPatching -> 23 tokens -> adaptive_avg_pool2d across patches,
    cross_modal.py:260). Mean-pooling a bag is the canonical MIL baseline that attention-MIL
    was invented to beat (Ilse/Tomczak/Welling arXiv:1802.04712; TimeMIL arXiv:2405.03140).\n\nTHE
    SAME OPERATOR EXPLAINS THE OTHER OPEN FAILURE. hr_peak/excursion sits at increment
    ~0.42 while hr_mean reads 0.97, and the registered diagnostic already records
    the pooling operator as the discriminating variable: on 23 patches / 24h / n=4010,
    excursion-target R2 is avg 0.1685, max 0.1273, avg+max concat 0.2968. Averaging
    destroys extrema; averaging also destroys locality. ONE defect, two symptoms.\n\nUNIFIED
    VARIABLE = THE READOUT OPERATOR AT FIXED 24h WINDOW, not the window length. Consistent
    with every rule-out rather than in tension: `ro-window-shortening-20260805` and
    `shortening-the-analysis-window-to-align-with-0f6d56` both killed SHORTENING THE
    WINDOW; neither touched how tokens INSIDE a fixed window are aggregated. Also
    consistent with external evidence — SensorLM (arXiv:2506.09108) and LSM-2 keep
    one-day 1440-minute windows, and Moirai 2.0 (arXiv:2511.11698) dropped multi-patch
    for a single patch size and improved, so 'dynamic window' is the wrong lever while
    'better readout' is untested.\n\nWHAT THE UNIFIED ARM TESTS (exp-e17): one token
    grid, one 24h window, sweep readout operator {avg (incumbent), max, avg+max, LSE}
    x patch grid {23 tokens (incumbent), 144 tokens (LSM-2)}, scoring the SAME representation
    on BOTH targets:\n  TASK A  cross-subject daily excursion -> eiv_peak_excursion_increment_r2\n
    \ TASK B  momentary state at the ping   -> GLOBAL pooled readout vs LOCAL time-indexed
    readout (tokens covering [ping-W, ping]), within-subject walk-forward, against
    clock-only AND the within-subject constant.\nOne operator change is predicted
    to move BOTH. If it moves only A, the state ceiling is confirmed as trait dominance
    and the state slot should be dropped rather than shipped. If it moves neither,
    the bottleneck is downstream in the decode stage where E6-E10 already localised
    the numeric-slot failures.\n\nLITERATURE ANCHORS for the localized readout: TimePerceiver
    (arXiv:2512.22550) decodes with learnable queries corresponding to TARGET TIMESTAMPS
    via cross-attention — a direct template for 'the EMA ping queries its own temporal
    neighbourhood while day-level targets read pooled tokens'. Perceiver IO latent
    queries and the Flamingo Perceiver Resampler are the same mechanism. Multi-scale
    token pyramids: MTST (PMLR v238), HiMTM (arXiv:2401.05012), DRFormer (arXiv:2408.02279).\n\nHONEST
    CAVEAT CARRIED FORWARD: the deep research found NO personalization architecture
    reporting a clean win over a per-user constant at comparable AUC deltas. The 0.7381
    trait ceiling may be a real signal ceiling rather than a readout bug, and the
    unified arm is designed so that outcome is a legible result rather than a null."
- at: '2026-08-14T22:18:08+00:00'
  text: '[2026-08-15 E17 RESULT + a vibescience bug fixed in passing]


    E17 (exp-e17-unified-readout-20260815) tested the unified account. SPLIT OUTCOME:


    TASK A — CONFIRMED AND LARGE. At the incumbent 23-token grid, window fixed at
    1440 min, the ONLY change being the cross-patch pooling operator, the excursion
    increment over the daily-mean control moves avg +0.0090 -> avgmax concat +0.3605
    (gap +0.3515, 7x the preregistered +0.05 bar). max alone +0.0378 and lse alone
    +0.0374 are near-tied and far below avgmax, so the mechanism is LEVEL AND EXTREMUM
    TOGETHER, not the extremum. The production projector uses adaptive_avg_pool2d
    across patches (cross_modal.py:260), i.e. it is sitting on the +0.0090 arm. THIS
    IS THE LEVER.


    TASK A SECONDARY — TOKEN COUNT REFUTED IN REVERSE. 144 tokens (LSM-2 geometry)
    are uniformly WORSE than 23 at every operator (avgmax 0.1470 vs 0.3605; avg -0.0383
    vs +0.0090; max -0.0302 vs +0.0378; lse -0.0311 vs +0.0374). Reads as a variance/dimensionality
    cost at fixed n=4000 subject-grouped, not an information deficit. rb-w3''s LSM-2
    token-count motivation is NOT supported on the CPU information bound and its GPU
    stage should be rescoped from TOKEN COUNT to OPERATOR.


    TASK B — STATE CEILING CONFIRMED BY AN INDEPENDENT ROUTE. Best absolute AUC across
    all 8 (token x operator) arms = 0.6051 vs the within-subject constant 0.7381 —
    0.1330 BELOW a per-user constant using zero physiology and zero clock. E16 hit
    this ceiling with peri-ping summary statistics; E17 hits it with patch-token pooling.
    Two independent feature constructions, same wall. Locality is sign-unstable (local-minus-global
    spans -0.0226 to +0.0376 across 8 arms, four each way, against MDE 0.028), so
    ''let the label read its own neighbourhood'' is not a lever either. E16 had confounded
    ''shorter window'' with ''read locally''; both now measure negative SEPARATELY.


    NET: averaging destroys extrema (huge, actionable) but locality was never the
    state bottleneck. The MIL/attention-pooling framing is right about the OPERATOR
    and wrong about the TARGET.


    TWO INSTRUMENT DEFECTS FOUND AND FIXED:

    1. data/canonical/population_stats.json stores median/iqr as per-channel LISTS,
    not a name-keyed dict. A name lookup silently returns (0.0, 1.0), leaving values
    NORMALISED while the code believes they are bpm — which zeroed the entire Task-A
    pool against a ''mean HR > 20 bpm'' gate. Fixed with scratch/canon_utils.py (channel_denorm
    + an assert_bpm tripwire). Correct values: median 72.833, iqr 23.622. E16 re-verified
    against the same bug: |delta difference| 0.00060435, 46x below E16''s MDE of 0.028,
    because per-feature affine rescaling preserves ranks and hence AUC — E16 STANDS.

    2. vibescience itself: close_experiment''s POWERED NULL message paired the delta
    from the PRIMARY diagnostic with the mde from the first failing blocking gate,
    two unrelated measurements, and emitted ''|D|=0.3515 ... is below the preregistered
    MDE of 0.028'' (12.5x above). compute_verdict() was already correct; only the
    rendered suggestion was wrong. Fixed at vibescience-mcp 9a0d8d3 with tests/test_powered_null_message.py
    reproducing the exact E17 shape; 164/164 tests pass.


    CAVEAT ON THE E17 VERDICT FIELD: it reads ''null'' because I preregistered gB
    (a Task-B gate) as BLOCKING on a hypothesis whose primary predicted effect was
    Task-A. Task B failing therefore demoted the whole experiment. The verdict is
    correct under the rules I committed to; the SUBSTANTIVE finding is a large Task-A
    confirmation plus a Task-B null, and the two should be read separately. Future
    multi-task hypotheses on this vault should be split into one hypothesis per target
    rather than bundled.'
- at: '2026-08-14T23:49:03+00:00'
  text: "[2026-08-15 CLOSING SUMMARY — the state axis is settled; the window axis
    resolved into a pooling finding]\n\nAll five hypotheses opened under this problem
    line are now closed in vibescience.\n\nSTATE DETECTION IS DEAD ON THIS CORPUS
    — three independent measurements, both grouping regimes:\n  E16 (cross-subject,
    peri-ping window sweep): delta over clock RISES with W, peaks at 24h; per-user
    constant AUC 0.7381 vs best physiological 0.5818. REFUTES.\n  E17 (cross-subject,
    readout operator x token count, GLOBAL vs LOCAL): best absolute 0.6051, still
    0.1330 BELOW the per-user constant; locality sign-unstable across 8 arms. Task
    B NULL.\n  E18 (WITHIN-subject, per-user walk-forward vs each subject's own trivials):
    best median skill +0.0037 (CI spans zero, 13x under MDE), best frac_positive 0.526
    vs 0.60 bar, 4 of 12 arms with CI ENTIRELY BELOW ZERO. POWERED NULL.\n\nThe N-of-1
    escape hatch — the one route E16/E17 explicitly left open — is now closed by measurement.
    Adding peri-ping HR/HRV makes ~2/3 of subjects WORSE than their own base rate.\n\nMY
    PREDICTED W-SHAPE WAS WRONG TWICE, IN THE SAME DIRECTION, AND THAT IS THE REAL
    FINDING. I predicted an interior optimum at short W cross-subject (E16) and a
    flattening/short-favouring profile within-subject (E18). Both times skill improved
    MONOTONICALLY with LONGER windows. Short peri-ping windows are the WORST arms
    in every regime tested. Whatever HR contributes to affect here is a slow day-scale
    quantity, not an acute autonomic response — which is the opposite of the mechanism
    the whole sub-daily line was built on.\n\nRECOMMENDATION: drop `state` from the
    emitted schema, or mark it explicitly non-sensor-derived. Shipping a slot whose
    content is worse than the user's own base rate is worse than shipping nothing.
    This also retires 'PerUserHeadStore state head' as the surviving product lever
    named in the E14 closure.\n\nWHAT THE WINDOW QUESTION TURNED INTO. Anton's original
    question was 1h/6h/24h/dynamic. The answer is: KEEP 24h, and the real variable
    was never the window — it was the AGGREGATION INSIDE it.\n  E19 (real trained
    encoder tokens): avg +0.2807 -> avgmax +0.6849, gap +0.4042, 8x the bar. Dimensionality
    confound controlled (max alone +0.6771 at identical dim). SUPPORTS.\n  E19b (5
    checkpoints + shuffle control): gap reproduces on EVERY checkpoint, min +0.3963
    max +0.4420, BAND 0.0457 — 8.9x smaller than the effect. Shuffle controls -0.0001..+0.0018,
    so it is not a high-dimensional readout artifact. SUPPORTS.\n\nSo `cross_modal.py:260`'s
    adaptive_avg_pool2d is discarding ~0.40 R2 of excursion the encoder has ALREADY
    computed, and that is a recipe-level property, not a basin draw. Live arm: rb-w4-projector-maxpool-end-to-end-20260815
    (priority 5), which must measure end-to-end on deployment metrics across >=3 seeds
    — a linear probe showing the information survives is NOT evidence the frozen LM
    will use it (the exp-e31 distinction).\n\nSTATUS: this problem stays OPEN only
    because the emitted-schema decision (drop the state slot) has not been executed.
    The scientific question it was opened to answer is settled."
- at: '2026-08-15T07:33:03+00:00'
  text: "[2026-08-15 RETRACTION OF AN OVER-CLAIM — 'state detection is dead on this
    corpus' is NOT supported by what I measured]\n\nAnton challenged the basis of
    the state refutation. He is right and I am retracting the scope of my own closing
    summary.\n\nWHAT I ACTUALLY MEASURED (E16/E17/E18): whether affect is decodable
    from ~17 HAND-CRAFTED SUMMARY SCALARS (mean, std, p10/p25/p50/p75/p90, min, max,
    IQR, coverage, n, OLS slope, last-minus-first, last value, hrv mean/std/coverage)
    by RandomForest or logistic regression.\n\nWHAT I WROTE: 'state detection is dead
    on this corpus at any grouping regime, any window, any readout — drop the state
    slot from the emitted schema.'\n\nThose are not the same claim. The production
    system is a PatchTST-style temporal encoder (learned linear patch map + attention
    across patches) followed by Phase B TRIPLET METRIC LEARNING ON THE STATE AXIS.
    A percentile vector destroys temporal shape, ordering, multi-scale structure and
    cross-channel timing by construction. My probe cannot bound that function class.\n\nTHIS
    VAULT ALREADY CONTAINS TWO COUNTEREXAMPLES TO THE ASSUMPTION I RELIED ON:\n  *
    h4b ladder, same target/pool: exactly the six caption scalars (RF) +0.0706 vs
    DEEP encoder->projector->soft tokens +0.4305 — a 6x gap, and its own note records
    that the 'a shallow model on the caption scalars will match the stack' worry is
    REFUTED.\n  * exp-e19-encoder-pooling-20260815, measured hours earlier in this
    same session: raw-patch avg pooling +0.0090 vs TRAINED ENCODER tokens under the
    identical operator +0.2807 — a 30x gap.\nThe rule-out affect-multichannel-supply-dead-20260814
    states the opposite assumption explicitly ('shallow-at-chance bounds deep on daily
    summaries'). On structural targets that assumption is contradicted by measurements
    in this vault. A shallow null means NO CHEAP WIN, not NO SIGNAL.\n\nTHE TRIPLET
    POINT IS SHARPER STILL. Problem phase-b-triplet-orthogonalization-is-f6d3c5 records
    that Phase B triplets are near-vacuous because 99.1% of training samples carry
    a single state label and every non-NONE label is a 100%-pure dataset identifier.
    That degeneracy is a property of the OLD label supply. Commit a7fc419 put ground
    truth on per-epoch momentary timestamps for the first time. So the one component
    built to separate states has NEVER been trained on labels capable of separating
    states, and none of E16/E17/E18 touches that.\n\nWHAT SURVIVES UNCHANGED (model-class
    independent):\n  * the clock-only control — any model gets hour-of-day for free,
    so it must be subtracted whatever the architecture\n  * within-window label disagreement
    0.776 at W=1440 vs 0.000 at W<=60 — a supervision fact about the data\n  * the
    label-supply census (four cohorts invisible at 24h)\n\nWHAT IS RETRACTED:\n  *
    'state detection is dead on this corpus' -> correct scope is 'SHALLOW probes on
    SUMMARY features of HR/HRV, on IN-THE-WILD EMA (lifesnaps/dryad), under ONE binarization,
    found no win over per-user trivials'\n  * the recommendation to DROP the state
    slot from the emitted schema — WITHDRAWN pending the encoder-representation test\n
    \ * the 0.7381 'per-user constant' framing — that is a CROSS-SUBJECT POOLED AUC,
    high because subjects with high base rates outrank subjects with low ones. Within
    one user a constant is AUC 0.5 by construction. It measures between-subject trait
    dominance, NOT a deployment bar. E18 is the only arm that speaks to deployment.\n\nTHREE
    ARMS NOW REGISTERED TO CLOSE THE GAP: E22 (probe the TRAINED ENCODER tokens for
    affect, exactly as E19 did for excursion), E23 (synthetic-effect power check —
    my MDE was asserted by analogy, never measured), E24 (the four lab-protocol cohorts
    with dense induced-stress labels that have ZERO usable windows at 24h and were
    never tested)."
- at: '2026-08-15T08:37:00+00:00'
  text: "[2026-08-15 CORRECTION TO E26's SHUFFLE CONTROL + E27 NO-GO + detector shipped]\n\nTHREE
    THINGS LANDED AFTER E26, and one of them corrects E26 itself.\n\n1. E26's SHUFFLE
    CONTROL WAS A SINGLE DRAW, AND ITS VALUE WAS LUCKY.\nE26 reported a within-window
    shuffle of 0.5151 and I read that as 'dead, ~0.50'. Re-running the identical protocol
    with a different permutation seed gave 0.5594. Neither is wrong; both are single
    samples. Measured properly over 40 permutations (scratch/state_detector_shuffle_null.py
    -> logs/state_detector_shuffle_null.json):\n    null mean 0.5492, std 0.0246,
    min 0.4793, max 0.6053, p95 0.5847\nSo the within-window shuffle null is NOT centred
    on 0.50 — it sits at ~0.549. There is a small structural floor, most plausibly
    because permuting labels within a window preserves the window's own feature distribution
    and the grouped CV can still exploit window-level structure.\nTHE HEADLINE SURVIVES
    AND IS NOW PROPERLY PRICED: real 0.6329 vs null mean 0.5492 = 3.41 SD above, permutation
    p = 0.0000 over 40 draws. E26's conclusion stands; its stated margin (+0.1178
    over 0.5151) was overstated and the correct margin is +0.0837 over the null MEAN.\nRULE
    FOR THIS VAULT GOING FORWARD: a shuffle control quoted from ONE draw is not a
    control. Report the null distribution (mean/std/p95) and the z or permutation
    p, never a single permuted score against an assumed 0.50.\n\n2. E27 (exp-e27-encoder-transitions-20260815)
    = NO-GO on the Phase B retrain.\nOn E26's exact rows, every frozen-encoder arm
    is BELOW the shallow percentile baseline: enc_global_avgmax 0.5876, enc_global_avg
    0.5728, enc_local_avgmax 0.5254, enc_local_avg 0.5026, against shallow 0.6329.
    Gap -0.0453 on the best.\nAND the shuffle exposed the encoder arm as mostly artefact:
    enc_global_avgmax scores 0.5830 on shuffled labels vs 0.5876 real — a gap of 0.0046.
    Destroying the state contrast costs it nothing. With 4608-9216 features against
    3248 rows this is capacity, not state.\nThis also makes E22's g3 'locality confirmed
    at the representation level' (+0.054 to +0.066 on daily labels) UNVERIFIED: it
    was measured without a shuffle control on the same features, and here locality
    REVERSES (local worse than global by -0.062 to -0.070) in exactly the way a capacity
    artefact would.\nDECISION, per the rule committed before the run: do NOT spend
    ~4 GPU-hours on the Phase B retrain on representational grounds. The retrain retains
    only the SUPPLY justification (E25 non-degenerate triplets; E26 real contrast
    the miner discards).\n\n3. DETECTOR SHIPPED (option 3, Anton's direction).\nsrc/annotation/momentary_state.py
    + outputs/annotation/momentary_state_detector.joblib. flow-vs-stress, W=120, HR/HRV
    only, no clock features (E26 measured hr+clock 0.5786 < hr alone 0.6329). The
    trainer RE-SCORES with E26's protocol and refuses to write the artefact unless
    the AUC reproduces within 0.02 — it reproduced EXACTLY at 0.6329. n_train 3248,
    87 subjects, 662 windows. Flagged provisional=True because optihealth_supabase
    still carries ZERO state labels, so this ships as a population model, not a per-user
    head.\n\nSTANDING ENGINEERING DEFECT, UNCHANGED: TripletStateDataset (src/training/datasets.py:2149)
    mines on infer_state_label, one majority-collapsed label per window, so all 1274
    transition windows lose their internal contrast before Phase B sees it. E26 shows
    that contrast is real (3.41 SD above its own null). Fixing the miner is the remaining
    supply-side lever."
- at: '2026-08-30T11:46:58+00:00'
  text: '2026-08-30: resolved per doctor(). 6 confirmed hypotheses, no live work.
    The remaining actionable residue moved into code and the collapse problem: mask_unlabelled_state
    fix landed (commit f1b15e9, exp-v11statemask measured it as hygiene-not-lever),
    and window admission/pool questions continue under new-corpus-12-547 problem.'
---

## Description
[2026-08-15, opened after the canonical rebuild at git_sha e849eb7, tensor [11318,1440,12]]

THE NEW FACT. Commit a7fc419 ("place ground truth on the epoch it was recorded in") plus 052088f (dryad night-summaries off the minute grid) and e849eb7 (local-midnight anchoring) made the EMA labels genuinely MOMENTARY for the first time. Verified in the landed data, not inferred from the commit message (scratch/ema_time_reality_check.py):
  - epoch mod 60 == 0 on 0.019 of lifesnaps mood events (chance 0.0167) -> not an hourly broadcast
  - top single epoch carries 0.004 of events -> not a hardcoded stamp
  - minute-of-hour histogram flat (min 55, max 101, mean 73.6, std 11.0 over 60 bins)
  - hour-of-day peaks at 10:00 / 15:00 / 20:00 = the LifeSnaps SEMA3 ping schedule
  - dryad stress_raw runs 19:00-02:00; nurse_stress peaks 14:00-18:00; wearable_exam_stress 13:00-19:00
  - pmdata is the exception: 07-09h clustered, top-epoch share 0.060, mod60 0.077 -> a MORNING DIARY, retrospective, NOT momentary. It must not be pooled with the momentary sources.

THE DEFECT. The tensor is one window per civil day, 1440 minutes. The label is a point event. Measured on the live corpus (scratch/w1_subdaily_supply.py -> outputs/w1_subdaily_supply.csv):

  within-window label DISAGREEMENT, lifesnaps mood_code:
    W=30    0.000     W=60   0.000     W=120  0.375     W=180  0.657
    W=360   0.596     W=720  0.714     W=1440 0.776
  i.e. at the shipped 24h window, 1484 multi-ping windows exist and 77.6% of them
  contain at least two DIFFERENT mood states, which infer_state_label() then
  collapses by majority vote. src/training/datasets.py:144 already says this in a
  docstring ("78% of multi-EMA LifeSnaps days carry a mood that changes within the
  day") but nothing downstream acts on it.

  usable label-windows (HR coverage >= 0.5 inside the window):
    W=30 9732 | W=60 9342 | W=360 10412 | W=720 10033 | W=1440 6775
  The 24h grid LOSES 35% of usable labelled windows relative to W=360, because a
  short-session stress dataset's 30 measured minutes are diluted below the gate by
  1410 empty ones. Per dataset at W=1440 vs W=30: wearable_exam_stress 0 vs 242,
  wesad 0 vs 63, neuro_stress_resilience_hci 0 vs 41, nurse_stress 0 vs 105.
  Four labelled cohorts are structurally invisible at the daily grid and become
  usable sub-daily.

WHY THIS IS NOT A RETRY. Two rule-outs sit near here and BOTH are scoped away:
  * ro-window-shortening-20260805 killed window shortening FOR hr_peak, because a
    max converges to the mean in a short window (increment fell to exactly 0.000).
    That is an order-statistic argument. A mood ping is a point event and has no
    order statistic to converge, so the mechanism does not transfer.
  * affect-multichannel-supply-dead-20260814 ruled out daily-summary multichannel
    affect supply. Its OWN scope note excludes "(c) sub-daily/event-level affect
    (the labels are daily self-reports; nothing sub-daily is testable)". That
    parenthetical premise is now measurably FALSE. The residual it explicitly left
    open is testable for the first time.

THE CONFOUND THAT MUST GATE ANY CLAIM HERE. Mood at 20:00 differs from mood at
10:00 and hour-of-day is free, needing no physiology. Any peri-ping window arm must
beat a CLOCK-ONLY control on the identical split, exactly as any peak claim must
beat R2(peak | true daily mean). Without that control a short window will post a
higher AUC for pure circadian bookkeeping.

STATUS: open. Blocks the window-size decision (1h / 6h / 24h / dynamic) and the
state-slot design. Deployment note: optihealth_supabase still carries ZERO state
labels, so the product-side N-of-1 state head remains supply-blocked regardless of
how this resolves; this problem is about whether the corpus can teach the encoder
momentary state at all.

## Synthesis
STATE: the premise was CONFIRMED as a data fact and the proposed FIX was
refuted; what survives is a supply verdict plus one localisation result whose
headline is now known to be READOUT-DEPENDENT (exp-046 + exp-e34, 2026-08-18).

WHAT WE NOW BELIEVE.
- The labels really are momentary now (commits a7fc419 / 052088f / e849eb7,
  verified in the landed data, not inferred): epoch mod 60 == 0 on 0.019 of
  lifesnaps mood events against 0.0167 chance, top single epoch 0.004, flat
  minute-of-hour histogram, hour-of-day peaks at the SEMA3 ping schedule
  (10:00/15:00/20:00).
- The 24h window really does destroy them: within-window disagreement rises
  0.000 (W=30/60) -> 0.375 (W=120) -> 0.657 (W=180) -> 0.776 (W=1440), with
  1484 multi-ping windows at the shipped width.
- AND SHORTENING THE WINDOW DOES NOT HELP. exp-e16 refuted it directly
  (0.0228 -> 0.0167). The defect is real; the obvious fix is not the fix.
- pmdata is a MORNING DIARY (07-09h clustered, top-epoch share 0.060, mod60
  0.077), retrospective and NOT momentary. It must never be pooled with the
  momentary sources.

WHAT IS RULED OUT, AND HOW HARD.
- Cross-subject multichannel affect supply is DEAD: exp-e5 measured delta
  -0.0278 against a >= +0.03 bar, perm p = 0.84, and decisively the HR-ONLY arm
  is itself at chance (RF AUC 0.4816, linear 0.5329, chance 0.50) with no
  composition exceeding 0.5183.
- Within-subject is ALSO dead, and this one is a POWERED null: exp-e14 pooled
  lifesnaps delta +0.0003, perm p = 0.6471 (50 perms, null mean +0.0048, std
  0.0168), linear check NEGATIVE. Its g0 power precondition PASSED — MDE ~0.028
  < the preregistered 0.05 bar — so an effect of the claimed size would have
  been seen.
- The design itself cannot recover much: exp-e23's synthetic-control power check
  planted a known effect in E18's EXACT design and recovered -0.0375 at a true
  oracle AUC of 0.589 and only +0.0741 at 0.741.
- The encoder does NOT carry ping-specific affect: ro-encoder-affect-is-time-of-
  day-20260815 retracted the earlier "locality confirmed" reading of E22.
- JEPA / latent-prediction as a Phase-A replacement is ruled out AS A MOMENTARY-
  STATE FIX (ro `that-a-jepa-latent-prediction-pretraining-7061c5`), along with
  the DIMENSIONALITY and PRODUCTION-STAGE accounts of E29's encoder deficit.
- The FUNCTION-CLASS account is ALSO ruled out (ro `the-function-class-account-
  of-exp-046-that-8-511840`): the encoder's tokens CAN linearly reconstruct
  their own input-patch order statistics (min/max/std mean R2 0.5182 against a
  preregistered <0.50 falsifier), so patch-construction surgery is unjustified.

WHAT SURVIVES AND IS WORTH BUILDING ON.
- exp-e24 lab cohorts: 0.564 -> 0.8497 (supports). Where the labels are dense
  and lab-grade, the signal is there — the deficit is SUPPLY in the field
  cohorts, not architecture.
- exp-e26 within-window transitions 0.5129 -> 0.6329 (supports). The transition
  design remains the only clean instrument on this problem.

THE CENTRAL METHODOLOGICAL FINDING OF 2026-08-18, AND IT COST TWO OF MY OWN
CLAIMS. E29's headline ("patch geometry innocent, learned weights guilty") was
challenged on two confounds; both challenges died (exp-046), which PROMOTED the
finding. Then a third probe (exp-e34) showed the whole comparison is an artifact
of the ESTIMATOR. All arms, identical rows (n=3226 / 87 subjects / 660 windows /
W=120), identical folds, each vs its OWN within-window shuffle null:

    arm                readout    AUC     null            over_null    z
    raw_patch_grid_8   RF      0.6309  0.5189+-0.0422     +0.1120   +2.66
    raw_patch_grid_8   LINEAR  0.5855  0.5711+-0.0130     +0.0143   +1.10
    enc_pca8           RF      0.4950  0.5442+-0.0293     -0.0492   -1.68
    enc_pca8           LINEAR  0.5715  0.5310+-0.0143     +0.0405   +2.83
    rand_pca8 (RANDOM) RF      0.5442  0.5464+-0.0290     -0.0022   -0.08
    prod_pca8          RF      0.4747  0.5554+-0.0262     -0.0807   -3.08
    enc_full (9216)    RF      0.5202  0.5495+-0.0351     -0.0293   -0.84

Under a matched LINEAR readout the ordering INVERTS: the trained encoder clears
its null (z+2.83) and the handcrafted features do not (z+1.10). The nulls
themselves move with the readout (RF 0.5189/0.5442 vs linear 0.5711/0.5310). So
"the trained encoder does not carry momentary state" is a statement about
(representation x ESTIMATOR) and must never be quoted unqualified again. The
diagnostic eiv_momentary_state_auc_over_null now MANDATES naming the estimator,
recomputing the null per readout, and reporting >= 2 readout families.

THREE FURTHER FACTS FROM THOSE RUNS, EACH KILLING A PLAUSIBLE STORY:
- DIMENSIONALITY is not the explanation: 9216 -> PCA-8 moved the trained arm
  DOWN (-0.0293 -> -0.0492) against a preregistered >= +0.05 recovery bar.
- STAGE is not the explanation, and it INVERTS: prod_pca8 is the worst arm
  measured. The 4.3x production-over-encoder advantage that
  ro-dual-avg-max-pooling-downstream-20260805 measured for hr_peak does NOT
  transport to a momentary target — prototype cross-attention plus a unit-RMS
  tail concentrates daily-aggregate structure and erases a 2-hour contrast.
  GENERAL RULE EARNED: evidence is bound to its TARGET as well as its substrate.
- RECONSTRUCTION is not evidence about training: random-init reconstructs the 8
  features as well as trained (0.6177 vs 0.5924), because the 120->192 patch
  projection is over-complete by construction.

HONEST RESIDUAL: under RF a random-init encoder is inert (-0.0022, z-0.08),
which is what makes "a new pretraining objective has no headroom" persuasive.
That arm was never measured under a LINEAR readout, so the argument is formally
RF-only. Anyone reopening JEPA-for-state should spend the ~10 CPU minutes to
measure it before spending GPU.

CONSEQUENCE FOR THE PROJECT. Four independent instruments (cross-subject,
within-subject, power check, encoder probe) agree there is no daily-summary
affect signal to fuse in the field cohorts, and further fusion-architecture work
on THIS supply should not be funded without new labels. On the transition thread,
the encoder's momentary-state signal is WEAK under every readout tried (best
+0.0405, z+2.83) and no arm has reached the raw_shallow bound of 0.6430. The one
lever never tried remains a SUPERVISED head trained directly on the momentary
label (rb-supervised-state-head-20260815, priority 3, supply 77081 epochs / 281
subjects) — and its preregistration must now state the weaker, honest premise and
score under two readout families.

## Updates
- **2026-08-14T21:41:57+00:00** — [2026-08-15 UNIFICATION — the window question and the state question are ONE question about the READOUT, not two questions about the window]

Anton's steer: unite the window-size arm and the EMA/state arm. Doing so exposes a shared mechanism neither arm could see alone.

THE REFRAME. A 24h window carrying 1-2 momentary EMA pings is EXACTLY a multiple-instance-learning bag: 1440 instances, a label attached to one of them, and the pipeline answers by MEAN-POOLING the whole bag (TemporalPatching -> 23 tokens -> adaptive_avg_pool2d across patches, cross_modal.py:260). Mean-pooling a bag is the canonical MIL baseline that attention-MIL was invented to beat (Ilse/Tomczak/Welling arXiv:1802.04712; TimeMIL arXiv:2405.03140).

THE SAME OPERATOR EXPLAINS THE OTHER OPEN FAILURE. hr_peak/excursion sits at increment ~0.42 while hr_mean reads 0.97, and the registered diagnostic already records the pooling operator as the discriminating variable: on 23 patches / 24h / n=4010, excursion-target R2 is avg 0.1685, max 0.1273, avg+max concat 0.2968. Averaging destroys extrema; averaging also destroys locality. ONE defect, two symptoms.

UNIFIED VARIABLE = THE READOUT OPERATOR AT FIXED 24h WINDOW, not the window length. Consistent with every rule-out rather than in tension: `ro-window-shortening-20260805` and `shortening-the-analysis-window-to-align-with-0f6d56` both killed SHORTENING THE WINDOW; neither touched how tokens INSIDE a fixed window are aggregated. Also consistent with external evidence — SensorLM (arXiv:2506.09108) and LSM-2 keep one-day 1440-minute windows, and Moirai 2.0 (arXiv:2511.11698) dropped multi-patch for a single patch size and improved, so 'dynamic window' is the wrong lever while 'better readout' is untested.

WHAT THE UNIFIED ARM TESTS (exp-e17): one token grid, one 24h window, sweep readout operator {avg (incumbent), max, avg+max, LSE} x patch grid {23 tokens (incumbent), 144 tokens (LSM-2)}, scoring the SAME representation on BOTH targets:
  TASK A  cross-subject daily excursion -> eiv_peak_excursion_increment_r2
  TASK B  momentary state at the ping   -> GLOBAL pooled readout vs LOCAL time-indexed readout (tokens covering [ping-W, ping]), within-subject walk-forward, against clock-only AND the within-subject constant.
One operator change is predicted to move BOTH. If it moves only A, the state ceiling is confirmed as trait dominance and the state slot should be dropped rather than shipped. If it moves neither, the bottleneck is downstream in the decode stage where E6-E10 already localised the numeric-slot failures.

LITERATURE ANCHORS for the localized readout: TimePerceiver (arXiv:2512.22550) decodes with learnable queries corresponding to TARGET TIMESTAMPS via cross-attention — a direct template for 'the EMA ping queries its own temporal neighbourhood while day-level targets read pooled tokens'. Perceiver IO latent queries and the Flamingo Perceiver Resampler are the same mechanism. Multi-scale token pyramids: MTST (PMLR v238), HiMTM (arXiv:2401.05012), DRFormer (arXiv:2408.02279).

HONEST CAVEAT CARRIED FORWARD: the deep research found NO personalization architecture reporting a clean win over a per-user constant at comparable AUC deltas. The 0.7381 trait ceiling may be a real signal ceiling rather than a readout bug, and the unified arm is designed so that outcome is a legible result rather than a null.
- **2026-08-14T22:18:08+00:00** — [2026-08-15 E17 RESULT + a vibescience bug fixed in passing]

E17 (exp-e17-unified-readout-20260815) tested the unified account. SPLIT OUTCOME:

TASK A — CONFIRMED AND LARGE. At the incumbent 23-token grid, window fixed at 1440 min, the ONLY change being the cross-patch pooling operator, the excursion increment over the daily-mean control moves avg +0.0090 -> avgmax concat +0.3605 (gap +0.3515, 7x the preregistered +0.05 bar). max alone +0.0378 and lse alone +0.0374 are near-tied and far below avgmax, so the mechanism is LEVEL AND EXTREMUM TOGETHER, not the extremum. The production projector uses adaptive_avg_pool2d across patches (cross_modal.py:260), i.e. it is sitting on the +0.0090 arm. THIS IS THE LEVER.

TASK A SECONDARY — TOKEN COUNT REFUTED IN REVERSE. 144 tokens (LSM-2 geometry) are uniformly WORSE than 23 at every operator (avgmax 0.1470 vs 0.3605; avg -0.0383 vs +0.0090; max -0.0302 vs +0.0378; lse -0.0311 vs +0.0374). Reads as a variance/dimensionality cost at fixed n=4000 subject-grouped, not an information deficit. rb-w3's LSM-2 token-count motivation is NOT supported on the CPU information bound and its GPU stage should be rescoped from TOKEN COUNT to OPERATOR.

TASK B — STATE CEILING CONFIRMED BY AN INDEPENDENT ROUTE. Best absolute AUC across all 8 (token x operator) arms = 0.6051 vs the within-subject constant 0.7381 — 0.1330 BELOW a per-user constant using zero physiology and zero clock. E16 hit this ceiling with peri-ping summary statistics; E17 hits it with patch-token pooling. Two independent feature constructions, same wall. Locality is sign-unstable (local-minus-global spans -0.0226 to +0.0376 across 8 arms, four each way, against MDE 0.028), so 'let the label read its own neighbourhood' is not a lever either. E16 had confounded 'shorter window' with 'read locally'; both now measure negative SEPARATELY.

NET: averaging destroys extrema (huge, actionable) but locality was never the state bottleneck. The MIL/attention-pooling framing is right about the OPERATOR and wrong about the TARGET.

TWO INSTRUMENT DEFECTS FOUND AND FIXED:
1. data/canonical/population_stats.json stores median/iqr as per-channel LISTS, not a name-keyed dict. A name lookup silently returns (0.0, 1.0), leaving values NORMALISED while the code believes they are bpm — which zeroed the entire Task-A pool against a 'mean HR > 20 bpm' gate. Fixed with scratch/canon_utils.py (channel_denorm + an assert_bpm tripwire). Correct values: median 72.833, iqr 23.622. E16 re-verified against the same bug: |delta difference| 0.00060435, 46x below E16's MDE of 0.028, because per-feature affine rescaling preserves ranks and hence AUC — E16 STANDS.
2. vibescience itself: close_experiment's POWERED NULL message paired the delta from the PRIMARY diagnostic with the mde from the first failing blocking gate, two unrelated measurements, and emitted '|D|=0.3515 ... is below the preregistered MDE of 0.028' (12.5x above). compute_verdict() was already correct; only the rendered suggestion was wrong. Fixed at vibescience-mcp 9a0d8d3 with tests/test_powered_null_message.py reproducing the exact E17 shape; 164/164 tests pass.

CAVEAT ON THE E17 VERDICT FIELD: it reads 'null' because I preregistered gB (a Task-B gate) as BLOCKING on a hypothesis whose primary predicted effect was Task-A. Task B failing therefore demoted the whole experiment. The verdict is correct under the rules I committed to; the SUBSTANTIVE finding is a large Task-A confirmation plus a Task-B null, and the two should be read separately. Future multi-task hypotheses on this vault should be split into one hypothesis per target rather than bundled.
- **2026-08-14T23:49:03+00:00** — [2026-08-15 CLOSING SUMMARY — the state axis is settled; the window axis resolved into a pooling finding]

All five hypotheses opened under this problem line are now closed in vibescience.

STATE DETECTION IS DEAD ON THIS CORPUS — three independent measurements, both grouping regimes:
  E16 (cross-subject, peri-ping window sweep): delta over clock RISES with W, peaks at 24h; per-user constant AUC 0.7381 vs best physiological 0.5818. REFUTES.
  E17 (cross-subject, readout operator x token count, GLOBAL vs LOCAL): best absolute 0.6051, still 0.1330 BELOW the per-user constant; locality sign-unstable across 8 arms. Task B NULL.
  E18 (WITHIN-subject, per-user walk-forward vs each subject's own trivials): best median skill +0.0037 (CI spans zero, 13x under MDE), best frac_positive 0.526 vs 0.60 bar, 4 of 12 arms with CI ENTIRELY BELOW ZERO. POWERED NULL.

The N-of-1 escape hatch — the one route E16/E17 explicitly left open — is now closed by measurement. Adding peri-ping HR/HRV makes ~2/3 of subjects WORSE than their own base rate.

MY PREDICTED W-SHAPE WAS WRONG TWICE, IN THE SAME DIRECTION, AND THAT IS THE REAL FINDING. I predicted an interior optimum at short W cross-subject (E16) and a flattening/short-favouring profile within-subject (E18). Both times skill improved MONOTONICALLY with LONGER windows. Short peri-ping windows are the WORST arms in every regime tested. Whatever HR contributes to affect here is a slow day-scale quantity, not an acute autonomic response — which is the opposite of the mechanism the whole sub-daily line was built on.

RECOMMENDATION: drop `state` from the emitted schema, or mark it explicitly non-sensor-derived. Shipping a slot whose content is worse than the user's own base rate is worse than shipping nothing. This also retires 'PerUserHeadStore state head' as the surviving product lever named in the E14 closure.

WHAT THE WINDOW QUESTION TURNED INTO. Anton's original question was 1h/6h/24h/dynamic. The answer is: KEEP 24h, and the real variable was never the window — it was the AGGREGATION INSIDE it.
  E19 (real trained encoder tokens): avg +0.2807 -> avgmax +0.6849, gap +0.4042, 8x the bar. Dimensionality confound controlled (max alone +0.6771 at identical dim). SUPPORTS.
  E19b (5 checkpoints + shuffle control): gap reproduces on EVERY checkpoint, min +0.3963 max +0.4420, BAND 0.0457 — 8.9x smaller than the effect. Shuffle controls -0.0001..+0.0018, so it is not a high-dimensional readout artifact. SUPPORTS.

So `cross_modal.py:260`'s adaptive_avg_pool2d is discarding ~0.40 R2 of excursion the encoder has ALREADY computed, and that is a recipe-level property, not a basin draw. Live arm: rb-w4-projector-maxpool-end-to-end-20260815 (priority 5), which must measure end-to-end on deployment metrics across >=3 seeds — a linear probe showing the information survives is NOT evidence the frozen LM will use it (the exp-e31 distinction).

STATUS: this problem stays OPEN only because the emitted-schema decision (drop the state slot) has not been executed. The scientific question it was opened to answer is settled.
- **2026-08-15T07:33:03+00:00** — [2026-08-15 RETRACTION OF AN OVER-CLAIM — 'state detection is dead on this corpus' is NOT supported by what I measured]

Anton challenged the basis of the state refutation. He is right and I am retracting the scope of my own closing summary.

WHAT I ACTUALLY MEASURED (E16/E17/E18): whether affect is decodable from ~17 HAND-CRAFTED SUMMARY SCALARS (mean, std, p10/p25/p50/p75/p90, min, max, IQR, coverage, n, OLS slope, last-minus-first, last value, hrv mean/std/coverage) by RandomForest or logistic regression.

WHAT I WROTE: 'state detection is dead on this corpus at any grouping regime, any window, any readout — drop the state slot from the emitted schema.'

Those are not the same claim. The production system is a PatchTST-style temporal encoder (learned linear patch map + attention across patches) followed by Phase B TRIPLET METRIC LEARNING ON THE STATE AXIS. A percentile vector destroys temporal shape, ordering, multi-scale structure and cross-channel timing by construction. My probe cannot bound that function class.

THIS VAULT ALREADY CONTAINS TWO COUNTEREXAMPLES TO THE ASSUMPTION I RELIED ON:
  * h4b ladder, same target/pool: exactly the six caption scalars (RF) +0.0706 vs DEEP encoder->projector->soft tokens +0.4305 — a 6x gap, and its own note records that the 'a shallow model on the caption scalars will match the stack' worry is REFUTED.
  * exp-e19-encoder-pooling-20260815, measured hours earlier in this same session: raw-patch avg pooling +0.0090 vs TRAINED ENCODER tokens under the identical operator +0.2807 — a 30x gap.
The rule-out affect-multichannel-supply-dead-20260814 states the opposite assumption explicitly ('shallow-at-chance bounds deep on daily summaries'). On structural targets that assumption is contradicted by measurements in this vault. A shallow null means NO CHEAP WIN, not NO SIGNAL.

THE TRIPLET POINT IS SHARPER STILL. Problem phase-b-triplet-orthogonalization-is-f6d3c5 records that Phase B triplets are near-vacuous because 99.1% of training samples carry a single state label and every non-NONE label is a 100%-pure dataset identifier. That degeneracy is a property of the OLD label supply. Commit a7fc419 put ground truth on per-epoch momentary timestamps for the first time. So the one component built to separate states has NEVER been trained on labels capable of separating states, and none of E16/E17/E18 touches that.

WHAT SURVIVES UNCHANGED (model-class independent):
  * the clock-only control — any model gets hour-of-day for free, so it must be subtracted whatever the architecture
  * within-window label disagreement 0.776 at W=1440 vs 0.000 at W<=60 — a supervision fact about the data
  * the label-supply census (four cohorts invisible at 24h)

WHAT IS RETRACTED:
  * 'state detection is dead on this corpus' -> correct scope is 'SHALLOW probes on SUMMARY features of HR/HRV, on IN-THE-WILD EMA (lifesnaps/dryad), under ONE binarization, found no win over per-user trivials'
  * the recommendation to DROP the state slot from the emitted schema — WITHDRAWN pending the encoder-representation test
  * the 0.7381 'per-user constant' framing — that is a CROSS-SUBJECT POOLED AUC, high because subjects with high base rates outrank subjects with low ones. Within one user a constant is AUC 0.5 by construction. It measures between-subject trait dominance, NOT a deployment bar. E18 is the only arm that speaks to deployment.

THREE ARMS NOW REGISTERED TO CLOSE THE GAP: E22 (probe the TRAINED ENCODER tokens for affect, exactly as E19 did for excursion), E23 (synthetic-effect power check — my MDE was asserted by analogy, never measured), E24 (the four lab-protocol cohorts with dense induced-stress labels that have ZERO usable windows at 24h and were never tested).
- **2026-08-15T08:37:00+00:00** — [2026-08-15 CORRECTION TO E26's SHUFFLE CONTROL + E27 NO-GO + detector shipped]

THREE THINGS LANDED AFTER E26, and one of them corrects E26 itself.

1. E26's SHUFFLE CONTROL WAS A SINGLE DRAW, AND ITS VALUE WAS LUCKY.
E26 reported a within-window shuffle of 0.5151 and I read that as 'dead, ~0.50'. Re-running the identical protocol with a different permutation seed gave 0.5594. Neither is wrong; both are single samples. Measured properly over 40 permutations (scratch/state_detector_shuffle_null.py -> logs/state_detector_shuffle_null.json):
    null mean 0.5492, std 0.0246, min 0.4793, max 0.6053, p95 0.5847
So the within-window shuffle null is NOT centred on 0.50 — it sits at ~0.549. There is a small structural floor, most plausibly because permuting labels within a window preserves the window's own feature distribution and the grouped CV can still exploit window-level structure.
THE HEADLINE SURVIVES AND IS NOW PROPERLY PRICED: real 0.6329 vs null mean 0.5492 = 3.41 SD above, permutation p = 0.0000 over 40 draws. E26's conclusion stands; its stated margin (+0.1178 over 0.5151) was overstated and the correct margin is +0.0837 over the null MEAN.
RULE FOR THIS VAULT GOING FORWARD: a shuffle control quoted from ONE draw is not a control. Report the null distribution (mean/std/p95) and the z or permutation p, never a single permuted score against an assumed 0.50.

2. E27 (exp-e27-encoder-transitions-20260815) = NO-GO on the Phase B retrain.
On E26's exact rows, every frozen-encoder arm is BELOW the shallow percentile baseline: enc_global_avgmax 0.5876, enc_global_avg 0.5728, enc_local_avgmax 0.5254, enc_local_avg 0.5026, against shallow 0.6329. Gap -0.0453 on the best.
AND the shuffle exposed the encoder arm as mostly artefact: enc_global_avgmax scores 0.5830 on shuffled labels vs 0.5876 real — a gap of 0.0046. Destroying the state contrast costs it nothing. With 4608-9216 features against 3248 rows this is capacity, not state.
This also makes E22's g3 'locality confirmed at the representation level' (+0.054 to +0.066 on daily labels) UNVERIFIED: it was measured without a shuffle control on the same features, and here locality REVERSES (local worse than global by -0.062 to -0.070) in exactly the way a capacity artefact would.
DECISION, per the rule committed before the run: do NOT spend ~4 GPU-hours on the Phase B retrain on representational grounds. The retrain retains only the SUPPLY justification (E25 non-degenerate triplets; E26 real contrast the miner discards).

3. DETECTOR SHIPPED (option 3, Anton's direction).
src/annotation/momentary_state.py + outputs/annotation/momentary_state_detector.joblib. flow-vs-stress, W=120, HR/HRV only, no clock features (E26 measured hr+clock 0.5786 < hr alone 0.6329). The trainer RE-SCORES with E26's protocol and refuses to write the artefact unless the AUC reproduces within 0.02 — it reproduced EXACTLY at 0.6329. n_train 3248, 87 subjects, 662 windows. Flagged provisional=True because optihealth_supabase still carries ZERO state labels, so this ships as a population model, not a per-user head.

STANDING ENGINEERING DEFECT, UNCHANGED: TripletStateDataset (src/training/datasets.py:2149) mines on infer_state_label, one majority-collapsed label per window, so all 1274 transition windows lose their internal contrast before Phase B sees it. E26 shows that contrast is real (3.41 SD above its own null). Fixing the miner is the remaining supply-side lever.
- **2026-08-30T11:46:58+00:00** — 2026-08-30: resolved per doctor(). 6 confirmed hypotheses, no live work. The remaining actionable residue moved into code and the collapse problem: mask_unlabelled_state fix landed (commit f1b15e9, exp-v11statemask measured it as hygiene-not-lever), and window admission/pool questions continue under new-corpus-12-547 problem.

#wearables #epoch-resolution #baseline #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
