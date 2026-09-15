---
artifacts:
- logs/w12_extent_heldout.json
- scratch/w12_extent_heldout.py
- scratch/_w12_g2_diagnosis.py
- logs/w12_extent_heldout.log
closed: true
config_note: 'W12 out-of-sample extent test. Runner scratch/w12_extent_heldout.py
  -> logs/w12_extent_heldout.json (RC=0, zero tracebacks/OOM). Probe only, NO training.


  Corpus: the 2026-08-30 rebuild [13879,1440,12], merge git_sha 56bb0b5. Encoder:
  outputs/checkpoints/baseline_sync/phase_b, which was trained on the PREVIOUS [13518]
  bundle and therefore has NEVER SEEN popane_s5 (199 windows) or popane_s6 (140) --
  that is the whole design. n=13818 admitted rows, 1407 subjects.


  Readout held IDENTICAL to W5/W6: subject-grouped GroupKFold(5) RidgeCV on production-stage
  soft tokens (random-projected to 48 dims, same seeded projection in both arms),
  predicting the window''s PRESENT-ONLY mean HR in bpm; ratio = std(OOF pred)/std(truth)
  per cohort. The ONLY difference between arms is patch_pool_mode + patch_presence.


  ONE DELIBERATE CHANGE vs w6_primary_ratio.py: cohort class is DATA-DRIVEN (median
  measured-HR minutes < 240 = SHORT) instead of a hardcoded 7-name list, because that
  list predates popane_s5/s6 and would have silently excluded exactly the cohorts
  under test.'
cost:
  gpu_hours: 0.05
  wall_hours: 0.08
crash_reason: ''
created_at: '2026-08-29T22:02:09+00:00'
diagnostics_measured:
- after: 0.6989
  before: 0.4577
  diagnostic_id: eiv_extent_variance_compression_ratio
do_not_retry:
- 'Do NOT score eiv_extent_variance_compression_ratio on a cohort MEAN. This run''s
  g2 failed purely because mmash (n=42, ratio 4.49 at r=0.009 -- a broken readout,
  not a compressed signal) dragged an 8-cohort mean by -0.14 while the median moved
  +0.0040 and no working cohort lost more than 0.05. rule_out that-the-mean-gap-naive-pooled-mean-present-5bb2e3
  already said this and I preregistered a mean anyway. Future gates on this diagnostic:
  per-cohort worst-case or median, with a ratio>2-and-|r|<0.15 degeneracy filter declared
  UP FRONT.'
- Do NOT use the hardcoded SHORT/LONG cohort lists in scratch/w6_primary_ratio.py
  on any corpus newer than [13518]. They predate popane_s5/s6 and would silently exclude
  the very cohorts under test. Classify by median measured-HR minutes as scratch/w12_extent_heldout.py
  does.
- 'Do NOT treat mmash as a usable cohort for this diagnostic until its readout is
  diagnosed: n=42, avg ratio 4.4902, r=0.009 in BOTH arms. Whatever it measures, it
  is not variance compression.'
- 'Do NOT read consumer_grade_wearables'' +0.1216 ratio gain as a tracking gain: its
  within-cohort r is 0.051 -> 0.030, i.e. dead in both arms. Ratio and tracking are
  separate axes (W6 established this); quote both or neither.'
external_run: logs/w12_extent_heldout.json
former_ids: []
gate_results:
- evidence: 'PASS, and it is the strongest extent result on record. On cohorts the
    encoder has NEVER seen, masked_avg raises the ratio from 0.4577 to 0.6989 -- delta
    +0.2412, 4.8x the preregistered +0.05 bar. Per cohort: popane_s5 0.4118 -> 0.6622
    (within-cohort r 0.870 -> 0.968, n=199), popane_s6 0.5036 -> 0.7355 (r 0.865 ->
    0.927, n=140). Both ratio AND tracking improve, and both start LOW exactly as
    the contraction identity predicts for 27- and 34-minute sessions in a 1440-epoch
    window. The held-out delta (+0.2412) is LARGER than the seen-cohort delta measured
    on the same encoder in the same run (+0.1505), which is the opposite of the memorisation
    account.'
  gate_id: g1-repair-generalises-to-unseen-cohorts
  passed: true
