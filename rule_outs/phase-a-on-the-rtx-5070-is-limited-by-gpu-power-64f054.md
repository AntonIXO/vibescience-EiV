---
created_at: '2026-08-31T08:50:19+00:00'
former_ids: []
id: phase-a-on-the-rtx-5070-is-limited-by-gpu-power-64f054
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-31T08:50:19+00:00'
topic_tags: []
---

## Claim
Phase A on the RTX 5070 is limited by GPU power, VRAM capacity or batch size, so the way to speed it up is a bigger batch or a power/clock change. Corollary claim, from a py-spy read: bitsandbytes AdamW8bit's per-parameter torch.cuda.synchronize() costs 43% of the step, so switching to a fused AdamW is a ~1.5-1.9x win.

## Method
Live profiling of the running v29 Phase A (systemd unit eiv-v29v30) 2026-08-31 11:33-11:50: nvidia-smi dmon -s pucm (20x1s), nvidia-smi -q -d PERFORMANCE for clock event reasons, ps -L for per-thread state, py-spy dump + py-spy record --duration 45 --rate 100 (raw folded, /tmp/phasea.folded, 4457 samples), then a direct CUDA-event benchmark of the optimiser step on the real parameter shapes: scratch/bench_optimizer.py -> logs/optimizer_bench.json. Step budget cross-checked against the v28 Phase A report (1243.32 s / 2000 steps = 621.7 ms/step).

## Evidence
NOT power/thermal/VRAM limited. nvidia-smi -q -d PERFORMANCE during a live Phase A: every clock event reason reads Not Active (SW Power Cap Not Active, HW Slowdown Not Active, HW/SW Thermal Not Active). SM clock 2827-2895 MHz against a 3090 max (~7% off boost), memory clock pinned at 13801 MHz, temp 50-70 C, power 153-191 W of a 250 W limit. VRAM 9,844 of 12,227 MiB with 2.4 GiB free -- and batch is capped by PHASE B (OOM at 48 and at 36, succeeds at 30), not by Phase A, so the free VRAM is not spendable here anyway.

WHAT IT ACTUALLY IS: host serialisation. sm% over 20x1s samples oscillates 17/96/23/100/100/2/100/3/100/100/11/92/43/100/90/2/100/59/65/89 -- multi-second valleys at 2-3%, the signature of pipeline bubbles, not of a saturated kernel. The process has 93 threads and exactly ONE is active+gil at 98.7%; the other 92 sit in futex_wait. DataLoader num_workers=0 (the key is set in NO profile, so it defaults to 0), hence pin_memory=False and persistent_workers=False, so the whole input pipeline runs on the training thread.

HOST TIME (py-spy, 4457 samples / 45 s at 100 Hz), against the measured 621.7 ms/step (v28 Phase A: 1243.32 s / 2000 steps):
  43.1% = 268 ms/step blocked in torch.cuda.synchronize <- bitsandbytes sync_gpu <- AdamW8bit.step
  30.8% = 191 ms/step dataset __getitem__ ON THE MAIN THREAD (population.to_raw denormalisation, contracts.observed label lookup, _aim_artificial_mask, HR caption string building)
  22.8% = 142 ms/step in _masked_mean_soft_tokens (pipeline.py:410)
   0.5% forward+loss, 0.1% backward -- the arithmetic is invisible on the host timeline

THE COROLLARY IS REFUTED BY DIRECT MEASUREMENT. scratch/bench_optimizer.py rebuilds the REAL Phase-A trainable set from a v28 checkpoint (52 tensors, 41.085 M params, 17 >= min_8bit_size=4096 so quantised, 35 stay fp32) and times opt.step() with CUDA events, 5 warmup + 30 iters, on this card while training shares it:
  AdamW8bit (current)  median 3.772 ms
  AdamW fused=True     median 2.058 ms   (1.8x on the step ITSELF)
  AdamW foreach=True   median 10.342 ms  (SLOWER)
  AdamW default        median 6.162 ms   (SLOWER)
So the optimiser's own arithmetic is 3.772 ms = 0.61% of a 621.7 ms step, and switching to fused saves 1.715 ms = 0.28% of the step -- not 43%, and not 1.5-1.9x. Over the whole 2000-step Phase A budget the fused optimiser saves 3.4 seconds.

The 43.1% is the host BLOCKING AT A BARRIER while the GPU drains the asynchronous forward/backward queued before it -- attributed to whoever calls synchronize first, which happens to be bitsandbytes. The same misattribution applies to the 22.8% at pipeline.py:410, which is `if fallback_needed.any():` -- a host branch on a GPU tensor, i.e. an implicit device sync, not pooling arithmetic.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
