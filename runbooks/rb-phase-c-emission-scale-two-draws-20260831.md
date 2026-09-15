---
created_at: '2026-08-31T12:06:21+00:00'
former_ids: []
gates:
- g0-dense-scale-slope-in-band
- g1-no-harm-supabase
- g2-dense-mae-in-band
hypothesis_id: null
id: rb-phase-c-emission-scale-two-draws-20260831
notes:
- at: '2026-09-01T07:14:22+00:00'
  text: "STEP 0 EXECUTED 2026-09-01, CPU-only, and it PASSES -- but only after a confound
    check the runbook did not ask for, and that check changes the size of the claim.\n\nPOOLED
    (scratch/_step0_emission_scale.py -> logs/step0_emission_scale.log, logs/step0_emission_scale.json).
    7,559 admitted C rows on the band04 roster, HR coverage p10/p50/p90 = 0.384/0.942/1.000,
    split at the 33rd/67th coverage percentiles:\n  sparse n=2498  hr_raw target mean
    78.26  SD 13.20   peak mean 130.55 SD 26.24\n  dense  n=2517  hr_raw target mean
    75.31  SD  8.26   peak mean 134.51 SD 19.37\n  dense/sparse SD ratio 0.626 ->
    |ratio-1| = 0.374, clears the preregistered 0.20 kill threshold on hr_raw (hr_peak
    0.262, also clears).\n\nTHE CONFOUND I CHECKED BEFORE BANKING IT (scratch/_step0_confound_check.py
    -> logs/step0_confound.log). Every individual dataset sits at target SD 8.3-10.7,
    and NONE at the sparse group's 13.20 -- so the pooled gap could have been between-dataset
    mean shift rather than coverage. The sparse group is 66% lifesnaps / 18% pmdata
    / 8% supabase / 4% dreamt; the dense group is 74% / 25% / 0% / 0%. Splitting by
    coverage WITHIN each dataset, where a mean shift cannot act:\n  lifesnaps           n=4794
    \ sparse SD 10.50  dense 7.43  ratio 0.708\n  pmdata              n=2179  sparse
    SD  8.49  dense 8.11  ratio 0.956\n  optihealth_supabase n= 299  sparse SD 13.28
    \ dense 4.23  ratio 0.318\n  big_ideas_glycemic  n= 145  sparse SD 11.33  dense
    9.75  ratio 0.861\n  dreamt              n= 100  sparse SD  8.74  dense 9.99  ratio
    1.142\n  mmash               n=  42  sparse SD  8.49  dense 7.75  ratio 0.913\n
    \ weighted within-dataset ratio 0.774 -> |ratio-1| = 0.226, STILL clears 0.20.\n\nSO:
    the coverage effect is real and not merely dataset mixing, but it is SMALLER than
    the pooled number implies -- 0.226 rather than 0.374, i.e. roughly 40% of the
    pooled gap was mixing. The account survives on a thinner margin than the runbook's
    framing suggested, and anyone quoting 0.626 as 'the' ratio is quoting a confounded
    figure.\n\nTHE STRONGEST CELL IS THE DEPLOYMENT COHORT ITSELF: optihealth_supabase
    reads 0.318 within its own rows (sparse SD 13.28 vs dense 4.23), the most extreme
    of the six. That is the population the promotion decision is made on, and it is
    exactly where a pooled band edge would be most wrong.\n\nGPU IS NOT COMMITTED
    YET: the recapture and the matched Phase-C seed family are running, and the coverage-aware
    band arm is the next queued spend."
priority: 1
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
status: running
topic_tags:
- deployment-distribution
- optihealth
- baseline
updated_at: '2026-09-01T07:14:22+00:00'
---

## Prompt
PHASE-C EMISSION SCALE: ONE ADAPTER IS CARRYING TWO INCOMPATIBLE OUTPUT SCALES. This is a self-contained prompt; a fresh session has no memory of the chat that queued it.

=== THE MEASURED FACT (2026-08-31, do not re-derive) ===
Two consecutive arms passed their Phase-A geometry gate and then failed deployment through emission SCALE, not through lost information. Cross-fitted (5-fold, out-of-fold) 2-parameter affine recalibration pred' = a*pred + b, scratch/_v31_calibration_probe.py, on the exact capture jsons:

  arm            draw     MAE    corr   std_ratio     a       MAE after affine
  v28affect01    supa    3.030  0.793     0.932     0.851        2.543
  v28affect01    dense   2.851  0.937     1.112     0.842        2.226
  v31detached    supa    2.699  0.755     0.786     0.961        2.755
  v31detached    dense   7.312  0.733     1.652     0.444        4.608
  v30state       supa    5.666  0.777     2.708     0.287        2.747
  v30state       dense  15.085  0.660     2.464     0.268        5.352

Read the `a` column. v28 needs almost no repair on EITHER draw (0.85/0.84 -- one consistent scale). v31 needs none on supabase (0.961) and a 2.25x squeeze on dense (0.444). v30 needs a ~3.7x squeeze on both. Correlation SURVIVES in every failing cell (0.66-0.73), so the encoder is not losing the signal; the emission head is choosing the wrong spread. Paired per-row confirmation for v31 dense vs v28 on 128 shared ids: mean +4.461 bpm, se 0.756, t +5.90, 68.8% of rows worse, 25 rows past 10 bpm that v28 held (scratch/_v31_error_shape.py).

=== WHY THIS IS THE RIGHT NEXT VARIABLE ===
The v20-v31 campaign has been spending GPU on Phase-A supervision knobs. v31 settled that axis: detaching the probes produced eff_rank 10.106 -- the highest ever measured here, above v28's 8.599 and the 8.35 healthy anchor -- and bought ZERO deployment credit. Phase-A rank is a cheap veto (v29 at 6.783 was genuinely broken) but not a direction. The damage that is left is downstream of the encoder.