- evidence: 'FAIL as preregistered, and I am recording it as a FAIL rather than reinterpreting
    it. LONG-cohort MEAN ratio moves 1.3983 -> 1.2548, delta -0.1436, breaching the
    -0.05 bar.


    DIAGNOSED, NOT OVERRIDDEN (scratch/_w12_g2_diagnosis.py): the entire breach is
    ONE degenerate cell. mmash (n=42) reads avg ratio 4.4902 with r=0.009 -- predictions
    varying 4.5x more than truth while carrying no correlation at all is a broken
    readout for that cell, not a compressed signal, and masked_avg moves it -1.2695.
    Excluding it, LONG mean goes 0.9566 -> 0.9739 (delta +0.0173) and NO long cohort
    loses more than 0.05 (worst is big_ideas_glycemic at exactly -0.0500). Median
    over all 8 long cohorts moves +0.0040. Only 2 of 7 non-degenerate long cohorts
    move at all beyond 0.05, both UPWARD (optihealth_supabase +0.0951, dreamt +0.0561).


    THE METHOD LESSON IS MINE TO OWN: this vault already carries rule_out that-the-mean-gap-naive-pooled-mean-present-5bb2e3,
    which says never score this diagnostic on a MEAN. I preregistered g2 on a mean
    anyway and a single n=42 cohort with a broken readout flipped it. The gate is
    scored FAIL because that is what I committed to; the honest reading is ''no harm
    on any cohort with a working readout, and the gate statistic was badly chosen''.'
  gate_id: g2-long-cohorts-no-harm
  passed: false
- evidence: PASS. All 10 seen SHORT cohorts improve under masked_avg (10/10), reproducing
    W6's 7/7 qualitative pattern on a different encoder and a different corpus. Seen-short
    mean 0.5038 -> 0.6543, delta +0.1505. Notably nurse_stress is again the laggard
    (+0.0132), as it was the weak cohort in W6's table, and consumer_grade_wearables
    has a near-dead readout in both arms (r 0.051 -> 0.030) so its ratio gain of +0.1216
    should not be read as a tracking gain.
  gate_id: g3-seen-cohorts-consistent-with-w6
  passed: true
git_ref: ''
hypothesis_id: the-mask-weighted-pooling-repair-w6-corrects-a-6a722b
id: exp-w12-extent-heldout-20260830
interpretation:
  at: '2026-08-29T22:03:46+00:00'
  failure_mode: test_invalid
  lesson: 'THE REPAIR IS GEOMETRIC, NOT MEMORISED -- and the cleanest evidence is
    that it works BETTER out-of-sample. On popane_s5/s6, which the baseline_sync encoder
    has never seen, masked_avg moves the variance-compression ratio 0.4577 -> 0.6989
    (+0.2412), while the SAME encoder in the SAME run gains only +0.1505 on the ten
    short cohorts it trained on. A memorisation account predicts the opposite ordering,
    so it is eliminated. Tracking improves alongside scale (s5 r 0.870 -> 0.968, s6
    0.865 -> 0.927), which matters because W6 established ratio and r as separate
    axes and a scale-only gain would have been the weaker result.


    WHY THE OUT-OF-SAMPLE GAIN IS LARGER, stated as a hypothesis rather than a finding:
    s5 and s6 are the shortest well-behaved cohorts in the corpus (27 and 34 measured-HR
    minutes, tight ranges 17-38 and 19-44) and the contraction is a function of coverage
    c, so the cohorts with the smallest c have the most to recover. The seen-short
    mean is diluted by nurse_stress (+0.0132, a shift-schedule cohort with r~0.34)
    and consumer_grade_wearables (dead readout, r 0.051 -> 0.030). Consistent with
    geometry, but ''held-out > seen'' must not be generalised to ''unseen data is
    easier''.


    THE FAILURE IS MINE AND IT IS A STATISTIC-CHOICE FAILURE, NOT AN OPERATOR FAILURE
    -- which is why the failure mode is test_invalid rather than claim_false. g2 asked
    whether long cohorts are harmed and I preregistered it on a MEAN. mmash -- n=42,
    avg ratio 4.4902 at r=0.009, i.e. predictions varying 4.5x more than truth while
    carrying no correlation whatsoever -- moved -1.2695 and flipped the whole gate.
    On the median the long half moves +0.0040; excluding that one cell the mean moves
    +0.0173 and no cohort loses more than 0.05. This vault ALREADY carries rule_out
    that-the-mean-gap-naive-pooled-mean-present-5bb2e3 saying never to score this
    diagnostic on a mean, and I did it anyway. directional_only is the correct verdict:
    the claim is not established, because the instrument I committed to could not
    test it.


    REUSABLE RULE: when a gate aggregates over heterogeneous cohorts, declare the
    degeneracy filter UP FRONT -- here ''ratio > 2 AND |r| < 0.15 is a broken readout,
    not a measurement'' -- and gate on the per-cohort worst case or the median. A
    mean over cohorts whose readouts differ in quality is not a summary of the operator;
    it is a summary of the worst readout.


    CONSEQUENCE: do not re-run this as-is. The right next test is the same measurement
    with a preregistered median/worst-case gate and mmash''s readout diagnosed or
    excluded on stated grounds. That is a re-scored instrument rather than a new arm,
    so it is cheap -- but it must be committed before looking, since I have now seen
    these numbers.'
  transfers_to:
  - h-w6-mask-weighted-pooling-restores-short-9da5b6
  - h-w10-train-serve-pooling-mismatch-the-w6-f512a3
