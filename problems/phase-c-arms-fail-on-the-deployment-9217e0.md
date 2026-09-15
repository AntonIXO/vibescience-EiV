---
created_at: '2026-08-12T08:57:29+00:00'
former_ids:
- phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
id: phase-c-arms-fail-on-the-deployment-9217e0
paper_refs: []
problem_tags: []
status: open
target: null
title: 'Phase-C arms fail on the deployment distribution (optihealth_supabase): hr_raw
  floor atom at 30.0 plus unlearned sparsity handling'
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
updated_at: '2026-09-01T14:12:48+00:00'
updates:
- at: '2026-09-01T08:20:25+00:00'
  text: "THE SEED FLOOR THIS PROBLEM'S SYNTHESIS RESTS ON WAS MEASURED IN A REGIME
    THAT NO LONGER EXISTS. Re-measured 2026-09-01 (scratch/_supa_noise_floor.py) on
    three same-recipe families already on disk, same frozen 200-row supabase draw,
    10 seeds:\n\n  ab_ctrl  6.031 / 2.836 / 3.075 / 4.174   sd 1.456\n  w6avg    3.187
    / 2.861 / 2.742           sd 0.230\n  w6msk    3.389 / 3.351 / 3.737           sd
    0.213\n  pooled within-family sigma 0.8601 (dof 7), grand mean MAE 3.484\n\nThe
    registered floor was 3.145, taken as range/2 on the v7noatom family (11.54/15.78/9.49)
    at a mean MAE of 12.27. The spread turns out to be PROPORTIONAL to the error level,
    not additive: CV is 0.256 then and 0.247 now -- the same fractional spread at
    a level 3.5x lower. So a sigma measured on arms erroring at ~12 bpm was being
    applied to arms erroring at ~3.\n\nTHE SYNTHESIS'S HARDEST SENTENCE NO LONGER
    HOLDS. It reads: 'The seed band therefore EXCEEDS the trivial baseline (4.02):
    no single-seed arm comparison on this diagnostic is interpretable, and v7noatom's
    own 11.54 is one draw from an unknown-width distribution.' At the measured floor
    the k=2 band is +/-1.720 bpm, comfortably inside the 4.02 trivial baseline. Single-seed
    supabase comparisons BETWEEN HEALTHY ARMS are interpretable again. The old statement
    was correct for the arms it was drawn on -- a noise floor is a property of a REGIME,
    not of a metric, and this one stopped applying when the arms got better.\n\nIT
    DOES NOT RESCUE THE CAMPAIGN'S HEADLINE CLAIMS, WHICH IS THE OTHER HALF OF THE
    POINT. Re-read against 0.8601: v33dispersion 2.142 vs incumbent 3.030 is z -1.03;
    v34nopeak 2.095 is z -1.09; v31detached 2.699 is z -0.38. All INSIDE noise at
    k=2. The supabase wins that looked decisive on a single seed are DIRECTIONAL ONLY,
    and the pending v36labels seed family is the right instrument for settling them.\n\nESTIMATOR
    NOTE: range/2 on n=3 understates the SD by ~15% (the unbiased range-to-sigma factor
    at n=3 is d2=1.693). The replacement is a pooled within-family SD.\n\nSTILL OPEN
    AND UNTOUCHED BY THIS: root cause 1 (floor atom) remains fixed; root cause 2 (coverage
    dilution) was refuted today on all five current arms (slopes -0.24 to +0.13 against
    a predicted 1.0, R2 <= 0.062, rule_out coverage-dilution-is-the-live-second-root-cause-of-deployment-error).
    What survives from root cause 2 is the architectural observation that TemporalEncoder.forward(x)
    has no mask input, which deserves its own arm."
- at: '2026-09-01T09:32:21+00:00'
  text: "WITHIN-ARM SEED SPREAD FOR v36labels, MEASURED BEFORE THE FAMILY FINISHES
    (scratch/_within_arm_seed_spread.py). Cells 1-2 are done (v36labels at seeds 43
    and 44); cells 3-4 (the incumbent at the same seeds) have not run, so the PAIRED
    comparison is not available and the readout correctly refuses to report it. But
    one thing is already knowable: how far a single arm moves when only the seed changes.\n\n
    \ SUPA (floor sigma 0.8601)          n     MAE    corr   scale\n    v36labels
    \ s42                 200   1.862   0.893   0.94\n    v36labels  s43                 200
    \  2.501   0.866   1.08\n    v36labels  s44                 200   2.408   0.850
    \  0.89\n    v28affect01 s42                200   3.030   0.793   0.93\n    ->
    v36labels own-seed range 0.639, sd 0.345\n\n  DENSE (floor sigma 0.7566)         n
    \    MAE    corr   scale\n    v36labels  s42                 128   4.017   0.844
    \  1.32\n    v36labels  s43                 128   2.825   0.887   1.38  <- scale
    outside [0.85,1.35]\n    v28affect01 s42                128   2.851   0.937   1.11\n
    \   -> v36labels own-seed range 1.192, sd 0.843, which EXCEEDS the registered
    dense floor 0.7566\n\nWHAT THIS DOES TO THE PROMOTION CASE. v36labels' headline
    was supa 1.862 against the incumbent's 3.030, a margin of 1.168 bpm. Its own seeds
    span 1.862-2.501 on the same draw, so roughly HALF the claimed margin is seed.
    The remaining gap is still directionally in v36's favour on supa, but it is no
    longer the clean 1.168 the single-seed capture suggested.\n\nOn DENSE the picture
    is worse for the claim and better for the arm than the single seed said: s42 read
    4.017 (a 1.166 loss to the incumbent) while s43 reads 2.825 (a 0.026 WIN). The
    dense 'loss' that has been quoted all campaign is within this arm's own seed noise,
    and the sign FLIPS between its two seeds. Any statement of the form 'v36labels
    wins supa and loses dense' rests on one seed of each.\n\nCALIBRATION CAVEAT, which
    must be read first: v36labels s43 emits at 1.38x the true spread, just outside
    the [0.85, 1.35] domain where the dense floor is valid. Its 2.825 is therefore
    not directly comparable to the incumbent's 1.11-ratio 2.851 -- the arm is over-expressing
    range, and MAE alone hides that. s42 sits at 1.32, inside but near the edge. Same
    failure mode the four wide-roster arms showed in extreme form (0.26-0.57 and 2.03-2.76).\n\nNOTHING
    IS PROMOTED ON THIS. The paired verdict needs cells 3 and 4, which are running.
    Recorded now because the within-arm spread is the cheaper half of the answer and
    it was already on disk."
- at: '2026-09-01T09:35:46+00:00'
  text: "THE CAMPAIGN'S HEADLINE DELTAS ARE MEASURED AGAINST THE WRONG NOISE SOURCE.
    There are two, they answer different questions, and every 'DEMONSTRATED' verdict
    in this campaign used the smaller one (scratch/_total_noise_readout.py).\n\n  FINITE-DRAW
    sigma -- 'if I re-drew the 128/200 eval rows from the SAME two trained checkpoints,
    would the sign hold?' This is what scripts/paired_bootstrap.py reports, and its
    own footer warns it is a LOWER BOUND.\n  SEED floor -- 'if I RETRAINED both arms
    at a different seed, would the sign hold?' Measured today: 0.7566 dense, 0.8601
    supa.\n\nA promotion is a claim about the RECIPE, not about one pair of checkpoints,
    so the seed floor binds. Independent components add in quadrature: sigma_total
    = sqrt(seed^2 + draw^2).\n\n  DENSE (seed floor 0.7566)        dMAE   s_draw   s_tot
    \  z_draw   z_tot\n    v36labels                    +1.166    0.438   0.874    +2.66
    \   +1.33\n    v33dispersion                +0.225    0.345   0.832    +0.65    +0.27\n
    \   v34nopeak                    +1.068    0.366   0.840    +2.92    +1.27\n    v35wide
    \                     +1.270    0.364   0.840    +3.49    +1.51\n\n  SUPA (seed
    floor 0.8601)\n    v36labels                    -1.168    0.160   0.875    -7.31
    \   -1.33\n    v33dispersion                -0.887    0.192   0.881    -4.63    -1.01\n
    \   v34nopeak                    -0.935    0.189   0.881    -4.96    -1.06\n    v35wide
    \                     -0.424    0.137   0.871    -3.09    -0.49\n\nEIGHT deltas.
    Every one clears k=2 on the draw sigma -- v36labels' supa win reads z -7.31 --
    and NOT ONE clears it on the total. The largest is |z_tot| 1.51.\n\nWHAT THIS
    MEANS, STATED PLAINLY: the wins are real FOR THOSE CHECKPOINTS and unestablished
    FOR THE RECIPE. Promoting on z_draw would promote a pair of checkpoints, not a
    method. This is the same failure the campaign documented once before -- 'the incumbent's
    headline MAE lead was 0.89 sigma, not a difference at all' -- now reaching the
    whole arm table rather than one comparison.\n\nWHY THE SEED FLOOR DOMINATES: s_draw
    is 0.14-0.44 while the seed floor is 0.76-0.86, so the total is 1.9-5.4x the draw
    sigma and is almost entirely retrain variance. Bootstrapping the eval rows harder
    cannot help; only more seeds can.\n\nCONSEQUENCE FOR THE RUNNING FAMILY: this
    is exactly what cells 3-4 are for. With both arms at three seeds the comparison
    becomes a paired difference of MEANS, whose standard error is sigma_seed/sqrt(3)
    ~ 0.44 dense -- roughly the draw sigma, and small enough that a real 1.17 bpm
    effect would clear k=2. The family is the right instrument and it is running.\n\nNOTHING
    HERE RETRACTS A MEASUREMENT. Every number stands; what changes is which sigma
    it is read against."
- at: '2026-09-01T09:39:20+00:00'
  text: "THE THIRD SEED LANDED, AND IT CHANGES THE HEADLINE IN BOTH DIRECTIONS (scratch/_three_seed_readout.py).
    Cell 2's dense capture completed, so v36labels now has seeds 42/43/44 on both
    draws. The incumbent still has one seed (cell 3 started 12:36), so this is NOT
    the paired test -- but three things are now settled.\n\n1. THE VARIANCE ALARM
    IS RESOLVED, AND IT WAS NOISE.\n   dense: 3-seed sd 0.849 against floor 0.7566
    = 1.12x\n   supa : 3-seed sd 0.345 against floor 0.8601 = 0.40x\n   The 2-seed
    dense estimate was 0.843 (1.11x) and I flagged it as EXCEEDING the floor. At n=3
    it is 0.849 -- essentially unchanged, and well inside what a pooled floor covers.
    On supa the arm is less than half as noisy as the floor. v36labels is not an unusually
    noisy recipe.\n\n2. THE DENSE 'LOSS' LARGELY EVAPORATES, AND THE SUPA 'WIN' SHRINKS.\n
    \  dense v36labels [4.017, 2.825, 2.374]  mean 3.072, SE 0.490\n   supa  v36labels
    [1.862, 2.501, 2.408]  mean 2.257, SE 0.199\n   Against the incumbent's single
    seed (dense 2.851, supa 3.030):\n     dense delta +0.221  (was +1.166 on seed
    42 alone)\n     supa  delta -0.773  (was -1.168 on seed 42 alone)\n   Seed 42
    was the arm's WORST dense seed and its BEST supa seed. The single-seed capture
    that framed this whole comparison was unlucky in one direction and lucky in the
    other, simultaneously.\n\n3. NEITHER IS OUTSIDE NOISE, EVEN ON THIS FAVOURABLE
    READING.\n   Treating the incumbent's unmeasured retrain variance as the registered
    floor:\n     dense SE 0.901 -> z +0.25\n     supa  SE 0.883 -> z -0.88\n   Both
    inside k=2. And this comparison FLATTERS v36: it averages three seeds of one arm
    against one draw of the other. The paired test from cells 3-4 will be stricter,
    not looser.\n\nCALIBRATION, read first as the rule requires: v36labels' dense
    scale ratios are [1.32, 1.38, 0.98]. Two of three sit at or outside the [0.85,
    1.35] edge where the dense floor is valid. The incumbent's is 1.11. So part of
    the dense story is an arm that over-expresses range on two seeds of three -- a
    property MAE alone would not show.\n\nWHAT THIS MEANS FOR THE PROMOTION QUESTION
    AS ASKED. 'v36labels wins supa by 1.168 and loses dense by 1.166' was a statement
    about seed 42. At three seeds it becomes 'wins supa by 0.773, loses dense by 0.221,
    neither outside noise'. Nothing is promoted; the direction on supa survives and
    is worth the remaining two cells."
