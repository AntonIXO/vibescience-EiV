---
artifacts:
- logs/emission_v12conly_supa.json
- logs/emission_v12conly_dense.json
- logs/v12_driver.log
- logs/v12_phase_c.log
- scratch/run_v12_conly.sh
- scratch/score_v12.sh
- scratch/_encoder_hr_probe.py
- scratch/_projector_hr_probe.py
- scratch/_healthy_vs_collapsed.py
- scratch/_caption_collision_audit.py
- scratch/_cache_staleness_probe.py
closed: true
config_note: 'v12 C-ONLY, single seed, off the FROZEN outputs/checkpoints/v11full/phase_b.
  Runner scratch/run_v12_conly.sh, scoring scratch/score_v12.sh. Phase C RC=0, zero
  tracebacks/OOM, adapter outputs/adapters/v12conly_c written, g3 roster assertion
  PASS (20 datasets, n_train_rows 9555).


  SINGLE VARIABLE, verified by dict diff: lora_proxy_12ch_v12conly vs lora_proxy_12ch_v11full
  differ ONLY in `notes`. Same 20-dataset roster, same band04 augmentation law, same
  masked_avg pooling, same fit256 prompt, same seed 43. The only real difference is
  that the encoder was loaded from a checkpoint written in a PREVIOUS session rather
  than one written minutes earlier in the same chain -- and it is literally the same
  directory, same sha256 per file (temporal_encoder 60dd501b, cross_modal_projector
  0aaaf72e, channel_fusion 3fdeb5ab).


  SINGLE SEED by instruction and by design: the C-only re-seed band is 0.80 bpm (exp-054)
  and the failure under test is a 2.6+ bpm collapse to constant output, ~3x outside
  it.


  BOTH OF TODAY''S FIXES CARRIED AND EXERCISED: the auto-warm path (warm_if_needed.py
  + the OOM-aware retry, 8 tests) ran unattended at launch and reported RC=0 with
  no manual step; the window-level state presence mask (mask_unlabelled_state, 12
  tests) is inert at Phase C because C does not run the state head.


  NOTE ON THE SCORER''S FOOTER: score_v12.sh was derived from score_v11.sh and still
  prints the ''this chain retrains A AND B -> price against 3.19'' line. That is WRONG
  for this arm -- v12 retrains only C and is priced against the 0.80 bpm C-only band.
  Cosmetic, but recorded so the log is not misread later.'
cost:
  gpu_hours: 0.65
  wall_hours: 1.0
crash_reason: ''
created_at: '2026-08-30T09:30:23+00:00'
diagnostics_measured:
- after: -0.097
  before: -0.035
  diagnostic_id: eiv_gen_peak_corr
- after: 4.384
  before: 5.49
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 21.585
  before: 21.277
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT propose any further ENCODER-side explanation for this collapse. Four independent
  measurements now exonerate Phase A/B: (a) production-stage HR probe, v11 phase_b
  R2 +0.7593 vs the healthy ab_ctrl_s43 +0.7715, corr 0.8793 vs 0.8791, soft-token
  norms 50.59 vs 50.61; (b) encoder-stage probe where the HEALTHY encoder is WORSE
  (-0.0269 vs +0.0256); (c) all 20 Phase-A hyperparameters byte-identical between
  healthy and collapsed checkpoints; (d) THIS ARM -- freezing the encoder entirely
  moved dense MAE by 0.308 bpm out of a 13.8 bpm deficit.'
- Do NOT re-run C-only variants hoping a different frozen encoder helps. The encoder
  identity is now measured to be worth ~1.4% of the collapse magnitude on dense.
- 'Do NOT read v12''s supabase MAE 4.384 (vs v11''s 5.490) as improvement. corr is
  -0.097: the arm has no relationship with truth, so a MAE nearer the trivial only
  means its constant sits nearer the median. Quote corr and top1_mass beside every
  MAE on a collapsed arm.'
- Do NOT trust score_v12.sh's footer line about the 3.19 bpm full-chain band -- it
  was inherited from score_v11.sh and is wrong for a C-only arm, which is priced against
  the 0.80 bpm C-only band (exp-054). Fix the string before the log is read by someone
  else.
- 'Do NOT blame the text-embedding cache without re-measuring: scratch/_cache_staleness_probe.py
  showed 1500/1500 cache HITs for this profile and the key is content-addressed on
  the caption text (sha1), so a stale caption cannot silently hit.'