=== STEP 0, BLOCKING, CPU-ONLY, MINUTES ===
Before any retrain, characterise the target distribution the Phase-C band construction actually presents on the two draws. lora_proxy_12ch_wl_band04 is the C profile every one of these arms used. Compute, from the C training rows themselves (n_train_rows 7566, 6 datasets, target_style json_slots, tensor data/canonical/X_canonical.npy shape [14041,1440,12]): the hr_raw target histogram, its std, and the same statistics restricted to rows drawn from the dense (lifesnaps-like) cohorts vs rows resembling the sparse supabase coverage regime. HYPOTHESIS TO TEST CHEAPLY: the band edges are set on a pooled distribution whose spread sits between the two draws, so an arm that fits one draw's spread must miss the other's. If the two sub-populations' target stds differ by less than ~20%, this account is WRONG and the scale failure comes from somewhere else (candidate: the emitted-token band vocabulary, distinct/top1_mass -- v31 supa distinct 14 top1 0.415 vs v28 20/0.470) -- record that and STOP before spending GPU.

=== IF STEP 0 SUPPORTS THE ACCOUNT ===
One variable, on the v28affect01 recipe (THE INCUMBENT -- supa 3.030/0.793, dense 2.851/0.937/hr_peak skill +0.543), never on top of a failing arm: make the Phase-C band construction coverage-aware, so the target spread the head is asked to hit is conditioned on the row's coverage rather than pooled. Do NOT simultaneously change Phase A supervision, the batch size, or the adapter rank.

=== SCORING RULES, NON-NEGOTIABLE ===
Registered noise floors (vault, 2026-08-31): hr_raw/hr_peak correlation finite-draw sigma 0.069 (k=2); paired arm-vs-arm supabase MAE sigma 0.195, so a gap under ~0.4 bpm is NOT a demonstrated difference; seed-to-seed retrain band on MAE is 3.19 bpm. Report BOTH draws always -- v31 is the proof that a supabase-only readout hides a 4.5 bpm dense regression. Report corr, std_ratio, distinct and top1_mass alongside MAE, never MAE alone. Run scratch/_v31_calibration_probe.py on the new captures and report the affine slope: if the arm's slope moves toward 1.0 on BOTH draws, the mechanism is confirmed even if MAE moves inside the noise floor.

## Preconditions
GPU free (v32detachaffect is running as of 2026-08-31 15:01, eiv-v32.service). Read exp-v31-detached-probes-chain-20260831 and exp-v30-state-healthy-substrate-20260831 first, plus logs/v31_posthoc_probes.log. Step 0 is CPU-only and must be run before any GPU is committed.

## Log
- **2026-09-01T07:14:22+00:00** — STEP 0 EXECUTED 2026-09-01, CPU-only, and it PASSES -- but only after a confound check the runbook did not ask for, and that check changes the size of the claim.

POOLED (scratch/_step0_emission_scale.py -> logs/step0_emission_scale.log, logs/step0_emission_scale.json). 7,559 admitted C rows on the band04 roster, HR coverage p10/p50/p90 = 0.384/0.942/1.000, split at the 33rd/67th coverage percentiles:
  sparse n=2498  hr_raw target mean 78.26  SD 13.20   peak mean 130.55 SD 26.24
  dense  n=2517  hr_raw target mean 75.31  SD  8.26   peak mean 134.51 SD 19.37
  dense/sparse SD ratio 0.626 -> |ratio-1| = 0.374, clears the preregistered 0.20 kill threshold on hr_raw (hr_peak 0.262, also clears).

THE CONFOUND I CHECKED BEFORE BANKING IT (scratch/_step0_confound_check.py -> logs/step0_confound.log). Every individual dataset sits at target SD 8.3-10.7, and NONE at the sparse group's 13.20 -- so the pooled gap could have been between-dataset mean shift rather than coverage. The sparse group is 66% lifesnaps / 18% pmdata / 8% supabase / 4% dreamt; the dense group is 74% / 25% / 0% / 0%. Splitting by coverage WITHIN each dataset, where a mean shift cannot act:
  lifesnaps           n=4794  sparse SD 10.50  dense 7.43  ratio 0.708
  pmdata              n=2179  sparse SD  8.49  dense 8.11  ratio 0.956
  optihealth_supabase n= 299  sparse SD 13.28  dense 4.23  ratio 0.318
  big_ideas_glycemic  n= 145  sparse SD 11.33  dense 9.75  ratio 0.861
  dreamt              n= 100  sparse SD  8.74  dense 9.99  ratio 1.142
  mmash               n=  42  sparse SD  8.49  dense 7.75  ratio 0.913
  weighted within-dataset ratio 0.774 -> |ratio-1| = 0.226, STILL clears 0.20.

SO: the coverage effect is real and not merely dataset mixing, but it is SMALLER than the pooled number implies -- 0.226 rather than 0.374, i.e. roughly 40% of the pooled gap was mixing. The account survives on a thinner margin than the runbook's framing suggested, and anyone quoting 0.626 as 'the' ratio is quoting a confounded figure.

THE STRONGEST CELL IS THE DEPLOYMENT COHORT ITSELF: optihealth_supabase reads 0.318 within its own rows (sparse SD 13.28 vs dense 4.23), the most extreme of the six. That is the population the promotion decision is made on, and it is exactly where a pooled band edge would be most wrong.

GPU IS NOT COMMITTED YET: the recapture and the matched Phase-C seed family are running, and the coverage-aware band arm is the next queued spend.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Intended gates: `g0-dense-scale-slope-in-band`, `g1-no-harm-supabase`, `g2-dense-mae-in-band`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#deployment-distribution #optihealth #baseline
