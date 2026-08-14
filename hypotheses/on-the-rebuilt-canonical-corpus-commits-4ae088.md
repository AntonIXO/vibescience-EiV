---
created_at: '2026-08-08T12:38:38+00:00'
former_ids:
- on-the-rebuilt-canonical-corpus-commits-c948d5c-00787b2-4a59ca5-tensor-10338-144
gates:
- blocking: true
  description: 'PRIMARY. On the Phase B soft-token prefix, subject-grouped OOF on
    data/eval_v3_1min: eiv_peak_excursion_increment_r2(repaired) - eiv_peak_excursion_increment_r2(incumbent)
    >= +0.03. The 0.03 bar is set BELOW the 0.066 Phase-A seed spread deliberately,
    so passing it is necessary but NOT sufficient - see gate-seed-band-honesty.'
  diagnostic_id: null
  id: gate-peak-increment-improves
  mde: null
- blocking: true
  description: NO REGRESSION ON THE SATURATED CHANNEL. eiv_heldout_hr_r2(repaired)
    >= eiv_heldout_hr_r2(incumbent) - 0.02 on the same pool. hr_mean is already at
    0.97-0.99, so a drop here means the repaired allowlist broke alignment rather
    than improving it, and would void a peak-side win.
  diagnostic_id: null
  id: gate-hr-mean-no-regression
  mde: null
- blocking: true
  description: 'MECHANISM MUST BE ATTRIBUTED, NOT ASSUMED. For EACH of the three phases,
    report the measured mechanism quantity for both arms in one table: (A) eiv-dataset-provenance-shortcut-balacc
    with its chance rate and class composition, (B) the state_label x dataset purity
    table with the count of non-vacuous triplets, (C) the realised HR-label marginal
    logged by the trainer before step 0 plus the subject count. A phase whose mechanism
    quantity does not move in the predicted direction is reported as UNEXPLAINED even
    if its outcome metric improves.'
  diagnostic_id: null
  id: gate-per-phase-mechanism-attributed
  mde: null
- blocking: false
  description: HONESTY GUARD, non-blocking by design. Any per-arm delta whose absolute
    value is below the documented Phase-A seed spread of 0.066 must be written up
    as 'no evidence of difference at one seed', never as a refutation or a win. This
    gate exists because the prior H1 arm was recorded as refuted on a single-seed
    -0.071 that sat inside that band.
  diagnostic_id: null
  id: gate-seed-band-honesty
  mde: null
- blocking: false
  description: 'DELIVERABLE-SIDE REALITY CHECK. Report eiv_gen_bucket_acc_over_majority
    for BOTH arms with the majority baseline recomputed on the scored pool. The incumbent
    1-minute pipeline emitted -0.108, i.e. worse than a constant. If both arms remain
    negative, say so in the first line of the writeup: the allowlist did not fix the
    deliverable, whatever it did to the encoder.'
  diagnostic_id: null
  id: gate-emission-honesty
  mde: null
id: on-the-rebuilt-canonical-corpus-commits-4ae088
interventions:
- dataset-allowlist-repaired-all-phases
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: 'Phase B soft-token prefix, subject-grouped OOF on data/eval_v3_1min
    (n=5889, 127 subjects). Repaired-allowlist arm minus incumbent arm >= +0.03 absolute.
    Control that MUST be reported in the same table: R2(peak | true day mean) on the
    same pool, measured at +0.0040, so the increment is essentially the whole R2.'
- diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  direction: down
  magnitude_note: 'Already measured pre-training (CPU): 0.9945 (chance 0.1250) ->
    0.8252 (chance 0.1667) for the 6-dataset repaired list. Excess-over-chance 0.8695
    -> 0.6585. This is the Phase A mechanism claim and is NOT the primary outcome
    gate.'
- diagnostic_id: eiv_gen_bucket_acc_over_majority
  direction: up
  magnitude_note: Phase C emitted bucket, label-stratified, majority baseline recomputed
    on the SAME pool. Incumbent 1-minute end-to-end read -0.108 (0.4609 vs 0.569 majority).
    Predict the repaired arm is still NEGATIVE but less so; I do NOT predict it crosses
    zero, because the allowlist is an upstream data lever and the emission failure
    was localised to the readout.
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: hr_mean is already saturated (R2 0.97-0.99) and has no headroom.
    Predicting NO change is the control that keeps this experiment from being scored
    on a metric that cannot move. If hr_mean moves materially in EITHER direction,
    something other than the allowlist changed and the comparison is void.
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags: []
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- sensor-text
- medgemma
- epoch-resolution
- dataset-whitelist
- wearables
- baseline
---