- at: '2026-09-01T10:00:30+00:00'
  text: "THE QUEUED ARMS WILL BE COMPARABLE TO THE INCUMBENT, CHECKED BEFORE THEY
    RUN (scratch/_queued_arm_code_drift.py).\n\nThe w6 failure was control-on-one-commit
    vs treatment-on-another, 1,155 lines apart. Four queued arms could reproduce it:
    v38prompt and v39scaled both run --profile lora_proxy_12ch_wl_band04, the INCUMBENT'S
    OWN profile, so verify_one_variable has nothing to compare -- their variable is
    not a profile key. That is why neither carries the gate, and it is correct. But
    it means the only thing separating each arm from the incumbent is CODE, and the
    incumbent's Phase C checkpoint was written on c3f482746ef3 (08-31 06:10) while
    the arms will run on cc29680 or later.\n\nsrc/ CHANGES IN THAT RANGE: cli_train.py
    +6, datasets.py +414, gemma_runtime.py +31, pipeline.py +22 (468 insertions).\n\nEVERY
    ONE IS DORMANT FOR band04, BY ONE OF TWO CHECKABLE MECHANISMS:\n  env flags   EIV_RESOLVE_NESTED_CONTEXT
    -> exported ONLY by run_v38_prompt.sh\n              EIV_SCALED_LABEL_CLAUSES
    \  -> exported ONLY by run_v39_scaled.sh\n  new params  detached_probes, max_clauses,
    sampler_coverage_strength,\n              state_peak, wide_channels -- none set
    in lora_proxy_12ch_wl_band04,\n              so all resolve to their defaults\n\nSo
    each flag is exported by exactly the arm whose variable it IS. That is the intended
    structure: the recipe is frozen for everyone else and the arm turns on its own
    one thing.\n\nHAND-READ THE UNCONDITIONAL EDITS, since the script cannot cover
    them: pipeline.py's changes are the two effective_* reads (profile-gated) and
    probe_input = fused.detach() if detached_probes else fused, which is False by
    default -- fused is passed exactly as before. gemma_runtime's scan list is inside
    the EIV_RESOLVE_NESTED_CONTEXT branch. Nothing unconditional reaches a band04
    run.\n\nSCANNER BUG FOUND AND FIXED IN PASSING, in the harder direction: the first
    version reported ONE flag where there are two. EIV_RESOLVE_NESTED_CONTEXT is written
    across a line break, and in a diff the continuation line begins with '+', so neither
    a plain regex nor re.S matched -- the pattern needs (?:\\n\\+\\s*)?. An UNDER-reporting
    scanner is worse than an over-reporting one: this one printed a short, clean,
    plausible list. Same family as this morning's guard that reported ALL CLEAR after
    checking zero drivers.\n\nWHAT REMAINS TRUE: the incumbent's OWN numbers (dense
    2.851, supa 3.030) were earned on 08-31 code. Nothing in this range changes a
    band04 run, so they stand -- but that is now a checked statement rather than an
    assumption."
- at: '2026-09-01T10:04:07+00:00'
  text: "WHAT A LABEL SLOT IN json_slots COULD ACTUALLY CARRY, MEASURED ON BOTH ROSTERS
    (scratch/_label_slot_feasibility.py). Successor question to 'Phase C emits 13
    slots and one comes from a label': before writing a slot renderer, measure what
    would fill it.\n\nREACH, band04 (7,592 windows) vs wide (9,530):\n  stress_level
    \     1665  21.9%  ->  1780  18.7%   [pmdata, +2 sources wide]\n  readiness         1665
    \ 21.9%  ->  1665  17.5%   [pmdata]\n  sleep_duration_h  1665  21.9%  ->  1665
    \ 17.5%   [pmdata]\n  sleep_quality     1665  21.9%  ->  1665  17.5%   [pmdata]\n
    \ mood_ordinal      1665  21.9%  ->  1665  17.5%   [pmdata]\n  fatigue           1665
    \ 21.9%  ->  1665  17.5%   [pmdata]\n  valence              0   0.0%  ->  1298
    \ 13.6%\n  arousal              0   0.0%  ->   609   6.4%\n  stai_state         242
    \  3.2%  ->   257   2.7%   [lifesnaps]\n  everything else  <= 2.7% on the wide
    roster\n\nTHE ROSTER WIDENING DILUTES EVERY EXISTING LABEL CHANNEL. 9,530 windows
    against 7,592: the six pmdata channels each fall from 21.9% to 17.5% because the
    numerator is unchanged (the same 1,665 pmdata windows) while the denominator grows
    by 1,938. The wide roster does not add label coverage -- it adds windows carrying
    AFFECT labels and dilutes the ones carrying EMA labels. valence 0 -> 1,298 is
    real and is bought at -4.4pp on six other channels.\n\nSHARED-AXIS CHECK (criterion
    3), source medians:\n  stress_level  brain_wearable 1.49 / nurse_stress 2.00 /
    pmdata 3.00   spread 1.507\n  valence       case 5.15 / dapper 3.33 / k_emocon
    3.00 / popane_s3 5.36 /\n                s5 5.68 / s6 4.95 / s7 6.44                            spread
    3.435\n  arousal       case 5.15 / dapper 3.00 / k_emocon 3.10                spread
    2.153\n  stai_state    lifesnaps 48.0 / wesad 12.2                            spread
    35.800\n  age           dreamt 58.69 / popane 19.5-23.0                        spread
    39.195\n\nstai_state's 48.0 vs 12.2 is not a population difference -- it is two
    instruments under one channel name, on 2.7% of rows. age's 58.7 vs ~21 IS a real
    population difference (sleep-clinic cohort vs undergraduates) and is a subject
    trait, not an epoch observation.\n\nTHE HONEST SHORTLIST FOR A LABEL SLOT IS SHORT:\n
    \ - the six pmdata channels at 17-22%, all on the SAME 1,665 windows (one indicator
    six ways, established earlier), and three of them -- sleep_duration_h, sleep_quality,
    readiness -- restate what deep_pct/rem_pct/awake_pct already emit from the tensor.
    That leaves stress_level, fatigue, mood_ordinal as genuinely new supervision on
    ~1 row in 5.\n  - valence/arousal, wide roster only, 13.6%/6.4%, and only after
    scale_to_declared_range -- which is exactly what v39scaled is testing on the CAPTION
    path right now.\n\nWHAT THIS MEANS FOR THE STANDING GOAL: 'all labels in all three
    phases' is not reachable by a target change either, as literally stated. 26 of
    the 35 declared channels reach under 3% of the wide roster and most reach a single
    dataset. What IS reachable is a slot for the two or three channels with real coverage
    -- a much smaller claim than the goal as written, and it should be stated as such
    rather than quietly redefined.\n\nORDERING CONSEQUENCE: v39scaled settles whether
    shared-axis rendering helps on the caption path before any of this is worth building
    into the target. If scaled clauses do not help in A/B, a json_slots label slot
    inherits the same axis problem with less data."
- at: '2026-09-01T10:28:59+00:00'
  text: "THE INCUMBENT REPRODUCES ITSELF TO 0.035 bpm ACROSS SEEDS (scratch/_incumbent_pair_spread.py).
    Cell 3 landed, so v28affect01 now has two supabase captures on the same frozen
    200-row draw:\n\n  v28affect01 s42   MAE 3.030   corr 0.793   scale 0.93\n  v28affect01
    s43   MAE 3.065   corr 0.774   scale 0.98\n  |s42 - s43| = 0.035\n\nAgainst the
    registered floor 0.8601 the expected absolute difference between two seeds is
    E|d| = sigma*sqrt(4/pi) = 0.971. The observed 0.035 is 0.04x that -- twenty-seven
    times tighter than the floor predicts. Both captures are in-band on calibration
    (0.93, 0.98) so neither is a broken emission.\n\nWHAT THIS SETTLES: the incumbent's
    headline 3.030 was not a lucky capture. Every comparison in this campaign is anchored
    on that number and none of them had ever checked it against a retrain.\n\nWHAT
    IT DOES NOT SETTLE, and the distinction matters: one pair cannot distinguish 'this
    recipe is quieter than the floor's families' from 'this pair was lucky'. The floor
    stays 0.8601 until a third seed lands. Lowering it on two points would be the
    same error as this morning's 2-seed sd alarm, run in the flattering direction
    instead of the alarming one -- and it would make every future arm easier to promote,
    which is exactly the direction where a mistake is expensive.\n\nSTATE OF THE PAIRED
    COMPARISON, 3 vs 2 and still incomplete:\n  v36labels   1.862 / 2.501 / 2.408
    \  mean 2.257  n=3\n  v28affect01 3.030 / 3.065           mean 3.047  n=2\n  delta
    -0.790, SE at the registered floor 0.785, z -1.01\nThe readout correctly refuses
    a verdict while cell 4 is missing. But note the direction of the remaining uncertainty:
    if the incumbent really is a 0.035-spread recipe, the SE that the floor imposes
    is far too wide, and the honest reading of the delta will depend on which sigma
    cell 4 supports. That is a question the family was built to answer and it is one
    cell from answering it."
