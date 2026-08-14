---
created_at: '2026-08-11T22:02:22+00:00'
id: the-shipped-p4-bucket-accuracies-hrv-acc-state-acc-deep-sleep-acc-can-be-compare
problem_id: p4-bucket-probe-scores-hr-acc-against-a-different-bucket-definition-than-the-training-target-perfect-model-scores-0-26-majority-scores-0-52
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-11T22:02:22+00:00'
topic_tags:
- denominator
- baseline
- control
---

## Claim
The shipped p4 bucket accuracies (hrv_acc, state_acc, deep_sleep_acc) can be compared across Phase-C arms as if they were accuracies on a common sample. Specifically, that v4c's hrv_acc 0.8125 versus v5c's 0.4000 shows v4c reading HRV roughly twice as well.

## Method
Exact rational reconstruction (Fraction.limit_denominator over the shipped ratios) of the denominators behind every p4 number in the four shipped full-suite JSONs (v4c/v4w/v5c/v5d, n_generated=128 each), which record only bare ratios and no denominators. Then the ground-truth hrv marginal measured independently on the same eval bundle at the probe's own grading cut points, reproducing the suite's exact RandomState(42) 512-row draw and mask-weighted denormalisation through population_stats (scratch/hrv_truth_marginal.py). Then Wilson 95% CIs, an exact one-sided binomial against the measured marginal, and a two-proportion z test across arms.

## Evidence
RECOVERED DENOMINATORS (n_generated=128 each): v4c hrv 13/16 = 12% of samples; v4w hrv 20/29 = 23%; v5c hrv 2/5 = 4%; v5d hrv 13/20 = 16%; v4c state 9/16 = 12%; v4c and v4w deep_sleep 0/1, a SINGLE sample; v5c and v5d deep_sleep NaN, never scored at all. THE SUBSET IS CLASS-SELECTIVE, NOT RANDOM: the pre-c53cd53 HRV_MAP knew only decreased/normal/increased/normal-range while the target builder (datasets.py:1514-1518) and prompts/json_slots_prompt.md use low/normal/high. Observed emitted values across the four arms were {high 64, normal 34, low 10} of 108, so the map admitted essentially only emissions saying 'normal'. hrv_acc therefore measured P(truth=normal | model said normal) -- the PRECISION OF ONE CLASS. GROUND-TRUTH MARGINAL (data/eval_v3_1min, same grading cut points hrv<30 low / <60 normal / else high, 263 of 512 drawn rows carry hrv): low 0.2548, normal 0.4791, high 0.2662; hrv raw median 40.46 ms, mean 47.18, sd 22.22. CROSS-ARM COMPARISON FAILS: Wilson 95% CIs v4c [0.570,0.934], v4w [0.508,0.827], v5c [0.118,0.769], v5d [0.433,0.819] are all mutually overlapping; v4c vs v5c two-proportion z=+1.782, p=0.075, NOT significant. One flipped sample moves v4c by 6.25 points and v5c by 20 points. The four denominators are four different subsets of days, so these are four different questions rather than four answers to one. IMPORTANT NON-REFUTATION, RECORDED SO THIS IS NOT OVER-READ: v4c's 13/16 against the 0.4791 base rate IS significant on its own (exact one-sided binomial P(X>=13 | p=0.4791, n=16) = 0.0068). When v4c says 'normal' it is right 81% of the time versus a 48% base rate, so the normal-class precision lift is REAL. What is eliminated is (a) calling that number hrv accuracy and (b) comparing it across arms. Recall on the low/high classes -- 52.1% of days -- remains entirely unmeasured because those emissions never entered the numerator OR the denominator.

## Links
Rules out for problem [[p4-bucket-probe-scores-hr-acc-against-a-different-bucket-definition-than-the-training-target-perfect-model-scores-0-26-majority-scores-0-52]].

#denominator #baseline #control