## Statement
On the REBUILT canonical corpus (commits c948d5c + 00787b2 + 4a59ca5, tensor [10338,1440,12], git_sha a85d085), propagating ONE repaired dataset allowlist {lifesnaps, pmdata, optihealth_supabase, big_ideas_glycemic, mmash, dreamt} through ALL THREE phases improves the end-to-end pipeline over the incumbent per-phase allowlists, and does so by a DIFFERENT mechanism in each phase: (A) InfoNCE - by removing a near-perfect dataset-provenance shortcut; (B) triplets - by removing state labels that are 100%-pure dataset identifiers; (C) CLM - by raising subject diversity 81 -> 216 at an essentially unchanged target marginal. The prior H1 refutation (peak increment -0.071) was measured on the PRE-REBUILD corpus in which lifesnaps HR was HOURLY and 1808 windows carried zero sensor data, so it does not transfer.

## Rationale
MEASURED ON THE REBUILT CORPUS, 2026-08-08, pure CPU (scratch/audit_newcorpus_composition.py, scratch/audit_newcorpus_rates.py, scratch/h_dataset_mechanism.py, scratch/h_star_pool.py, scratch/h0_ladder_corrected.py).

0. THE CORPUS CHANGED UNDER THE OLD REFUTATION, WHICH IS WHY THIS IS NOT A RETRY.
   [13909,1440,12] -> [10338,1440,12]; 1808 zero-sensor windows -> 0; corpus hr_mean coverage 47.0% -> 65.9%.
   DECISIVE: lifesnaps is no longer HOURLY. Native HR modal inter-sample gap is now 1 minute (526451 of 526775 gaps), present coverage 0.579 -> 0.916, held fraction 0.000. The processor's intraday mongo-dump path replaced the hourly CSV. Consequence: the strict-clean eval pool goes 0 lifesnaps days -> 3519 of 5889 (59.8%), and problem lifesnaps-hourly-hr-splits-train-and-eval-populations-20260807 is materially obsolete for HR.
   Strict-clean pool rebuilt: n=5889 / 127 subjects, peak median 132.56 bpm, tachy(>=120) 75.1%.

1. PHASE A MECHANISM - PROVENANCE SHORTCUT, subject-grouped OOF RF on PRESENCE GEOMETRY ONLY (no physiological values):
     INCUMBENT 10-dataset list : bal-acc 0.9945 vs chance 0.1250 (8 classes, n=5907)
     REPAIRED 6-dataset list   : bal-acc 0.8252 vs chance 0.1667 (6 classes, n=6151)
   Excess-over-chance 0.8695 -> 0.6585. With encoder_format_version: with_presence_v1 feeding the presence flags in as input channels, InfoNCE is handed this fingerprint explicitly.
   BATCH-COMPOSITION AMPLIFIER (scratch/h_star_pool.py): stratified_batches = {lifesnaps:45, pmdata:12, '*':35}. The '*' bucket is 38% of EVERY batch. Incumbent: it is 249 samples (4.22% of the pool) at mean HR coverage 0.523, each seen 9.03x/epoch. Repaired+dreamt: 493 samples (8.01%) at mean HR coverage 0.685, each seen 4.75x/epoch. So the incumbent draws 38% of every contrastive batch from a 249-sample pool half of which is near-empty - i.e. near-pure provenance labels - which is the exact failure StratifiedDomainBatchSampler was written to prevent.

2. PHASE B MECHANISM - THE TRIPLET LABEL IS A DATASET IDENTIFIER. Triplets are mined on state_label. Under the INCUMBENT list there are 7 distinct states over 5907 gated samples, but 5847 (99.0%) are NONE and EVERY non-NONE state is 100% PURE by dataset: exam_stress=wearable_exam_stress (16), transient=wesad (15), n_back=brain_wearable_monitoring (8), high+medium=nurse_stress (8), neutral=big_ideas_glycemic (13). Those five datasets carry HR coverage 0.022-0.157, i.e. they are the emptiest days in the corpus. So the only non-vacuous triplets in Phase B are exactly the confounded ones, and the margin is spent separating provenance rather than physiological state.

3. PHASE C MECHANISM - SUBJECT DIVERSITY AT CONSTANT MARGINAL. Incumbent C list {lifesnaps,pmdata,optihealth_supabase}: n=5927 over 81 subjects, tachy 0.295 / elev 0.577 / normal 0.128, median peak 131.8. Repaired list: n=6051 over 116 subjects (216 with dreamt), tachy 0.302 / elev 0.572 / normal 0.126, median peak 132.2. optihealth_supabase alone is ONE subject supplying 269 days. dreamt adds 100 subjects and is the only night/sleep cohort in the corpus, absent from every phase.
   dryad_ifh_affect stays EXCLUDED in every arm: measured_cov 0.070 vs present_cov 0.313 (77.6% carried cells) = 15-minute native, and its peak marginal is degenerate (tachy 0.000, elev 0.006, median peak 89.0 bpm against the corpus 131.8).