external_run: logs/emission_v12conly_supa.json
former_ids: []
gate_results:
- evidence: 'FAIL on both halves. supabase hr_raw corr -0.097 against a >= 0.60 bar
    (v11full was -0.035, so freezing made it marginally WORSE, not better); top1_mass
    0.470 against a <= 0.50 bar, which nominally passes but with distinct=6 over 200
    rows. hr_peak agrees: corr -0.084. Dense is unmoved at corr -0.347. The collapse
    survives freezing the encoder entirely.'
  gate_id: g1-not-collapsed
  passed: false
- evidence: 'FAIL. supabase hr_raw MAE 4.384 vs trivial 3.954 -> skill -0.429. Better
    than v11full''s 5.490 by 1.106 bpm, but still short of the absolute bar. And the
    improvement is not interpretable as progress: with corr -0.097 the arm has no
    relationship with truth, so a MAE closer to the trivial only means its constant
    sits nearer the median -- exactly the degenerate reading the g1 top1_mass clause
    exists to catch.'
  gate_id: g2-beats-trivial
  passed: false
- evidence: FAIL, and this is the sharpest number in the run. dense-128 hr_raw MAE
    21.585 vs trivial 7.774 -> skill -13.811, against v11full's 21.277. The delta
    is +0.308 bpm, i.e. freezing the encoder changed the dense collapse by 1.4% of
    its magnitude. Whatever is broken is completely indifferent to which encoder the
    C stage reads.
  gate_id: g3-dense-sane
  passed: false
git_ref: ''
hypothesis_id: the-fresh-chain-collapse-is-not-caused-by-the-37ddda
id: exp-v12-conly-frozen-encoder-20260830
interpretation:
  at: '2026-08-30T09:31:25+00:00'
  failure_mode: claim_false
  lesson: 'THE ENCODER IS FULLY EXONERATED, AND THAT IS WORTH MORE THAN THE FAILED
    GATES. Freezing it changed the dense collapse by 0.308 bpm out of a 13.811 bpm
    deficit -- 1.4% -- and made supabase correlation slightly WORSE (-0.035 -> -0.097).
    Combined with four measurements taken before the arm (production-stage HR R2 0.7593
    vs the healthy 0.7715 with corr 0.8793 vs 0.8791; soft-token norms 50.59 vs 50.61;
    an encoder-stage probe where the HEALTHY encoder is worse; all 20 Phase-A hyperparameters
    byte-identical; captions 99.7% unique), Phase A/B can be removed from the candidate
    list. Six chains'' worth of suspicion, closed.


    WHAT I GOT WRONG, and it is the same error twice in a row. I predicted corr >=
    0.60 and MAE 2.7-4.0 on the reasoning that ''every C-only family off a frozen
    init is healthy''. That generalisation was drawn from families whose frozen init
    came from the 12,547 corpus. I treated ''frozen'' as the operative property when
    the records equally supported ''trained on 12,547''. Two variables were confounded
    in the healthy set and I picked the wrong one -- exactly the mistake the roster
    arm made a run earlier.


    THE CONFOUND THAT SURVIVES, stated precisely so the next arm actually breaks it:
    every HEALTHY C arm on record used a 3- or 6-dataset roster AND the 12,547 corpus.
    Every COLLAPSED one uses a post-12,547 corpus. No arm has yet run the OLD roster
    on the NEW corpus, or the NEW roster on the OLD corpus. Until one does, ''corpus''
    and ''roster'' remain inseparable, and I have now spent two arms varying things
    that were never the difference.


    CHEAPEST DISCRIMINATOR, and it needs no training at all: score the EXISTING healthy
    adapter (v10conly / ab_ctrl_s43) against the CURRENT eval bundle. If it stays
    healthy on the new eval rows, the eval bundle and the emission instrument are
    clean and the defect is in what the new corpus teaches C. If it ALSO collapses,
    the defect is in the eval bundle or the scorer -- and every ''collapse'' recorded
    today, including v11 and v12, is an instrument artifact rather than a model failure.
    That single capture splits the remaining hypothesis space in half for ~20 minutes
    and zero training.


    METHOD NOTE WORTH KEEPING: v12''s MAE (4.384) is BETTER than v11''s (5.490) while
    its correlation is WORSE (-0.097 vs -0.035). On a collapsed arm, MAE measures
    where the constant sits relative to the median, not skill. Quote corr and top1_mass
    beside every MAE or the improvement is read backwards.'
  transfers_to:
  - a-full-a-b-c-retrain-whose-roster-actually-052f9a