- at: '2026-09-01T10:30:31+00:00'
  text: "THE TWO NOISE FLOORS WERE COMPUTED UNDER DIFFERENT RULES, AND THE SUPABASE
    ONE BREAKS THE RULE THE DENSE ONE ESTABLISHED (scratch/_floor_calibration_consistency.py).
    Found while trying to explain why the incumbent reproduces itself to 0.035 against
    a floor of 0.8601.\n\nTHE DENSE FLOOR'S OWN STATED RULE, from this problem's synthesis:
    eiv_dense128_hr_raw_mae_bpm carries sigma 0.7566 'over the seeds whose emit_sd/true_sd
    sits in [0.85, 1.35]'. Two ab_ctrl seeds were EXCLUDED for emitting at 2.33x and
    2.17x, because 'pooling them would have set sigma at 2.57 bpm -- wider than the
    entire asserted no-harm band -- and made every future arm unfalsifiable'.\n\nTHE
    SUPABASE FLOOR APPLIED NO SUCH FILTER. Scale ratios of its ten captures, on the
    same frozen 200-row draw (true_sd 5.352 for every one, verified directly):\n  ab_ctrl
    s42 1.61 | s43 0.70 | s44 0.83 | s45 1.36   <- ALL FOUR outside [0.85,1.35]\n
    \ w6avg   s43 0.62 | s44 0.85 | s45 0.91              <- s43 outside\n  w6msk
    \  s43 1.19 | s44 0.88 | s45 1.17              <- all in band\n\nSo five of ten
    captures behind the supabase floor are outside the calibration domain the dense
    floor defines, and the entire ab_ctrl family -- whose sd 1.456 is 7x the other
    two families' 0.230 and 0.213 -- is out on every seed.\n\nPOOLED SIGMA, both ways:\n
    \ ALL captures (the registered floor)      0.9680  dof 7   [ab_ctrl 1.456 | w6avg
    0.230 | w6msk 0.213]\n  IN-BAND only (the campaign's own rule)   0.1804  dof 3
    \  [w6avg 0.085 | w6msk 0.213]\n\nThat is a 5.4x difference, and it explains the
    incumbent's 0.035 pair difference completely: at sigma 0.18 the expected |d| is
    0.203, and 0.035 is an unremarkable draw. At 0.86 it looked like a 27x anomaly.
    The anomaly was in the floor, not the arm.\n\nI AM NOT CHANGING THE REGISTERED
    FLOOR ON THIS, and the reason is the same one that makes the finding matter. dof
    3 is very thin, the in-band set has no ab_ctrl representation at all, and a floor
    of 0.18 would make almost every delta in this campaign 'significant' -- including
    the eight that the total-noise readout just showed are not. A floor that flatters
    is worse than one that is too wide.\n\nWHAT IT DOES ESTABLISH: the two floors
    are not comparable to each other, and any statement of the form 'this arm clears
    the supabase floor but not the dense one' has been comparing numbers computed
    under different admission rules. The honest next step is a supabase floor measured
    on in-band captures with enough seeds to have dof -- which cell 4 contributes
    to, and which the queued w6-style families could extend.\n\nMETHOD NOTE: the pooling
    itself assumes a COMMON sigma across families. With per-family sds of 1.456, 0.230
    and 0.213 that assumption is already violated by a factor of 7 before any calibration
    filter is applied. Pooling heterogeneous families produces a number that estimates
    nothing in particular."
- at: '2026-09-01T10:34:29+00:00'
  text: "THE SEED FAMILY IS GENUINELY PAIRED -- VERIFIED ROW BY ROW BEFORE THE VERDICT
    (scratch/_family_draw_identity.py). Checked while cell 4 is still running, because
    this defect is invisible afterwards: two captures with the same n and plausible
    MAEs look identical whether or not they cover the same windows.\n\nSUPABASE, five
    cells present:\n  every capture n=200, id key 'idx', 200 unique ids\n  pairwise
    against v36labels s42: shared 200, only-base 0, only-other 0, max|delta truth|
    0.00e+00\n\nDENSE, four cells present:\n  every capture n=128, 128 unique ids\n
    \ pairwise: shared 128, only-base 0, only-other 0, max|delta truth| 0.00e+00\n\nSo
    all cells score the SAME rows and agree on every row's true value to the bit.
    A difference of means is a difference on identical data, and the SE derivation
    the verdict rests on is valid.\n\nWHY THIS WAS WORTH A CHECK RATHER THAN AN ASSUMPTION:
    MAE is an average, so an id present in one cell and absent from another silently
    changes what the mean is over -- and dropping the hardest rows from one arm flatters
    it by exactly what those rows cost. The strongest test is the truth comparison:
    if two captures claimed the same id but disagreed on its true_mean, the id would
    not be a stable key and nothing paired could be computed from these files at all.
    max|delta truth| is exactly zero on all 9 comparisons.\n\nREMAINING: v28affect01
    s43 dense (in flight) and v28affect01 s44 on both draws. When they land the same
    check should be re-run before the verdict -- a fresh capture is a fresh opportunity
    for the draw to move."
- at: '2026-09-01T10:35:34+00:00'
  text: "THE INCUMBENT REPRODUCES ITSELF ON BOTH DRAWS, AND THE SECOND PAIR IS INDEPENDENT
    CONFIRMATION OF THE FIRST. Cell 3's dense capture landed, so v28affect01 now has
    two seeds on each draw:\n\n  SUPA   s42 3.030 (scale 0.93) | s43 3.065 (0.98)
    \  |d| = 0.035\n  DENSE  s42 2.851 (scale 1.11) | s43 2.781 (1.07)   |d| = 0.070\n\nAgainst
    the registered floors the expected absolute difference is E|d| = sigma*sqrt(4/pi):\n
    \ supa   expected 0.971, observed 0.035 -> 0.036x, implied sigma 0.031\n  dense
    \ expected 0.854, observed 0.070 -> 0.082x, implied sigma 0.062\n\nWhen I recorded
    the supabase pair an hour ago I explicitly refused to read a variance from it,
    on the grounds that one pair cannot separate 'quiet recipe' from 'lucky pair'.
    The dense pair is a SECOND, INDEPENDENT draw -- different evaluation population,
    different rows, different capture run -- and it lands at 0.082x of expectation.
    Two independent pairs both landing under a tenth of expectation is no longer plausibly
    luck: P(both under 0.1x) under the floor's own model is well below 1%.\n\nSo the
    honest reading has moved: v28affect01 IS a quiet recipe, by roughly an order of
    magnitude, on both populations. Its implied sigmas (0.031 supa, 0.062 dense) are
    20-30x below the registered floors.\n\nALL FOUR CAPTURES ARE IN-BAND on calibration
    (0.93, 0.98, 1.11, 1.07), so none of this is a broken-emission artefact.\n\nI
    AM STILL NOT LOWERING THE REGISTERED FLOORS, and the reason is now sharper than
    'thin evidence'. Today's proportionality test showed retrain spread is a property
    of the RECIPE -- per-family sd varies 58x across five families. The incumbent
    being quiet says nothing about v36labels, which measures 0.345 supa and 0.849
    dense over three seeds each. A promotion decision needs BOTH arms' own spreads,
    which is exactly what the four-cell design produces and what cell 4 completes.\n\nDENSE
    PAIRED STATE, 3 vs 2, still incomplete:\n  v36labels   4.017 / 2.825 / 2.374   mean
    3.072\n  v28affect01 2.851 / 2.781           mean 2.816\n  delta +0.256 (v36 WORSE),
    SE 0.691 at the registered floor, z +0.37\nAnd the calibration warning stands:
    v36labels s43 emits at 1.38, outside [0.85,1.35], where the dense floor is not
    valid. Two of v36's three dense seeds sit at or past the edge (1.32, 1.38) while
    all four incumbent captures are comfortably inside.\n\nDRAW IDENTITY RE-VERIFIED
    after the new capture: dense now has five cells, all n=128, 128 unique ids, max|delta
    truth| 0.00e+00 across every pairwise comparison. The family remains genuinely
    paired."
- at: '2026-09-01T10:41:01+00:00'
  text: "THE 5-7 bpm hr_peak GAP IS REAL AND IS NOT AN ARGUMENT FOR PROMOTION -- BOTH
    ARMS SIT AT OR BELOW A CONSTANT (scratch/_hr_peak_gap.py). Found by misreading
    the family report's tail, going to look for a bug, and finding none: the -5.774
    was hr_peak, not hr_raw. Reading it properly surfaced a metric promotion is not
    scored on.\n\nSUPA hr_peak, trivial baseline = predict the population median peak
    (17.410):\n  v36labels    s42 16.057  skill +1.353  scale 0.61\n               s43
    17.730  skill -0.320  scale 0.82   <- worse than a constant\n               s44
    16.292  skill +1.118  scale 0.75\n  v28affect01  s42 21.832  skill -4.422  scale
    0.50   <- worse than a constant\n               s43 24.342  skill -6.932  scale
    0.49   <- worse than a constant\n\nDENSE hr_peak, trivial 17.218:\n  v36labels
    \   15.751 (+1.466, 0.72) | 16.981 (+0.237, 0.90) | 17.673 (-0.456, 0.65)\n  v28affect01
    \ 16.674 (+0.543, 0.86) | 16.886 (+0.332, 0.70)\n\nSO THE SUPABASE GAP IS ENTIRELY
    THE INCUMBENT COLLAPSING. v28affect01 reads 21.8 and 24.3 against a constant's
    17.4 -- 4.4 and 6.9 bpm WORSE than emitting one number for every row, on the deployment
    population. v36labels is merely near zero (+1.35, -0.32, +1.12). A gap between
    'slightly better than a constant' and 'much worse than a constant' is a smaller
    loss, not a gain.\n\nTHE CALIBRATION COLUMN GIVES THE MECHANISM: every hr_peak
    capture emits at 0.49-0.90 of the true spread, all below the [0.85,1.35] band,
    and the incumbent's supabase captures are the most compressed at 0.50 and 0.49.
    The arms regress the peak toward a central value -- which is what produces a MAE
    worse than the median baseline, since the median IS the best constant. The incumbent's
    21.8-24.3 means it emits a constant-ish value that is not the median.\n\nCONSISTENT
    WITH THE RECORD, which has hr_peak skill negative on every seed (-2.88/-2.73/-3.16
    on v9band04) and calls it an encoder excursion limit rather than a target-style
    property. This adds two things: (1) the limit is far worse on supabase than on
    dense for the incumbent (-4.4/-6.9 vs +0.5/+0.3), so it is population-dependent
    rather than purely architectural; (2) v36labels does NOT share the collapse, which
    is the first sign that the excursion limit is recipe-sensitive.\n\nNOT PROMOTING
    ON THIS. hr_peak is not the deployment metric, both arms straddle zero skill,
    and the incumbent has n=2. Recorded because a 6.9 bpm sub-baseline result on the
    production population is a defect worth naming even when it is not the metric
    under test."
- at: '2026-09-01T10:48:26+00:00'
  text: "THE SUPABASE NOISE FLOOR WAS MEASURED ON LIFESNAPS. Every capture behind
    the registered floor 0.8601 is named `*_supa.json` and contains 200 rows of lifesnaps,
    not optihealth_supabase (scratch/_capture_join.py).\n\n  emission_ab_ctrl_supa.json
    \       200 rows  lifesnaps\n  emission_ab_ctrl_s43/44/45_supa   200 rows  lifesnaps\n
    \ emission_w6avg_s43/44/45_supa     200 rows  lifesnaps\n  emission_w6msk_s43/44/45_supa
    \    200 rows  lifesnaps\n  ---- versus the seed family, which IS supabase ----\n
    \ emission_v36labels_supa           200 rows  optihealth_supabase\n  emission_v36labels_s43/44_supa
    \   200 rows  optihealth_supabase\n  emission_v28affect01_supa         200 rows
    \ optihealth_supabase\n  emission_v28affect01_s43_supa     200 rows  optihealth_supabase\n\nAll
    ten floor captures read HR coverage p50 0.862 (lifesnaps); the family reads 0.789
    (supabase). Two different populations under one filename convention.\n\nHOW IT
    WAS FOUND: I built a guard that resolves the scorer's bundle from its own source
    and attaches per-row dataset and coverage, after wasting an hour on a wrong-bundle
    join. Its first run printed the dataset column and the discrepancy was immediate.
    The guard was built to prevent a repeat of my own error and caught a larger, older
    one.\n\nWHAT THIS INVALIDATES: the registered supabase floor sigma 0.8601 with
    dof 7, pooled over ab_ctrl (sd 1.456), w6avg (0.230) and w6msk (0.213). Those
    are LIFESNAPS retrain spreads. They may be a reasonable prior for supabase spread,
    but that is an assumption nobody stated, and the populations differ in HR coverage
    (0.862 vs 0.789) and sparsity structure.\n\nIT ALSO EXPLAINS TODAY'S 27x PUZZLE
    CLEANLY. The incumbent reproduces itself to 0.035 bpm on supabase and 0.070 on
    dense against a floor of 0.8601 -- 27x and 12x tighter. I recorded that as 'the
    incumbent is a quiet recipe' and refused to lower the floor. The real explanation
    is that the floor describes a DIFFERENT POPULATION, and the supabase retrain spread
    has never been measured at all.\n\nWHAT IS UNAFFECTED: the seed family itself.
    All four cells score optihealth_supabase, the draw-identity check passed (n=200,
    identical ids, max|delta truth| 0), and its internal comparisons are sound. What
    changes is that its verdict cannot be read against the registered floor -- the
    family must supply its own spread, which is exactly what a four-cell design does.\n\nAlso
    unaffected: every DENSE floor number. Those captures are the mixed 128-row draw
    and were never dataset-restricted.\n\nIMMEDIATE CONSEQUENCE FOR THE PENDING VERDICT:
    read the v36-vs-v28 supabase comparison using the two arms' OWN seed spreads (v36
    sd 0.345 over 3 seeds, v28 sd 0.025 over 2), not sigma 0.8601. The registered
    floor is a lifesnaps number and should be renamed as such rather than reused."
- at: '2026-09-01T10:49:46+00:00'
  text: "FULL CENSUS OF *_supa CAPTURES: 17 ARE LIFESNAPS, 19 ARE REALLY SUPABASE
    (scratch/_supa_capture_census.py). Every capture classified by the dataset of
    its own rows, joined through the scorer's declared bundle.\n\nLIFESNAPS group
    (17 captures, HR cov p50 0.862), MAE mean 7.307, min 2.738, max 24.554:\n  ab_ctrl
    s42/s43/s44/s45   6.031 / 2.836 / 3.075 / 4.174\n  w6avg   s43/s44/s45       3.187
    / 2.861 / 2.742\n  w6msk   s43/s44/s45       3.389 / 3.351 / 3.737\n  conly   s44/s45
    \          3.538 / 2.738\n  ab_wl 20.284 | c_patch120 s42/s43 23.595 / 24.554
    | mc01 11.078 |\n  v9band04_newdraw 3.044\n\nOPTIHEALTH_SUPABASE group (19 captures,
    HR cov p50 0.789), MAE mean 4.143, min 1.862, max 10.058:\n  v16fixed 10.058 |
    v17oldenc 4.279 | v18oldbundle 7.847 | v21chain 5.775 |\n  v23aconly 5.414 | v24btripoff
    5.282 | v25r30 3.203 | v27affect 4.688 |\n  v30state 5.666 | v31detached 2.699
    | v32detachaffect 4.090 |\n  v33dispersion 2.142 | v34nopeak 2.095 | v35wide 2.606
    |\n  v28affect01 s42/s43 3.030 / 3.065 | v36labels s42/s43/s44 1.862 / 2.501 /
    2.408\n\nTHE GOOD NEWS, AND IT IS SUBSTANTIAL: every arm the campaign actually
    reasons about -- the incumbent, v36labels, and the whole v30..v35 series -- is
    in the REAL supabase group. Their MAEs are deployment numbers and stand exactly
    as recorded. The mislabelling is confined to the floor families and to older arms
    (ab_*, w6*, conly, c_patch120, mc01).\n\nTHE BAD NEWS IS THE ONE ALREADY RECORDED:
    those floor families are precisely what sigma 0.8601 was pooled from, so the supabase
    floor is a lifesnaps floor. It is now also visible WHY nobody caught it -- the
    two populations have similar MAE ranges (lifesnaps 2.7-4.2 for the healthy arms,
    supabase 1.9-4.1), so no number looked out of place.\n\nA SUPABASE-NATIVE SPREAD
    IS NOW COMPUTABLE from the 19 real captures, and it needs stating carefully: those
    are DIFFERENT ARMS, so their spread mixes recipe differences with retrain noise
    and is an upper bound, not a floor. The only same-recipe supabase families on
    disk are the seed family's own two: v36labels 1.862/2.501/2.408 (sd 0.345) and
    v28affect01 3.030/3.065 (sd 0.025). That is the honest basis for the pending verdict,
    and cell 4 doubles the incumbent's contribution.\n\nWHAT NEEDS RESTATING RATHER
    THAN RE-RUNNING: the early-vs-late presence comparison (w6avg vs w6msk) was already
    retracted today for a code confound; it is now ALSO a lifesnaps comparison, not
    a deployment one. Both retractions point the same way -- that question is untested
    on the deployment population."
- at: '2026-09-01T10:54:32+00:00'
  text: "THE EVAL POOL IS UNCHANGED ACROSS THE 2026-08-30 REBUILD -- THE ARM TABLE
    AND THE FLOOR BOTH STAND (scratch/_eval_population_continuity.py). This closes
    the thread opened by the stale-index discovery, in the arms' favour.\n\nTHE WORRY:
    the eval bundle was rebuilt 2026-08-30 18:18, so captures before it index a different
    space than captures after. MAE needs no join, so the arm table freely mixes both
    eras. Safe only if the rebuild renumbered windows WITHOUT changing which windows
    the supabase draw contains.\n\nTHE TEST, from the captures alone, no re-scoring:
    every row carries true_mean and the draw is deterministic, so the sorted multiset
    of true values fingerprints the population a capture saw. Two captures of the
    same draw must agree on it however the indices are numbered.\n\nRESULT:\n  PRE-rebuild
    \ 17 captures, all one fingerprint\n  POST-rebuild 19 captures, all one fingerprint\n
    \ PRE vs POST: same n (200), identical truth, max |difference| 0.000000\n  PRE
    \ truth mean 57.838  sd 5.352\n  POST truth mean 57.838  sd 5.352\n\nThe two eras
    see the SAME 200 days. The rebuild moved windows within the tensor and did not
    alter the supabase pool's membership.\n\nWHAT THIS RESTORES:\n  - the arm table
    is a single ranking on one population, v16fixed through v36labels\n  - the registered
    supabase floor 0.8601 stands; its MAEs came from these same 200 rows\n  - the
    census's MAE columns stand -- only its dataset/coverage LABELS for pre-rebuild
    captures were wrong\n\nWHAT REMAINS TRUE FROM THE EARLIER FINDING: a stale capture's
    idx still cannot be joined to the current bundle, and the guard refusing it stays.
    The distinction is now exact -- INDICES are invalid across the rebuild, VALUES
    are not.\n\nNET EFFECT OF THE WHOLE THREAD: one guard, zero retracted arm results.
    I spent several steps chasing a defect confined to per-row joins while the headline
    numbers were never affected. Recording that plainly because two intermediate entries
    said otherwise, and a future reader should not inherit the alarm without the resolution."
