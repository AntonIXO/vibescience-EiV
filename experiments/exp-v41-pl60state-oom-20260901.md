---
artifacts: []
closed: true
config_note: 'v41pl60state driver scratch/run_v41_pl60state.sh, unit eiv-v41 (systemd-run),
  started 15:44 MSK, GPU acquired 17:36. Profiles phase_a_12ch_v41pl60 / phase_b_12ch_v41pl60
  / lora_proxy_12ch_v41pl60. Pre-flight declared-variables gate PASSED (A: patch_len,
  stride, state_weight, state_patch_weight, seed, notes; B and C: patch_len, stride,
  notes only). PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True exported.'
cost: null
crash_reason: 'CUDA OutOfMemoryError in Phase A forward (torch.rms_norm inside the
  MedGemma text path) at step 0: ''Tried to allocate 1014.00 MiB. GPU 0 has a total
  capacity of 11.53 GiB of which 783.00 MiB is free; 10.32 GiB allocated by PyTorch''.
  logs/v41_phase_a.log. PHASE A RC=0 was printed by the driver because the RC read
  tail''s exit code (pipeline), not eiv''s -- the same masked-EXIT-0 shape recorded
  on 2026-08-19. g0 then fired on a missing phase_config.json (eff_rank None), which
  is the veto working: no checkpoint was written, no GPU-hours spent on B or C. Wall
  time 1h53 (1h52 of it in the GPU wait loop), 1min CPU, zero GPU.'
created_at: '2026-09-01T18:54:58+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@8c483a1+dirty
hypothesis_id: a-held-out-scored-chain-combining-every-079038
id: exp-v41-pl60state-oom-20260901
interpretation: null
key_numbers: {}
observed_effects: []
parent_experiment_id: null
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[a-held-out-scored-chain-combining-every-079038]].
At commit `fix/soft-token-norm-scale@8c483a1+dirty`.

**Verdict:** crashed

**Crash:** CUDA OutOfMemoryError in Phase A forward (torch.rms_norm inside the MedGemma text path) at step 0: 'Tried to allocate 1014.00 MiB. GPU 0 has a total capacity of 11.53 GiB of which 783.00 MiB is free; 10.32 GiB allocated by PyTorch'. logs/v41_phase_a.log. PHASE A RC=0 was printed by the driver because the RC read tail's exit code (pipeline), not eiv's -- the same masked-EXIT-0 shape recorded on 2026-08-19. g0 then fired on a missing phase_config.json (eff_rank None), which is the veto working: no checkpoint was written, no GPU-hours spent on B or C. Wall time 1h53 (1h52 of it in the GPU wait loop), 1min CPU, zero GPU.

## Notes
NO MEASUREMENT: the hypothesis is untested, not refuted. WHY IT WAS FORESEEABLE: the vault ALREADY held the VRAM facts -- references/analog-state-campaign-ops.md and ab-chain-pitfalls-20260819.md record that PL60/ST30 (47 patches vs 23) roughly doubles activation memory, that Phase B at batch 16 OOMs 530 MiB short (use 8), and that 'PL60 does not fit the C stage on the 5070 at all -- OOM in backward at batch 2, 908 MiB short, even with expandable segments; C runs PL120 only on this card'. The v41 profiles copied phase_a batch 16 / phase_b batch 30 / phase_c batch 2 from the PL120 bases unchanged. Phase A at PL60 was never measured before today (the 08-19 PL60 chains ran A at a different caption/head recipe); it now is: batch 16 x grad-accum 4 does not fit either. Even with Phase A and B reduced to batch 8 the chain cannot complete locally because Phase C at PL60 batch 2 is a measured OOM. THE HYPOTHESIS THEREFORE NEEDS EITHER (a) a >=16 GiB card, or (b) a code change -- gradient checkpointing on the LLM in Phase C is currently gated on swap_contrastive_weight>0 (pipeline.py ~4226) and would have to be exposed as a default-off profile key first, then that flag is its own one-variable arm before v41 can ride on it. Neither is a knob on this driver. The 'PL120 with 0.5/0.5 heads' fallback is NOT a clean residual: v30state (PL120, state_patch_weight 0.1, soft) already collapsed deployment at held-out dense 19.935 / scale 2.77, and exp-v22/v29 measured heads costing -1.5 to -1.8 eff_rank, so a heavier head dose on the same geometry walks into a refuted region.

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline
