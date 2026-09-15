---
created_at: '2026-07-28T08:18:25+00:00'
former_ids: []
id: optihealth-multipositive-dcl-20260728
paper_refs:
- paper-dcl-2110-06848
- paper-supcon-2004-11362
- paper-debiased-cl-neurips2020
- paper-dclr-2205-00656
- paper-align-uniform-2005-10242
- paper-dimensional-collapse-2110-09348
problem_tags:
- false-negatives
- representation-collapse
- subject-heldout
- optihealth-eiv
status: resolved
target: null
title: Can exact-caption multi-positive DCL remove EiV false negatives without low-rank
  collapse?
topic_tags:
- dcl
- multi-positive
- contrastive
- sensor-text
updated_at: '2026-08-30T11:47:15+00:00'
updates:
- at: '2026-08-30T11:47:15+00:00'
  text: '2026-08-30: resolved per doctor() -- 1 confirmed hypothesis, no live work.'
---

## Description
The corrected stratified loader repeats no raw rows, yet 25/48 audited training batches contain canonical-caption-equivalent off-diagonal positives (mean 0.667 extras, max 3). The incumbent one-positive DCL therefore repels known equivalents. Test a symmetric multi-positive DCL that averages attraction over a frozen exact caption-equivalence set and excludes all equivalents from the negative-only DCL denominator. Keep the native 2560D downstream representation, all incumbent hyperparameters, subject-disjoint validation protocol, population normalization disabled, and the locked test untouched until a paired validation confirmation passes. Primary metric is duplicate-aware Recall@5; native effective rank and median retrieval rank are key secondary diagnostics; HR Ridge R2 is demoted because the untrained encoder saturates it.

## Updates
- **2026-08-30T11:47:15+00:00** — 2026-08-30: resolved per doctor() -- 1 confirmed hypothesis, no live work.

## Links
Cites [[paper-dcl-2110-06848]], [[paper-supcon-2004-11362]], [[paper-debiased-cl-neurips2020]], [[paper-dclr-2205-00656]], [[paper-align-uniform-2005-10242]], [[paper-dimensional-collapse-2110-09348]].

#dcl #multi-positive #contrastive #sensor-text #false-negatives #representation-collapse #subject-heldout #optihealth-eiv
