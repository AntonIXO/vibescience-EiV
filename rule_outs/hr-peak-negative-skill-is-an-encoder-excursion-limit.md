---
created_at: '2026-09-01T10:43:03+00:00'
former_ids: []
id: hr-peak-negative-skill-is-an-encoder-excursion-limit
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:43:03+00:00'
topic_tags: []
---

## Claim
hr_peak's negative skill is an encoder excursion limit -- the model cannot represent within-day extremes, so the peak slot underperforms a constant.

## Method
Read the peak's definition in _build_json_slots_target, then subsampled the observed epochs of 400 high-coverage windows at fixed window identity and measured how far the masked max falls as a function of retained coverage; denormalised through population_stats.

## Evidence
THE TARGET ITSELF IS COVERAGE-BIASED, AND THE BIAS IS THE SAME SIZE AS THE DEFICIT (scratch/_peak_target_coverage_bias.py). _build_json_slots_target computes the peak as np.nanmax over the OBSERVED epochs of channel 0. A masked max is monotone in coverage by construction: observing fewer epochs can only lower it, never raise it. So on a sparsely worn day the target states a lower peak as fact.

SUBSAMPLING TEST -- the clean one, because it holds the WINDOW fixed and varies only how many epochs are observed. 400 windows with HR coverage >= 0.90 and >= 10 observed epochs, thinned at random:

  keep    peak p50 (SD)   drop      in bpm (iqr 23.62)
  1.0        2.593        +0.000     --
  0.8        2.547        -0.046     -1.09
  0.6        2.515        -0.077     -1.82
  0.4        2.368        -0.225     -5.31
  0.2        2.227        -0.365     -8.62
  0.1        1.927        -0.666    -15.73

No physiology, no dataset mix, no model -- identical windows, only the mask changes. At 40% coverage the target peak is 5.3 bpm low; at 20% it is 8.6 bpm low.

THAT IS THE SAME MAGNITUDE AS THE THING IT WOULD EXPLAIN. The incumbent's supabase hr_peak MAE is 21.8-24.3 against a 17.4 constant, i.e. 4.4-6.9 bpm of unexplained deficit, and supabase mean HR coverage is 0.789 with a long sparse tail. A target that is several bpm low on the sparse rows trains the model to under-predict exactly there, which is also what the compressed emission scale shows (every hr_peak capture at 0.49-0.90 of true spread, never in band).

THE CORPUS-WIDE GRADIENT IS CONSISTENT: observed peak by HR-coverage decile rises 0.758 -> 2.654 (SD units) from the sparsest to the 9th decile, corr(coverage, observed peak) = +0.280 over 14,010 windows.

SO THE 'EXCURSION LIMIT' FRAMING IS AT BEST INCOMPLETE. It says the encoder cannot represent extremes. The measurement says the LABEL does not contain them on sparse days -- the model is being trained on a systematically low target and reproducing it faithfully. Those predict the same MAE and are distinguished by whether the deficit tracks coverage, which it does.

WHAT I AM NOT CLAIMING: that this is the whole story. hr_raw is computed from the same masked cells and does NOT show the collapse, because a masked MEAN is unbiased under random missingness while a masked MAX is not -- that asymmetry is exactly why the peak slot is the one that fails. But real missingness is not random (nights, charging, non-wear), so the true bias could be larger or smaller than the random-thinning estimate above.

NO ARM QUEUED. The candidate fix -- state the peak conditional on coverage, or omit it below a coverage floor the way `state` is omitted when unobserved -- is a target-schema change, and the seed family is mid-flight. It also needs its own one-variable arm because the json_slots schema is what the prompt documents.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