- at: '2026-09-01T10:56:00+00:00'
  text: "THE DENSE DRAW DID CHANGE ACROSS THE REBUILD, UNLIKE SUPABASE -- AND EVERY
    ARM THE CAMPAIGN USES IS ON ONE SIDE OF IT (scratch/_dense_continuity_and_baseline.py).
    Applied the truth-fingerprint test to the dense draw, which is the riskier one:
    it is not dataset-restricted, so its rng(0) draw runs over the WHOLE bundle and
    any change in row count re-rolls every index.\n\n  dense captures 44: 22 pre-rebuild,
    22 post-rebuild\n  PRE  reference v15exact_dense   n=128\n  POST reference v16fixed_dense
    \  n=128\n  PRE vs POST: same n, identical truth FALSE, max |difference| 6.774\n
    \ PRE  truth mean 74.782  sd 9.797\n  POST truth mean 74.358  sd 8.884\n\nSo the
    dense pool is NOT the same 128 days before and after. Supabase held because its
    pool is filtered by dataset and that dataset's membership did not change; dense
    is drawn from the whole tensor and moved.\n\nWHAT SAVES THE CAMPAIGN HERE: all
    22 POST captures agree with each other exactly -- zero disagreements. The 17 that
    disagree are all pre-rebuild (ab_*, w6*, conly, c_patch120, mc01, v9band04_newdraw).
    Every arm in the current comparison -- v16fixed through v36labels, the incumbent,
    both seed-family arms -- was captured after 08-30 and sits on ONE dense population.\n\nCONSEQUENCE,
    narrow and real: a dense MAE from before 08-30 must not be ranked against one
    from after. That includes the w6avg/w6msk dense numbers, which is the third independent
    reason today's early-vs-late presence comparison does not stand (code confound,
    and now a population change).\n\nTRIVIAL BASELINE RECOMPUTED ON THE CURRENT DENSE
    DRAW, because skill is a property of the draw and the draw moved: median 74.971
    bpm, baseline MAE 6.986 bpm over 128 rows.\n\nSKILL OF EVERY CURRENT-ERA ARM against
    it:\n  v36labels s44   2.374  +4.611     v28affect01 s43  2.781  +4.205\n  v36labels
    s43   2.825  +4.161     v28affect01 s42  2.851  +4.135\n  v33dispersion   3.076
    \ +3.910     v34nopeak        3.919  +3.067\n  v36labels s42   4.017  +2.969     v35wide
    \         4.121  +2.865\n  ---- at or below the constant ----\n  v31detached     7.312
    \ -0.326     v25r30           7.388  -0.402\n  v27affect       8.587  -1.601     v32detachaffect
    \ 9.006  -2.021\n  v30state       15.085  -8.099     v23aconly       15.908  -8.922\n
    \ v21chain       17.534 -10.548     v24btripoff     18.652 -11.666\n  v16fixed
    \      24.519 -17.533     v18oldbundle    30.255 -23.269\n\nThe eight arms at
    the top are the only ones that beat a constant on dense, and the seed family occupies
    the top four. That is worth stating because 'v31detached had the best geometry'
    has been quoted in this campaign: on dense it is 0.33 bpm WORSE than emitting
    74.971 for every row."
- at: '2026-09-01T10:58:48+00:00'
  text: "THE DENSE FLOOR WAS MEASURED ON THE PRE-REBUILD POPULATION, AND THE SEED
    FAMILY IS NOW THE ONLY CURRENT-ERA SOURCE (scratch/_dense_floor_era.py). This
    is the case the supabase check ruled out and the dense draw does not escape.\n\nEVERY
    family behind the registered dense floor 0.7566 is OLD-era:\n  ab_ctrl  s44 2.562
    (scale 1.05) | s45 10.168 (2.17)        captured 08-20\n  w6avg    3.560 (1.00)
    / 2.642 (1.03) / 3.334 (1.32)        captured 08-21\n  w6msk    3.947 (1.33) /
    2.439 (0.95) / 3.301 (1.09)        captured 08-21\nAll pre-date the 2026-08-30
    rebuild, and the dense pool CHANGED at that rebuild (max |delta truth| 6.774).
    Unlike supabase, where the pool was byte-identical and the number transferred,
    here the floor describes 128 days that no current arm is scored against.\n\nPOOLED
    SIGMA BY ERA:\n  OLD-era     families ab_ctrl(3), w6avg(3), w6msk(3)   sigma 2.5683
    \ dof 6\n  CURRENT-era families v36labels(3), v28affect01(2)     sigma 0.6937
    \ dof 3\n\nTHE OLD-ERA NUMBER IS 2.5683, NOT 0.7566 -- because this pooling includes
    ab_ctrl s45 at scale 2.17, which the registered floor's own calibration rule excludes.
    That is consistent with the synthesis, which records exactly this: 'pooling them
    would have set sigma at 2.57 bpm'. So 0.7566 is the OLD-era in-band pool, and
    my recomputation here is the OLD-era unfiltered pool. Both describe the pre-rebuild
    population.\n\nTHE CURRENT-ERA POOL IS 0.6937 AT dof 3, and it is close to the
    registered 0.7566 -- which is the reassuring outcome, not a contradiction. Two
    same-recipe families on the current draw give a spread within 9% of the number
    that has been in use. The floor was not wrong; it was measured elsewhere and happens
    to transfer.\n\nWHAT THIS CHANGES IN PRACTICE: very little, and that is worth
    stating plainly after several steps of alarm. The dense floor can keep being used
    at ~0.70-0.76. What it does establish is that the CURRENT-era estimate rests on
    dof 3 from the seed family alone, so cell 4 is not merely the last cell of a comparison
    -- it is also the fourth point of the only current-era dense floor that exists.\n\nCALIBRATION
    NOTE that survives independent of era: v36labels' dense captures read scale 1.32
    / 1.38 / 0.98 while v28affect01 reads 1.11 / 1.07. The incumbent is the better-calibrated
    arm on this draw by a clear margin, and two of v36's three seeds sit at or past
    the [0.85,1.35] edge."
- at: '2026-09-01T12:24:06+00:00'
  text: "THE SEED FAMILY IS COMPLETE. VERDICT: NO PROMOTION -- v36labels is INSIDE
    NOISE on both draws and clearly WORSE on held-out subjects. All six cells closed
    14:32; the population gate confirms all six scored identical rows.\n\nSUPABASE
    (in-sample -- supabase is one TRAIN subject, so not a generalisation number)\n
    \ v36labels    1.862 / 2.501 / 2.408   mean 2.257\n  v28affect01  3.030 / 3.065
    / 2.415   mean 2.837\n  delta -0.580, SE 0.702, z -0.83 -> INSIDE NOISE\n\nDENSE,
    full draw\n  v36labels    4.017 / 2.825 / 2.374   mean 3.072   scale 1.32 / 1.38
    / 0.98\n  v28affect01  2.851 / 2.781 / 3.110   mean 2.914   scale 1.11 / 1.07
    / 1.05\n  delta +0.158, SE 0.618, z +0.26 -> INSIDE NOISE\n  v36labels s43 sits
    outside [0.85,1.35] where the floor is not valid\n\nDENSE, HELD-OUT ROWS ONLY
    (23 rows, the only generalisation signal available)\n  v36labels    4.960 / 4.937
    / 2.395   mean 4.097\n  v28affect01  2.593 / 1.906 / 3.306   mean 2.602\n  delta
    +1.495 to the incumbent -- 2.6x the in-sample delta and in the OPPOSITE direction
    from the supabase reading\n\nTHE THREE READINGS DISAGREE, AND THAT IS ITSELF THE
    RESULT. In-sample deployment: v36 leads by 0.580. Full dense: tie. Unseen subjects:
    incumbent leads by 1.495. That ordering is what a memorisation advantage looks
    like -- the label clauses help most where the subject was seen and hurt where
    it was not.\n\nCALIBRATION, read first: v36labels dense scale 1.32 / 1.38 / 0.98
    against the incumbent's 1.11 / 1.07 / 1.05. Two of three v36 seeds at or past
    the band edge; all three incumbent seeds comfortably inside. On held-out rows
    the separation is starker -- v36 reads 1.59 and 2.08 on its two losing seeds.\n\nDECISION:
    v28affect01 REMAINS THE INCUMBENT. v36labels is not promoted. The label-clause
    mechanism is not refuted -- its s44 seed reached 2.395 held-out, third-best in
    the whole table -- but it is not established either, and its seed-to-seed spread
    on unseen subjects (2.395 / 4.937 / 4.960) is far worse than the incumbent's (1.906
    / 2.593 / 3.306).\n\nWHAT THE FAMILY COST AND BOUGHT: four Phase-C cells, ~4 hours.
    It bought the campaign's first paired verdict read against a RETRAIN floor rather
    than a finite-draw sigma, the first current-era dense floor estimate (0.6937 at
    dof 3), and the discovery that the promotion comparison is not symmetric under
    the subject leak."
- at: '2026-09-01T14:12:48+00:00'
  text: "v37cov AND v38prompt CLOSED, BOTH INSIDE NOISE, NEITHER PROMOTES.\n\n  arm
    \        supa MAE (scale)   dense MAE (scale)   HELD-OUT dense\n  v38prompt   3.049
    (1.00)       2.886 (1.09)        2.644 (0.95)\n  v37cov      3.078 (0.95)       2.846
    (1.07)        2.980 (0.99)\n  --- reference ---\n  v28affect01 2.837 (3-seed)
    \     2.914 (3-seed)      2.602 (3-seed)\n  v36labels   2.257 (3-seed)      3.072
    (3-seed)      4.097 (3-seed)\n\nEVERY CAPTURE IS IN-BAND ON CALIBRATION (0.95-1.09),
    worth stating: these two are better behaved than v36labels, whose dense seeds
    read 1.32/1.38/0.98 and held-out 1.59 and 2.08. Well-calibrated and unremarkable
    is a different failure from miscalibrated and flattering.\n\nv38prompt closed
    the train/serve prompt skew (EIV_RESOLVE_NESTED_CONTEXT=1, train budget 256 ->
    456, so the model trains on the same 317-token prompt it is tested on). Held-out
    2.644 against the incumbent's 2.602 -- a 0.042 bpm difference, far inside any
    floor. The 61-token skew was real and closing it changed nothing measurable at
    deployment. A POWERED NULL on a defect the vault had flagged as potentially load-bearing
    for the JSON-termination failures.\n\nv37cov tested coverage-aware sampler balancing
    (sampler_coverage_strength 1.0). Held-out 2.980 against 2.602 -- worse, inside
    noise. Consistent with the coverage-dilution law refuted earlier today on all
    five arms (slopes -0.24 to +0.13 against a predicted +1.0).\n\nTHE DAY'S TABLE
    ON THE ONLY GENERALISATION NUMBER (held-out dense):\n  v28affect01  2.602   <-
    incumbent, unbeaten\n  v38prompt    2.644\n  v37cov       2.980\n  v36labels    4.097\nFour
    candidates, no promotion. Each is inside noise or clearly worse, and the one closest
    (v38prompt) tested a defect that turned out not to bind.\n\nv39scaled produced
    NO numbers: it died in Phase A on a CLI flag already fixed on disk while it waited
    (rule_out editing-a-queued-drivers-flag-repairs-the-queued-run). Its hypothesis
    stays open and untested. Cost 4h43 of wall time, ZERO GPU -- the g0 gate stopped
    the chain before Phase B.\n\nSTILL RUNNING: v40roster (one-variable wide-roster
    test) and v41pl60state (state-detection mechanism arm, gates preregistered)."
---

## Description
MEASURED 2026-08-12 (commit 0940a9e, outputs/probes/{v4w,v5c,v5d}_emissions_supabase.json, 200 identical-draw supabase rows, production prompt, greedy decode).

THE FAILURE. On the production annotate-path population (optihealth_supabase, mean HR coverage 0.789, truth HR mean 58.3 bpm, never <45 bpm):
- v4w hr_raw MAE 16.72 (trivial supabase-median baseline 4.02, skill -12.70), emits exactly "30" on 119/200
- v5c MAE 20.83 (skill -16.81), "30" on 150/200
- v5d MAE 25.05 (skill -21.03), "30" on 179/200
Arm ranking v4w > v5c > v5d is the REVERSE of the dense 128-row draw (where v5c beat v4w 108/128, p<1e-4). Anton's field annotations detected this before the instrument did, because the eval draw was 97% lifesnaps+pmdata (84/40/3 of 128) while supabase is 223/6151 eval rows.

ROOT CAUSE 1 (proven mechanism, fix in flight): the hr-augmentation clamp in src/training/datasets.py mapped the below-window tail of the uniform target_peak draw onto lo_peak EXACTLY, so the augmented daily mean landed at exactly min_valid_hr_bpm=30.0 on ~36.5% of augmented samples => ~14.6% of ALL hr_raw LM targets were the literal string "30.0" (scratch/floor_atom_estimate.py; prob=0.40, range 40-182.72). The supabase days are the lowest-HR sparsest days, nearest the attractor. Conditional on ESCAPING the floor, the arms read supabase fine: v4w n=81 MAE 4.20 corr 0.839; v5c n=50 MAE 3.65 (beats trivial 4.39); v5d n=21 MAE 6.26.

ROOT CAUSE 2 (separate defect, verified on a different regime): coverage dilution. Encoder zero-fills masked cells in normalized space (zero = population median 71.8), so a day with HR coverage c pools to 71.8 + c*(truth-71.8). Verified on wearable_exam_stress sample 10313: coverage 0.129, predicted 77.7, emitted 82.0 (~91% of the 36-bpm error). REFUTED as the supabase mechanism: regression of signed error on (cov-1)*(truth-med) gives slope -1.5/-0.47/+0.42 vs predicted 1.0, R2<=0.11. Two defects, not one.

