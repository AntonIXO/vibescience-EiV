---
artifacts:
- logs/t2_pl60_smoke.json
- logs/t2_smoke_results.csv
- logs/t2_smoke_phase_a.log
- logs/t2_smoke_phase_b.log
- logs/t2_smoke_phase_c.log
- scratch/run_t2_pl60_smoke.sh
- scratch/t2_micro_batch_equivalence.py
- scripts/oom_decode.py
- outputs/checkpoints/t2_pl60_smoke/
closed: true
config_note: '30-step smokes of all three stages at PL60/ST30 (47 patches) with sensor_micro_batches=4
  on Phase A and B. Driver scratch/run_t2_pl60_smoke.sh, unit eiv-t2smoke, 1h14m wall
  (mostly waiting for eiv-v39c to clear the card), 4m27s CPU. max_steps=30 injected
  via a TEMPORARY profile config (eiv train has no --max-steps), verified by dict-diffing
  the whole reloaded yaml: exactly three profiles changed, exactly max_steps, and
  sensor_micro_batches survived at 4 on A and B. configs/training.yaml itself untouched
  by the smoke. Text cache warmed for phase_a_12ch_v41pl60 on an empty card first
  (the v39b lesson). VRAM instrument is DRIVER-reported `nvidia-smi memory.used` sampled
  every 2s and maxed -- NOT torch.cuda.max_memory_allocated, which the training pipeline
  does not expose; the driver figure includes the caching allocator''s reserve and
  is therefore the larger, conservative number for a "does it fit" claim. Card: RTX
  5070, 12,227 MiB total, ~11.51 GiB usable. Every stage graded by grepping its own
  log for traceback/OOM as well as by RC, because run_v41_pl60state.sh printed RC=0
  over a real OOM.'
cost:
  gpu_hours: 0.15
  wall_hours: 1.24
crash_reason: ''
created_at: '2026-09-01T21:46:42+00:00'
diagnostics_measured:
- after: 6.972
  before: 11.51
  diagnostic_id: eiv_peak_vram_gib
do_not_retry:
- 'Do NOT cite the 2026-08-19 VRAM table''s PL60 row for Phase C (''batch 2 OOMs 908
  MiB short'') or for Phase B (''needs batch 8''). Both are superseded by measurement:
  Phase C fits at 10.949 GiB and Phase B fits at its shipped batch 30 (7.532 GiB),
  with sensor_micro_batches=4 on A and B. The Phase C figure was taken before `sensor_slots`
  existed, when the prefix was 1,128 tokens instead of 96.'
- Do NOT drop Phase B's batch_size to 8 for PL60. It was a workaround for a memory
  limit that no longer binds, and it is a real recipe change -- fewer negatives in
  the triplet loss -- that would put v41 an extra axis away from its comparator.
- Do NOT build the LLM gradient-checkpointing flag (pipeline.py ~4230, hard-gated
  on swap_contrastive_weight > 0) on the strength of the old Phase C cell. Phase C
  fits. Build it only if a future recipe actually OOMs there, and re-measure before
  assuming it will.
- Do NOT read 10.949 GiB as comfortable. It is ~0.56 GiB under the card's usable ~11.51,
  so PL60 Phase C has no room for another concurrent GPU process, a larger sensor_slots,
  or a longer prefix. Anything added to that stage needs a fresh smoke, not an extrapolation.
