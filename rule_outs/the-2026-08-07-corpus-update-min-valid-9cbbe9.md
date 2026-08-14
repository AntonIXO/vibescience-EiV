---
created_at: '2026-08-07T20:52:40+00:00'
former_ids:
- the-2026-08-07-corpus-update-min-valid-mask-enforcement-lifesnaps-hourly-true-15
id: the-2026-08-07-corpus-update-min-valid-9cbbe9
problem_id: canonical-m-mask-marks-all-zero-889451
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-07T20:52:40+00:00'
topic_tags: []
---

## Claim
The 2026-08-07 corpus update (min_valid mask enforcement + LifeSnaps hourly->true 15-min + fixed DREAMT + sleep_prob renormalisation) improves the hr_mean readout of the sensor encoder.

## Method
Controlled before/after on the ENCODER, the one comparison the overwritten corpus still permits. The pre-update Phase B checkpoint outputs/checkpoints/12ch_v1/phase_b survives on disk and its temporal_encoder.pt md5-differs from the new outputs/checkpoints/12ch_v2/phase_b. Both were run through the SAME scripts/probe_phase_a_hr.py, the SAME eval population (data/eval_v2, n=1024 draw) and the SAME code. The only variable is which corpus the encoder was trained on. hr_mean is normalization='none' in schema.py so it is raw bpm under both merges and is directly comparable. I also added a GroupKFold-by-subject probe (p1_subject_grouped_probe) because the shipped random train_test_split leaks subjects (hr_mean between-subject ICC 0.755) and the registered diagnostic eiv_heldout_hr_r2 requires subjects to be EXCLUDED. Unpreregistered; no calibration weight.

## Evidence
SAME probe, SAME eval population, only the training corpus differs:
  hr_mean R2 subject-grouped    OLD 0.9793   NEW 0.9750   (-0.0043)
  hr skill vs trivial (bpm)     OLD +5.909   NEW +5.838   (-0.071)
  hr_mean R2 random split       OLD 0.9869   NEW 0.9839   (-0.0031)
The new corpus is NOT better on this axis -- it is a wash, marginally worse.

WHY THE METRIC COULD NOT HAVE SHOWN AN IMPROVEMENT, AND WHY THIS RULE_OUT IS NARROW: hr_mean is SATURATED. R2 0.98 = 1.3% residual variance; MAE 0.69 bpm against target SD 8.52 and a trivial baseline of 6.60. There is no headroom. Critically, hr_mean was never the quantity the corpus update repaired: the recorded LifeSnaps hourly-grid emulation measured daily PEAK 120.6 -> 110.7 bpm (-9.90 bpm on 73.1% of days) while daily MEAN moved -0.03 bpm. The update targeted hr_PEAK; this probe measures hr_MEAN.

THEREFORE THIS RULE_OUT ELIMINATES ONLY THE hr_mean CLAIM. It must NOT be read as 'the corpus update was worthless'. Independently verified gains that this instrument cannot see: zero-but-present HR steps 13923 samples -> 0; present HR range now exactly [30.00, 200.85]; sleep_prob_* sum per present step 0.031 -> 0.9999; strict-clean scoring pool 1546 (100% pmdata) -> 5334 across 3 cohorts, 63% lifesnaps; LifeSnaps genuinely 15-min sampled (time-adjacent duplicate fraction 0.0000, 1 of 35791 -- not forward-filled). The open question is hr_PEAK skill and the prefix-attributable donor gap, which the shipped probe suite does not emit at all; they require scratch/eval_e34_native_emission.py.

SUGGESTIVE BUT NOT CLAIMED: respiratory_rate subject-grouped R2 went OLD +0.182 -> NEW -0.076. That would be a real regression consistent with the rolling per-user z-score, but rr is only 11.6% present in this pool so n is small; treated as noise until measured where rr is actually present.

PROCESS NOTE FOR THE NEXT SESSION: the saturation was PREDICTABLE from the record before any GPU was spent -- the recorded p1 hr_r2 was already 0.9254 and hr_raw corr +0.977. Check the headroom of the instrument BEFORE running a retrain intended to show improvement.

## Links
Rules out for problem [[canonical-m-mask-marks-all-zero-889451]].
