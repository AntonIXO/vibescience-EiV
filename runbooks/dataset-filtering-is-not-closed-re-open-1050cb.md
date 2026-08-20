---
created_at: '2026-08-08T08:13:59+00:00'
former_ids:
- dataset-filtering-is-not-closed-re-open-and-do-it-properly-two-allowlist-arms-ca
gates: []
hypothesis_id: allowlist-a-b-full-chain-executes-runbook-dc0de8
id: dataset-filtering-is-not-closed-re-open-1050cb
notes:
- at: '2026-08-20T02:07:49+00:00'
  text: '2026-08-20: EXECUTED as exp-050 (verdict refutes, failure_mode recipe_conditional).
    Both full chains trained and audited on the 12,547 corpus; prediction inverted
    (ctrl 6.03 beat wl 20.28) and the experiment surfaced a bigger variable: ALL fresh
    new-corpus A/B encoders produce deployment-broken C adapters (new problem new-corpus-12-547-0-0-phase-a-b-encoders...).
    The allowlist question is only re-askable once that problem is resolved — reopen
    the hypothesis then (recipe_conditional).'
priority: 1
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
status: done
topic_tags: []
updated_at: '2026-08-20T02:07:49+00:00'
---

## Prompt
DATASET FILTERING IS NOT CLOSED. Re-open and do it properly: two allowlist arms carried through
the FULL A -> B -> C chain, scored on GENERATION metrics.

WHY THIS IS QUEUED AT PRIORITY 1 AGAIN. It was previously recorded as closed-negative on the
strength of ONE Phase A arm (single seed, -0.0708, inside the 0.066 seed spread) plus a per-phase
mechanism argument made from CPU measurements of the data. The rule_out has been RETRACTED as
over-claimed. Phase B and Phase C were never trained with a changed allowlist, and every number used
to argue they could not matter was ENCODER READOUT, while the actual failure is in GENERATION.

=== 2026-08-14 UPDATES (read before launching) ===
 * SEED WARNING: until 702acb1 `eiv train phase-a` IGNORED profile `seed:` (always 42), and until
   0b26d38 Phase B had NO seed parameter at all AND silently dropped min_hr_coverage (the e828b9e
   YAML fix never executed). Both now plumbed; Phase B checkpoints record seed + min_hr_coverage in
   phase_config.json. The '0.066 seed spread' above is PRE-FIX and suspect: the first real phase-a
   3-seed band measured 0.1022 on the increment (e15h4b). Verify multi-seed claims by checkpoint sha.
 * TEXT-ENCODER COST NOTE IS STALE: measured headless 2026-08-14 (logs/bench_text_encoder_gpu.json)
   bf16 MedGemma-4b peaks 8.48 GiB on the 5070 and does 98.5 captions/s, so a per-filter precompute
   is ~1 MINUTE on GPU, not ~50 min CPU. EIV_TEXT_ENCODER_DEVICE=cpu has been removed from the chain
   scripts; do NOT set it headless. Cache is device-equivalent (GPU/CPU cosine >= 0.99986).

DESIGN
  arm CONTROL      : phase_a_12ch_pmdata  -> phase_b_12ch -> lora_proxy_12ch   (incumbent filters)
  arm REPAIRED     : phase_a_12ch_1min_wl -> phase_b (same filter) -> lora_proxy (same filter)
  The allowlist must be changed CONSISTENTLY IN ALL THREE PHASES, which is the actual hypothesis and
  is NOT what the previous arm tested (it changed Phase A only, then ran nothing downstream).
  One seed per arm unless the result lands inside the seed band, in which case say so rather
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

COST: ~4 h GPU for both arms; text-embedding precompute per distinct dataset_filter is now ~1 min
on GPU (see 2026-08-14 update above).

## Log
- **2026-08-20T02:07:49+00:00** — 2026-08-20: EXECUTED as exp-050 (verdict refutes, failure_mode recipe_conditional). Both full chains trained and audited on the 12,547 corpus; prediction inverted (ctrl 6.03 beat wl 20.28) and the experiment surfaced a bigger variable: ALL fresh new-corpus A/B encoders produce deployment-broken C adapters (new problem new-corpus-12-547-0-0-phase-a-b-encoders...). The allowlist question is only re-askable once that problem is resolved — reopen the hypothesis then (recipe_conditional).

## Links
Queued for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Run as [[allowlist-a-b-full-chain-executes-runbook-dc0de8]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
