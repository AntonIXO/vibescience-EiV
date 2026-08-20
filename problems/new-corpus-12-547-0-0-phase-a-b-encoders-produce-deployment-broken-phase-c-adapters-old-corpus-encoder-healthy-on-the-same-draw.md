---
created_at: '2026-08-20T00:33:34+00:00'
former_ids: []
id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
paper_refs: []
problem_tags: []
status: open
target: null
title: New-corpus (12,547 @ 0.0) Phase A/B encoders produce deployment-broken Phase
  C adapters; old-corpus encoder healthy on the same draw
topic_tags:
- deployment-distribution
- collapse
- sensor-text
- epoch-resolution
updated_at: '2026-08-20T00:33:34+00:00'
updates: []
---

## Description
Every A→B→C chain whose Phase A/B encoder was trained on the 12,547-window min_coverage-0.0 corpus (sha ac7d2b1c) produces a deployment-broken Phase C, while the SAME C recipe off an old-corpus encoder is healthy on the SAME rebuilt eval draw. Measured 2026-08-19/20, four fresh chains vs one anchor, all supabase-200 batch-1 captures on the rebuilt data/eval_v3_1min (trivial 3.95):
  v9band04_s42 (v5c old-corpus encoder + band04 C): MAE 3.044, skill +0.91, corr 0.68 — HEALTHY
  ab_ctrl   (new-corpus 10-ds A/B + 3-ds C, band04): MAE 6.03, corr 0.357, top1 0.44
  ab_wl     (new-corpus 6-ds wl A/B/C, band04):      MAE 20.28, corr 0.282
  c_patch120_s42/s43 (analog state A/B + wl_noatom C): MAE 23.6/24.6, 35.1/32.5-value attractors
State slot collapses to a prior in every fresh chain (200/200 neutral or 198/200 stress; never flow).
The C recipe is exonerated (band04 law held everywhere); the corpus tensor is exonerated (v9band04 reads the same eval rows fine); the variable is the ENCODER/PROJECTOR TRAINED ON THE NEW CORPUS. Candidate mechanisms, none yet isolated: (a) Phase A/B InfoNCE pool now includes the 974 min_coverage<0.1 sparse windows (analog arms gate at 0.0; ab arms inherited profile floors — CHECK per-profile realized pools); (b) captions regenerated on the new corpus differ numerically → text targets shifted; (c) text-embedding caches rebuilt this session — byte-equivalence with the v5c-era cache never verified; (d) population_stats refit (shifts ≤0.63%) interacting with denormalised caption values; (e) POPANE/CASE windows (255 new) entering the A/B pool with 41-min sessions in 1440-epoch windows. DISCRIMINATING NEXT EXPERIMENT (cheapest first): retrain ONE Phase A with the old corpus's exact profile on the new tensor but min_coverage 0.1 (drops the 974) → B → C; if healthy, (a) is the mechanism; if broken, diff captions/caches next. Until resolved, NO C-stage experiment on a new-corpus encoder is interpretable and v9band04 remains the only deployable adapter.

#deployment-distribution #collapse #sensor-text #epoch-resolution
