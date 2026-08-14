---
created_at: '2026-08-05T22:32:35+00:00'
gates:
- gate-strict-clean-population
- gate-powered-n-and-ci
- gate-donor-prefix-null
- gate-scored-vs-max-baseline
- gate-seed-selection-honest
hypothesis_id: the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin
id: rb-e34-powered-native-emission-eval-20260805
priority: 10
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: done
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
updated_at: '2026-08-07T12:35:39+00:00'
---

## Prompt
E34 -- A PROPERLY POWERED, STRICT-CLEAN EVALUATION OF NATIVE hr_peak EMISSION.

=== WHY THIS IS NOW THE TOP OF THE QUEUE ===
This problem was opened on the premise that the model emits hr_peak with -5.88 bpm skill vs always-predict-median (-6.48 clean sparse). Ten experiments (E24-E33) were reasoned against that number. exp-e33-collapse-and-premise-audit-20260805 audited it and found it IS NOT QUOTABLE AS STATED:

  * SOURCE POPULATION IS UNFIT FOR A MAX-BASED TARGET. The per-sample sidecar it came from has n=96, of which 8 rows carry a true 'daily peak' of 0.0 bpm -- physiologically impossible for a MAXIMUM, i.e. sensor dropout swallowed by the max-reducer -- and 25.0% sit below 90 bpm versus 5.0% on the strict-clean population.
  * THE POINT ESTIMATE IS FILTER-DEPENDENT. Same rows, same decode, same model, baseline median recomputed within each population:
      ALL rows              n=96  SKILL -2.37  CI95 [-7.01,+2.38]  r=0.604
      drop zero peaks       n=88  SKILL -6.15  CI95 [-10.22,-1.89] r=0.154
      + daily mean > 40     n=81  SKILL -4.64  CI95 [-8.43,-0.65]  r=0.202
      + coverage > 0.9      n=34  SKILL -3.45  CI95 [-8.52,+2.02]  r=0.422
      drop peak < 90        n=72  SKILL -5.33  CI95 [-9.27,-1.49]  r=0.084
  * ON THE STRICT-CLEAN POPULATION THE CI CROSSES ZERO (n=34).

The deficit appears real but is SMALLER and FAR LESS CERTAIN than -5.88 conveys. Every downstream mechanism hypothesis is currently aimed at a number that may be mostly measurement noise. Establishing a trustworthy effect size is therefore worth more than any further mechanism work, and rb-e33 (gradient attribution) has been deprioritised to 50 and BLOCKED behind this.

=== WHAT TO DO ===
1. SELECT THE STAGE C SEED HONESTLY. The retrain ran N_SEEDS_C=3 because run_12ch_pipeline.sh documents Stage C as bistable (~50% land in a mode-collapse basin emitting a constant ~54-67 bpm regardless of input). Use the pipeline's own LM-level tripwires for selection (parse_failure_rate < 0.5, hr_acc >= 0.40, swap_attribution_acc >= 0.30) and REPORT ALL THREE SEEDS' NUMBERS, not just the winner. Selecting the best of 3 and quoting it as the system's performance is a selection effect and must be labelled as such.

2. EVALUATE NATIVE EMISSION ON THE STRICT-CLEAN POPULATION, at the largest n the eval path supports -- target n >= 300, not 96. Strict-clean filter, identical to E26-E33: in_train & (coverage > 0.9) & isfinite(mean) & isfinite(peak) & (zero_frac == 0) & (daily mean > 40). Explicitly EXCLUDE any sample whose true daily peak is below 40 bpm; such a value is impossible for a maximum and its presence indicts the sample, not the model.

3. REPORT SKILL WITH n AND CI95 ALWAYS. A bare point estimate is not a result. Paired bootstrap on the per-sample absolute errors. The baseline median MUST be computed WITHIN the evaluated population -- importing a median across populations is itself a sign-flipping error and is what made the original numbers unstable.

4. SCORE AGAINST MAX(always-predict-median, donor-prefix, hr_raw-derived), not against the median alone. Majority-only scoring has produced two false passes in this problem already (E25, E26b).

5. RUN THE DONOR-PREFIX ARM at the same n. exp-e33 measured the real prefix as worth +9.916 bpm over a donor prefix on the fitted decode; establish the equivalent for NATIVE emission. If the real-vs-donor gap is large, the conditioning works and the deficit is a calibration/emission problem; if it is near zero, the prefix is inert at emission time.

6. DISTINGUISH NATIVE EMISSION FROM FITTED PROBES EXPLICITLY. probe_e26_digit_ceiling.py's free_run is documented as 'Autoregressive decode driven by the fitted linear classifiers' -- a PROBE, not an emission. In exp-e33 I briefly mistook one for the other and manufactured a false 'sign contradiction'. Label every number in the report as NATIVE EMISSION or FITTED PROBE.

=== DECISION RULE, PREREGISTER BEFORE LOOKING ===
  * If native emission skill is NEGATIVE with a CI EXCLUDING ZERO at n >= 300 -> the deficit is real and powered; unblock rb-e33 gradient attribution against a trustworthy target.
  * If the CI INCLUDES ZERO -> there is no established deficit to explain. The correct conclusion is that the system performs at roughly baseline on native hr_peak emission, the ten-experiment mechanism hunt was chasing an unpowered number, and the problem should be re-scoped or closed rather than extended.
  * If skill is POSITIVE with a CI excluding zero -> the premise is refuted outright and must be retracted on the record.

