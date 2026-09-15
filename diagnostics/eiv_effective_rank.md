---
direction: higher_better
id: eiv_effective_rank
name: eiv_effective_rank
noise_k: 2.0
noise_n: 8
noise_note: "FINITE-DRAW sigma, measured 2026-09-01 by scratch/_eff_rank_noise_floor.py
  -> logs/eff_rank_noise_floor.json. The probe's only stochastic input is its draw
  (300 windows, then 4,000 tokens from them); the checkpoint and forward pass are
  deterministic. Re-running the SAME probe core with 8 different draw seeds on five
  real Phase-A checkpoints:\n  v28affect01   mean 8.599-ish  sd ~0.09\n  v33dispersion
  mean 8.572  sd 0.092  band 0.290\n  v34nopeak     mean 5.341  sd 0.080  band 0.205\n
  \ v36labels     mean 9.767  sd 0.104  band 0.328\n  v29all01      mean 6.738  sd
  0.023  band 0.073\nPooled sigma 0.076, k=2 band 0.153.\n\nCONSEQUENCE FOR THE RECORD:
  v29all01 was killed at 6.783 against a 7.0 gate, a margin of 0.217 = 2.84 sigma.
  That decision is SOUND -- it clears k=2. Conversely the sigma is small enough that
  most recorded eff_rank gaps in this campaign are real as MEASUREMENTS; what remains
  false is treating rank as a direction, which is a separate, already-recorded finding
  (v31 at 10.106 lost deployment; v34 at 5.414 posted the campaign's best supabase
  numbers).\n\nSCOPE: finite-draw only. It excludes seed-to-seed RETRAIN variance,
  which is not measured for this diagnostic and is certainly larger. A gap inside
  0.153 is definitely draw noise; a gap outside it may still be training-seed luck."
noise_sigma: 0.076
noise_source: measured
requires_control: null
topic_tags: []
unit: rank
---

## Description
Entropy effective rank of centered held-out sensor embeddings; collapse guard rejects rank below 5.

*Unit:* `rank` — *higher_better*