ALSO OBSERVED on production rows: state slot is a near-constant prior with arm-dependent polarity (v5c "stress" 139/200, v4w "neutral" 198/200, v5d "flow" 65/200); both arms confabulate sleep/rr/temp slots on days where those channels are absent (though sparse days can drop schema slots entirely -- conditional slot emission partially works).

GOAL: restore deployment-distribution hr_raw accuracy to v4w's level (MAE <=6.14, its dense-draw level) and better, with floor-emission rate <10%, without regressing the dense population. The standing eval defect (draws must stratify or restrict to supabase) is part of this problem.

## Synthesis
STATE: root cause 1 is fixed and verified. Root cause 2 is REFUTED, and its one surviving "architectural gap" turned out to describe a layer rather than the model. The constraint this synthesis used to impose on every number here was re-measured and is 3.66x too wide for today's arms. Exactly one live question remains, and it is queued.

ROOT CAUSE 1 (fixed, unchanged). The hr-augmentation clamp mapped the below-window tail of the uniform target_peak draw onto lo_peak exactly, so ~36.5% of augmented samples landed at exactly min_valid_hr_bpm=30.0 => ~14.6% of ALL hr_raw LM targets were the literal string "30.0". The atom fix (a71034c) removed it and REPLICATES ACROSS SEEDS: exact-30 emissions 0/200 at seeds 42/43/44. The arms could always READ supabase once they escaped the floor (v4w n=81 MAE 4.20 corr 0.839; v5c n=50 MAE 3.65 against a 4.39 trivial) -- the unconditional MAE conflated attractor capture with reading skill.

ROOT CAUSE 2, COVERAGE DILUTION: REFUTED 2026-09-01. The law predicts signed error (emit-truth) = (c-1)*(truth-median) with slope 1.0. Regressed on the dense draw for all five current arms (scratch/_dilution_law_audit.py): slopes -0.093, -0.181, +0.131, -0.209, -0.236, R2 <= 0.062. Four of five NEGATIVE against a predicted +1.0, and the observed biases do not agree in SIGN across arms fed identical rows. The record had already refuted it on supabase for the July arms (slopes -1.5/-0.47/+0.42) but kept it open as 'verified on a different regime'; it now fails on both populations and every arm ever measured. The premise weakened too: the law needs the zero fill at the population median -- it still is (-0.02 SD) -- but hr_mean now carries 13.35% of first-layer input variance instead of 96.07%, so pooling on one channel cannot dominate an emission.

THE "MASK-BLIND ENCODER" GAP IS ABOUT LAYER PLACEMENT, NOT BLINDNESS (corrected 2026-09-01, after this synthesis asserted it twice). Presence ALREADY reaches the representation: cross_modal.py:224 takes patch_presence [B,N]/[B,C,N] as an explicit forward argument, :275 runs a value_presence_fusion module, channel_fusion.py consumes channel_mask with 10 key_padding_mask uses, and pipeline.py passes channel_mask 9 times. Decisive: cross_modal.py:394 RAISES when presence is missing unless allow_presence_blind=True is set -- a guard that must be explicitly disabled exists only because presence is normally REQUIRED, and the emission audit sets it only to reproduce 'the pre-2026-08-28 broken serving path' as a deliberate ablation. So an arm adding a mask input to TemporalEncoder would test REDUNDANCY, and its null would read as 'presence does not matter' when the truth is 'presence was already there'. What survives: at the FIRST layer FlexMLP is nn.Linear over the channel axis with no mask term, so a zero-filled cell is arithmetically identical to a measured population-median cell THERE. The honest question is whether EARLY presence beats LATE presence -- a much narrower claim that needs its own justification before it earns GPU.

THE SEED-FLOOR CONSTRAINT WAS A PROPERTY OF A REGIME, NOT OF THE METRIC. The old text said sigma 3.145 (k=2 => 6.29 bpm) exceeds the 4.02 trivial baseline, therefore no single-seed comparison is interpretable. That figure came from the v7noatom family (11.54/15.78/9.49) at a mean MAE of 12.27, taken as range/2 on n=3. Re-measured on three same-recipe families at today's error level, 10 seeds (scratch/_supa_noise_floor.py): pooled within-family sigma 0.8601 at a grand mean of 3.484. The spread is PROPORTIONAL to the level (CV 0.256 then, 0.247 now), so a sigma from arms erroring at ~12 bpm was gating arms erroring at ~3. At k=2 the band is +/-1.720, inside the 4.02 trivial baseline: single-seed supabase comparisons between HEALTHY arms are interpretable again. Estimator note: range/2 on n=3 understates SD by ~15% (d2=1.693).

IT DOES NOT RESCUE THE CAMPAIGN'S HEADLINE CLAIMS. Against 0.8601: v33dispersion 2.142 vs incumbent 3.030 is z -1.03, v34nopeak 2.095 is z -1.09, v31detached 2.699 is z -0.38. All INSIDE noise at k=2. The single-seed supa wins are directional only; the running v36labels seed family is the instrument that settles them.

A SECOND FLOOR EXISTS FOR THE DENSE DRAW, WITH A DOMAIN. eiv_dense128_hr_raw_mae_bpm carries sigma 0.7566 (k=2 => 1.513), over the seeds whose emit_sd/true_sd sits in [0.85, 1.35]. Two ab_ctrl seeds emit at 2.33x and 2.17x the true spread; they are not broken captures (json_ok 128/128, distinct 36/35, corr 0.70) but calibration failures with their own signature. Pooling them would have set sigma at 2.57 bpm -- wider than the entire asserted no-harm band -- and made every future arm unfalsifiable. ALWAYS read the scale ratio before the MAE.

WHAT IS STILL OPEN -- ONE THING.
Whether the wide Phase C roster costs deployment. FOUR arms carry it (v11full/v12conly/v13flat/v14long) and all four are catastrophic (dense 21.3-24.6 vs 2.851, z +24..+29, emit/true 0.26-0.57 or 2.03-2.76 -- all outside the calibration domain). But each differs from band04 in FIVE keys, two separately load-bearing (a different prompt; seed 42->43, which alone spans the seed floor). 'The roster fails' and 'one of the other four fails' predict the identical table. v40roster is queued as the one-variable test, gates preregistered.

BOOKKEEPING, because this synthesis carried a stale line that I copied forward once before checking: the old text ended with '10 of 13 experiments carry no interpretation'. CLEARED -- progress() reports interpretation_debt: [] and all 13 have an ## Interpretation section, verified over the vault markdown by matching experiments to the problem through hypothesis_id (experiment records carry no problem_id). Do not re-derive that debt from this paragraph.

## Updates
- **2026-09-01T08:20:25+00:00** — THE SEED FLOOR THIS PROBLEM'S SYNTHESIS RESTS ON WAS MEASURED IN A REGIME THAT NO LONGER EXISTS. Re-measured 2026-09-01 (scratch/_supa_noise_floor.py) on three same-recipe families already on disk, same frozen 200-row supabase draw, 10 seeds:

  ab_ctrl  6.031 / 2.836 / 3.075 / 4.174   sd 1.456
  w6avg    3.187 / 2.861 / 2.742           sd 0.230
  w6msk    3.389 / 3.351 / 3.737           sd 0.213
  pooled within-family sigma 0.8601 (dof 7), grand mean MAE 3.484

The registered floor was 3.145, taken as range/2 on the v7noatom family (11.54/15.78/9.49) at a mean MAE of 12.27. The spread turns out to be PROPORTIONAL to the error level, not additive: CV is 0.256 then and 0.247 now -- the same fractional spread at a level 3.5x lower. So a sigma measured on arms erroring at ~12 bpm was being applied to arms erroring at ~3.

THE SYNTHESIS'S HARDEST SENTENCE NO LONGER HOLDS. It reads: 'The seed band therefore EXCEEDS the trivial baseline (4.02): no single-seed arm comparison on this diagnostic is interpretable, and v7noatom's own 11.54 is one draw from an unknown-width distribution.' At the measured floor the k=2 band is +/-1.720 bpm, comfortably inside the 4.02 trivial baseline. Single-seed supabase comparisons BETWEEN HEALTHY ARMS are interpretable again. The old statement was correct for the arms it was drawn on -- a noise floor is a property of a REGIME, not of a metric, and this one stopped applying when the arms got better.

IT DOES NOT RESCUE THE CAMPAIGN'S HEADLINE CLAIMS, WHICH IS THE OTHER HALF OF THE POINT. Re-read against 0.8601: v33dispersion 2.142 vs incumbent 3.030 is z -1.03; v34nopeak 2.095 is z -1.09; v31detached 2.699 is z -0.38. All INSIDE noise at k=2. The supabase wins that looked decisive on a single seed are DIRECTIONAL ONLY, and the pending v36labels seed family is the right instrument for settling them.

ESTIMATOR NOTE: range/2 on n=3 understates the SD by ~15% (the unbiased range-to-sigma factor at n=3 is d2=1.693). The replacement is a pooled within-family SD.

STILL OPEN AND UNTOUCHED BY THIS: root cause 1 (floor atom) remains fixed; root cause 2 (coverage dilution) was refuted today on all five current arms (slopes -0.24 to +0.13 against a predicted 1.0, R2 <= 0.062, rule_out coverage-dilution-is-the-live-second-root-cause-of-deployment-error). What survives from root cause 2 is the architectural observation that TemporalEncoder.forward(x) has no mask input, which deserves its own arm.
- **2026-09-01T09:32:21+00:00** — WITHIN-ARM SEED SPREAD FOR v36labels, MEASURED BEFORE THE FAMILY FINISHES (scratch/_within_arm_seed_spread.py). Cells 1-2 are done (v36labels at seeds 43 and 44); cells 3-4 (the incumbent at the same seeds) have not run, so the PAIRED comparison is not available and the readout correctly refuses to report it. But one thing is already knowable: how far a single arm moves when only the seed changes.

  SUPA (floor sigma 0.8601)          n     MAE    corr   scale
    v36labels  s42                 200   1.862   0.893   0.94
    v36labels  s43                 200   2.501   0.866   1.08
    v36labels  s44                 200   2.408   0.850   0.89
    v28affect01 s42                200   3.030   0.793   0.93
    -> v36labels own-seed range 0.639, sd 0.345

  DENSE (floor sigma 0.7566)         n     MAE    corr   scale
    v36labels  s42                 128   4.017   0.844   1.32
    v36labels  s43                 128   2.825   0.887   1.38  <- scale outside [0.85,1.35]
    v28affect01 s42                128   2.851   0.937   1.11
    -> v36labels own-seed range 1.192, sd 0.843, which EXCEEDS the registered dense floor 0.7566

WHAT THIS DOES TO THE PROMOTION CASE. v36labels' headline was supa 1.862 against the incumbent's 3.030, a margin of 1.168 bpm. Its own seeds span 1.862-2.501 on the same draw, so roughly HALF the claimed margin is seed. The remaining gap is still directionally in v36's favour on supa, but it is no longer the clean 1.168 the single-seed capture suggested.

On DENSE the picture is worse for the claim and better for the arm than the single seed said: s42 read 4.017 (a 1.166 loss to the incumbent) while s43 reads 2.825 (a 0.026 WIN). The dense 'loss' that has been quoted all campaign is within this arm's own seed noise, and the sign FLIPS between its two seeds. Any statement of the form 'v36labels wins supa and loses dense' rests on one seed of each.

CALIBRATION CAVEAT, which must be read first: v36labels s43 emits at 1.38x the true spread, just outside the [0.85, 1.35] domain where the dense floor is valid. Its 2.825 is therefore not directly comparable to the incumbent's 1.11-ratio 2.851 -- the arm is over-expressing range, and MAE alone hides that. s42 sits at 1.32, inside but near the edge. Same failure mode the four wide-roster arms showed in extreme form (0.26-0.57 and 2.03-2.76).

NOTHING IS PROMOTED ON THIS. The paired verdict needs cells 3 and 4, which are running. Recorded now because the within-arm spread is the cheaper half of the answer and it was already on disk.
- **2026-09-01T09:35:46+00:00** — THE CAMPAIGN'S HEADLINE DELTAS ARE MEASURED AGAINST THE WRONG NOISE SOURCE. There are two, they answer different questions, and every 'DEMONSTRATED' verdict in this campaign used the smaller one (scratch/_total_noise_readout.py).

  FINITE-DRAW sigma -- 'if I re-drew the 128/200 eval rows from the SAME two trained checkpoints, would the sign hold?' This is what scripts/paired_bootstrap.py reports, and its own footer warns it is a LOWER BOUND.
  SEED floor -- 'if I RETRAINED both arms at a different seed, would the sign hold?' Measured today: 0.7566 dense, 0.8601 supa.

A promotion is a claim about the RECIPE, not about one pair of checkpoints, so the seed floor binds. Independent components add in quadrature: sigma_total = sqrt(seed^2 + draw^2).

  DENSE (seed floor 0.7566)        dMAE   s_draw   s_tot   z_draw   z_tot
    v36labels                    +1.166    0.438   0.874    +2.66    +1.33
    v33dispersion                +0.225    0.345   0.832    +0.65    +0.27
    v34nopeak                    +1.068    0.366   0.840    +2.92    +1.27
    v35wide                      +1.270    0.364   0.840    +3.49    +1.51

  SUPA (seed floor 0.8601)
    v36labels                    -1.168    0.160   0.875    -7.31    -1.33
    v33dispersion                -0.887    0.192   0.881    -4.63    -1.01
    v34nopeak                    -0.935    0.189   0.881    -4.96    -1.06
    v35wide                      -0.424    0.137   0.871    -3.09    -0.49

