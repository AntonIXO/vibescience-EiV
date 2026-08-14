---
direction: lower_better
id: eiv_phase_c_seed_band_range_bpm
name: Phase C seed-to-seed range of emitted hr_peak skill
requires_control: null
topic_tags:
- medgemma
- cross-modal
- baseline
- control
unit: bpm
---

## Description
max - min of eiv_gen_peak_skill_bpm across >=3 runs of the IDENTICAL Phase-C configuration differing ONLY in the training seed, all off the SAME Phase-B checkpoint, scored on the SAME eval samples with the SAME draw.

WHY THIS EXISTS. Phase C ran with NO seeding at all until 2026-08-09 (fix 0b5aff2): run_lora_proxy_adaptation had zero manual_seed / np.random.seed / random.seed / Generator across 1156 lines while Phase A and B both seeded at 42. Two adapters with BYTE-IDENTICAL manifests (v4w and v5c; only final_loss differed, 1.2183 vs 0.5821) measured 2.673 bpm apart on emitted hr_peak skill and 0.712 bpm apart on hr_raw skill, on 48 identical eval samples.

Every effect this project has claimed is SMALLER than that accidental two-point spread: the H-1 dryad hr_acc delta was 0.0078 (one sample in 128) and the v4c/v4w allowlist primary gate was +0.0249 against a +0.03 bar. Without this number, a Phase-C A/B cannot state its own minimum detectable effect, and an arm that 'wins' by less than the band has demonstrated nothing.

HOW TO USE IT. Measure it ONCE per pipeline configuration change that could plausibly alter optimisation (loss balance, LoRA target modules, sampler, step count). Then require every subsequent single-seed arm to exceed it, or run enough seeds that the difference of means is significant against it. It is a LOWER_BETTER property of the PIPELINE, not of any arm: a large band is itself a defect worth fixing (via seeding, longer training, or a less bistable objective) before more arms are spent.

MUST be reported with (a) the number of seeds, (b) the individual per-seed values, not just the range, and (c) the same quantity for hr_raw, because the two slots have different basin sensitivity (2.673 vs 0.712 bpm on the v4w/v5c pair).

*Unit:* `bpm` — *lower_better*

#medgemma #cross-modal #baseline #control