key_numbers:
  heldout_short_avg: 0.4577
  heldout_short_delta: 0.2412
  heldout_short_masked: 0.6989
  long_all_mean_delta: -0.1436
  long_clean_mean_delta: 0.0173
  long_median_delta: 0.004
  mmash_avg_r: 0.009
  mmash_avg_ratio: 4.4902
  mmash_delta: -1.2695
  n_rows: 13818.0
  n_subjects: 1407.0
  popane_s5_avg: 0.4118
  popane_s5_masked: 0.6622
  popane_s5_r_avg: 0.87
  popane_s5_r_masked: 0.968
  popane_s6_avg: 0.5036
  popane_s6_masked: 0.7355
  preregistered_g1_bar: 0.05
  seen_short_delta: 0.1505
observed_effects:
- delta: 0.2412
  diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_extent_variance_compression_ratio: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[the-mask-weighted-pooling-repair-w6-corrects-a-6a722b]].
External run: logs/w12_extent_heldout.json

## Measurements
- [[eiv_extent_variance_compression_ratio]]: before 0.4577 → after 0.6989  (Δ +0.2412, up ✓ predicted up)

## Gates
- `g1-repair-generalises-to-unseen-cohorts`: PASS — PASS, and it is the strongest extent result on record. On cohorts the encoder has NEVER seen, masked_avg raises the ratio from 0.4577 to 0.6989 -- delta +0.2412, 4.8x the preregistered +0.05 bar. Per cohort: popane_s5 0.4118 -> 0.6622 (within-cohort r 0.870 -> 0.968, n=199), popane_s6 0.5036 -> 0.7355 (r 0.865 -> 0.927, n=140). Both ratio AND tracking improve, and both start LOW exactly as the contraction identity predicts for 27- and 34-minute sessions in a 1440-epoch window. The held-out delta (+0.2412) is LARGER than the seen-cohort delta measured on the same encoder in the same run (+0.1505), which is the opposite of the memorisation account.
- `g2-long-cohorts-no-harm`: **FAIL** — FAIL as preregistered, and I am recording it as a FAIL rather than reinterpreting it. LONG-cohort MEAN ratio moves 1.3983 -> 1.2548, delta -0.1436, breaching the -0.05 bar.

DIAGNOSED, NOT OVERRIDDEN (scratch/_w12_g2_diagnosis.py): the entire breach is ONE degenerate cell. mmash (n=42) reads avg ratio 4.4902 with r=0.009 -- predictions varying 4.5x more than truth while carrying no correlation at all is a broken readout for that cell, not a compressed signal, and masked_avg moves it -1.2695. Excluding it, LONG mean goes 0.9566 -> 0.9739 (delta +0.0173) and NO long cohort loses more than 0.05 (worst is big_ideas_glycemic at exactly -0.0500). Median over all 8 long cohorts moves +0.0040. Only 2 of 7 non-degenerate long cohorts move at all beyond 0.05, both UPWARD (optihealth_supabase +0.0951, dreamt +0.0561).

