---
created_at: '2026-08-05T16:45:40+00:00'
former_ids:
- the-claim-arm-1-was-a-genuine-win-for-the-numeric-slots-is-only-half-true-and-mu
id: the-claim-arm-1-was-a-genuine-win-for-d1299e
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T16:45:40+00:00'
topic_tags: []
---

## Claim
The claim 'Arm 1 was a genuine win for the numeric slots' is only HALF true and must be split. hr_raw SURVIVES the zero-exclusion audit; hr_peak DOES NOT and neither does the bucket-entropy regime split. Additionally the preregistered outcome gate 'gate-peak-corr-positive' PASSED (+0.604 pooled) on an artifact and should never have been credited. Specifically ELIMINATED: (a) 'the SPARSE hr_raw +0.965 is a bimodality artifact' -- it is NOT, it survives at +0.830 and beats the clump-only ceiling; (b) 'Arm 1's peak_corr gain is evidence of peak tracking' -- it is NOT, 69.3% of it is the zero cluster and the clean residual loses to an always-predict-median constant; (c) 'the SPARSE split entropy 0.537 would pass the 0.50 entropy gate on its own' -- it would NOT, it is 0.245 once degenerate samples are excluded.

## Method
Patched scratch/eval_e23_gates.py to persist per-sample rows (true hr_mean, true hr_peak, pred hr_raw, pred hr_peak, pred bucket, true bucket, hr coverage) to a JSON sidecar -- previously only aggregates were saved, which blocked all offline decomposition. Re-ran the FULL eval on outputs/adapters/medgemma_12ch_arm1_attnfix (RTX 5070, 4-bit, greedy do_sample=False, rng seed 0, 12 per true label per coverage group) producing 96 per-sample rows, then decomposed offline via scratch/audit_e0_decompose.py. Added a CLUMP-ONLY control predictor (knows only zero-cluster membership, emits the cluster median => zero within-cluster tracking ability by construction) to establish the artifact ceiling a correlation must clear, per skill debugging-conditioning-collapse Step 3.9. Also reconstructed the exact scored population offline with no GPU (scratch/audit_e0_population.py) by replaying the deterministic filter+seed, which independently confirmed the FULL-split reference numbers (target std 10.54, always-predict-median MAE 8.24).

## Evidence
METHOD NOTE: the re-run reproduced the recorded Arm 1 aggregates BIT-EXACTLY (FULL hr_peak +0.276 / hr_raw +0.740 / entropy 0.073 / 47-of-48 tachycardic; SPARSE +0.646 / +0.965 / 0.537; pooled entropy 0.370, peak_corr +0.604), so the per-sample rows are the same population that produced the original numbers.

SCORED POPULATION (exact, seed 0, label-stratified 12/label):
  FULL cov>0.5   n=48, zero cluster (true hr_mean<20) = 0/48 (0.0%)
  SPARSE cov<=0.5 n=48, zero cluster = 12/48 (25.0%); true_mean percentiles p0=0.0 p25=23.1 p50=67.5 p100=84.5 (confirms the suspected zero + physiological bimodality)

(a) hr_raw SPARSE -- ARTIFACT HYPOTHESIS REJECTED, claim survives but shrinks:
  corr +0.9649 (all, n=48) -> +0.8302 (clean true>=20, n=36); drop +0.1347 = only 14.0% of the correlation
  CLUMP-ONLY control (1 bit = in/out of zero cluster, emits cluster median, zero within-cluster skill by construction): corr +0.9065. Model +0.9649 BEATS this ceiling, so it carries genuine within-cluster tracking.
  Skill vs always-predict-median: +16.37 bpm (all) -> +4.27 bpm (clean, baseline MAE 10.73 vs model 6.46). Real, and still positive after the audit.

(b) hr_peak SPARSE -- ARTIFACT CONFIRMED, and worse than 'artifact':
  corr +0.6460 (all) -> +0.1980 (clean, n=40); drop +0.4480 = 69.3% of the correlation came from the zero cluster
  CLUMP-ONLY ceiling +0.8789 > model +0.6460 => the model DOES NOT beat a 1-bit cluster-membership predictor
  Skill vs constant: +1.14 bpm (all) -> -6.48 bpm (clean): model MAE 25.12 vs always-predict-median MAE 18.64. NEGATIVE skill.

(c) hr_peak FULL-coverage (no zero cluster at all, so nothing to blame on contamination):
  corr +0.2764 but model MAE 25.05 vs always-predict-median MAE 19.17 => skill -5.88 bpm. The hr_peak slot is WORSE THAN A CONSTANT on the clean full-coverage regime.

(d) CONSEQUENCE FOR THE PREREGISTERED GATE: pooled eiv_gen_peak_corr = +0.604 PASSED its 0.30 bar. But it is a pool of a split where hr_peak loses to a constant (-5.88 bpm skill) and a split where 69.3% of the correlation is cluster separation. The PASS is an artifact of pooling plus contamination; hr_peak passes no honest test in either regime.

(e) BUCKET REGIME SPLIT ALSO FAILS: SPARSE entropy 0.537 -> 0.245 excluding zero samples, and accuracy 0.438 -> 0.306 against a 0.333 majority-class baseline (i.e. BELOW majority). The recorded reading 'SPARSE cov<=0.5 entropy 0.537 would pass alone' is withdrawn -- the apparent bucket diversity was the model emitting 'bradycardic' on degenerate all-zero samples (13 bradycardic predictions, of which the clean subset retains 1). FULL split is unaffected by exclusion (0 zero samples): entropy 0.073, acc 0.271 vs 0.250 majority.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].
