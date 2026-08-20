---
created_at: '2026-08-20T03:31:44+00:00'
former_ids: []
id: that-the-12-547-corpus-phase-a-b-encoder-8e8d8e
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-20T03:31:44+00:00'
topic_tags:
- deployment-distribution
- collapse
- baseline
---

## Claim
That the 12,547-corpus Phase A/B encoder training DETERMINISTICALLY breaks Phase C deployment (the exp-050 framing 'all fresh chains are broken; the corpus/pipeline is the variable'). FALSIFIED BY COUNTEREXAMPLE: the identical ab_ctrl chain at seed 43 is HEALTHY — supabase MAE 2.84, skill +1.12, corr 0.739, no attractor (top1 0.285) — better than the v9band04 anchor (3.04) on the same draw. The correct statement: fresh new-corpus chains are BISTABLE at the seed level — the family seed band spans 2.84–11.08 bpm on same-code single-seed draws (s43 2.84, ab_ctrl s42 6.03, mc01 s42 11.08, ab_wl s42 20.28, c_patch120 both seeds ~24) — the pre-atomfix 11.37-band disease, back in a new family. The corpus is NOT categorically guilty; some seeds land in a good basin.

## Method
Same-chain seed pair (the instrument exp-051's closure demanded before any further mechanism test): re-run the full ab_ctrl A→B→C chain changing ONLY the seed, score on the frozen deployment draw.

## Evidence
scratch/run_ctrl_s43.sh, this session: a_ctrl_s43 → b_ctrl_s43 → c_ctrl_s43 (byte-identical profiles to ab_ctrl's chain, only seed 42→43), audited on the same frozen supabase-200 batch-1 draw. MAE 2.84 vs seed-42's 6.03 — a 3.2 bpm seed-only delta, 5x the v9band04-family band (0.60). logs/emission_ab_ctrl_s43_supa_summary.json. Consequences: (1) exp-050's allowlist inversion (ctrl 6.03 vs wl 20.28) and exp-051's mechanism-(a) rejection (mc01 11.08 vs 6.03) are BOTH inside this seed band and carry no recipe information; (2) the discriminating question is no longer 'what breaks new-corpus chains' but 'what makes the new-corpus family BISTABLE when v9band04's family measured 0.60' — candidate: v9band04's C was seeded off v5c's FIXED Phase B (one shared A/B init across seeds), while today's chains re-train A/B per seed, so the chain-level band compounds A, B and C basins; (3) v9band04 is no longer uniquely healthy — ab_ctrl_s43 matches it — so a new-corpus baseline family (>=3 seeds of the ctrl chain) is now the right candidate for baseline designation.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].

#deployment-distribution #collapse #baseline