THE METHOD LESSON IS MINE TO OWN: this vault already carries rule_out that-the-mean-gap-naive-pooled-mean-present-5bb2e3, which says never score this diagnostic on a MEAN. I preregistered g2 on a mean anyway and a single n=42 cohort with a broken readout flipped it. The gate is scored FAIL because that is what I committed to; the honest reading is 'no harm on any cohort with a working readout, and the gate statistic was badly chosen'.
- `g3-seen-cohorts-consistent-with-w6`: PASS — PASS. All 10 seen SHORT cohorts improve under masked_avg (10/10), reproducing W6's 7/7 qualitative pattern on a different encoder and a different corpus. Seen-short mean 0.5038 -> 0.6543, delta +0.1505. Notably nurse_stress is again the laggard (+0.0132), as it was the weak cohort in W6's table, and consumer_grade_wearables has a near-dead readout in both arms (r 0.051 -> 0.030) so its ratio gain of +0.1216 should not be read as a tracking gain.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `test_invalid`

THE REPAIR IS GEOMETRIC, NOT MEMORISED -- and the cleanest evidence is that it works BETTER out-of-sample. On popane_s5/s6, which the baseline_sync encoder has never seen, masked_avg moves the variance-compression ratio 0.4577 -> 0.6989 (+0.2412), while the SAME encoder in the SAME run gains only +0.1505 on the ten short cohorts it trained on. A memorisation account predicts the opposite ordering, so it is eliminated. Tracking improves alongside scale (s5 r 0.870 -> 0.968, s6 0.865 -> 0.927), which matters because W6 established ratio and r as separate axes and a scale-only gain would have been the weaker result.

WHY THE OUT-OF-SAMPLE GAIN IS LARGER, stated as a hypothesis rather than a finding: s5 and s6 are the shortest well-behaved cohorts in the corpus (27 and 34 measured-HR minutes, tight ranges 17-38 and 19-44) and the contraction is a function of coverage c, so the cohorts with the smallest c have the most to recover. The seen-short mean is diluted by nurse_stress (+0.0132, a shift-schedule cohort with r~0.34) and consumer_grade_wearables (dead readout, r 0.051 -> 0.030). Consistent with geometry, but 'held-out > seen' must not be generalised to 'unseen data is easier'.

THE FAILURE IS MINE AND IT IS A STATISTIC-CHOICE FAILURE, NOT AN OPERATOR FAILURE -- which is why the failure mode is test_invalid rather than claim_false. g2 asked whether long cohorts are harmed and I preregistered it on a MEAN. mmash -- n=42, avg ratio 4.4902 at r=0.009, i.e. predictions varying 4.5x more than truth while carrying no correlation whatsoever -- moved -1.2695 and flipped the whole gate. On the median the long half moves +0.0040; excluding that one cell the mean moves +0.0173 and no cohort loses more than 0.05. This vault ALREADY carries rule_out that-the-mean-gap-naive-pooled-mean-present-5bb2e3 saying never to score this diagnostic on a mean, and I did it anyway. directional_only is the correct verdict: the claim is not established, because the instrument I committed to could not test it.

REUSABLE RULE: when a gate aggregates over heterogeneous cohorts, declare the degeneracy filter UP FRONT -- here 'ratio > 2 AND |r| < 0.15 is a broken readout, not a measurement' -- and gate on the per-cohort worst case or the median. A mean over cohorts whose readouts differ in quality is not a summary of the operator; it is a summary of the worst readout.

CONSEQUENCE: do not re-run this as-is. The right next test is the same measurement with a preregistered median/worst-case gate and mmash's readout diagnosed or excluded on stated grounds. That is a re-scored instrument rather than a new arm, so it is cheap -- but it must be committed before looking, since I have now seen these numbers.

Transfers to: [[h-w6-mask-weighted-pooling-restores-short-9da5b6]], [[h-w10-train-serve-pooling-mismatch-the-w6-f512a3]]

## Key numbers
- `heldout_short_avg`: 0.4577
- `heldout_short_delta`: 0.2412
- `heldout_short_masked`: 0.6989
- `long_all_mean_delta`: -0.1436
- `long_clean_mean_delta`: 0.0173
- `long_median_delta`: 0.004
- `mmash_avg_r`: 0.009
- `mmash_avg_ratio`: 4.4902
- `mmash_delta`: -1.2695
- `n_rows`: 13818
- `n_subjects`: 1407
- `popane_s5_avg`: 0.4118
- `popane_s5_masked`: 0.6622
- `popane_s5_r_avg`: 0.87
- `popane_s5_r_masked`: 0.968
- `popane_s6_avg`: 0.5036
- `popane_s6_masked`: 0.7355
- `preregistered_g1_bar`: 0.05
- `seen_short_delta`: 0.1505

## Artifacts
- `logs/w12_extent_heldout.json`
- `scratch/w12_extent_heldout.py`
- `scratch/_w12_g2_diagnosis.py`
- `logs/w12_extent_heldout.log`

## Do not retry
- Do NOT score eiv_extent_variance_compression_ratio on a cohort MEAN. This run's g2 failed purely because mmash (n=42, ratio 4.49 at r=0.009 -- a broken readout, not a compressed signal) dragged an 8-cohort mean by -0.14 while the median moved +0.0040 and no working cohort lost more than 0.05. rule_out that-the-mean-gap-naive-pooled-mean-present-5bb2e3 already said this and I preregistered a mean anyway. Future gates on this diagnostic: per-cohort worst-case or median, with a ratio>2-and-|r|<0.15 degeneracy filter declared UP FRONT.
- Do NOT use the hardcoded SHORT/LONG cohort lists in scratch/w6_primary_ratio.py on any corpus newer than [13518]. They predate popane_s5/s6 and would silently exclude the very cohorts under test. Classify by median measured-HR minutes as scratch/w12_extent_heldout.py does.
- Do NOT treat mmash as a usable cohort for this diagnostic until its readout is diagnosed: n=42, avg ratio 4.4902, r=0.009 in BOTH arms. Whatever it measures, it is not variance compression.
- Do NOT read consumer_grade_wearables' +0.1216 ratio gain as a tracking gain: its within-cohort r is 0.051 -> 0.030, i.e. dead in both arms. Ratio and tracking are separate axes (W6 established this); quote both or neither.

## Notes
THE HEADLINE: the pooling repair works BETTER on cohorts the encoder has never seen (+0.2412) than on cohorts it trained on (+0.1505), measured in the SAME run on the SAME encoder. That contrast is what makes this a mechanism result rather than another in-sample number.

HELD-OUT (popane_s5, popane_s6 -- absent from the [13518] corpus baseline_sync trained on):
  popane_s5  n=199  27 HR-min  ratio 0.4118 -> 0.6622  r 0.870 -> 0.968
  popane_s6  n=140  34 HR-min  ratio 0.5036 -> 0.7355  r 0.865 -> 0.927
  mean 0.4577 -> 0.6989, delta +0.2412 against a +0.05 bar (4.8x).
SEEN SHORT: 10/10 cohorts improve, mean 0.5038 -> 0.6543 (+0.1505).
LONG: median +0.0040; excluding one degenerate cell, mean +0.0173 and nothing loses >0.05.

g2 FAILED as committed and is recorded FAIL. Diagnosis (not an override): mmash alone, n=42, avg ratio 4.4902 at r=0.009, moved -1.2695 and flipped an 8-cohort mean. Remove it and the long half is inert-to-slightly-positive. My gate statistic was wrong in a way this vault had already written down.

SCOPE: probe only, no training, HR-mean readout, frozen baseline_sync modules. This says the OPERATOR generalises; it says nothing about deployment numbers, which W10 already showed serving-path parity does not move.

#extent-heterogeneity #pooling #wearables #control #baseline #denominator #state-supervision #deployment-distribution #matched-baseline #subject-heldout #optihealth-eiv #data-contract-violation
