---
created_at: '2026-09-01T07:46:57+00:00'
former_ids: []
id: the-encoder-input-is-dominated-by-hr_mean-96-percent-of-variance
problem_id: canonical-merge-defaults-to-rolling-per-253051
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:46:57+00:00'
topic_tags: []
---

## Claim
The encoder's input is dominated by hr_mean (96.07% of present-only first-layer pre-activation variance, 99.77% as actually fed), because hr_mean is declared normalization=none, FlexMLP has no input normalisation and use_layernorm is false in every 12ch profile. The 0.0 missing-fill sits at -4.18 SD on 45.7% of cells. Consequently the ro-sensor-slot-granularity and ro-window-shortening rule-outs are CONDITIONAL on that input scaling and deserve re-testing, and a use_layernorm arm is the cheap discriminating test.

## Method
Recomputed the first-layer input variance decomposition the addendum describes, both on present cells only and as actually fed (zero-filled), plus the position of the 0.0 fill in SDs of each channel's present distribution, over ~2,000 sampled windows. Cross-read merge_manifest.json's channel_policy and grepped every training profile for use_layernorm.

## Evidence
STALE: the premise was fixed by the population-norm re-merge, and the addendum was never re-measured after it. Measured on the current [14041,1440,12] bundle (scratch/_input_variance_audit.py, CPU):

  channel            policy       present var%   as-fed var%   fill in SD
  hr_mean            population        13.35%        13.04%        -0.02
  hrv_rmssd          population        13.75%         4.04%        -0.06
  hour_of_day_sin    none              10.42%        16.75%        -0.00
  hour_of_day_cos    none              10.42%        16.75%        -0.00
  day_of_week_sin    none              10.17%        16.29%        -0.00
  day_of_week_cos    none              10.56%        16.94%        -0.10
  skin_temperature   population        12.45%         4.87%         0.18
  respiratory_rate   population         5.53%         4.04%         1.45
  sleep_prob_*       none          1.86-5.09%    0.78-3.57%  -0.35..-1.03

hr_mean is now 13.35% present-only and 13.04% as-fed, against the recorded 96.07% / 99.77% -- a 7x reduction, and it is no longer the largest contributor (the four circadian channels are, at ~16.8% each as-fed, which is expected since they are dense by construction). The missing-fill now sits at -0.02 SD instead of -4.18 SD, exactly as the record predicted population norm would achieve: 'after standardisation the fill value 0.0 IS the channel median, so missing becomes neutral'.

The manifest's channel_policy confirms the cause: hr_mean is declared 'population' now, not 'none'. The record's own text says hr_mean is 'the one channel declared normalization=none' -- that is no longer the schema in force.

WHAT THIS RELEASES. The record flags ro-sensor-slot-granularity-20260805 and ro-window-shortening-20260805 as rule-outs CONDITIONAL on the 96%-one-channel input scaling, and explicitly says the causal claim is untested and must not be treated as a retraction. Since the scaling condition is gone, that conditionality is discharged on its own terms: both rule-outs were measured under an input regime that no longer exists, so neither is invalidated NOR re-validated by this -- they simply lose the pending caveat, and anyone re-opening them must re-measure rather than cite the addendum.

WHAT IS STILL TRUE: use_layernorm remains false in all profiles (0 of them set it). The proposed one-line arm is still runnable, but its motivation is now much weaker -- it was justified by a 59x variance imbalance that is now ~1.3x between the largest contributor and hr_mean.

## Links
Rules out for problem [[canonical-merge-defaults-to-rolling-per-253051]].