EIGHT deltas. Every one clears k=2 on the draw sigma -- v36labels' supa win reads z -7.31 -- and NOT ONE clears it on the total. The largest is |z_tot| 1.51.

WHAT THIS MEANS, STATED PLAINLY: the wins are real FOR THOSE CHECKPOINTS and unestablished FOR THE RECIPE. Promoting on z_draw would promote a pair of checkpoints, not a method. This is the same failure the campaign documented once before -- 'the incumbent's headline MAE lead was 0.89 sigma, not a difference at all' -- now reaching the whole arm table rather than one comparison.

WHY THE SEED FLOOR DOMINATES: s_draw is 0.14-0.44 while the seed floor is 0.76-0.86, so the total is 1.9-5.4x the draw sigma and is almost entirely retrain variance. Bootstrapping the eval rows harder cannot help; only more seeds can.

CONSEQUENCE FOR THE RUNNING FAMILY: this is exactly what cells 3-4 are for. With both arms at three seeds the comparison becomes a paired difference of MEANS, whose standard error is sigma_seed/sqrt(3) ~ 0.44 dense -- roughly the draw sigma, and small enough that a real 1.17 bpm effect would clear k=2. The family is the right instrument and it is running.

NOTHING HERE RETRACTS A MEASUREMENT. Every number stands; what changes is which sigma it is read against.
- **2026-09-01T09:39:20+00:00** — THE THIRD SEED LANDED, AND IT CHANGES THE HEADLINE IN BOTH DIRECTIONS (scratch/_three_seed_readout.py). Cell 2's dense capture completed, so v36labels now has seeds 42/43/44 on both draws. The incumbent still has one seed (cell 3 started 12:36), so this is NOT the paired test -- but three things are now settled.

1. THE VARIANCE ALARM IS RESOLVED, AND IT WAS NOISE.
   dense: 3-seed sd 0.849 against floor 0.7566 = 1.12x
   supa : 3-seed sd 0.345 against floor 0.8601 = 0.40x
   The 2-seed dense estimate was 0.843 (1.11x) and I flagged it as EXCEEDING the floor. At n=3 it is 0.849 -- essentially unchanged, and well inside what a pooled floor covers. On supa the arm is less than half as noisy as the floor. v36labels is not an unusually noisy recipe.

2. THE DENSE 'LOSS' LARGELY EVAPORATES, AND THE SUPA 'WIN' SHRINKS.
   dense v36labels [4.017, 2.825, 2.374]  mean 3.072, SE 0.490
   supa  v36labels [1.862, 2.501, 2.408]  mean 2.257, SE 0.199
   Against the incumbent's single seed (dense 2.851, supa 3.030):
     dense delta +0.221  (was +1.166 on seed 42 alone)
     supa  delta -0.773  (was -1.168 on seed 42 alone)
   Seed 42 was the arm's WORST dense seed and its BEST supa seed. The single-seed capture that framed this whole comparison was unlucky in one direction and lucky in the other, simultaneously.

3. NEITHER IS OUTSIDE NOISE, EVEN ON THIS FAVOURABLE READING.
   Treating the incumbent's unmeasured retrain variance as the registered floor:
     dense SE 0.901 -> z +0.25
     supa  SE 0.883 -> z -0.88
   Both inside k=2. And this comparison FLATTERS v36: it averages three seeds of one arm against one draw of the other. The paired test from cells 3-4 will be stricter, not looser.

CALIBRATION, read first as the rule requires: v36labels' dense scale ratios are [1.32, 1.38, 0.98]. Two of three sit at or outside the [0.85, 1.35] edge where the dense floor is valid. The incumbent's is 1.11. So part of the dense story is an arm that over-expresses range on two seeds of three -- a property MAE alone would not show.

WHAT THIS MEANS FOR THE PROMOTION QUESTION AS ASKED. 'v36labels wins supa by 1.168 and loses dense by 1.166' was a statement about seed 42. At three seeds it becomes 'wins supa by 0.773, loses dense by 0.221, neither outside noise'. Nothing is promoted; the direction on supa survives and is worth the remaining two cells.
- **2026-09-01T10:00:30+00:00** — THE QUEUED ARMS WILL BE COMPARABLE TO THE INCUMBENT, CHECKED BEFORE THEY RUN (scratch/_queued_arm_code_drift.py).

The w6 failure was control-on-one-commit vs treatment-on-another, 1,155 lines apart. Four queued arms could reproduce it: v38prompt and v39scaled both run --profile lora_proxy_12ch_wl_band04, the INCUMBENT'S OWN profile, so verify_one_variable has nothing to compare -- their variable is not a profile key. That is why neither carries the gate, and it is correct. But it means the only thing separating each arm from the incumbent is CODE, and the incumbent's Phase C checkpoint was written on c3f482746ef3 (08-31 06:10) while the arms will run on cc29680 or later.

src/ CHANGES IN THAT RANGE: cli_train.py +6, datasets.py +414, gemma_runtime.py +31, pipeline.py +22 (468 insertions).

EVERY ONE IS DORMANT FOR band04, BY ONE OF TWO CHECKABLE MECHANISMS:
  env flags   EIV_RESOLVE_NESTED_CONTEXT -> exported ONLY by run_v38_prompt.sh
              EIV_SCALED_LABEL_CLAUSES   -> exported ONLY by run_v39_scaled.sh
  new params  detached_probes, max_clauses, sampler_coverage_strength,
              state_peak, wide_channels -- none set in lora_proxy_12ch_wl_band04,
              so all resolve to their defaults

So each flag is exported by exactly the arm whose variable it IS. That is the intended structure: the recipe is frozen for everyone else and the arm turns on its own one thing.

HAND-READ THE UNCONDITIONAL EDITS, since the script cannot cover them: pipeline.py's changes are the two effective_* reads (profile-gated) and probe_input = fused.detach() if detached_probes else fused, which is False by default -- fused is passed exactly as before. gemma_runtime's scan list is inside the EIV_RESOLVE_NESTED_CONTEXT branch. Nothing unconditional reaches a band04 run.

SCANNER BUG FOUND AND FIXED IN PASSING, in the harder direction: the first version reported ONE flag where there are two. EIV_RESOLVE_NESTED_CONTEXT is written across a line break, and in a diff the continuation line begins with '+', so neither a plain regex nor re.S matched -- the pattern needs (?:\n\+\s*)?. An UNDER-reporting scanner is worse than an over-reporting one: this one printed a short, clean, plausible list. Same family as this morning's guard that reported ALL CLEAR after checking zero drivers.

WHAT REMAINS TRUE: the incumbent's OWN numbers (dense 2.851, supa 3.030) were earned on 08-31 code. Nothing in this range changes a band04 run, so they stand -- but that is now a checked statement rather than an assumption.
- **2026-09-01T10:04:07+00:00** — WHAT A LABEL SLOT IN json_slots COULD ACTUALLY CARRY, MEASURED ON BOTH ROSTERS (scratch/_label_slot_feasibility.py). Successor question to 'Phase C emits 13 slots and one comes from a label': before writing a slot renderer, measure what would fill it.

REACH, band04 (7,592 windows) vs wide (9,530):
  stress_level      1665  21.9%  ->  1780  18.7%   [pmdata, +2 sources wide]
  readiness         1665  21.9%  ->  1665  17.5%   [pmdata]
  sleep_duration_h  1665  21.9%  ->  1665  17.5%   [pmdata]
  sleep_quality     1665  21.9%  ->  1665  17.5%   [pmdata]
  mood_ordinal      1665  21.9%  ->  1665  17.5%   [pmdata]
  fatigue           1665  21.9%  ->  1665  17.5%   [pmdata]
  valence              0   0.0%  ->  1298  13.6%
  arousal              0   0.0%  ->   609   6.4%
  stai_state         242   3.2%  ->   257   2.7%   [lifesnaps]
  everything else  <= 2.7% on the wide roster

THE ROSTER WIDENING DILUTES EVERY EXISTING LABEL CHANNEL. 9,530 windows against 7,592: the six pmdata channels each fall from 21.9% to 17.5% because the numerator is unchanged (the same 1,665 pmdata windows) while the denominator grows by 1,938. The wide roster does not add label coverage -- it adds windows carrying AFFECT labels and dilutes the ones carrying EMA labels. valence 0 -> 1,298 is real and is bought at -4.4pp on six other channels.

SHARED-AXIS CHECK (criterion 3), source medians:
  stress_level  brain_wearable 1.49 / nurse_stress 2.00 / pmdata 3.00   spread 1.507
  valence       case 5.15 / dapper 3.33 / k_emocon 3.00 / popane_s3 5.36 /
                s5 5.68 / s6 4.95 / s7 6.44                            spread 3.435
  arousal       case 5.15 / dapper 3.00 / k_emocon 3.10                spread 2.153
  stai_state    lifesnaps 48.0 / wesad 12.2                            spread 35.800
  age           dreamt 58.69 / popane 19.5-23.0                        spread 39.195

stai_state's 48.0 vs 12.2 is not a population difference -- it is two instruments under one channel name, on 2.7% of rows. age's 58.7 vs ~21 IS a real population difference (sleep-clinic cohort vs undergraduates) and is a subject trait, not an epoch observation.

THE HONEST SHORTLIST FOR A LABEL SLOT IS SHORT:
  - the six pmdata channels at 17-22%, all on the SAME 1,665 windows (one indicator six ways, established earlier), and three of them -- sleep_duration_h, sleep_quality, readiness -- restate what deep_pct/rem_pct/awake_pct already emit from the tensor. That leaves stress_level, fatigue, mood_ordinal as genuinely new supervision on ~1 row in 5.
  - valence/arousal, wide roster only, 13.6%/6.4%, and only after scale_to_declared_range -- which is exactly what v39scaled is testing on the CAPTION path right now.

WHAT THIS MEANS FOR THE STANDING GOAL: 'all labels in all three phases' is not reachable by a target change either, as literally stated. 26 of the 35 declared channels reach under 3% of the wide roster and most reach a single dataset. What IS reachable is a slot for the two or three channels with real coverage -- a much smaller claim than the goal as written, and it should be stated as such rather than quietly redefined.

ORDERING CONSEQUENCE: v39scaled settles whether shared-axis rendering helps on the caption path before any of this is worth building into the target. If scaled clauses do not help in A/B, a json_slots label slot inherits the same axis problem with less data.
- **2026-09-01T10:28:59+00:00** — THE INCUMBENT REPRODUCES ITSELF TO 0.035 bpm ACROSS SEEDS (scratch/_incumbent_pair_spread.py). Cell 3 landed, so v28affect01 now has two supabase captures on the same frozen 200-row draw:

  v28affect01 s42   MAE 3.030   corr 0.793   scale 0.93
  v28affect01 s43   MAE 3.065   corr 0.774   scale 0.98
  |s42 - s43| = 0.035

Against the registered floor 0.8601 the expected absolute difference between two seeds is E|d| = sigma*sqrt(4/pi) = 0.971. The observed 0.035 is 0.04x that -- twenty-seven times tighter than the floor predicts. Both captures are in-band on calibration (0.93, 0.98) so neither is a broken emission.

WHAT THIS SETTLES: the incumbent's headline 3.030 was not a lucky capture. Every comparison in this campaign is anchored on that number and none of them had ever checked it against a retrain.

WHAT IT DOES NOT SETTLE, and the distinction matters: one pair cannot distinguish 'this recipe is quieter than the floor's families' from 'this pair was lucky'. The floor stays 0.8601 until a third seed lands. Lowering it on two points would be the same error as this morning's 2-seed sd alarm, run in the flattering direction instead of the alarming one -- and it would make every future arm easier to promote, which is exactly the direction where a mistake is expensive.

STATE OF THE PAIRED COMPARISON, 3 vs 2 and still incomplete:
  v36labels   1.862 / 2.501 / 2.408   mean 2.257  n=3
  v28affect01 3.030 / 3.065           mean 3.047  n=2
  delta -0.790, SE at the registered floor 0.785, z -1.01
The readout correctly refuses a verdict while cell 4 is missing. But note the direction of the remaining uncertainty: if the incumbent really is a 0.035-spread recipe, the SE that the floor imposes is far too wide, and the honest reading of the delta will depend on which sigma cell 4 supports. That is a question the family was built to answer and it is one cell from answering it.
- **2026-09-01T10:30:31+00:00** — THE TWO NOISE FLOORS WERE COMPUTED UNDER DIFFERENT RULES, AND THE SUPABASE ONE BREAKS THE RULE THE DENSE ONE ESTABLISHED (scratch/_floor_calibration_consistency.py). Found while trying to explain why the incumbent reproduces itself to 0.035 against a floor of 0.8601.

THE DENSE FLOOR'S OWN STATED RULE, from this problem's synthesis: eiv_dense128_hr_raw_mae_bpm carries sigma 0.7566 'over the seeds whose emit_sd/true_sd sits in [0.85, 1.35]'. Two ab_ctrl seeds were EXCLUDED for emitting at 2.33x and 2.17x, because 'pooling them would have set sigma at 2.57 bpm -- wider than the entire asserted no-harm band -- and made every future arm unfalsifiable'.

THE SUPABASE FLOOR APPLIED NO SUCH FILTER. Scale ratios of its ten captures, on the same frozen 200-row draw (true_sd 5.352 for every one, verified directly):
  ab_ctrl s42 1.61 | s43 0.70 | s44 0.83 | s45 1.36   <- ALL FOUR outside [0.85,1.35]
  w6avg   s43 0.62 | s44 0.85 | s45 0.91              <- s43 outside
  w6msk   s43 1.19 | s44 0.88 | s45 1.17              <- all in band

