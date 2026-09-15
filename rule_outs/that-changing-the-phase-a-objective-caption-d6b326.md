---
created_at: '2026-09-03T06:44:10+00:00'
former_ids: []
id: that-changing-the-phase-a-objective-caption-d6b326
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-03T06:44:10+00:00'
topic_tags:
- optihealth
- readout
- control
---

## Claim
That changing the Phase A objective — caption content (labels-in-caption), masked patch RECONSTRUCTION (AIM/MAE-style), or PULSE-style EDA-teacher representation DISTILLATION — lifts the frozen PL60 encoder's subject-held-out momentary-state readout (flow vs stress) above the 15-feature handcrafted multi-scale bar, or reliably above its own random-init twin.

## Method
Three one-variable Phase A arms on the v41rebased recipe, each scored frozen by scratch/g5_pool_ceiling.py on identical rows/folds/permutation nulls with a random-init twin and two estimator families (ridge, gb): exp-c3b-caption-clause-state-20260903 (caption clauses), exp-h-reconstruction-sensorfm-bar-20260903 (reconstruction_weight, contrastive_weight 0), exp-e-eda-distillation-20260903 (privileged_distill_weight against a frozen EDA teacher). Bar = raw_multiscale/rf on the same rows.

## Evidence
Held-out pool (26 subjects, 666 rows): handcrafted raw_multiscale/rf +0.1372 over null (clean pool +0.1515). Encoder cells: InfoNCE captions (v41) enc_ms/gb +0.0852 vs twin +0.0512 on the clean pool, and on held-out every encoder-only cell at or below its own null; reconstruction best cell +0.0674 (needed +0.1815 to clear the bar, gap ~7 sigma at fold-seed sigma 0.0155); EDA-distilled enc_ms/gb +0.0634 vs twin +0.0516, hr_raw regressed 2.843 -> 3.164 (outside the +/-0.416 band), teacher embedding effective rank 3.0 at d=64. Caption control: the hr_only incumbent (v28affect01) reads BELOW its own random twin on 3 of 4 cells (-0.0303, -0.0032, -0.0504). Three objectives, one answer. A fourth Phase A objective arm at this corpus scale (2e7 minutes) is an unpowered repeat unless it first shows frozen-embedding + linear head beating the handcrafted features (SensorFM's bar).

## Links
Rules out for problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].

#optihealth #readout #control
