---
ambition_class: mechanism
created_at: '2026-09-01T20:28:59+00:00'
former_ids: []
gap_closure_estimate: 0.1
gates:
- blocking: true
  description: BLOCKING. `phase_a_12ch_v41pl60` with sensor_micro_batches=4 completes
    >=30 optimizer steps at its shipped composition-sum batch of 92 with no CUDA OOM,
    and the log is GREPPED for tracebacks rather than trusted to its exit code --
    run_v41_pl60state.sh printed 'PHASE A RC=0' over a real OOM because `... | tee
    log | tail -4; echo RC=$?` reads tail's status. Peak VRAM reported from torch.cuda.max_memory_allocated,
    not nvidia-smi.
  diagnostic_id: eiv_peak_vram_gib
  id: g1-pl60-phase-a-completes
  mde: null
- blocking: true
  description: BLOCKING ON THE CLAIM, not on the arm. A 30-step smoke of `lora_proxy_12ch_v41pl60`
    at sensor_slots 96 either completes or OOMs, and the 2026-08-19 'C does not fit
    at PL60' cell is replaced by a measurement taken on the CURRENT recipe. If it
    OOMs, expose the existing LLM gradient checkpointing (pipeline.py ~4230, hard-gated
    on swap_contrastive_weight > 0) as its own flag and re-smoke. Either outcome closes
    this gate; inheriting the old number does not.
  diagnostic_id: eiv_peak_vram_gib
  id: g2-phase-c-at-pl60-settled-either-way
  mde: null
- blocking: true
  description: 'BLOCKING. The flag must be inert when unset. Evidenced by: default
    0/1 taking the original single-call path; the executed equivalence proof; and
    a full-suite run whose 26 failures are byte-identical to the clean-tree baseline.
    Any NEW test failure attributable to these three files fails this gate.'
  diagnostic_id: eiv_peak_vram_gib
  id: g3-no-existing-arm-perturbed
  mde: null
id: pl60-fits-once-the-batch-92-projector-activation-is-chunked
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_vram_gib
  direction: down
  magnitude_note: PRIMARY. PL60 Phase A at composition-sum batch 92 currently does
    not complete at all -- it OOMs at step 0 asking for 1013.4 MiB on top of a nearly-full
    card. With sensor_micro_batches=4 the offending activation is 253.4 MiB, a 760
    MiB reduction in that one tensor, and the stage should complete 30 steps with
    peak VRAM under 11.51 GiB. The prediction is completion-without-OOM; the GiB figure
    is the instrument.
problem_id: the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all
problem_tags:
- optihealth-eiv
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- deployment-distribution
- optihealth
- readout
---

## Statement
TRACK 2 STEP 1. The PL60/ST30 OOM is a single oversized activation, not a card limit, and chunking the sensor forward makes the PL60 chain fit the 5070 unchanged in every other respect.

THE MECHANISM, decoded rather than guessed. v41's Phase A asked for exactly 1014 MiB. That is `92 x (24 ch x 47 patches) x 2560 x 4 B = 1013.4 MiB` -- ONE fp32 projector output for the whole stratified batch, at `cross_modal.py:262` (`output_norm`). The batch axis is 92, not the profile's `batch_size: 16`, because Phase A's stratified sampler overrides it with its composition sum and says so in the log. Phase A holds no LLM at all (the text side is a warm cache), so this one sensor-side tensor is the entire problem. At PL120 the same tensor is 496 MiB, which is why every prior arm fit.

COMMITTED PREDICTION. With `sensor_micro_batches: 4` (new flag, default 0 = off), `phase_a_12ch_v41pl60` runs at its shipped composition-sum batch of 92 without OOM, and peak VRAM stays under the 11.51 GiB the card makes usable. Direction on eiv_peak_vram_gib: DOWN.

WHAT I AM NOT PREDICTING, and this is the part to hold me to: this says NOTHING about whether v41 is a good arm, whether PL60 geometry helps state, or whether g3 will move. It is a feasibility claim about memory. A pass buys the right to run the state experiment; it is not evidence for it.

WHY THE FLAG IS NOT A RECIPE CHANGE, measured rather than asserted (scratch/t2_micro_batch_equivalence.py, executed before this hypothesis was filed): the batch is split into N chunks under `torch.utils.checkpoint` and CONCATENATED BEFORE any loss, so InfoNCE sees the same batch and the same negatives and the triplet miner the same triples. With dropout off, chunking is EXACT -- soft tokens max|diff| 0.0 at N=2, 1.2e-06 at N=4/5; gradients agree to 2.7e-07 RELATIVE across 41 tensors, which is float32 associativity in the chunk reduction. With the projector's own `dropout=0.1` live it is NOT bitwise, because each chunk draws its own mask -- and the control settles what that residue is: two UNCHUNKED runs with redrawn dropout differ by 9.8e-02 (tokens) / 3.2e+01 (grads), and the chunked-vs-unchunked difference is 1.00-1.11x that. The residue is the RNG stream, not the arithmetic.

