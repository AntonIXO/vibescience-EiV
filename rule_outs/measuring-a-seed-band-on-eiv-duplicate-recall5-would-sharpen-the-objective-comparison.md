---
created_at: '2026-09-01T07:59:23+00:00'
former_ids: []
id: measuring-a-seed-band-on-eiv-duplicate-recall5-would-sharpen-the-objective-comparison
problem_id: optihealth-contrastive-loss-20260727
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:59:23+00:00'
topic_tags: []
---

## Claim
The objective comparison (DCL / DCL+HSIC / Soft CCA / VICReg / native KTA+VICReg) can be sharpened by measuring a seed band on eiv_duplicate_recall5, as the synthesis proposes -- that being 'the cheapest thing that would sharpen this problem'.

## Method
Repository-wide grep for the diagnostic's implementation and for each candidate objective across src/**.py and configs/training.yaml; read merge_manifest.json for the current corpus identity; cross-read the 2026-08-07 reconnaissance document's standing recommendation. No GPU.

## Evidence
NOT ACTIONABLE AS WRITTEN: the instrument no longer exists in the codebase, so there is nothing to re-run a seed band ON.

Grepped the entire repository (excluding .git, node_modules, __pycache__) for `duplicate_recall`: exactly ONE hit, and it is prose -- scratch/recon_vibescience_20260807.md, the reconnaissance document that already flagged this line as at-risk. There is no implementation in src/, no scorer, no probe, no scratch script.

The four objectives are equally absent: `kta`, `vicreg`, `soft_cca`, `VICReg` return zero hits across src/**.py, and configs/training.yaml has no `contrastive_objective`, `loss_type` or `objective:` key on any profile. Phase A ships one objective (InfoNCE via RankNContrastLoss, plus the optional triplet term in Phase B). The comparison was run on a pipeline variant that has since been removed.

CORPUS DRIFT ON TOP OF THAT. Every number in this problem is 500-step, seed-17, validation-only pilot scale on the mid-2026 canonical corpus. The bundle today is [14041,1440,12] at merge f1b15e9e with normalization sha 24e731a6 and 21 datasets, and it has been through at least three rebuilds since (12547 -> 13518 -> 14041/14092), each of which changes the caption-equivalence sets that `duplicate_recall5` is defined over. Even with an implementation, the old numbers would not be comparable.

THIS WAS ALREADY THE STANDING ADVICE. recon_vibescience_20260807.md line 362 recommends retiring hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728 outright -- 'Given Anton has parked the DCL-vs-InfoNCE line, retire it rather than let it rot' -- and flags the multipositive hypothesis as corpus-dependent and equally at risk. That advice was never executed, which is why the problem still reads as live.

WHAT SURVIVES AND IS WORTH KEEPING. Two findings here are about METHOD, not about this pipeline, and they transfer: (1) a mechanism gate satisfied BY CONSTRUCTION carries no evidential weight -- the exact-caption equivalence set cannot fail its own false-negative gate; (2) geometry moved (effective rank +0.1368, spectral entropy up) while retrieval moved by EXACTLY 0.0 against a preregistered >= +0.005 bar, which is the same geometry-is-not-deployment lesson the v29-v37 campaign independently re-learned five times.

## Links
Rules out for problem [[optihealth-contrastive-loss-20260727]].
