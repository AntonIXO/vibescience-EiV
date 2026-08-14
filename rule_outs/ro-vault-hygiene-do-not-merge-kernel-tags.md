---
created_at: '2026-08-14T20:24:32+00:00'
former_ids: []
id: ro-vault-hygiene-do-not-merge-kernel-tags
problem_id: vault-hygiene-and-record-readability
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-14T20:24:32+00:00'
topic_tags:
- kernel
- kernel-alignment
---

## Claim
The `kernel` and `kernel-alignment` tags are duplicates and should be merged into one.

## Method
Read every entity carrying each tag. `kernel` is used by eiv-dcl-rbf-hsic and hyp-eiv-dcl-hsic-20260727 (RBF/HSIC statistical DEPENDENCE). `kernel-alignment` is used by eiv-native-rbf-kta-vicreg, hyp-eiv-native-kta-vicreg-20260727, exp-eiv-native-kta-vicreg-20260727 and paper-kta-1610-06434 (kernel-TARGET alignment, KTA).

## Evidence
Disjoint entity sets measuring different quantities: HSIC is a dependence statistic between two representations, KTA is alignment of a kernel matrix with a target. doctor() flags them only because they share a string stem. Merging would collapse a real scientific distinction, so both now carry descriptions saying so. DO NOT MERGE — doctor() will keep reporting this pair; it is a known, deliberate false positive.

## Links
Rules out for problem [[vault-hygiene-and-record-readability]].

#kernel #kernel-alignment