- Do NOT grade a stage by its exit code. Every stage here was ALSO graded by grepping
  its own log for traceback/OutOfMemory, because run_v41_pl60state.sh printed 'PHASE
  A RC=0' over a real OOM -- `... | tee log | tail -4; echo RC=$?` reads tail's status,
  not the trainer's.
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS. Phase A at PL60/ST30 completed 30 optimizer steps, rc=0, peak VRAM
    7,139 MiB = 6.972 GiB against ~11.51 GiB usable -- 4.5 GiB of headroom on the
    stage that previously OOMed at step 0.


    GRADED BY LOG, NOT BY RC, as the gate required: `grep -ciE ''traceback|outofmemory|cuda
    out of memory'' logs/t2_smoke_phase_a.log` = 0.


    AND THE BATCH WAS THE OFFENDING ONE. The log carries `WARNING: stratified_batches
    sums to 92 but batch_size=16; using composition sum` and `composition={''lifesnaps'':
    45, ''pmdata'': 12, ''*'': 35}`, so this ran at the exact effective batch of 92
    that produced the 1013.4 MiB request, not at the profile''s nominal 16.


    THE FLAG WAS LIVE, PROVEN FROM THE CHECKPOINT rather than from the config we wrote:
    outputs/checkpoints/t2_pl60_smoke/phase_a/phase_config.json records `sensor_micro_batches:
    4`, `patch_len: 60`, `stride: 30`, `steps: 30`. This project has been burned by
    flags that were set but inert (patch_pool_mode silently trained ''avg'' for three
    arms), so the check is the checkpoint''s own record.


    VRAM instrument caveat, restated because it deviates from the gate''s wording:
    driver-reported nvidia-smi memory.used sampled every 2s and maxed, not torch.cuda.max_memory_allocated.
    It is the larger of the two and the one that decides whether the card OOMs.'
  gate_id: g1-pl60-phase-a-completes
  passed: true