=== HONEST SCOPE ===
This measures what the retrained model emits. It does not explain why. It also cannot rule out that a different seed would behave differently -- which is exactly why all three seeds get reported.

=== DO NOT RE-LITIGATE ===
Three rule-outs are recorded on this problem: (i) geometry/scale of the handed-over representation (projector whitening, vocab-statistics init, soft-token norm rescaling); (ii) target encoding (prior-domination, variable-width/place-value rendering, 'the target lacks information'); (iii) the -5.88 figure as a stable quotable property, plus mode collapse and prior sampling as explanations for it. Also settled: dual avg+max pooling (E1), non-linear readout heads (E28), more/finer bins (E28), window shortening.

## Preconditions
*** CRITICAL UPDATE FROM exp-e33f-tripwire-validity-20260806: DO NOT USE p3 OR p6 FOR SEED SELECTION -- BOTH ARE BROKEN ***

The Stage C driver's own selection logic is defective. It declared seed 1 'collapsed=yes:swap<0.3' while the SAME probe file showed output_diff_rate_zero 1.0000, monotonic_scale_response 0.8438, null_sensor_bucket_disagreement 0.6563, parse_failure_rate 0.0000 and hr_acc 0.4297 -- a model that ignores its prefix cannot produce those numbers.

DEFECT 1 -- p3 swap_attribution_acc IS VACUOUS. src/probes/counterfactual.py:142 counts a pair only when bucket_a != bucket_b; line 148 returns 0.0 when swap_total == 0. 'Never measured' is numerically identical to 'failed every attempt', and swap_total was NOT persisted, so the ambiguity is permanent in old artifacts. counterfactual.py:100 already states this generate-based P3 is 'superseded by scratch/diag_swap_gap.py ... the decisive swap metric'. FIXED 2026-08-06: counterfactual.py now also returns swap_total, swap_correct and swap_measured -- CHECK swap_measured BEFORE trusting the ratio.

DEFECT 2 -- p6 SHUFFLE CONTROL SCORES AGAINST THE WRONG LABEL. src/probes/shuffle_control.py:79-87 derives ground truth from hardcoded thresholds on the daily MEAN, while training uses datasets.hr_label_bucket on the daily PEAK. Measured on strict-clean n=1546: agreement 19.40%, disagreement 80.60%. The trained label assigns TACHYCARDIC to 40.88% of the population and p6 assigns it to 0.0000 -- that class is UNREACHABLE under the eval label. A correctly-answering model is marked wrong on ~81% of samples, which is why both p6 arms read exactly 0.0. A warning is now in the source; the accuracies must not be read as model quality until it is switched to hr_label_bucket.

USE THESE FOR SELECTION INSTEAD:
  * p4 hr_acc and parse_failure_rate -- these use the TRAINED label and are unaffected.
  * the p2 ablation family -- output_diff_rate_zero / _random / _permute, monotonic_scale_response, null_sensor_bucket_disagreement.
  * scratch/diag_swap_gap.py -- the teacher-forced value-token swap-gap the source itself names as decisive.
RE-EXAMINE any seed the driver marked collapsed against p2/p4 before discarding it; seed 1 in particular is NOT established as collapsed.

--- PRIOR CONTEXT (unchanged) ---
Stage C retrain launched 2026-08-05 22:22 UTC: 'STAGES=c N_SEEDS_C=3 LORA_DIR=outputs/adapters/medgemma_12ch_e33 PROBE_C_OUT=outputs/probes/e33_phase_c.json METRICS_LOG_C=logs/e33_phase_c_metrics.jsonl PHASE_LOG_C=logs/e33_phase_c.log bash run_12ch_pipeline.sh', driver log logs/e33_stage_c_driver.log, per-seed logs logs/e33_phase_c.seed_N.log, per-seed probes outputs/probes/e33_phase_c.seed_N.json, adapters outputs/adapters/medgemma_12ch_e33.seed_N. ~3 h per seed. Profile lora_proxy_12ch (hr_regression_weight 0.002 -- documented LOAD-BEARING; the older lora_proxy_12ch_attnfix used 0.1). Phase B checkpoint outputs/checkpoints/12ch_v1/phase_b exists. GPU RTX 5070, ceiling 11.25 GiB, export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True. Never pipe a long script to tail/head; use 'timeout <N> uv run python -u <script> > logs/<name>.log 2>&1' with background=true, notify_on_complete=true. Eval entry points: scratch/eval_e23_gates.py (writes a <out>_per_sample.json sidecar), scratch/diag_e23M_effect_vs_prior.py, scratch/diag_e23N_dose_response.py, scratch/diag_swap_gap.py.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Run as [[the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin]].
Intended gates: `gate-strict-clean-population`, `gate-powered-n-and-ci`, `gate-donor-prefix-null`, `gate-scored-vs-max-baseline`, `gate-seed-selection-honest`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#cross-modal #medgemma #collapse #sensor-text
