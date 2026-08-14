---
created_at: '2026-08-07T20:52:17+00:00'
former_ids:
- clearing-the-phase-c-dataset-allowlist-admitting-all-13-datasets-instead-of-life
id: clearing-the-phase-c-dataset-allowlist-292953
problem_id: canonical-m-mask-marks-all-zero-889451
retracted: true
retraction_reason: "RETRACTED 2026-08-07 — the rule_out answered the WRONG QUESTION
  and its conclusion ('keep the allowlist, change nothing') is withdrawn as an over-generalisation.\n\nThe
  tested arm was ALL 13 DATASETS vs 3. That bundles dryad — the single dataset with
  a measured defect — together with eight clean ones, so it could only ever fail,
  and failing it told us nothing about the datasets the 2026-08-07 corpus update actually
  repaired. It was a strawman.\n\nThe offline measurement I had ALREADY MADE, and
  then failed to act on, decomposes it per-dataset (between-dataset eta^2 of the hr_mean
  target, and the cohort-only predictor that goes with it):\n  current Phase C allowlist
  (3)         n= 7196  eta^2 0.246  cohort-only corr +0.496 / +1.12 bpm\n  + dreamt
  \                                       eta^2 0.250   (free)\n  + big_ideas_glycemic
  \                           eta^2 0.245   (free)\n  + mmash                                         eta^2
  0.249   (free)\n  + wearable_exam_stress                          eta^2 0.280   (small,
  n=20, HR 115.9)\n  ALL EXCEPT DRYAD (12)                 n= 7515  eta^2 0.270  cohort-only
  corr +0.519 / +1.20 bpm\n  + dryad / all 13                      n=12025  eta^2
  0.487  cohort-only corr +0.698 / +3.35 bpm\nDRYAD ALONE causes the shortcut. Every
  other dataset is essentially free.\n\nWHAT SURVIVES THE RETRACTION: the finding
  that dryad specifically must stay out. It is identifiable at balanced accuracy 0.978
  from six structural features (coverage, per-channel presence, mean time-of-day of
  present steps) with NO physiology, and its cohort mean is 61.3 bpm vs lifesnaps
  78.9, so 'recognise dryad -> emit 61' is a complete loss-reducing strategy. The
  trained arm's grounding collapse (monotonic scale response 0.80 -> 0.34, null-sensor
  disagreement 0.91 -> 0.61) is consistent with that, though it remains confounded
  with 1.7x fewer passes per sample.\n\nWHAT IS WITHDRAWN: the implied action 'do
  not change the allowlist'. The correct action, which the data supported all along,
  is to ADMIT the now-repaired datasets and exclude ONLY dryad. DREAMT in particular
  was explicitly fixed in this corpus update (n=100, 89% of present steps 22:00-08:00,
  HR 51-104, sleep block present) and is the ONLY night/sleep cohort, yet it was still
  absent from Phase C.\n\nSUPERSEDED BY: a real config change to lora_proxy_12ch_v2
  (allowlist -> all 12 non-dryad datasets) and a fresh Phase C run, not another throwaway
  arm."
ruled_out_at: '2026-08-07T20:52:17+00:00'
topic_tags: []
---

## Claim
Clearing the Phase C dataset allowlist (admitting all 13 datasets instead of lifesnaps+pmdata+optihealth_supabase) improves grounding, now that the M_mask min_valid contract is enforced and the zero-contamination that originally motivated the allowlist is gone.

## Method
Two Phase C arms trained on the 2026-08-07 re-merged corpus, IDENTICAL in every respect except dataset_filter: lora_proxy_12ch_v2 (3 datasets) vs lora_proxy_12ch_v2_nofilter (all 13). Same Phase B checkpoint outputs/checkpoints/12ch_v2/phase_b, same e40 pure-CLM recipe (probe_weight 0, hr_regression_weight 0, warmup 0), same 2600 steps, same bs=1/ga=8 (effective batch 8; bs=2 OOMed on the 5070 at a ~905 MiB LM-head logits allocation). Both scored with `eiv probe --probes all --num-samples 96` on data/eval_v2 -- a CLEAN corpus view I built (hr_cov>=0.75, restricted to the 3 datasets BOTH arms trained on, n=5334) because the shipped probe samples rng.choice over the whole tensor with no filter and linear_probe.py:156 fills missing HR with median[ch0]=0.0 bpm, which on the seed-42 n=96 draw gives 8.3% zero-bpm targets and a +0.836 clump-only correlation ceiling, plus 45% dryad that arm1 never trained on. PREREGISTRATION STATUS: NONE. Predictions were never committed before this run, so this is recorded as a rule_out and carries NO calibration weight.

## Evidence
OFFLINE MOTIVATION (measured on the corpus before training): between-dataset eta^2 of the hr_mean target = share of variance obtainable from cohort identity alone. {lifesnaps,pmdata,supabase} 0.249; +dreamt 0.250; +dryad 0.513; all 13 = 0.520. A cohort-only predictor (1 categorical bit, zero within-cohort skill by construction) scores corr +0.499 / +1.13 bpm on the allowlist and corr +0.721 / +3.47 bpm on the cleared filter. dryad ALONE causes this; every other dataset leaves eta^2 unchanged.