- evidence: "PASS, and the 2026-08-19 cell was indeed STALE -- Phase C FITS at PL60.\n\n
    \ stage    rc  peak MiB  peak GiB  traceback lines  fits\n  phase_a   0     7,139
    \    6.972         0          yes\n  phase_b   0     7,713     7.532         0
    \         yes\n  phase_c   0    11,212    10.949         0          yes\n\nPhase
    C reached step=20 of its 30 with 0 tracebacks and exited rc=0. The prior record
    -- 'batch 2 OOMs 908 MiB short in backward' -- was measured on profiles with NO
    `sensor_slots`, i.e. a 1,128-token prefix. Today's recipe pools to `sensor_slots:
    96` BEFORE the LLM regardless of patch count, so the prefix is 96 tokens at PL60
    exactly as at PL120. The suspicion recorded in the VRAM table was correct and
    the cell is now replaced by a measurement on the current recipe. 10.949 GiB of
    ~11.51 usable is tight (about 0.56 GiB of headroom) but it completed clean, so
    the LLM-gradient-checkpointing fallback is NOT needed and stays unbuilt.\n\nSECOND
    STALE CELL FOUND IN PASSING, and it is a recipe saving: Phase B ran at its shipped
    `batch_size: 30` and fits at 7.532 GiB. The 2026-08-19 record says PL60 Phase
    B needs batch 8 ('batch 16 OOMs 530 MiB short; batch 8 fits'). With micro-batching
    it does not -- so the planned drop to batch 8, which would have been a genuine
    recipe change costing negatives in the triplet loss, is unnecessary. v41 can keep
    the incumbent's batch."
  gate_id: g2-phase-c-at-pl60-settled-either-way
  passed: true
- evidence: 'PASS on all three legs of the evidence the gate named.


    (1) DEFAULT PATH UNCHANGED. `_sensor_forward` returns the original single fused
    call when micro_batches is 0 or 1, which is every profile except the two v41 ones.
    No existing profile carries the key.


    (2) EXECUTED EQUIVALENCE PROOF (scratch/t2_micro_batch_equivalence.py). Dropout
    off: soft tokens max|diff| 0.0 at N=2 and 1.2e-06 at N=4/5; fused sidecar identical;
    gradients agree to 2.7e-07 RELATIVE across 41 tensors -- float32 associativity
    in the chunk reduction and nothing else. Dropout 0.1 as trained: NOT bitwise,
    and the control identifies the residue -- two UNCHUNKED runs with redrawn dropout
    differ by 9.8e-02 (tokens) / 3.2e+01 (grads), and chunked-vs-unchunked is 1.00-1.11x
    that. The projector defaults to dropout=0.1 inside its cross-attention (cross_modal.py:79,125,203)
    and Phase A/B run it in train(), so each chunk draws its own mask; the batch,
    the negatives and the gradient ESTIMATOR are preserved, the individual draw is
    not.


    (3) TEST ATTRIBUTION. Full suite: 26 failed / 866 passed. The IDENTICAL 26 fail
    on a clean tree with pipeline.py, cli_train.py and configs/training.yaml stashed
    -- the diff of the sorted FAILED lists is empty. A further 8 files cannot be collected
    at all in this environment for a pre-existing reason (ModuleNotFoundError: pkg_resources
    via heartpy) and were excluded from both sides. Zero new failures attributable
    to this change.


    (4) CONFIG EDIT SCOPED. `sensor_micro_batches: 4` was added to phase_a_12ch_v41pl60
    and phase_b_12ch_v41pl60 only, verified by dict-diffing the whole reloaded yaml
    against a pre-edit copy: exactly two profiles changed, exactly one key each.'
  gate_id: g3-no-existing-arm-perturbed
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: pl60-fits-once-the-batch-92-projector-activation-is-chunked
id: exp-t2-pl60-vram-smoke-20260902
interpretation:
  at: '2026-09-01T21:47:52+00:00'
  failure_mode: null
  lesson: 'PL60/ST30 fits the RTX 5070 at every stage — Phase A 6.972 GiB, Phase B
    7.532, Phase C 10.949 against ~11.51 usable — once one fp32 projector activation
    is chunked. It was never a card limit; it was `92 × (24 ch × 47 patches) × 2560`
    at the stratified sampler''s composition-sum batch, which silently overrides `batch_size`.
    Two further cells in the 2026-08-19 VRAM table were also stale and fell here:
    Phase C fits (that number predates `sensor_slots`, so it was measured on a 1,128-token
    prefix instead of 96) and Phase B fits at its shipped batch 30 (so the planned
    drop to batch 8, a real change to the triplet loss''s negatives, is unnecessary).
    The general lesson is the one the skill already prescribes and that had no tool
    behind it until now: decode an OOM''s requested bytes into a tensor shape before
    concluding anything about hardware — `scripts/oom_decode.py` now does it and reproduces
    this decode from the original crashed log.'
  transfers_to:
  - v41-pl60-state-chain-rebased-and-split-honest
key_numbers:
  card_usable_gib: 11.51
  dropout_residue_vs_control_ratio: 1.11
  effective_batch_phase_a: 92.0
  grad_rel_diff_dropout_off: 2.7e-07
  n_patches_pl60: 47.0
  offending_activation_mib_n1: 1013.4
  offending_activation_mib_n4: 253.4
  phase_a_peak_gib: 6.972
  phase_a_peak_mib: 7139.0
  phase_b_batch_size_that_fit: 30.0
  phase_b_peak_gib: 7.532
  phase_b_peak_mib: 7713.0
  phase_c_headroom_gib: 0.561
  phase_c_peak_gib: 10.949
  phase_c_peak_mib: 11212.0
  tracebacks_all_stages: 0.0
observed_effects:
- delta: -4.538
  diagnostic_id: eiv_peak_vram_gib
  direction: down
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_peak_vram_gib: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: supports
---

## Summary
Tests hypothesis [[pl60-fits-once-the-batch-92-projector-activation-is-chunked]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.

## Measurements
- [[eiv_peak_vram_gib]]: before 11.51 → after 6.972  (Δ -4.538, down ✓ predicted down)

## Gates
- `g1-pl60-phase-a-completes`: PASS — PASS. Phase A at PL60/ST30 completed 30 optimizer steps, rc=0, peak VRAM 7,139 MiB = 6.972 GiB against ~11.51 GiB usable -- 4.5 GiB of headroom on the stage that previously OOMed at step 0.

GRADED BY LOG, NOT BY RC, as the gate required: `grep -ciE 'traceback|outofmemory|cuda out of memory' logs/t2_smoke_phase_a.log` = 0.

AND THE BATCH WAS THE OFFENDING ONE. The log carries `WARNING: stratified_batches sums to 92 but batch_size=16; using composition sum` and `composition={'lifesnaps': 45, 'pmdata': 12, '*': 35}`, so this ran at the exact effective batch of 92 that produced the 1013.4 MiB request, not at the profile's nominal 16.

THE FLAG WAS LIVE, PROVEN FROM THE CHECKPOINT rather than from the config we wrote: outputs/checkpoints/t2_pl60_smoke/phase_a/phase_config.json records `sensor_micro_batches: 4`, `patch_len: 60`, `stride: 30`, `steps: 30`. This project has been burned by flags that were set but inert (patch_pool_mode silently trained 'avg' for three arms), so the check is the checkpoint's own record.

VRAM instrument caveat, restated because it deviates from the gate's wording: driver-reported nvidia-smi memory.used sampled every 2s and maxed, not torch.cuda.max_memory_allocated. It is the larger of the two and the one that decides whether the card OOMs.
- `g2-phase-c-at-pl60-settled-either-way`: PASS — PASS, and the 2026-08-19 cell was indeed STALE -- Phase C FITS at PL60.

  stage    rc  peak MiB  peak GiB  traceback lines  fits
  phase_a   0     7,139     6.972         0          yes
  phase_b   0     7,713     7.532         0          yes
  phase_c   0    11,212    10.949         0          yes

Phase C reached step=20 of its 30 with 0 tracebacks and exited rc=0. The prior record -- 'batch 2 OOMs 908 MiB short in backward' -- was measured on profiles with NO `sensor_slots`, i.e. a 1,128-token prefix. Today's recipe pools to `sensor_slots: 96` BEFORE the LLM regardless of patch count, so the prefix is 96 tokens at PL60 exactly as at PL120. The suspicion recorded in the VRAM table was correct and the cell is now replaced by a measurement on the current recipe. 10.949 GiB of ~11.51 usable is tight (about 0.56 GiB of headroom) but it completed clean, so the LLM-gradient-checkpointing fallback is NOT needed and stays unbuilt.

SECOND STALE CELL FOUND IN PASSING, and it is a recipe saving: Phase B ran at its shipped `batch_size: 30` and fits at 7.532 GiB. The 2026-08-19 record says PL60 Phase B needs batch 8 ('batch 16 OOMs 530 MiB short; batch 8 fits'). With micro-batching it does not -- so the planned drop to batch 8, which would have been a genuine recipe change costing negatives in the triplet loss, is unnecessary. v41 can keep the incumbent's batch.
- `g3-no-existing-arm-perturbed`: PASS — PASS on all three legs of the evidence the gate named.

(1) DEFAULT PATH UNCHANGED. `_sensor_forward` returns the original single fused call when micro_batches is 0 or 1, which is every profile except the two v41 ones. No existing profile carries the key.

(2) EXECUTED EQUIVALENCE PROOF (scratch/t2_micro_batch_equivalence.py). Dropout off: soft tokens max|diff| 0.0 at N=2 and 1.2e-06 at N=4/5; fused sidecar identical; gradients agree to 2.7e-07 RELATIVE across 41 tensors -- float32 associativity in the chunk reduction and nothing else. Dropout 0.1 as trained: NOT bitwise, and the control identifies the residue -- two UNCHUNKED runs with redrawn dropout differ by 9.8e-02 (tokens) / 3.2e+01 (grads), and chunked-vs-unchunked is 1.00-1.11x that. The projector defaults to dropout=0.1 inside its cross-attention (cross_modal.py:79,125,203) and Phase A/B run it in train(), so each chunk draws its own mask; the batch, the negatives and the gradient ESTIMATOR are preserved, the individual draw is not.

(3) TEST ATTRIBUTION. Full suite: 26 failed / 866 passed. The IDENTICAL 26 fail on a clean tree with pipeline.py, cli_train.py and configs/training.yaml stashed -- the diff of the sorted FAILED lists is empty. A further 8 files cannot be collected at all in this environment for a pre-existing reason (ModuleNotFoundError: pkg_resources via heartpy) and were excluded from both sides. Zero new failures attributable to this change.

(4) CONFIG EDIT SCOPED. `sensor_micro_batches: 4` was added to phase_a_12ch_v41pl60 and phase_b_12ch_v41pl60 only, verified by dict-diffing the whole reloaded yaml against a pre-edit copy: exactly two profiles changed, exactly one key each.

**Verdict:** supports

## Interpretation
PL60/ST30 fits the RTX 5070 at every stage — Phase A 6.972 GiB, Phase B 7.532, Phase C 10.949 against ~11.51 usable — once one fp32 projector activation is chunked. It was never a card limit; it was `92 × (24 ch × 47 patches) × 2560` at the stratified sampler's composition-sum batch, which silently overrides `batch_size`. Two further cells in the 2026-08-19 VRAM table were also stale and fell here: Phase C fits (that number predates `sensor_slots`, so it was measured on a 1,128-token prefix instead of 96) and Phase B fits at its shipped batch 30 (so the planned drop to batch 8, a real change to the triplet loss's negatives, is unnecessary). The general lesson is the one the skill already prescribes and that had no tool behind it until now: decode an OOM's requested bytes into a tensor shape before concluding anything about hardware — `scripts/oom_decode.py` now does it and reproduces this decode from the original crashed log.

Transfers to: [[v41-pl60-state-chain-rebased-and-split-honest]]

## Key numbers
- `card_usable_gib`: 11.51
- `dropout_residue_vs_control_ratio`: 1.11
- `effective_batch_phase_a`: 92
- `grad_rel_diff_dropout_off`: 2.7e-07
- `n_patches_pl60`: 47
- `offending_activation_mib_n1`: 1013.4
- `offending_activation_mib_n4`: 253.4
- `phase_a_peak_gib`: 6.972
- `phase_a_peak_mib`: 7139
- `phase_b_batch_size_that_fit`: 30
- `phase_b_peak_gib`: 7.532
- `phase_b_peak_mib`: 7713
- `phase_c_headroom_gib`: 0.561
- `phase_c_peak_gib`: 10.949
- `phase_c_peak_mib`: 11212
- `tracebacks_all_stages`: 0

## Artifacts
- `logs/t2_pl60_smoke.json`
- `logs/t2_smoke_results.csv`
- `logs/t2_smoke_phase_a.log`
- `logs/t2_smoke_phase_b.log`
- `logs/t2_smoke_phase_c.log`
- `scratch/run_t2_pl60_smoke.sh`
- `scratch/t2_micro_batch_equivalence.py`
- `scripts/oom_decode.py`
- `outputs/checkpoints/t2_pl60_smoke/`

## Do not retry
- Do NOT cite the 2026-08-19 VRAM table's PL60 row for Phase C ('batch 2 OOMs 908 MiB short') or for Phase B ('needs batch 8'). Both are superseded by measurement: Phase C fits at 10.949 GiB and Phase B fits at its shipped batch 30 (7.532 GiB), with sensor_micro_batches=4 on A and B. The Phase C figure was taken before `sensor_slots` existed, when the prefix was 1,128 tokens instead of 96.
- Do NOT drop Phase B's batch_size to 8 for PL60. It was a workaround for a memory limit that no longer binds, and it is a real recipe change -- fewer negatives in the triplet loss -- that would put v41 an extra axis away from its comparator.
- Do NOT build the LLM gradient-checkpointing flag (pipeline.py ~4230, hard-gated on swap_contrastive_weight > 0) on the strength of the old Phase C cell. Phase C fits. Build it only if a future recipe actually OOMs there, and re-measure before assuming it will.
- Do NOT read 10.949 GiB as comfortable. It is ~0.56 GiB under the card's usable ~11.51, so PL60 Phase C has no room for another concurrent GPU process, a larger sensor_slots, or a longer prefix. Anything added to that stage needs a fresh smoke, not an extrapolation.
- Do NOT grade a stage by its exit code. Every stage here was ALSO graded by grepping its own log for traceback/OutOfMemory, because run_v41_pl60state.sh printed 'PHASE A RC=0' over a real OOM -- `... | tee log | tail -4; echo RC=$?` reads tail's status, not the trainer's.

## Notes
ALL THREE STAGES FIT AT PL60/ST30. The rule-out `pl60-chain-fits-the-5070` remains true as written (it was scoped to the shipped profiles, which had no micro-batching) and is now bounded: it was never a card limit.

  stage    rc  peak GiB  tracebacks  fits
  phase_a   0     6.972       0       yes    <- previously OOMed at STEP 0
  phase_b   0     7.532       0       yes
  phase_c   0    10.949       0       yes    <- the cell recorded as "does not fit"

=== THE PREDICTION, AND WHERE IT UNDERSHOT ===
I predicted Phase A would complete. It does, with 4.5 GiB of headroom. What I did NOT predict is that the other two stale cells would fall as well:

1. PHASE C FITS. The 2026-08-19 record said "batch 2 OOMs 908 MiB short in backward". That was measured on profiles with NO `sensor_slots` -- a 1,128-token prefix. The current recipe pools to `sensor_slots: 96` BEFORE the LLM regardless of patch count, so PL60's prefix is 96 tokens exactly as PL120's. The suspicion written into the VRAM table was right. The LLM-gradient-checkpointing fallback is NOT needed and stays unbuilt.
2. PHASE B FITS AT BATCH 30. The same record said PL60 Phase B needs batch 8. It does not, with micro-batching. That matters scientifically, not just operationally: dropping to batch 8 would have cut the triplet loss's negatives, which is a real recipe change, and v41 would have differed from the incumbent on an extra axis nobody was testing.

=== WHAT ACTUALLY FIXED IT ===
One tensor. `92 x (24 ch x 47 patches) x 2560 x 4 B = 1013.4 MiB`, the projector output at `cross_modal.py:262` for the whole stratified batch -- and the batch axis is 92, not the profile's `batch_size: 16`, because Phase A's sampler overrides it with its composition sum and says so in the log. At N=4 that tensor is 253.4 MiB. Phase A holds no LLM at all; the text side is a warm cache.

=== THE FLAG WAS LIVE, NOT MERELY SET ===
Read back from the checkpoints rather than from the config that was written: `phase_config.json` records `sensor_micro_batches: 4` at BOTH Phase A and Phase B, alongside patch_len 60 / stride 30 / steps 30. And Phase A's log carries the `stratified_batches sums to 92` warning, so the smoke exercised the exact effective batch that produced the original OOM.

=== INSTRUMENT, STATED BECAUSE IT DEVIATES FROM MY OWN GATE WORDING ===
Peak VRAM is driver-reported `nvidia-smi memory.used`, sampled every 2 s and maxed -- not `torch.cuda.max_memory_allocated`, which this pipeline does not expose and which I would not add mid-experiment. The driver figure includes the caching allocator's reserve, so it is the larger and more conservative number for a "does it fit" claim.

=== TOOLING BUILT ALONG THE WAY ===
`scripts/oom_decode.py` now exists. The operating skill has prescribed it by that exact path for weeks, but the file had never been written -- which is why this arithmetic kept being done by hand and why one session read the OOM as a card limit. Run against the real crashed log it reproduces the decode independently: `MATCH [92 x (24 ch x 47 patches) x 2560] fp32 = 1013.44 MiB`.

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline
