---
created_at: '2026-07-11T04:03:50+00:00'
former_ids: []
gates: []
id: deep-vae-rhr-in-worker
interventions:
- rhr-cusum-detector
paper_refs:
- pcovnet-2023
predicted_effects:
- diagnostic_id: new_deps
  direction: up
  magnitude_note: torch + GPU training
- diagnostic_id: runtime_sec
  direction: up
  magnitude_note: training loop per run
problem_id: oh-missing-earlywarning-rhythm
problem_tags:
- missing-early-warning
- missing-circadian
- missing-directionality
status: refuted
supersedes: null
topic_tags:
- anomaly-detection
- deep-learning
- optihealth
- wearables
- n-of-1
- resting-hr
- illness-early-warning
updated_at: '2026-08-20T22:03:29.662940+00:00'
---

## Statement
A deep CNN-VAE/LSTM (PCovNet+) over resting-HR windows is the right way to add early-warning to the worker.

## Rationale
Higher sensitivity in the paper.

## Plan
(Not pursued — violates the classical/no-train/900m-RAM worker contract.)

## Links
Tests problem [[oh-missing-earlywarning-rhythm]].
Applies [[rhr-cusum-detector]].
Predicts [[new_deps]] (up).
Predicts [[runtime_sec]] (up).
Cites [[pcovnet-2023]].

#anomaly-detection #deep-learning #optihealth #wearables #n-of-1 #resting-hr #illness-early-warning #missing-early-warning #missing-circadian #missing-directionality

## Closed 2026-08-20 without an experiment (rule_out, not a run)

Superseded by rule_out `that-a-deep-cnn-vae-lstm-pcovnet-style-over-84a24c`. No compute was
spent because the record is self-refuting: both of its predicted_effects (new_deps `up`,
runtime_sec `up`) name LOWER_BETTER diagnostics, so it preregistered movement in the wrong
direction on both of its own metrics. Its Plan field already recorded the non-pursuit and the
reason. The contract-satisfying sibling `rhr-cusum-adds-earlywarning` is CONFIRMED at
new_deps=0. Eliminated ON CONTRACT GROUNDS ONLY -- the accuracy claim is untested and the
rule_out scopes what stays open.