So five of ten captures behind the supabase floor are outside the calibration domain the dense floor defines, and the entire ab_ctrl family -- whose sd 1.456 is 7x the other two families' 0.230 and 0.213 -- is out on every seed.

POOLED SIGMA, both ways:
  ALL captures (the registered floor)      0.9680  dof 7   [ab_ctrl 1.456 | w6avg 0.230 | w6msk 0.213]
  IN-BAND only (the campaign's own rule)   0.1804  dof 3   [w6avg 0.085 | w6msk 0.213]

That is a 5.4x difference, and it explains the incumbent's 0.035 pair difference completely: at sigma 0.18 the expected |d| is 0.203, and 0.035 is an unremarkable draw. At 0.86 it looked like a 27x anomaly. The anomaly was in the floor, not the arm.

I AM NOT CHANGING THE REGISTERED FLOOR ON THIS, and the reason is the same one that makes the finding matter. dof 3 is very thin, the in-band set has no ab_ctrl representation at all, and a floor of 0.18 would make almost every delta in this campaign 'significant' -- including the eight that the total-noise readout just showed are not. A floor that flatters is worse than one that is too wide.

WHAT IT DOES ESTABLISH: the two floors are not comparable to each other, and any statement of the form 'this arm clears the supabase floor but not the dense one' has been comparing numbers computed under different admission rules. The honest next step is a supabase floor measured on in-band captures with enough seeds to have dof -- which cell 4 contributes to, and which the queued w6-style families could extend.

METHOD NOTE: the pooling itself assumes a COMMON sigma across families. With per-family sds of 1.456, 0.230 and 0.213 that assumption is already violated by a factor of 7 before any calibration filter is applied. Pooling heterogeneous families produces a number that estimates nothing in particular.
- **2026-09-01T10:34:29+00:00** — THE SEED FAMILY IS GENUINELY PAIRED -- VERIFIED ROW BY ROW BEFORE THE VERDICT (scratch/_family_draw_identity.py). Checked while cell 4 is still running, because this defect is invisible afterwards: two captures with the same n and plausible MAEs look identical whether or not they cover the same windows.

SUPABASE, five cells present:
  every capture n=200, id key 'idx', 200 unique ids
  pairwise against v36labels s42: shared 200, only-base 0, only-other 0, max|delta truth| 0.00e+00

DENSE, four cells present:
  every capture n=128, 128 unique ids
  pairwise: shared 128, only-base 0, only-other 0, max|delta truth| 0.00e+00

So all cells score the SAME rows and agree on every row's true value to the bit. A difference of means is a difference on identical data, and the SE derivation the verdict rests on is valid.

WHY THIS WAS WORTH A CHECK RATHER THAN AN ASSUMPTION: MAE is an average, so an id present in one cell and absent from another silently changes what the mean is over -- and dropping the hardest rows from one arm flatters it by exactly what those rows cost. The strongest test is the truth comparison: if two captures claimed the same id but disagreed on its true_mean, the id would not be a stable key and nothing paired could be computed from these files at all. max|delta truth| is exactly zero on all 9 comparisons.

REMAINING: v28affect01 s43 dense (in flight) and v28affect01 s44 on both draws. When they land the same check should be re-run before the verdict -- a fresh capture is a fresh opportunity for the draw to move.
- **2026-09-01T10:35:34+00:00** — THE INCUMBENT REPRODUCES ITSELF ON BOTH DRAWS, AND THE SECOND PAIR IS INDEPENDENT CONFIRMATION OF THE FIRST. Cell 3's dense capture landed, so v28affect01 now has two seeds on each draw:

  SUPA   s42 3.030 (scale 0.93) | s43 3.065 (0.98)   |d| = 0.035
  DENSE  s42 2.851 (scale 1.11) | s43 2.781 (1.07)   |d| = 0.070

Against the registered floors the expected absolute difference is E|d| = sigma*sqrt(4/pi):
  supa   expected 0.971, observed 0.035 -> 0.036x, implied sigma 0.031
  dense  expected 0.854, observed 0.070 -> 0.082x, implied sigma 0.062

When I recorded the supabase pair an hour ago I explicitly refused to read a variance from it, on the grounds that one pair cannot separate 'quiet recipe' from 'lucky pair'. The dense pair is a SECOND, INDEPENDENT draw -- different evaluation population, different rows, different capture run -- and it lands at 0.082x of expectation. Two independent pairs both landing under a tenth of expectation is no longer plausibly luck: P(both under 0.1x) under the floor's own model is well below 1%.

So the honest reading has moved: v28affect01 IS a quiet recipe, by roughly an order of magnitude, on both populations. Its implied sigmas (0.031 supa, 0.062 dense) are 20-30x below the registered floors.

ALL FOUR CAPTURES ARE IN-BAND on calibration (0.93, 0.98, 1.11, 1.07), so none of this is a broken-emission artefact.

I AM STILL NOT LOWERING THE REGISTERED FLOORS, and the reason is now sharper than 'thin evidence'. Today's proportionality test showed retrain spread is a property of the RECIPE -- per-family sd varies 58x across five families. The incumbent being quiet says nothing about v36labels, which measures 0.345 supa and 0.849 dense over three seeds each. A promotion decision needs BOTH arms' own spreads, which is exactly what the four-cell design produces and what cell 4 completes.

DENSE PAIRED STATE, 3 vs 2, still incomplete:
  v36labels   4.017 / 2.825 / 2.374   mean 3.072
  v28affect01 2.851 / 2.781           mean 2.816
  delta +0.256 (v36 WORSE), SE 0.691 at the registered floor, z +0.37
And the calibration warning stands: v36labels s43 emits at 1.38, outside [0.85,1.35], where the dense floor is not valid. Two of v36's three dense seeds sit at or past the edge (1.32, 1.38) while all four incumbent captures are comfortably inside.

DRAW IDENTITY RE-VERIFIED after the new capture: dense now has five cells, all n=128, 128 unique ids, max|delta truth| 0.00e+00 across every pairwise comparison. The family remains genuinely paired.
- **2026-09-01T10:41:01+00:00** — THE 5-7 bpm hr_peak GAP IS REAL AND IS NOT AN ARGUMENT FOR PROMOTION -- BOTH ARMS SIT AT OR BELOW A CONSTANT (scratch/_hr_peak_gap.py). Found by misreading the family report's tail, going to look for a bug, and finding none: the -5.774 was hr_peak, not hr_raw. Reading it properly surfaced a metric promotion is not scored on.

SUPA hr_peak, trivial baseline = predict the population median peak (17.410):
  v36labels    s42 16.057  skill +1.353  scale 0.61
               s43 17.730  skill -0.320  scale 0.82   <- worse than a constant
               s44 16.292  skill +1.118  scale 0.75
  v28affect01  s42 21.832  skill -4.422  scale 0.50   <- worse than a constant
               s43 24.342  skill -6.932  scale 0.49   <- worse than a constant

DENSE hr_peak, trivial 17.218:
  v36labels    15.751 (+1.466, 0.72) | 16.981 (+0.237, 0.90) | 17.673 (-0.456, 0.65)
  v28affect01  16.674 (+0.543, 0.86) | 16.886 (+0.332, 0.70)

SO THE SUPABASE GAP IS ENTIRELY THE INCUMBENT COLLAPSING. v28affect01 reads 21.8 and 24.3 against a constant's 17.4 -- 4.4 and 6.9 bpm WORSE than emitting one number for every row, on the deployment population. v36labels is merely near zero (+1.35, -0.32, +1.12). A gap between 'slightly better than a constant' and 'much worse than a constant' is a smaller loss, not a gain.

THE CALIBRATION COLUMN GIVES THE MECHANISM: every hr_peak capture emits at 0.49-0.90 of the true spread, all below the [0.85,1.35] band, and the incumbent's supabase captures are the most compressed at 0.50 and 0.49. The arms regress the peak toward a central value -- which is what produces a MAE worse than the median baseline, since the median IS the best constant. The incumbent's 21.8-24.3 means it emits a constant-ish value that is not the median.

CONSISTENT WITH THE RECORD, which has hr_peak skill negative on every seed (-2.88/-2.73/-3.16 on v9band04) and calls it an encoder excursion limit rather than a target-style property. This adds two things: (1) the limit is far worse on supabase than on dense for the incumbent (-4.4/-6.9 vs +0.5/+0.3), so it is population-dependent rather than purely architectural; (2) v36labels does NOT share the collapse, which is the first sign that the excursion limit is recipe-sensitive.

NOT PROMOTING ON THIS. hr_peak is not the deployment metric, both arms straddle zero skill, and the incumbent has n=2. Recorded because a 6.9 bpm sub-baseline result on the production population is a defect worth naming even when it is not the metric under test.
- **2026-09-01T10:48:26+00:00** — THE SUPABASE NOISE FLOOR WAS MEASURED ON LIFESNAPS. Every capture behind the registered floor 0.8601 is named `*_supa.json` and contains 200 rows of lifesnaps, not optihealth_supabase (scratch/_capture_join.py).

  emission_ab_ctrl_supa.json        200 rows  lifesnaps
  emission_ab_ctrl_s43/44/45_supa   200 rows  lifesnaps
  emission_w6avg_s43/44/45_supa     200 rows  lifesnaps
  emission_w6msk_s43/44/45_supa     200 rows  lifesnaps
  ---- versus the seed family, which IS supabase ----
  emission_v36labels_supa           200 rows  optihealth_supabase
  emission_v36labels_s43/44_supa    200 rows  optihealth_supabase
  emission_v28affect01_supa         200 rows  optihealth_supabase
  emission_v28affect01_s43_supa     200 rows  optihealth_supabase

All ten floor captures read HR coverage p50 0.862 (lifesnaps); the family reads 0.789 (supabase). Two different populations under one filename convention.

HOW IT WAS FOUND: I built a guard that resolves the scorer's bundle from its own source and attaches per-row dataset and coverage, after wasting an hour on a wrong-bundle join. Its first run printed the dataset column and the discrepancy was immediate. The guard was built to prevent a repeat of my own error and caught a larger, older one.

WHAT THIS INVALIDATES: the registered supabase floor sigma 0.8601 with dof 7, pooled over ab_ctrl (sd 1.456), w6avg (0.230) and w6msk (0.213). Those are LIFESNAPS retrain spreads. They may be a reasonable prior for supabase spread, but that is an assumption nobody stated, and the populations differ in HR coverage (0.862 vs 0.789) and sparsity structure.

IT ALSO EXPLAINS TODAY'S 27x PUZZLE CLEANLY. The incumbent reproduces itself to 0.035 bpm on supabase and 0.070 on dense against a floor of 0.8601 -- 27x and 12x tighter. I recorded that as 'the incumbent is a quiet recipe' and refused to lower the floor. The real explanation is that the floor describes a DIFFERENT POPULATION, and the supabase retrain spread has never been measured at all.

WHAT IS UNAFFECTED: the seed family itself. All four cells score optihealth_supabase, the draw-identity check passed (n=200, identical ids, max|delta truth| 0), and its internal comparisons are sound. What changes is that its verdict cannot be read against the registered floor -- the family must supply its own spread, which is exactly what a four-cell design does.

Also unaffected: every DENSE floor number. Those captures are the mixed 128-row draw and were never dataset-restricted.

IMMEDIATE CONSEQUENCE FOR THE PENDING VERDICT: read the v36-vs-v28 supabase comparison using the two arms' OWN seed spreads (v36 sd 0.345 over 3 seeds, v28 sd 0.025 over 2), not sigma 0.8601. The registered floor is a lifesnaps number and should be renamed as such rather than reused.
- **2026-09-01T10:49:46+00:00** — FULL CENSUS OF *_supa CAPTURES: 17 ARE LIFESNAPS, 19 ARE REALLY SUPABASE (scratch/_supa_capture_census.py). Every capture classified by the dataset of its own rows, joined through the scorer's declared bundle.

LIFESNAPS group (17 captures, HR cov p50 0.862), MAE mean 7.307, min 2.738, max 24.554:
  ab_ctrl s42/s43/s44/s45   6.031 / 2.836 / 3.075 / 4.174
  w6avg   s43/s44/s45       3.187 / 2.861 / 2.742
  w6msk   s43/s44/s45       3.389 / 3.351 / 3.737
  conly   s44/s45           3.538 / 2.738
  ab_wl 20.284 | c_patch120 s42/s43 23.595 / 24.554 | mc01 11.078 |
  v9band04_newdraw 3.044

OPTIHEALTH_SUPABASE group (19 captures, HR cov p50 0.789), MAE mean 4.143, min 1.862, max 10.058:
  v16fixed 10.058 | v17oldenc 4.279 | v18oldbundle 7.847 | v21chain 5.775 |
  v23aconly 5.414 | v24btripoff 5.282 | v25r30 3.203 | v27affect 4.688 |
  v30state 5.666 | v31detached 2.699 | v32detachaffect 4.090 |
  v33dispersion 2.142 | v34nopeak 2.095 | v35wide 2.606 |
  v28affect01 s42/s43 3.030 / 3.065 | v36labels s42/s43/s44 1.862 / 2.501 / 2.408

THE GOOD NEWS, AND IT IS SUBSTANTIAL: every arm the campaign actually reasons about -- the incumbent, v36labels, and the whole v30..v35 series -- is in the REAL supabase group. Their MAEs are deployment numbers and stand exactly as recorded. The mislabelling is confined to the floor families and to older arms (ab_*, w6*, conly, c_patch120, mc01).

THE BAD NEWS IS THE ONE ALREADY RECORDED: those floor families are precisely what sigma 0.8601 was pooled from, so the supabase floor is a lifesnaps floor. It is now also visible WHY nobody caught it -- the two populations have similar MAE ranges (lifesnaps 2.7-4.2 for the healthy arms, supabase 1.9-4.1), so no number looked out of place.

A SUPABASE-NATIVE SPREAD IS NOW COMPUTABLE from the 19 real captures, and it needs stating carefully: those are DIFFERENT ARMS, so their spread mixes recipe differences with retrain noise and is an upper bound, not a floor. The only same-recipe supabase families on disk are the seed family's own two: v36labels 1.862/2.501/2.408 (sd 0.345) and v28affect01 3.030/3.065 (sd 0.025). That is the honest basis for the pending verdict, and cell 4 doubles the incumbent's contribution.

WHAT NEEDS RESTATING RATHER THAN RE-RUNNING: the early-vs-late presence comparison (w6avg vs w6msk) was already retracted today for a code confound; it is now ALSO a lifesnaps comparison, not a deployment one. Both retractions point the same way -- that question is untested on the deployment population.
- **2026-09-01T10:54:32+00:00** — THE EVAL POOL IS UNCHANGED ACROSS THE 2026-08-30 REBUILD -- THE ARM TABLE AND THE FLOOR BOTH STAND (scratch/_eval_population_continuity.py). This closes the thread opened by the stale-index discovery, in the arms' favour.

THE WORRY: the eval bundle was rebuilt 2026-08-30 18:18, so captures before it index a different space than captures after. MAE needs no join, so the arm table freely mixes both eras. Safe only if the rebuild renumbered windows WITHOUT changing which windows the supabase draw contains.

THE TEST, from the captures alone, no re-scoring: every row carries true_mean and the draw is deterministic, so the sorted multiset of true values fingerprints the population a capture saw. Two captures of the same draw must agree on it however the indices are numbered.

RESULT:
  PRE-rebuild  17 captures, all one fingerprint
  POST-rebuild 19 captures, all one fingerprint
  PRE vs POST: same n (200), identical truth, max |difference| 0.000000
  PRE  truth mean 57.838  sd 5.352
  POST truth mean 57.838  sd 5.352

The two eras see the SAME 200 days. The rebuild moved windows within the tensor and did not alter the supabase pool's membership.

WHAT THIS RESTORES:
  - the arm table is a single ranking on one population, v16fixed through v36labels
  - the registered supabase floor 0.8601 stands; its MAEs came from these same 200 rows
  - the census's MAE columns stand -- only its dataset/coverage LABELS for pre-rebuild captures were wrong

WHAT REMAINS TRUE FROM THE EARLIER FINDING: a stale capture's idx still cannot be joined to the current bundle, and the guard refusing it stays. The distinction is now exact -- INDICES are invalid across the rebuild, VALUES are not.

NET EFFECT OF THE WHOLE THREAD: one guard, zero retracted arm results. I spent several steps chasing a defect confined to per-row joins while the headline numbers were never affected. Recording that plainly because two intermediate entries said otherwise, and a future reader should not inherit the alarm without the resolution.
- **2026-09-01T10:56:00+00:00** — THE DENSE DRAW DID CHANGE ACROSS THE REBUILD, UNLIKE SUPABASE -- AND EVERY ARM THE CAMPAIGN USES IS ON ONE SIDE OF IT (scratch/_dense_continuity_and_baseline.py). Applied the truth-fingerprint test to the dense draw, which is the riskier one: it is not dataset-restricted, so its rng(0) draw runs over the WHOLE bundle and any change in row count re-rolls every index.

  dense captures 44: 22 pre-rebuild, 22 post-rebuild
  PRE  reference v15exact_dense   n=128
  POST reference v16fixed_dense   n=128
  PRE vs POST: same n, identical truth FALSE, max |difference| 6.774
  PRE  truth mean 74.782  sd 9.797
  POST truth mean 74.358  sd 8.884

So the dense pool is NOT the same 128 days before and after. Supabase held because its pool is filtered by dataset and that dataset's membership did not change; dense is drawn from the whole tensor and moved.

WHAT SAVES THE CAMPAIGN HERE: all 22 POST captures agree with each other exactly -- zero disagreements. The 17 that disagree are all pre-rebuild (ab_*, w6*, conly, c_patch120, mc01, v9band04_newdraw). Every arm in the current comparison -- v16fixed through v36labels, the incumbent, both seed-family arms -- was captured after 08-30 and sits on ONE dense population.

CONSEQUENCE, narrow and real: a dense MAE from before 08-30 must not be ranked against one from after. That includes the w6avg/w6msk dense numbers, which is the third independent reason today's early-vs-late presence comparison does not stand (code confound, and now a population change).

TRIVIAL BASELINE RECOMPUTED ON THE CURRENT DENSE DRAW, because skill is a property of the draw and the draw moved: median 74.971 bpm, baseline MAE 6.986 bpm over 128 rows.

SKILL OF EVERY CURRENT-ERA ARM against it:
  v36labels s44   2.374  +4.611     v28affect01 s43  2.781  +4.205
  v36labels s43   2.825  +4.161     v28affect01 s42  2.851  +4.135
  v33dispersion   3.076  +3.910     v34nopeak        3.919  +3.067
  v36labels s42   4.017  +2.969     v35wide          4.121  +2.865
  ---- at or below the constant ----
  v31detached     7.312  -0.326     v25r30           7.388  -0.402
  v27affect       8.587  -1.601     v32detachaffect  9.006  -2.021
  v30state       15.085  -8.099     v23aconly       15.908  -8.922
  v21chain       17.534 -10.548     v24btripoff     18.652 -11.666
  v16fixed       24.519 -17.533     v18oldbundle    30.255 -23.269

The eight arms at the top are the only ones that beat a constant on dense, and the seed family occupies the top four. That is worth stating because 'v31detached had the best geometry' has been quoted in this campaign: on dense it is 0.33 bpm WORSE than emitting 74.971 for every row.
- **2026-09-01T10:58:48+00:00** — THE DENSE FLOOR WAS MEASURED ON THE PRE-REBUILD POPULATION, AND THE SEED FAMILY IS NOW THE ONLY CURRENT-ERA SOURCE (scratch/_dense_floor_era.py). This is the case the supabase check ruled out and the dense draw does not escape.

EVERY family behind the registered dense floor 0.7566 is OLD-era:
  ab_ctrl  s44 2.562 (scale 1.05) | s45 10.168 (2.17)        captured 08-20
  w6avg    3.560 (1.00) / 2.642 (1.03) / 3.334 (1.32)        captured 08-21
  w6msk    3.947 (1.33) / 2.439 (0.95) / 3.301 (1.09)        captured 08-21
All pre-date the 2026-08-30 rebuild, and the dense pool CHANGED at that rebuild (max |delta truth| 6.774). Unlike supabase, where the pool was byte-identical and the number transferred, here the floor describes 128 days that no current arm is scored against.

POOLED SIGMA BY ERA:
  OLD-era     families ab_ctrl(3), w6avg(3), w6msk(3)   sigma 2.5683  dof 6
  CURRENT-era families v36labels(3), v28affect01(2)     sigma 0.6937  dof 3

THE OLD-ERA NUMBER IS 2.5683, NOT 0.7566 -- because this pooling includes ab_ctrl s45 at scale 2.17, which the registered floor's own calibration rule excludes. That is consistent with the synthesis, which records exactly this: 'pooling them would have set sigma at 2.57 bpm'. So 0.7566 is the OLD-era in-band pool, and my recomputation here is the OLD-era unfiltered pool. Both describe the pre-rebuild population.

THE CURRENT-ERA POOL IS 0.6937 AT dof 3, and it is close to the registered 0.7566 -- which is the reassuring outcome, not a contradiction. Two same-recipe families on the current draw give a spread within 9% of the number that has been in use. The floor was not wrong; it was measured elsewhere and happens to transfer.

WHAT THIS CHANGES IN PRACTICE: very little, and that is worth stating plainly after several steps of alarm. The dense floor can keep being used at ~0.70-0.76. What it does establish is that the CURRENT-era estimate rests on dof 3 from the seed family alone, so cell 4 is not merely the last cell of a comparison -- it is also the fourth point of the only current-era dense floor that exists.

CALIBRATION NOTE that survives independent of era: v36labels' dense captures read scale 1.32 / 1.38 / 0.98 while v28affect01 reads 1.11 / 1.07. The incumbent is the better-calibrated arm on this draw by a clear margin, and two of v36's three seeds sit at or past the [0.85,1.35] edge.
- **2026-09-01T12:24:06+00:00** — THE SEED FAMILY IS COMPLETE. VERDICT: NO PROMOTION -- v36labels is INSIDE NOISE on both draws and clearly WORSE on held-out subjects. All six cells closed 14:32; the population gate confirms all six scored identical rows.

SUPABASE (in-sample -- supabase is one TRAIN subject, so not a generalisation number)
  v36labels    1.862 / 2.501 / 2.408   mean 2.257
  v28affect01  3.030 / 3.065 / 2.415   mean 2.837
  delta -0.580, SE 0.702, z -0.83 -> INSIDE NOISE

DENSE, full draw
  v36labels    4.017 / 2.825 / 2.374   mean 3.072   scale 1.32 / 1.38 / 0.98
  v28affect01  2.851 / 2.781 / 3.110   mean 2.914   scale 1.11 / 1.07 / 1.05
  delta +0.158, SE 0.618, z +0.26 -> INSIDE NOISE
  v36labels s43 sits outside [0.85,1.35] where the floor is not valid

DENSE, HELD-OUT ROWS ONLY (23 rows, the only generalisation signal available)
  v36labels    4.960 / 4.937 / 2.395   mean 4.097
  v28affect01  2.593 / 1.906 / 3.306   mean 2.602
  delta +1.495 to the incumbent -- 2.6x the in-sample delta and in the OPPOSITE direction from the supabase reading

THE THREE READINGS DISAGREE, AND THAT IS ITSELF THE RESULT. In-sample deployment: v36 leads by 0.580. Full dense: tie. Unseen subjects: incumbent leads by 1.495. That ordering is what a memorisation advantage looks like -- the label clauses help most where the subject was seen and hurt where it was not.

CALIBRATION, read first: v36labels dense scale 1.32 / 1.38 / 0.98 against the incumbent's 1.11 / 1.07 / 1.05. Two of three v36 seeds at or past the band edge; all three incumbent seeds comfortably inside. On held-out rows the separation is starker -- v36 reads 1.59 and 2.08 on its two losing seeds.

DECISION: v28affect01 REMAINS THE INCUMBENT. v36labels is not promoted. The label-clause mechanism is not refuted -- its s44 seed reached 2.395 held-out, third-best in the whole table -- but it is not established either, and its seed-to-seed spread on unseen subjects (2.395 / 4.937 / 4.960) is far worse than the incumbent's (1.906 / 2.593 / 3.306).

WHAT THE FAMILY COST AND BOUGHT: four Phase-C cells, ~4 hours. It bought the campaign's first paired verdict read against a RETRAIN floor rather than a finite-draw sigma, the first current-era dense floor estimate (0.6937 at dof 3), and the discovery that the promotion comparison is not symmetric under the subject leak.
- **2026-09-01T14:12:48+00:00** — v37cov AND v38prompt CLOSED, BOTH INSIDE NOISE, NEITHER PROMOTES.

  arm         supa MAE (scale)   dense MAE (scale)   HELD-OUT dense
  v38prompt   3.049 (1.00)       2.886 (1.09)        2.644 (0.95)
  v37cov      3.078 (0.95)       2.846 (1.07)        2.980 (0.99)
  --- reference ---
  v28affect01 2.837 (3-seed)      2.914 (3-seed)      2.602 (3-seed)
  v36labels   2.257 (3-seed)      3.072 (3-seed)      4.097 (3-seed)

EVERY CAPTURE IS IN-BAND ON CALIBRATION (0.95-1.09), worth stating: these two are better behaved than v36labels, whose dense seeds read 1.32/1.38/0.98 and held-out 1.59 and 2.08. Well-calibrated and unremarkable is a different failure from miscalibrated and flattering.

v38prompt closed the train/serve prompt skew (EIV_RESOLVE_NESTED_CONTEXT=1, train budget 256 -> 456, so the model trains on the same 317-token prompt it is tested on). Held-out 2.644 against the incumbent's 2.602 -- a 0.042 bpm difference, far inside any floor. The 61-token skew was real and closing it changed nothing measurable at deployment. A POWERED NULL on a defect the vault had flagged as potentially load-bearing for the JSON-termination failures.

v37cov tested coverage-aware sampler balancing (sampler_coverage_strength 1.0). Held-out 2.980 against 2.602 -- worse, inside noise. Consistent with the coverage-dilution law refuted earlier today on all five arms (slopes -0.24 to +0.13 against a predicted +1.0).

THE DAY'S TABLE ON THE ONLY GENERALISATION NUMBER (held-out dense):
  v28affect01  2.602   <- incumbent, unbeaten
  v38prompt    2.644
  v37cov       2.980
  v36labels    4.097
Four candidates, no promotion. Each is inside noise or clearly worse, and the one closest (v38prompt) tested a defect that turned out not to bind.

v39scaled produced NO numbers: it died in Phase A on a CLI flag already fixed on disk while it waited (rule_out editing-a-queued-drivers-flag-repairs-the-queued-run). Its hypothesis stays open and untested. Cost 4h43 of wall time, ZERO GPU -- the g0 gate stopped the chain before Phase B.

STILL RUNNING: v40roster (one-variable wide-roster test) and v41pl60state (state-detection mechanism arm, gates preregistered).

#deployment-distribution #optihealth #collapse #baseline