4. HONEST BASELINE LADDER ON THE REBUILT STRICT POOL (n=5889, trivial always-predict-median MAE 16.954 bpm, level-only R2 +0.0040): six caption scalars +0.0707 (+0.61 bpm skill); +hr_std +0.5636 (+5.97); +hr_p99 +0.7645 (+9.20); best shallow +0.7999 (+9.70). The supervision deficit found in H4b REPRODUCES on the rebuilt corpus, so it is a live confound this experiment does NOT fix and must not be credited with.

## Plan
CONTROLLED A/B, ONE VARIABLE (dataset_filter), ONE SEED PER ARM, run end-to-end A->B->C in both arms on the SAME rebuilt tensor.

CONTROL ARM   : phase_a_12ch_pmdata -> phase_b_12ch -> lora_proxy_12ch_clmbal
INTERVENTION  : phase_a_12ch_1min_wl -> phase_b_12ch_wl -> lora_proxy_12ch_wl
Every other knob is identical, including the E40 pure-CLM loss balance (probe_weight 0, hr_regression_weight 0, no aux warmup) in BOTH Phase C arms - the loss balance is settled and is not re-litigated here.

EVAL POOL: data/eval_v3_1min, rebuilt from the new tensor by scratch/build_eval_v3_1min.py (strict: no zero-bpm present HR step, day mean > 40, HR coverage > 0.5). n=5889, 127 subjects.

MEASUREMENTS, per arm, per phase:
  A: scripts/probe_phase_a_hr.py - subject-grouped OOF; hr_mean R2 AND hr_peak R2 AND the level-only control R2(peak|true mean).
  B: same probe on the Phase B checkpoint - this is where the primary gate is read.
  C: full eiv probe suite + label-stratified generation eval with the majority baseline and the always-predict-median MAE recomputed on the scored pool.

EXPLICIT NON-CLAIMS: single seed per arm against a documented Phase-A seed spread of 0.066, so any |delta| below that band is reported as 'no evidence of difference', NOT as a refutation. e40's +7.14 / +3.50 figures are NOT valid comparators - different corpus - and the control arm is re-measured inside this same experiment.

## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Applies [[dataset-allowlist-repaired-all-phases]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv-dataset-provenance-shortcut-balacc]] (down).
Predicts [[eiv_gen_bucket_acc_over_majority]] (up).
Predicts [[eiv_heldout_hr_r2]] (none).
Gate `gate-peak-increment-improves` (blocking): PRIMARY. On the Phase B soft-token prefix, subject-grouped OOF on data/eval_v3_1min: eiv_peak_excursion_increment_r2(repaired) - eiv_peak_excursion_increment_r2(incumbent) >= +0.03. The 0.03 bar is set BELOW the 0.066 Phase-A seed spread deliberately, so passing it is necessary but NOT sufficient - see gate-seed-band-honesty.
Gate `gate-hr-mean-no-regression` (blocking): NO REGRESSION ON THE SATURATED CHANNEL. eiv_heldout_hr_r2(repaired) >= eiv_heldout_hr_r2(incumbent) - 0.02 on the same pool. hr_mean is already at 0.97-0.99, so a drop here means the repaired allowlist broke alignment rather than improving it, and would void a peak-side win.
Gate `gate-per-phase-mechanism-attributed` (blocking): MECHANISM MUST BE ATTRIBUTED, NOT ASSUMED. For EACH of the three phases, report the measured mechanism quantity for both arms in one table: (A) eiv-dataset-provenance-shortcut-balacc with its chance rate and class composition, (B) the state_label x dataset purity table with the count of non-vacuous triplets, (C) the realised HR-label marginal logged by the trainer before step 0 plus the subject count. A phase whose mechanism quantity does not move in the predicted direction is reported as UNEXPLAINED even if its outcome metric improves.
Gate `gate-seed-band-honesty` (advisory): HONESTY GUARD, non-blocking by design. Any per-arm delta whose absolute value is below the documented Phase-A seed spread of 0.066 must be written up as 'no evidence of difference at one seed', never as a refutation or a win. This gate exists because the prior H1 arm was recorded as refuted on a single-seed -0.071 that sat inside that band.
Gate `gate-emission-honesty` (advisory): DELIVERABLE-SIDE REALITY CHECK. Report eiv_gen_bucket_acc_over_majority for BOTH arms with the majority baseline recomputed on the scored pool. The incumbent 1-minute pipeline emitted -0.108, i.e. worse than a constant. If both arms remain negative, say so in the first line of the writeup: the allowlist did not fix the deliverable, whatever it did to the encoder.

#cross-modal #sensor-text #medgemma #epoch-resolution #dataset-whitelist #wearables #baseline