TRAINED RESULT (n=96, same population, ARM1=allowlist, ARM2=cleared):
  p2 monotonic_scale_response         ARM1 0.7969  ARM2 0.3438   (-0.4531)
  p2 null_sensor_bucket_disagreement  ARM1 0.9062  ARM2 0.6094   (-0.2969)
  p4 state_acc (majority 0.4688)      ARM1 0.6458  ARM2 0.4271   -> ARM2 is BELOW a constant
  p7 sensor_attention_mass            ARM1 0.1533  ARM2 0.1402
  p7 sensor_vs_text_attention_ratio   ARM1 0.1814  ARM2 0.1631
  p2 output_diff_rate zero/random     1.00/1.00 both -> prefix live in BOTH arms, no mode collapse
Every definition-independent metric favours the ALLOWLIST arm.

THE ONE METRIC THAT FAVOURS CLEARING IS AN ARTEFACT AND IS WITHDRAWN: p4 hr_acc ARM1 0.0521 vs ARM2 0.1667 (+0.1146, '3.2x'). p4 scores against a DIFFERENT bucket definition than the training target (scorer bins hr_MEAN <60/<80/<100; target bins hr_PEAK >=120/>=100 with mean<55 -> bradycardic). On the scored draw the definitions agree on 26.0% of samples, so a PERFECT model scores hr_acc 0.2604 while 'always elevated' scores 0.5208. ARM2 looks better only because its training corpus is shifted toward low HR (dryad = 33% of it, cohort mean 61.3 bpm; train marginal resting 38.4% vs ARM1 21.4%), so it emits low buckets more often and accidentally matches the WRONG scorer's marginal. See problem p4-bucket-probe-scores-hr-acc-against-a-different-bucket-definition-than-the-training-target-perfect-model-scores-0-26-majority-scores-0-52.

ALTERNATIVE EXPLANATION TESTED AND REFUTED: that I had created a train/eval mismatch by scoring on a strict-coverage pool. ARM1 train marginal (resting 21.4 / elevated 45.0 / tachy 32.3 / brady 1.3) nearly equals eval (19.1 / 48.1 / 32.2 / 0.6). Not the cause.

CONCLUSION: keep the allowlist; dryad stays excluded. The allowlist is no longer an inherited default justified by a 2026-06-26 comment about dryad's 'resting mass' -- it is now measured. NOTE the reason has CHANGED: the original justification (zero-contamination) is gone, but dryad is still daytime-only (81% of present steps 08:00-18:00) with the LOWEST mean HR of any large cohort (61.3 vs lifesnaps 78.9), an uncharacterised cohort offset that is not a sleep effect.

## Links
Rules out for problem [[canonical-m-mask-marks-all-zero-889451]].

**RETRACTED:** RETRACTED 2026-08-07 — the rule_out answered the WRONG QUESTION and its conclusion ('keep the allowlist, change nothing') is withdrawn as an over-generalisation.

The tested arm was ALL 13 DATASETS vs 3. That bundles dryad — the single dataset with a measured defect — together with eight clean ones, so it could only ever fail, and failing it told us nothing about the datasets the 2026-08-07 corpus update actually repaired. It was a strawman.

The offline measurement I had ALREADY MADE, and then failed to act on, decomposes it per-dataset (between-dataset eta^2 of the hr_mean target, and the cohort-only predictor that goes with it):
  current Phase C allowlist (3)         n= 7196  eta^2 0.246  cohort-only corr +0.496 / +1.12 bpm
  + dreamt                                        eta^2 0.250   (free)
  + big_ideas_glycemic                            eta^2 0.245   (free)
  + mmash                                         eta^2 0.249   (free)
  + wearable_exam_stress                          eta^2 0.280   (small, n=20, HR 115.9)
  ALL EXCEPT DRYAD (12)                 n= 7515  eta^2 0.270  cohort-only corr +0.519 / +1.20 bpm
  + dryad / all 13                      n=12025  eta^2 0.487  cohort-only corr +0.698 / +3.35 bpm
DRYAD ALONE causes the shortcut. Every other dataset is essentially free.

WHAT SURVIVES THE RETRACTION: the finding that dryad specifically must stay out. It is identifiable at balanced accuracy 0.978 from six structural features (coverage, per-channel presence, mean time-of-day of present steps) with NO physiology, and its cohort mean is 61.3 bpm vs lifesnaps 78.9, so 'recognise dryad -> emit 61' is a complete loss-reducing strategy. The trained arm's grounding collapse (monotonic scale response 0.80 -> 0.34, null-sensor disagreement 0.91 -> 0.61) is consistent with that, though it remains confounded with 1.7x fewer passes per sample.

WHAT IS WITHDRAWN: the implied action 'do not change the allowlist'. The correct action, which the data supported all along, is to ADMIT the now-repaired datasets and exclude ONLY dryad. DREAMT in particular was explicitly fixed in this corpus update (n=100, 89% of present steps 22:00-08:00, HR 51-104, sleep block present) and is the ONLY night/sleep cohort, yet it was still absent from Phase C.

SUPERSEDED BY: a real config change to lora_proxy_12ch_v2 (allowlist -> all 12 non-dryad datasets) and a fresh Phase C run, not another throwaway arm.