key_numbers:
  dense_corr: -0.347
  dense_mae: 21.585
  dense_mae_v11: 21.277
  dense_skill: -13.811
  encoder_probe_healthy_r2: -0.0269
  encoder_probe_v11_r2: 0.0256
  production_probe_healthy_r2: 0.7715
  production_probe_v11_r2: 0.7593
  supabase_corr: -0.097
  supabase_distinct: 6.0
  supabase_mae: 4.384
  supabase_skill: -0.429
  supabase_top1_mass: 0.47
  supabase_trivial: 3.954
observed_effects:
- delta: -0.062
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: -1.106
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: 0.308
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v11-full-corpus-chain-20260830
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-fresh-chain-collapse-is-not-caused-by-the-37ddda]].
Branches from [[exp-v11-full-corpus-chain-20260830]].
External run: logs/emission_v12conly_supa.json

## Measurements
- [[eiv_gen_peak_corr]]: before -0.035 → after -0.097  (Δ -0.062, down ✗ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 5.49 → after 4.384  (Δ -1.106, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 21.277 → after 21.585  (Δ +0.308, up)

## Gates
- `g1-not-collapsed`: **FAIL** — FAIL on both halves. supabase hr_raw corr -0.097 against a >= 0.60 bar (v11full was -0.035, so freezing made it marginally WORSE, not better); top1_mass 0.470 against a <= 0.50 bar, which nominally passes but with distinct=6 over 200 rows. hr_peak agrees: corr -0.084. Dense is unmoved at corr -0.347. The collapse survives freezing the encoder entirely.
- `g2-beats-trivial`: **FAIL** — FAIL. supabase hr_raw MAE 4.384 vs trivial 3.954 -> skill -0.429. Better than v11full's 5.490 by 1.106 bpm, but still short of the absolute bar. And the improvement is not interpretable as progress: with corr -0.097 the arm has no relationship with truth, so a MAE closer to the trivial only means its constant sits nearer the median -- exactly the degenerate reading the g1 top1_mass clause exists to catch.
- `g3-dense-sane`: **FAIL** — FAIL, and this is the sharpest number in the run. dense-128 hr_raw MAE 21.585 vs trivial 7.774 -> skill -13.811, against v11full's 21.277. The delta is +0.308 bpm, i.e. freezing the encoder changed the dense collapse by 1.4% of its magnitude. Whatever is broken is completely indifferent to which encoder the C stage reads.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

THE ENCODER IS FULLY EXONERATED, AND THAT IS WORTH MORE THAN THE FAILED GATES. Freezing it changed the dense collapse by 0.308 bpm out of a 13.811 bpm deficit -- 1.4% -- and made supabase correlation slightly WORSE (-0.035 -> -0.097). Combined with four measurements taken before the arm (production-stage HR R2 0.7593 vs the healthy 0.7715 with corr 0.8793 vs 0.8791; soft-token norms 50.59 vs 50.61; an encoder-stage probe where the HEALTHY encoder is worse; all 20 Phase-A hyperparameters byte-identical; captions 99.7% unique), Phase A/B can be removed from the candidate list. Six chains' worth of suspicion, closed.

WHAT I GOT WRONG, and it is the same error twice in a row. I predicted corr >= 0.60 and MAE 2.7-4.0 on the reasoning that 'every C-only family off a frozen init is healthy'. That generalisation was drawn from families whose frozen init came from the 12,547 corpus. I treated 'frozen' as the operative property when the records equally supported 'trained on 12,547'. Two variables were confounded in the healthy set and I picked the wrong one -- exactly the mistake the roster arm made a run earlier.

THE CONFOUND THAT SURVIVES, stated precisely so the next arm actually breaks it: every HEALTHY C arm on record used a 3- or 6-dataset roster AND the 12,547 corpus. Every COLLAPSED one uses a post-12,547 corpus. No arm has yet run the OLD roster on the NEW corpus, or the NEW roster on the OLD corpus. Until one does, 'corpus' and 'roster' remain inseparable, and I have now spent two arms varying things that were never the difference.

CHEAPEST DISCRIMINATOR, and it needs no training at all: score the EXISTING healthy adapter (v10conly / ab_ctrl_s43) against the CURRENT eval bundle. If it stays healthy on the new eval rows, the eval bundle and the emission instrument are clean and the defect is in what the new corpus teaches C. If it ALSO collapses, the defect is in the eval bundle or the scorer -- and every 'collapse' recorded today, including v11 and v12, is an instrument artifact rather than a model failure. That single capture splits the remaining hypothesis space in half for ~20 minutes and zero training.

METHOD NOTE WORTH KEEPING: v12's MAE (4.384) is BETTER than v11's (5.490) while its correlation is WORSE (-0.097 vs -0.035). On a collapsed arm, MAE measures where the constant sits relative to the median, not skill. Quote corr and top1_mass beside every MAE or the improvement is read backwards.

Transfers to: [[a-full-a-b-c-retrain-whose-roster-actually-052f9a]]

## Key numbers
- `dense_corr`: -0.347
- `dense_mae`: 21.585
- `dense_mae_v11`: 21.277
- `dense_skill`: -13.811
- `encoder_probe_healthy_r2`: -0.0269
- `encoder_probe_v11_r2`: 0.0256
- `production_probe_healthy_r2`: 0.7715
- `production_probe_v11_r2`: 0.7593
- `supabase_corr`: -0.097
- `supabase_distinct`: 6
- `supabase_mae`: 4.384
- `supabase_skill`: -0.429
- `supabase_top1_mass`: 0.47
- `supabase_trivial`: 3.954

## Artifacts
- `logs/emission_v12conly_supa.json`
- `logs/emission_v12conly_dense.json`
- `logs/v12_driver.log`
- `logs/v12_phase_c.log`
- `scratch/run_v12_conly.sh`
- `scratch/score_v12.sh`
- `scratch/_encoder_hr_probe.py`
- `scratch/_projector_hr_probe.py`
- `scratch/_healthy_vs_collapsed.py`
- `scratch/_caption_collision_audit.py`
- `scratch/_cache_staleness_probe.py`

## Do not retry
- Do NOT propose any further ENCODER-side explanation for this collapse. Four independent measurements now exonerate Phase A/B: (a) production-stage HR probe, v11 phase_b R2 +0.7593 vs the healthy ab_ctrl_s43 +0.7715, corr 0.8793 vs 0.8791, soft-token norms 50.59 vs 50.61; (b) encoder-stage probe where the HEALTHY encoder is WORSE (-0.0269 vs +0.0256); (c) all 20 Phase-A hyperparameters byte-identical between healthy and collapsed checkpoints; (d) THIS ARM -- freezing the encoder entirely moved dense MAE by 0.308 bpm out of a 13.8 bpm deficit.
- Do NOT re-run C-only variants hoping a different frozen encoder helps. The encoder identity is now measured to be worth ~1.4% of the collapse magnitude on dense.
- Do NOT read v12's supabase MAE 4.384 (vs v11's 5.490) as improvement. corr is -0.097: the arm has no relationship with truth, so a MAE nearer the trivial only means its constant sits nearer the median. Quote corr and top1_mass beside every MAE on a collapsed arm.
- Do NOT trust score_v12.sh's footer line about the 3.19 bpm full-chain band -- it was inherited from score_v11.sh and is wrong for a C-only arm, which is priced against the 0.80 bpm C-only band (exp-054). Fix the string before the log is read by someone else.
- Do NOT blame the text-embedding cache without re-measuring: scratch/_cache_staleness_probe.py showed 1500/1500 cache HITs for this profile and the key is content-addressed on the caption text (sha1), so a stale caption cannot silently hit.

## Notes
FREEZING THE ENCODER DID NOT HELP, AND THAT IS THE RESULT. Single variable against exp-v11 (profile dict-diff: only `notes`), single seed, RC=0, no tracebacks.

  supabase hr_raw  MAE 4.384 vs trivial 3.954 -> skill -0.429, corr -0.097, distinct 6, top1 0.470
  supabase hr_peak MAE 19.763 -> skill -2.353, corr -0.084
  dense-128 hr_raw MAE 21.585 vs trivial 7.774 -> skill -13.811, corr -0.347

Against v11full (fresh chain): corr -0.035 -> -0.097 (worse), dense 21.277 -> 21.585 (+0.308, i.e. 1.4% of a 13.8 bpm deficit). The C stage is INDIFFERENT to whether its encoder was retrained in the same session.

COMBINED WITH THE PROBES RUN BEFORE THIS ARM, Phase A/B is now exonerated four ways:
  production-stage HR probe : v11 phase_b R2 +0.7593 corr 0.8793 | healthy ab_ctrl_s43 R2 +0.7715 corr 0.8791
  soft-token norms          : 50.59 vs 50.61
  encoder-stage probe       : healthy -0.0269, v11 +0.0256 (healthy is WORSE)
  Phase-A hyperparameters   : all 20 keys byte-identical
  caption collisions        : 99.7% unique in both pools

So the prefix is intact and the collapse is downstream of it. What is NOT yet separated: the C stage's own recipe against THIS corpus. Every healthy C arm on record (v9band04, v10conly) used the 3- or 6-dataset roster on the 12,547 corpus; every collapsed one uses a post-12,547 corpus. That pairing has never been broken because no C-only arm has been run with the OLD roster on the NEW corpus, or the NEW roster on the OLD corpus.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