THE THIRD CELL OF THE VRAM TABLE IS SUSPECTED STALE AND IS TESTED HERE TOO. "Phase C batch 2 OOMs 908 MiB short at PL60" was measured on 2026-08-19 against profiles with NO `sensor_slots`, i.e. a 1,128-token prefix. Today's recipe pools to `sensor_slots: 96` BEFORE the LLM regardless of patch count, so the prefix is 96 tokens either way. A 30-step smoke settles it instead of inheriting it.

## Rationale
Anton's steer is "fit the card, do not rent". The rule-out `pl60-chain-fits-the-5070` is correctly scoped to the shipped profiles AS WRITTEN and stays true; this hypothesis changes the profiles. The alternative on the table was a PL120 fallback with 0.5/0.5 heads, which walks straight into v30state (held-out dense 19.935, scale 2.77) and the exp-v22/v29 finding that heavier heads cost 1.5-1.8 eff_rank -- so the fallback is worse than fixing the memory.

## Plan
1. DONE, pre-registration: `_sensor_forward` in src/training/pipeline.py wraps encoder -> fusion -> projector and optionally chunks it under torch.utils.checkpoint; wired at all three sensor forwards (Phase A InfoNCE, Phase B InfoNCE, Phase B triplet -- the triplet path carries 3x the pair batch and is the one that OOMs first). `fused` is returned alongside because three auxiliary heads read it and it is only 79 MiB at these sizes. Plumbed as `sensor_micro_batches` through cli_train for both phases and recorded in phase_config.json. Default 0/1 is the untouched single-call path.
2. DONE: equivalence proof, two regimes, above.
3. DONE: `sensor_micro_batches: 4` added to `phase_a_12ch_v41pl60` and `phase_b_12ch_v41pl60` only, verified by dict-diffing the WHOLE reloaded yaml against its pre-edit copy -- exactly two profiles changed, exactly one key each.
4. DONE: full suite run and ATTRIBUTED. 26 failures, and the identical 26 fail on a clean tree with these three files stashed (diff of the sorted FAILED lists is empty). 866 passed. A further 8 files cannot be collected at all in this environment for a pre-existing reason (`ModuleNotFoundError: pkg_resources` via heartpy) and are excluded from both sides.
5. TO RUN (needs the GPU, queued behind eiv-v39c): a 30-step Phase A smoke at PL60 with the flag on, reporting peak VRAM; then a 30-step Phase C smoke of `lora_proxy_12ch_v41pl60` at `sensor_slots: 96` to settle the stale cell.

## Links
Tests problem [[the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all]].
Predicts [[eiv_peak_vram_gib]] (down).
Gate `g1-pl60-phase-a-completes` (blocking): BLOCKING. `phase_a_12ch_v41pl60` with sensor_micro_batches=4 completes >=30 optimizer steps at its shipped composition-sum batch of 92 with no CUDA OOM, and the log is GREPPED for tracebacks rather than trusted to its exit code -- run_v41_pl60state.sh printed 'PHASE A RC=0' over a real OOM because `... | tee log | tail -4; echo RC=$?` reads tail's status. Peak VRAM reported from torch.cuda.max_memory_allocated, not nvidia-smi.
Gate `g2-phase-c-at-pl60-settled-either-way` (blocking): BLOCKING ON THE CLAIM, not on the arm. A 30-step smoke of `lora_proxy_12ch_v41pl60` at sensor_slots 96 either completes or OOMs, and the 2026-08-19 'C does not fit at PL60' cell is replaced by a measurement taken on the CURRENT recipe. If it OOMs, expose the existing LLM gradient checkpointing (pipeline.py ~4230, hard-gated on swap_contrastive_weight > 0) as its own flag and re-smoke. Either outcome closes this gate; inheriting the old number does not.
Gate `g3-no-existing-arm-perturbed` (blocking): BLOCKING. The flag must be inert when unset. Evidenced by: default 0/1 taking the original single-call path; the executed equivalence proof; and a full-suite run whose 26 failures are byte-identical to the clean-tree baseline. Any NEW test failure attributable to these three files fails this gate.

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline
