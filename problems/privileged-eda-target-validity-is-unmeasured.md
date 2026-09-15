---
created_at: '2026-09-01T19:58:30+00:00'
former_ids: []
id: privileged-eda-target-validity-is-unmeasured
paper_refs:
- yang-2022-privileged-features-distillation-2209-08754
problem_tags:
- optihealth-eiv
status: resolved
target: null
title: Privileged EDA is 1.16M observations no arm has ever consumed, and its validity
  AS A TARGET is unmeasured
topic_tags: []
updated_at: '2026-09-02T19:02:38+00:00'
updates:
- at: '2026-09-02T19:02:38+00:00'
  text: '2026-09-02 — RESOLVED by exp-t3-eda-target-validity-20260901 (`supports`),
    which is what the problem asked for: the validity is now measured. 49,860 joined
    EDA x state cells; the usable target is `eda_tonic` on the `by_subject` fit, NOT
    `eda_scr_rate` — tonic''s dataset-to-state information ratio is 1.1-3.1x against
    scr_rate''s 9.1-37.0x, which REVERSES NEXT.md''s Yang-2022-derived prescription
    to prefer the less predictive target. The per-subject standardisation is the mechanism:
    it collapses I(EDA;dataset) 6.3x while the state signal stays flat. What is now
    open is a separate question and belongs to a new hypothesis, not to this one:
    whether distilling that target into the encoder helps a trained chain. Note the
    standing caution from exp-055 and from the twin-control problem `no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state`
    — on `eda_scr_rate` the random-init twin BEAT the trained encoder under both RF
    (+0.073 vs +0.055) and ridge (+0.048 vs -0.011), so any LUPI arm must carry its
    random twin or its result will not be interpretable.'
---

## Description
`data/canonical/privileged_events.parquet` holds 1,158,980 EDA observations (eda_tonic 429,847; eda_scr_rate 729,133) across 14 declared corpora, built by `eiv data privileged` off an already-merged bundle. No training arm has ever consumed it.

WHAT IS AND IS NOT ALREADY SETTLED. exp-055 (and the A2 null before it) asked a DECODABILITY question -- can a probe read EDA back out of the frozen encoder's tokens -- and answered null twice, at two different supplies, with a do_not_retry saying the next attempt needs a different MECHANISM rather than more supply. That is a question about the ENCODER. It leaves completely open the prior question about the TARGET: does EDA carry information about momentary state at all, once you condition on which corpus a window came from?

WHY THE QUESTION IS NOT RHETORICAL. `eda_tonic` is bimodal by corpus -- CASE reads a median 19.59 uS against POPANE study 4's 2.06, and the pooled IQR (13.89) exceeds the pooled median (5.35) -- which is why `privileged_stats.json` fits median/IQR three ways (pooled / by_dataset / by_subject) and `privileged_patch_targets` prefers the narrowest present. Between SUBJECTS inside one dataset the spread is the same order again: nurse_stress 0.081-8.96 uS (111x), DREAMT 0.026-7.66 (280x), popane_s4 0.102-12.43 (122x). A per-patch head asked to predict a target with that structure can satisfy it by learning corpus (or subject) identity -- the shortcut this project already measured at bal-acc 0.9945 against 0.1250 chance on dataset provenance -- while learning nothing about skin.

THE LITERATURE MAKES THIS A PRE-CHECK RATHER THAN A ROBUSTNESS NOTE. Yang et al., NeurIPS 2022 (arXiv:2209.08754) show privileged-feature distillation is NON-MONOTONE: a privileged teacher that is TOO predictive produces high-variance student targets and a WORSE student. So "the head fits the target well" is not evidence the arm is working, and the failure is silent.

WHAT WOULD RESOLVE IT. A data-level measurement of I(EDA ; state | dataset) and I(EDA ; state | subject) against within-stratum permutation nulls, under all three standardisations and two estimator families, at the cells that carry both an EDA observation and a state label -- reported beside the confound I(EDA ; dataset).

SCOPE LIMIT THAT BINDS ANY ANSWER HERE. Per the project's standing rule (five rule_outs retracted 2026-08-08 for breaking it), a data-level or encoder-readout measurement CANNOT close a claim about the trained pipeline. A negative result here rules out "EDA as registered carries state information beyond corpus identity at the annotated epochs" and withdraws the GPU justification; it does not refute generalised distillation, which stays untested rather than refuted.

## Updates
- **2026-09-02T19:02:38+00:00** — 2026-09-02 — RESOLVED by exp-t3-eda-target-validity-20260901 (`supports`), which is what the problem asked for: the validity is now measured. 49,860 joined EDA x state cells; the usable target is `eda_tonic` on the `by_subject` fit, NOT `eda_scr_rate` — tonic's dataset-to-state information ratio is 1.1-3.1x against scr_rate's 9.1-37.0x, which REVERSES NEXT.md's Yang-2022-derived prescription to prefer the less predictive target. The per-subject standardisation is the mechanism: it collapses I(EDA;dataset) 6.3x while the state signal stays flat. What is now open is a separate question and belongs to a new hypothesis, not to this one: whether distilling that target into the encoder helps a trained chain. Note the standing caution from exp-055 and from the twin-control problem `no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state` — on `eda_scr_rate` the random-init twin BEAT the trained encoder under both RF (+0.073 vs +0.055) and ridge (+0.048 vs -0.011), so any LUPI arm must carry its random twin or its result will not be interpretable.

## Links
Cites [[yang-2022-privileged-features-distillation-2209-08754]].

#optihealth-eiv
