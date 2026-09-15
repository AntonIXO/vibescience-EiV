---
created_at: '2026-07-27T21:03:32+00:00'
former_ids: []
id: optihealth-contrastive-loss-20260727
paper_refs: []
problem_tags:
- contrastive-loss
- subject-heldout
- representation-learning
- optihealth-eiv
status: parked
target: null
title: Which cross-modal objective best preserves raw heart-rate information in OptiHealth
  EiV?
topic_tags:
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
updated_at: '2026-09-01T07:59:40+00:00'
updates:
- at: '2026-09-01T07:59:40+00:00'
  text: "PARKED 2026-09-01: THE INSTRUMENT AND ALL FOUR CANDIDATE OBJECTIVES ARE GONE
    FROM THE CODEBASE. The synthesis names measuring a seed band on eiv_duplicate_recall5
    as 'the cheapest thing that would sharpen this problem'. There is nothing left
    to measure it on.\n\nVERIFIED BY GREP, not by memory. Repository-wide search (excluding
    .git, node_modules, __pycache__) for `duplicate_recall` returns exactly ONE hit
    and it is prose: scratch/recon_vibescience_20260807.md. No scorer, no probe, no
    script. Searching src/**.py for `kta`, `vicreg`, `soft_cca`, `VICReg` returns
    zero hits, and configs/training.yaml carries no `contrastive_objective`, `loss_type`
    or `objective:` key on any profile. Phase A ships one objective today: InfoNCE
    via RankNContrastLoss, plus the optional Phase-B triplet term. The pipeline variant
    these five arms compared no longer exists.\n\nCORPUS DRIFT COMPOUNDS IT. Every
    number here is 500-step, seed-17, validation-only pilot scale on the mid-2026
    corpus. Today's bundle is [14041,1440,12], merge f1b15e9e, normalization sha 24e731a6,
    21 datasets, after at least three rebuilds (12547 -> 13518 -> 14041/14092). Each
    rebuild changes the caption-equivalence sets that duplicate_recall5 is defined
    over, so even with an implementation the old numbers would not be comparable.\n\nTHE
    RETIREMENT WAS ALREADY RECOMMENDED AND NEVER EXECUTED. recon_vibescience_20260807.md
    line 362: 'Given Anton has parked the DCL-vs-InfoNCE line, retire it rather than
    let it rot', and it flags the multipositive hypothesis as equally corpus-dependent.
    That is why this problem still reads as live three weeks later.\n\nWHAT IS PRESERVED,
    because it is about METHOD and transfers beyond this pipeline:\n 1. A mechanism
    gate satisfied BY CONSTRUCTION carries no evidential weight -- the exact-caption
    equivalence set cannot fail its own false-negative gate.\n 2. Geometry moved while
    retrieval did not: effective rank +0.1368 and spectral entropy up, with duplicate
    recall@5 moving by EXACTLY 0.0 against a preregistered >= +0.005 bar and cross-modal
    median rank going the WRONG way (38 -> 39). This is the same geometry-is-not-deployment
    law the v29-v37 campaign re-learned five times independently (v31 at the highest
    eff_rank ever measured lost deployment; v34 at 5.414, below the threshold that
    condemned v29, posted the best supabase numbers).\n 3. The DCL-vs-hard-InfoNCE
    arm is INCONCLUSIVE FOR A CAPACITY REASON, not an evidential one -- both arms
    OOMed symmetrically at 10.314 GiB before step 1. It must never be cited as a refutation
    of DCL.\n\nPARKED, not resolved: 'resolved' needs a confirmed hypothesis and the
    top-arm ordering was never resolved (the +0.0030 gap between KTA+VICReg arms sits
    far inside any plausible seed band). If the objective question is ever reopened
    it starts from a fresh implementation on the current corpus, not from these numbers."
---

## Description
Compare the current DCL + uniformity + auxiliary-DCL Phase A package against RBF-HSIC dependence regularization, Soft CCA, and VICReg on frozen raw 12-channel tensors. Use an experiment-local correction for HR==0 values incorrectly marked present, deterministic dataset-stratified subject splits, validation-only pilot selection, and eight-seed subject-held-out confirmation on the local RTX 5070. Population stats are disabled because HR is schema-excluded and merge-time fitting before the test split would leak.

## Synthesis
STATE: the objective comparison is DONE at pilot scale; what remains open is not
which loss to use but whether retrieval can move at all at this scale.

WHAT WE NOW BELIEVE.
- Native KTA+VICReg is the best of the four candidates on the primary
  (eiv_duplicate_recall5): the trajectory rises 0.0175 -> 0.1072 -> 0.1197 ->
  0.1212 across exp-eiv-dcl-20260727, exp-eiv-native-kta-vicreg-20260727 and
  exp-014. Plain VICReg (0.0075) and Soft CCA (0.0125) both COLLAPSE retrieval
  by ~10x versus the DCL package and are not candidates.
- The multi-positive construction moves GEOMETRY, not retrieval. In
  exp-eiv-multipositive-spectral-entropy-pilot-20260728: effective rank
  +0.1368 (4.3809 -> 4.5177), spectral entropy up, caption-equivalent false
  negatives held at exactly 0.0 — while duplicate recall@5 moved by EXACTLY
  0.0 (0.10723192 -> 0.10723192) against a preregistered >= +0.005 bar, and
  cross-modal median rank went the WRONG way (38 -> 39, predicted down).
  Removing false negatives from the denominator is therefore not sufficient
  for a retrieval gain at 500 steps.

WHAT IS RULED OUT.
- Soft CCA and plain VICReg as the Phase-A objective (measured, above).
- Inheriting a mechanism gate that is satisfied BY CONSTRUCTION as evidence:
  the exact-caption equivalence set cannot fail its own false-negative gate, so
  that gate carries no evidential weight (see exp-multipositive-dcl-resolved-
  via-spectral-supersession-20260818's interpretation).

WHAT IS UNTESTED — AND MUST NOT BE READ AS REFUTED.
- DCL vs hard InfoNCE on soft tokens NEVER RAN. Both arms of
  exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728 OOMed symmetrically at 10.314
  GiB on the 11.51 GiB 5070 before step 1, while materialising 4,962 missing
  corrected-mask caption embeddings with fp32 MedGemma resident. The two
  hypotheses in that chain are `inconclusive` for a CAPACITY reason, not an
  evidential one. Reviving it needs the text-cache corpus materialised under a
  new campaign root first, so the embedding pass never coincides with a
  resident fp32 backbone.

CAVEAT ON EVERY NUMBER HERE. All of it is 500-step, seed-17, validation-only
pilot scale, and no noise floor has ever been measured on eiv_duplicate_recall5
(10 evaluations ride on it, doctor still flags it). The +0.0030 gap between the
KTA+VICReg arms is far inside any plausible seed band; treat the ordering
between the top arms as UNRESOLVED and the ordering against Soft CCA/VICReg as
solid because it is a 10x effect. Measuring a seed band on this diagnostic is
the cheapest thing that would sharpen this problem.

## Updates
- **2026-09-01T07:59:40+00:00** — PARKED 2026-09-01: THE INSTRUMENT AND ALL FOUR CANDIDATE OBJECTIVES ARE GONE FROM THE CODEBASE. The synthesis names measuring a seed band on eiv_duplicate_recall5 as 'the cheapest thing that would sharpen this problem'. There is nothing left to measure it on.

VERIFIED BY GREP, not by memory. Repository-wide search (excluding .git, node_modules, __pycache__) for `duplicate_recall` returns exactly ONE hit and it is prose: scratch/recon_vibescience_20260807.md. No scorer, no probe, no script. Searching src/**.py for `kta`, `vicreg`, `soft_cca`, `VICReg` returns zero hits, and configs/training.yaml carries no `contrastive_objective`, `loss_type` or `objective:` key on any profile. Phase A ships one objective today: InfoNCE via RankNContrastLoss, plus the optional Phase-B triplet term. The pipeline variant these five arms compared no longer exists.

CORPUS DRIFT COMPOUNDS IT. Every number here is 500-step, seed-17, validation-only pilot scale on the mid-2026 corpus. Today's bundle is [14041,1440,12], merge f1b15e9e, normalization sha 24e731a6, 21 datasets, after at least three rebuilds (12547 -> 13518 -> 14041/14092). Each rebuild changes the caption-equivalence sets that duplicate_recall5 is defined over, so even with an implementation the old numbers would not be comparable.

THE RETIREMENT WAS ALREADY RECOMMENDED AND NEVER EXECUTED. recon_vibescience_20260807.md line 362: 'Given Anton has parked the DCL-vs-InfoNCE line, retire it rather than let it rot', and it flags the multipositive hypothesis as equally corpus-dependent. That is why this problem still reads as live three weeks later.

WHAT IS PRESERVED, because it is about METHOD and transfers beyond this pipeline:
 1. A mechanism gate satisfied BY CONSTRUCTION carries no evidential weight -- the exact-caption equivalence set cannot fail its own false-negative gate.
 2. Geometry moved while retrieval did not: effective rank +0.1368 and spectral entropy up, with duplicate recall@5 moving by EXACTLY 0.0 against a preregistered >= +0.005 bar and cross-modal median rank going the WRONG way (38 -> 39). This is the same geometry-is-not-deployment law the v29-v37 campaign re-learned five times independently (v31 at the highest eff_rank ever measured lost deployment; v34 at 5.414, below the threshold that condemned v29, posted the best supabase numbers).
 3. The DCL-vs-hard-InfoNCE arm is INCONCLUSIVE FOR A CAPACITY REASON, not an evidential one -- both arms OOMed symmetrically at 10.314 GiB before step 1. It must never be cited as a refutation of DCL.

PARKED, not resolved: 'resolved' needs a confirmed hypothesis and the top-arm ordering was never resolved (the +0.0030 gap between KTA+VICReg arms sits far inside any plausible seed band). If the objective question is ever reopened it starts from a fresh implementation on the current corpus, not from these numbers.

#contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #representation-learning #optihealth-eiv
