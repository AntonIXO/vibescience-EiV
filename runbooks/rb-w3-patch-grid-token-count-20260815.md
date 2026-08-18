---
created_at: '2026-08-14T21:36:20+00:00'
former_ids: []
gates:
- g1_cpu_information_bound_increment
- g2_hr_mean_not_reallocated
- g3_gpu_deployment_beats_seed_band
hypothesis_id: null
id: rb-w3-patch-grid-token-count-20260815
notes: []
priority: 20
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
status: queued
topic_tags:
- epoch-resolution
- pooling
- cross-modal
- control
updated_at: '2026-08-14T21:36:20+00:00'
---

## Prompt
W3 — PATCH-GRID / TOKEN-COUNT ablation at a FIXED 24h window, scored on channel reconstruction. Repo /root/optiHealth-EiV.

WHY THIS ARM EXISTS (context for a fresh session with no memory of the prior chat).
optiHealth-EiV / SensorLM: temporal encoder over wearable physiology -> soft-token prefixes for MedGemma. Canonical tensor [11318 days, 1440 minutes, 12 channels], one window per civil day, git_sha e849eb7. The encoder reads HR LEVEL well (hr_mean subject-grouped R2 0.971-0.99) while hr_PEAK / excursion is the open axis (~0.42 at production soft tokens).

The window-size question was asked as '1h / 6h / 24h / dynamic?'. Half of it is now answered and the answer is DO NOT SHORTEN THE WINDOW:
  - rule-out `ro-window-shortening-20260805`: shortening 24h->1h for hr_peak raises raw R2 0.4659->0.8385 but the increment over R2(peak | true daily mean) falls to EXACTLY 0.0000 — pure max-converges-to-mean artifact — and it destroys the label distribution (tachycardic base rate 18.3% -> 1.4%).
  - rule-out `shortening-the-analysis-window-to-align-with-0f6d56` (exp-e16, 2026-08-15): peri-ping sub-daily windows do NOT recover cross-subject state detection; the delta over a clock-only control RISES with window length and peaks at 24h, and a per-user constant (AUC 0.7381) beats every physiological arm (best 0.5818).

So the window stays 24h. The UNTESTED variable is the PATCH GRID INSIDE it — token count per channel — which has never been isolated, because every prior window experiment moved the window and the patching together.

EXTERNAL EVIDENCE (deep research, 2026-08-15) that motivates the specific geometry:
  - SensorLM (Google, arXiv:2506.09108): input is explicitly [26 features x 1440 minutes], one-day windows at minute resolution — the SAME shape as ours. Our window choice is field-consensus, not an accident.
  - LSM-2 (Google, Learning from Incomplete Wearable Sensor Data): [1440 minutes x 26 features], ViT-1D tokenizer with a 10-MINUTE 1D patch giving 144 tokens PER SIGNAL, shared kernel across channels, 2D positional embedding over (time, signal).
  - SensorFM (Google, 2026): 34 one-minute aggregate features, ~1 trillion minutes of pretraining.
  - Moirai 2.0 (arXiv:2511.11698) REMOVED multi-patch inputs in favour of a SINGLE patch size and reported IMPROVED accuracy — direct published evidence against multi-scale patching as a default.
  - The multi-scale literature (Pathformer arXiv:2402.05956, TimeMixer, Scaleformer) is benchmarked on generic forecasting (electricity/traffic), not physiology. No major wearable foundation model uses it.

THE CONCRETE GAP: our TemporalPatching uses patch_len=8, stride=4 -> 23 tokens per channel. LSM-2 uses a 10-minute patch -> 144 tokens per signal. We are at roughly 1/6 the token budget per channel. That is a checkable number, and it is a TOKEN-COUNT question at fixed window, orthogonal to both rule-outs above.

ARMS (tokens per channel in brackets), window fixed at 1440 minutes throughout:
  A  patch 8,  stride 4   [23]   <- INCUMBENT / control
  B  patch 10, stride 10  [144]  <- LSM-2 geometry
  C  patch 30, stride 15  [95]
  D  patch 60, stride 30  [47]

PRIMARY METRIC: `eiv_peak_excursion_increment_r2` — the registered diagnostic built for exactly this, whose description explicitly warns that raw R2-to-max must NEVER be used to judge a resolution change (it rises to 0.839 at 1h while the increment goes to zero). Subject-grouped, out-of-fold.
MANDATORY COMPANION: `eiv_heldout_hr_r2` on hr_mean, to catch the reallocation failure where peak rises only because level fell. Report both in the same table; peak up + level down is NOT a win (this exact trap refuted the H4b caption arm).

STAGE 1 — CPU, do this FIRST, no GPU, no encoder training.
Build each patch grid directly over raw canonical data and fit the ridge readout. This bounds what each geometry CAN carry before any run is spent — the diagnostic's own description prescribes this ('a cheap CPU-only ridge probe requiring no GPU and no trained adapter, so it can gate an architecture change BEFORE any training run is spent'). Reference points already measured on 23 patches / 24h / n=4010 clean days: avg-pooled increment +0.0030; on the direct excursion target (max minus mean, std 19.1 bpm) avg 0.1685, max 0.1273, avg+max concat 0.2968. There is also a known information bound: patch maxes + HistGB reach R2 +0.9706 / +14.72 bpm skill, so the headroom is real and large.
GATE g1 (BLOCKING): if arm B does not beat arm A on the CPU information bound, the GPU stage is NOT justified — stop and record the null. Commit the MDE before running.

STAGE 2 — GPU, ONLY if Stage 1 passes.
Retrain Phase A/B at the winning geometry with >=3 SEEDS and compare against the v9band04 baseline recipe (`lora_proxy_12ch_wl_band04`) on DEPLOYMENT metrics: `eiv_supabase_hr_raw_mae_bpm` quoted with its 4.02 trivial baseline on the same 200-row draw, plus `eiv_dense128_hr_raw_mae_bpm` to guard against buying deployment accuracy by wrecking the dense population.
SEED DISCIPLINE (non-negotiable on this project): the measured post-atomfix supabase seed band is 0.60 bpm and the pre-fix band was 11.37. A single-seed difference smaller than the band demonstrates NOTHING. Report per-seed values and the worst seed, never a single point estimate.

PREREGISTER BEFORE STAGE 1 (propose_hypothesis with predicted_effects + gates, then start_experiment): does increment rise monotonically with token count, or is there an interior optimum? Commit the direction and the MDE first. Note that a NULL here is genuinely valuable — it would say the 23-token bottleneck is not what limits excursion, which redirects effort to the readout/decode stage where E6-E10 already localised the numeric-slot failures.

PAPER ANCHORS to attach via add_paper/link_paper: SensorLM arXiv:2506.09108; LSM-2 (Google, 1440x26, 10-min patch, 144 tokens/signal); Moirai 2.0 arXiv:2511.11698 (single patch beat multi-patch); Pathformer arXiv:2402.05956 (multi-scale, generic forecasting only).

## Preconditions
STAGE 1 is CPU-only and has no preconditions beyond the corpus (data/canonical, git_sha e849eb7, tensor [11318,1440,12] — read shape/epoch_minutes/git_sha from merge_manifest.json, do not assume). STAGE 2 requires Stage 1 to have PASSED its gate; do not start GPU work otherwise. Baseline recipe for any Stage-2 comparison is v9band04 (`lora_proxy_12ch_wl_band04`), documented in docs/BASELINE.md.

## Links
Queued for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Intended gates: `g1_cpu_information_bound_increment`, `g2_hr_mean_not_reallocated`, `g3_gpu_deployment_beats_seed_band`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#epoch-resolution #pooling #cross-modal #control
