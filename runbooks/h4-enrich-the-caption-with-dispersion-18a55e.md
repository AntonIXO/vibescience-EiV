---
created_at: '2026-08-08T04:03:48+00:00'
former_ids:
- h4-enrich-the-caption-with-dispersion-temporal-structure-then-re-probe-hr-peak-s
gates: []
hypothesis_id: h4b-supersedes-h4-sharpened-from-add-82c23a
id: h4-enrich-the-caption-with-dispersion-18a55e
notes: []
priority: 1
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
status: done
topic_tags: []
updated_at: '2026-08-14T16:28:43+00:00'
---

## Prompt
H4: enrich the caption with dispersion + temporal structure, then re-probe hr_peak.

STATUS: H4 is PREREGISTERED AND UNTESTED. No experiment has been started against it. Its
predicted_effects are on the record so it stays calibration-eligible; do not read it as a result.

WHY PRIORITY 1. It is the hypothesis that most directly addresses the measured gap, and the
measurement points at it: on identical subject-grouped folds, hr_peak reads
  level only 0.023 -> CAPTION SCALARS 0.124 -> deep stack 0.421 -> hand dispersion 0.708 -> bound 0.971
The five features responsible for the 0.124 -> 0.708 jump (hr_std, hr_p25, hr_p75, hr_iqr, fraction
of minutes above the day's own p75) are all DISPERSION and none appear in caption_style: hr_only.
Meanwhile hr_MEAN is solved (0.971, MAE 1.15 bpm vs a trivial 7.60). An objective aligned against
level-only text learns level and discards spread, by construction.

STEPS
1. Extend the caption builder with (a) dispersion in physical units - the peak, p25/p75, minutes
   above a personal threshold; (b) WHEN the peak occurred and the nocturnal/diurnal split, i.e.
   temporal structure rather than more decimals; (c) at least one conjunctive statement.
2. Retrain Phase A only: ~15 min GPU plus a fresh ~50 min CPU text-embedding precompute, because
   the cache key includes caption_style. Set EIV_TEXT_ENCODER_DEVICE=cpu (MedGemma bf16 needs
   ~10.3 GiB and does not fit the 11.5 GiB RTX 5070 with a desktop attached).
3. Probe with scripts/probe_phase_a_hr.py against data/eval_v3_1min at --num-samples 6624 and read
   eiv_peak_excursion_increment_r2 WITH its level-only control quoted alongside.
4. Compare against the recorded control arm: increment +0.4086, hr_mean 0.9713, hr_peak MAE 12.80
   bpm vs trivial 16.97. Preregistered gate: increment >= 0.56.

CONTROLS THAT MUST SHIP WITH IT
 - Recompute the level-only control on the same pool: a caption that merely restates level would
   raise the raw R2 without raising the increment.
 - Report hr_mean in the same table. hr_peak rising while hr_mean FALLS means the caption is a
   fixed-capacity channel being reallocated, not a ceiling being raised - a different and less
   interesting result that must not be reported as a win.
 - Run >= 3 seeds. The documented seed spread for this profile is 0.066 and the H1 arm was lost
   inside exactly that band.

=== CORRECTED 2026-08-08: THE OLD 'DO NOT BOTHER WITH' CLAUSE IS WITHDRAWN ===
This runbook previously said: "DO NOT BOTHER WITH (refuted by direct measurement): window
shortening, patch-construction / 'don't pool at all', and the allowlist repair." ALL THREE of the
rule_outs it pointed at have since been RETRACTED BY THEIR AUTHOR (2026-08-08 11:13-11:27) as
compound claims - a narrow measured half filed as if it closed a broad untested half. Do NOT rely
on that clause. Current standing:
 - allowlist repair: RE-OPENED and separately queued at priority 1 (full A->B->C, generation-scored).
   Phase B and Phase C were never trained with a changed allowlist at all.
 - window shortening: the peak-prediction RATIONALE is dead (R2(peak<-mean) 0.023 -> 0.748 at W=60,
   tachy base rate 32.1% -> 2.5%), but no shortened-window model was ever trained and W=60 gives
   21x more windows (6624 -> 143418) to a data-hungry objective. UNTESTED, not refuted.
 - patch construction: part (a) is exact algebra and STANDS (max over 23 patch maxima == true daily
   peak to 0.0000 bpm on all 6624 days; operator rank 1440/1440). Part (b), the LEARNED projector's
   pool->sensor_slots stage, rests on a 15-MINUTE measurement and is UNTESTED at 1-minute.
None of these is a reason to delay H4 - H4 remains priority 1 - but none may be cited as closed.

SEPARATE, CHEAPER, ALSO UNTESTED: the Phase B coverage-gate defect. phase_b_12ch has NO
min_hr_coverage and its min_coverage=0.25 is all-channel with a 0.333 floor, so it admits 1981
samples Phase A rejects, 91.5% of them with exactly zero HR coverage (~22% of Phase B's training
set). Fixing that is a one-line config change and is independent of H4.

## Links
Queued for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Run as [[h4b-supersedes-h4-sharpened-from-add-82c23a]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
