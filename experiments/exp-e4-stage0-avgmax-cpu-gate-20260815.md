---
artifacts: []
closed: true
config_note: 'STAGE 0 ONLY (CPU/probe stage of the E4 plan). Evidence supplied by
  exp-e17-unified-readout-20260815 (raw canonical patches) and exp-e19-encoder-pooling-20260815
  + exp-e19b-pooling-recipe-level-20260815 (REAL trained Phase-B encoder tokens, 5
  checkpoints, shuffle control).


  PROCESS NOTE, RECORDED AGAINST MYSELF: E4 was proposed 2026-08-13 and predicts exactly
  the avg+max pooling effect I re-derived on 2026-08-15 as E17/E19 without noticing
  it existed. My recall() call used window/EMA/state keywords and did not surface
  it. E17/E19 are therefore INDEPENDENT REPLICATIONS of E4''s Stage-0 premise rather
  than novel hypotheses, and the credit belongs to E4. Lesson for the operating skill:
  before proposing an architecture hypothesis, recall on the INTERVENTION (pooling/avgmax/patching)
  as well as the symptom.


  Stage 1 (GPU end-to-end) is NOT run here and remains open as rb-w4-projector-maxpool-end-to-end-20260815.'
cost: null
crash_reason: ''
created_at: '2026-08-15T06:55:14+00:00'
diagnostics_measured:
- after: 0.6849
  before: 0.003
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry:
- Do NOT cite this experiment's +0.6819 as evidence for avg+max pooling as a LEVER.
  It is a Stage-0 encoder-stage measurement; the same intervention at the real production
  site is +0.0090 (ro-dual-avg-max-pooling-downstream-20260805) and +0.0235/+0.0447
  (ro-avgmax-pooling-production-site-20260815) -- a ~9x shrinkage, below the +0.05
  decision bar. avgmax remains REFUTED at the production stage.
- Do NOT re-open the avgmax GPU arm. rb-w4-projector-maxpool-end-to-end-20260815,
  the Stage-1 runbook this record left open, is already ABANDONED, and exp-e4-stage0-excursion-gate-20260820
  closed the same hypothesis directional_only with the blocking e4-g2 gate FAILED.
  Two independent closures agree.
external_run: logs/e19_encoder_pooling.json + logs/e19b_pooling_across_checkpoints.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: e4-g0-cpu-excursion-increment-ge-0-25
  passed: true
- evidence: 'NOT MEASURED IN THIS EXPERIMENT, and recorded as FAIL because an unreported
    gate is not a passed gate. This record is Stage-0 only: CPU/probe evidence from
    exp-e17/e19/e19b on encoder tokens, with no GPU end-to-end arm and therefore no
    hr-axis measurement against the v9band04 band at all.


    The gate WAS subsequently measured on the same hypothesis by exp-e4-stage0-excursion-gate-20260820,
    which scored it FAILED (hr axes outside the v9band04 band) and closed that experiment
    directional_only. So the blocking gate is failed on the record by a sibling run;
    nothing here rescues it.


    Supporting context for why no further attempt is warranted: the Stage-1 site this
    gate guards was independently measured at +0.0090 (ro-dual-avg-max-pooling-downstream-20260805)
    and +0.0235/+0.0447 (ro-avgmax-pooling-production-site-20260815), both below the
    +0.05 decision bar.'
  gate_id: e4-g2-BLOCKING-hr-axes-within-v9band04-band
  passed: false
git_ref: ''
hypothesis_id: e4-encoder-excursion-avgmax-20260813
id: exp-e4-stage0-avgmax-cpu-gate-20260815
interpretation:
  at: '2026-08-29T22:24:15+00:00'
  failure_mode: test_invalid
  lesson: 'A STAGE-0 PROBE CANNOT CLOSE A STAGE-1 GATE, AND LEAVING IT OPEN FOR 15
    DAYS WAS THE REAL COST. This record measured what it could measure honestly (encoder-stage
    excursion increment 0.003 -> 0.6849, its Stage-0 gate passed) and then sat open
    because its blocking gate lives at a stage it never ran. Meanwhile the same question
    was answered twice by other runs. failure_mode is test_invalid rather than claim_false
    because the experiment never tested the blocking claim -- not because the claim
    survived.


    THE SUBSTANTIVE ANSWER, from the siblings: at the real production pooling site
    avg+max is worth +0.0090 (ro-dual-avg-max-pooling-downstream-20260805) and +0.0235/+0.0447
    across checkpoints (ro-avgmax-pooling-production-site-20260815), against a +0.05
    decision bar -- roughly a 9x shrinkage from the encoder-stage figure. exp-e4-stage0-excursion-gate-20260820
    measured e4-g2 directly and FAILED it. The lever is dead at the site that matters.


    THIRD RECORDED INSTANCE OF THE SAME PATTERN, and the direction never varies: hr_peak
    encoder-vs-production 4.3x, E4 +0.4344 -> +0.0235, W6''s frozen-token 0.8159 ->
    end-to-end 0.6897. THE ENCODER STAGE ALWAYS FLATTERS. Any hypothesis whose evidence
    is an encoder-stage probe should state its expected shrinkage before Stage 1,
    and should not be left open pending a GPU arm that the vault has already priced
    as unfunded.


    PROCESS LESSON, preserved from its own config_note because it is still the sharpest
    one here: E4 was proposed 2026-08-13 and then independently re-derived as E17/E19
    on 2026-08-15 because recall() was queried on the SYMPTOM (window/EMA/state) and
    not on the INTERVENTION (pooling/avgmax/patching). Recall on the intervention
    as well as the symptom before proposing an architecture hypothesis, or you will
    pay twice for the same idea.'
  transfers_to:
  - h-w6-mask-weighted-pooling-restores-short-9da5b6
key_numbers: {}
observed_effects:
- delta: 0.6819
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e19b-pooling-recipe-level-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: false
    eiv_peak_excursion_increment_r2: true
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[e4-encoder-excursion-avgmax-20260813]].
Branches from [[exp-e19b-pooling-recipe-level-20260815]].
External run: logs/e19_encoder_pooling.json + logs/e19b_pooling_across_checkpoints.json

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.003 → after 0.6849  (Δ +0.6819, up ✓ predicted up)

## Gates
- `e4-g0-cpu-excursion-increment-ge-0-25`: PASS
- `e4-g2-BLOCKING-hr-axes-within-v9band04-band`: **FAIL** — NOT MEASURED IN THIS EXPERIMENT, and recorded as FAIL because an unreported gate is not a passed gate. This record is Stage-0 only: CPU/probe evidence from exp-e17/e19/e19b on encoder tokens, with no GPU end-to-end arm and therefore no hr-axis measurement against the v9band04 band at all.

The gate WAS subsequently measured on the same hypothesis by exp-e4-stage0-excursion-gate-20260820, which scored it FAILED (hr axes outside the v9band04 band) and closed that experiment directional_only. So the blocking gate is failed on the record by a sibling run; nothing here rescues it.

Supporting context for why no further attempt is warranted: the Stage-1 site this gate guards was independently measured at +0.0090 (ro-dual-avg-max-pooling-downstream-20260805) and +0.0235/+0.0447 (ro-avgmax-pooling-production-site-20260815), both below the +0.05 decision bar.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `test_invalid`

A STAGE-0 PROBE CANNOT CLOSE A STAGE-1 GATE, AND LEAVING IT OPEN FOR 15 DAYS WAS THE REAL COST. This record measured what it could measure honestly (encoder-stage excursion increment 0.003 -> 0.6849, its Stage-0 gate passed) and then sat open because its blocking gate lives at a stage it never ran. Meanwhile the same question was answered twice by other runs. failure_mode is test_invalid rather than claim_false because the experiment never tested the blocking claim -- not because the claim survived.

THE SUBSTANTIVE ANSWER, from the siblings: at the real production pooling site avg+max is worth +0.0090 (ro-dual-avg-max-pooling-downstream-20260805) and +0.0235/+0.0447 across checkpoints (ro-avgmax-pooling-production-site-20260815), against a +0.05 decision bar -- roughly a 9x shrinkage from the encoder-stage figure. exp-e4-stage0-excursion-gate-20260820 measured e4-g2 directly and FAILED it. The lever is dead at the site that matters.

THIRD RECORDED INSTANCE OF THE SAME PATTERN, and the direction never varies: hr_peak encoder-vs-production 4.3x, E4 +0.4344 -> +0.0235, W6's frozen-token 0.8159 -> end-to-end 0.6897. THE ENCODER STAGE ALWAYS FLATTERS. Any hypothesis whose evidence is an encoder-stage probe should state its expected shrinkage before Stage 1, and should not be left open pending a GPU arm that the vault has already priced as unfunded.

PROCESS LESSON, preserved from its own config_note because it is still the sharpest one here: E4 was proposed 2026-08-13 and then independently re-derived as E17/E19 on 2026-08-15 because recall() was queried on the SYMPTOM (window/EMA/state) and not on the INTERVENTION (pooling/avgmax/patching). Recall on the intervention as well as the symptom before proposing an architecture hypothesis, or you will pay twice for the same idea.

Transfers to: [[h-w6-mask-weighted-pooling-restores-short-9da5b6]]

## Do not retry
- Do NOT cite this experiment's +0.6819 as evidence for avg+max pooling as a LEVER. It is a Stage-0 encoder-stage measurement; the same intervention at the real production site is +0.0090 (ro-dual-avg-max-pooling-downstream-20260805) and +0.0235/+0.0447 (ro-avgmax-pooling-production-site-20260815) -- a ~9x shrinkage, below the +0.05 decision bar. avgmax remains REFUTED at the production stage.
- Do NOT re-open the avgmax GPU arm. rb-w4-projector-maxpool-end-to-end-20260815, the Stage-1 runbook this record left open, is already ABANDONED, and exp-e4-stage0-excursion-gate-20260820 closed the same hypothesis directional_only with the blocking e4-g2 gate FAILED. Two independent closures agree.

## Notes
CLOSED 2026-08-30 AS AN ADMINISTRATIVE CLEANUP, not as new evidence. It sat open for 15 days while its own scientific question was answered twice elsewhere, and an open experiment is a claim that work is in flight.

WHAT IT ESTABLISHED: its Stage-0 gate PASSED -- encoder-stage excursion increment 0.003 -> 0.6849. Real, not retracted.
WHAT IT DOES NOT ESTABLISH: the intervention. Its blocking e4-g2 gate was never measured here (Stage-0 only, no GPU arm) and is now recorded FAIL on that basis; the sibling exp-e4-stage0-excursion-gate-20260820 measured it and also failed it.

This is the vault's canonical stage-mismatch pattern, third recorded instance (hr_peak encoder-vs-production 4.3x; E4 +0.4344 -> +0.0235; W6's 0.8159 -> 0.6897), and it always flatters the encoder stage.

The process note in its config_note remains the most useful thing here: E4 was proposed 2026-08-13 and independently re-derived as E17/E19 two days later because recall() was queried on the SYMPTOM (window/EMA/state) rather than the INTERVENTION (pooling/avgmax/patching).

#cross-modal #collapse #deployment-distribution #optihealth #baseline
