---
created_at: '2026-08-08T08:13:59+00:00'
gates: []
hypothesis_id: null
id: dataset-filtering-is-not-closed-re-open-and-do-it-properly-two-allowlist-arms-ca
priority: 1
problem_id: the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid
status: queued
topic_tags: []
updated_at: '2026-08-08T08:13:59+00:00'
---

## Prompt
DATASET FILTERING IS NOT CLOSED. Re-open and do it properly: two allowlist arms carried through
the FULL A -> B -> C chain, scored on GENERATION metrics.

WHY THIS IS QUEUED AT PRIORITY 1 AGAIN. It was previously recorded as closed-negative on the
strength of ONE Phase A arm (single seed, -0.0708, inside the 0.066 seed spread) plus a per-phase
mechanism argument made from CPU measurements of the data. The rule_out has been RETRACTED as
over-claimed. Phase B and Phase C were never trained with a changed allowlist, and every number used
to argue they could not matter was ENCODER READOUT, while the actual failure is in GENERATION.

DESIGN
  arm CONTROL      : phase_a_12ch_pmdata  -> phase_b_12ch -> lora_proxy_12ch   (incumbent filters)
  arm REPAIRED     : phase_a_12ch_1min_wl -> phase_b (same filter) -> lora_proxy (same filter)
  The allowlist must be changed CONSISTENTLY IN ALL THREE PHASES, which is the actual hypothesis and
  is NOT what the previous arm tested (it changed Phase A only, then ran nothing downstream).
  One seed per arm unless the result lands inside the 0.066 seed band, in which case say so rather
  than silently promoting it.

SCORE ON GENERATION, NOT ENCODER READOUT. Primary: p4 hr_acc from the CORRECTED bucket probe
(commit bba9bcf), priced against the always-predict-majority baseline of 0.569 on this pool -- the
incumbent scores 0.4609, i.e. 10.8 points BELOW a constant predictor. Secondary: p7 sensor attention
mass against the 0.2238 uniform baseline (incumbent 0.1277), p5 retrieval, and the encoder-side
eiv_peak_excursion_increment_r2 for continuity with the existing record.

BLOCKED ON TWO VACUOUS PROBES. p3 (swap_measured=False, swap_total=0) and p6 (both arms 0.0) are
dark, and they are exactly the probes that would attribute a generation change to the conditioning
rather than to a shifted prior. Fix those FIRST or the arms cannot be interpreted causally. See the
separate problem record on the vacuous probe suite.

CHEAPER PREREQUISITE WORTH DOING FIRST: the four narrow facts that survived the retraction predict
this arm will be null. If it is null, that is a real and reportable result -- but it must be a
MEASURED null this time, on generation metrics, not an inferred one.

COST: ~4 h GPU for both arms plus a text-embedding precompute per distinct dataset_filter (the cache
key includes it). Note EIV_TEXT_ENCODER_DEVICE=cpu costs ~50 min per precompute and leaves the GPU
idle; try device_map/max_memory to keep most layers on GPU before defaulting to CPU.

## Links
Queued for problem [[the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
