---
artifacts:
- logs/p3/paired_llm_vs_head.json
- logs/p3/emission_pulse_ppg_heldout.json
- logs/p3/emission_pulse_ppg_heldout_summary.json
- logs/p3_ppgfm_heads.json
- outputs/p3_ppgfm_tokens/heldout_predictions_trained.json
- outputs/adapters/p3_pulse_ppg_c
- logs/p3/phase_c_pulse_ppg.log
- scratch/p3_ppgfm_tokens.py
- scratch/run_p3_ppgfm.sh
- configs/training.yaml#lora_proxy_12ch_ppgfm
closed: true
config_note: 'Same frozen Pulse-PPG per-minute tokens (outputs/ppgfm/pulse_ppg), two
  consumers, identical rows. Rows: PPG-carrying windows of six corpora split by split_subjects.json
  -- 159 train / 58 held-out (val+test; nurse_stress 31, case 6, wearable_exam_stress
  6, brain_wearable 5, cogwear 5, wesad 5). (i) HEAD: scratch/p3_ppgfm_tokens.py --
  96 x 15-min masked-mean slots (512 + presence), t1b''s AttnReadout (~2M), 3000 steps,
  smooth-L1, seed 0, CPU; random twin = outputs/ppgfm/pulse_ppg_random through the
  same path. (ii) LLM: profile lora_proxy_12ch_ppgfm (band04 recipe: MedGemma-1.5-4b-it
  QLoRA r32, 2600 steps, batch 2 x accum 4, json_slots target, split: train, seed
  42) with sensor_tower: ppgfm -- PPGFMPatchEncoder (512->192 Linear+LN, PL60/ST30
  from the v41 phase_b geometry, C=1) -> fresh ChannelFusion -> fresh CrossModalProjector
  (96 slots) -> soft prefix; freeze_encoder false (the adapter, fusion, projector
  and LoRA all train -- no Phase A/B exists for this tower); hr_augment OFF. Scored
  greedy by scratch/emission_diversity_audit.py --eval-bundle data/canonical --rows-from
  outputs/p3_ppgfm_tokens/heldout_meta.json (58/58 parsed after making a malformed
  number unparseable rather than fatal). GPU: RTX 5070, 11.8 GiB peak, 37 min train
  + 1 min score. Not run: the LLM twin arm (the head''s twin exists; the LLM reads
  at chance on the trained tower).'
cost:
  gpu_hours: 0.65
  wall_hours: 1.2
crash_reason: ''
created_at: '2026-09-05T00:03:44+00:00'
diagnostics_measured:
- after: 8.547
  before: 4.338
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
- after: 8.36
  before: 10.713
  diagnostic_id: eiv_gen_between_sample_std
do_not_retry:
- 'Do NOT re-run the LLM arm with a random-twin tower expecting to learn anything:
  the trained-tower LLM already reads hr_raw at the always-median constant (8.547
  vs 8.544); a twin cannot read lower than chance and the head''s twin (5.334) already
  prices the tower.'
- 'Do NOT compare this 58-row hr_raw MAE (8.5 / 4.3) to the 128-row v41 numbers (2.843
  / 0.812): different pool (PPG-carrying windows of six corpora, 31 of 58 from nurse_stress),
  different tokens, and hr_raw here must be ESTIMATED from a waveform rather than
  read off a channel that contains it.'
- 'Do NOT read the LLM''s hr_peak parity with the head (+0.83, CI crossing zero) as
  the LLM ''matching'' on peak: both arms sit 6-7 bpm above the trivial 22.05 on a
  58-row pool where the seed band is unmeasured.'
- 'Do NOT run a P3 narrator rung (g3) on this tower until a head worth narrating exists:
  4.34 bpm off a waveform is real skill but the product path already has 0.81 off
  the 12-channel tokens.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'Identical 58 held-out PPG-carrying windows (val+test subjects of split_subjects.json),
    identical frozen Pulse-PPG tokens. Attention head hr_raw MAE 4.338 (trivial 8.544,
    skill +4.21, corr 0.858, scale 0.97); mean-pool RidgeCV 5.924. MedGemma-1.5-4b
    + LoRA over the same tokens through a trained projector: 8.547 (skill -0.003 --
    at the always-median constant), corr 0.485, scale 0.76 (outside the [0.85, 1.35]
    band), 58/58 parsed, 18 distinct values, top-1 mass 0.21. Paired per-row: LLM
    - head = +4.209 bpm, bootstrap CI95 [+2.154, +6.275], head better on 65.5% of
    rows, ratio 1.97x -- the gate bar was head + 0.416. hr_peak: LLM 16.17 vs head
    15.34, paired +0.83 [-3.45, +4.96] (no difference; both beat trivial 22.05). Head''s
    random twin 5.334 (Pulse-PPG head beats twin by 1.0 bpm). logs/p3/paired_llm_vs_head.json,
    logs/p3/emission_pulse_ppg_heldout_summary.json, logs/p3_ppgfm_heads.json.'
  gate_id: g1-llm-within-seed-band-of-head
  passed: false
git_ref: track-p/raw-ppg@10d9063+dirty
hypothesis_id: p3-medgemma-soft-tokens-over-frozen-ppg-fm-tokens-match-an-attention-head
id: exp-p3-llm-vs-head-over-pulse-ppg-tokens-20260905
interpretation:
  at: '2026-09-05T00:05:06+00:00'
  failure_mode: claim_false
  lesson: 'The soft-token LLM path does not match a small head even on a pretrained,
    field-trained PPG tower: given identical frozen Pulse-PPG minute tokens and identical
    159 training windows, MedGemma+LoRA emits hr_raw at the always-median constant
    (8.55 vs trivial 8.54, scale 0.76) while a ~2M attention head reads 4.34 (paired
    +4.21 bpm, CI95 [+2.15, +6.28]) -- the §2d.1 formatter finding reproduced on the
    tower the field cares about, closing the fair per-task probe-vs-LLM ablation PulseLM
    did not run. The ''engine'' verdict is directional_only because the committed
    prediction was that the gate fails; read it as the claim being false. The hypothesis
    stays inconclusive only in the engine''s bookkeeping; no soft-token arm is funded
    on this tower, the product path (head -> narrator) is unchanged, and the next
    LLM-side question would be a Flamingo-style cross-attention binding (OpenTSLM)
    rather than a longer prefix.'
  transfers_to:
  - p1b-frozen-ppg-fm-embeddings-clear-the-sensorfm-bar-on-nurse-stress-free-living-and-wesad-lab
key_numbers:
  ci95_hi: 6.275
  ci95_lo: 2.154
  gate_bar_bpm: 0.416
  head_better_frac: 0.655
  head_corr: 0.858
  head_hr_raw_mae: 4.338
  head_twin_hr_raw_mae: 5.334
  hr_peak_head: 15.339
  hr_peak_llm: 16.165
  hr_peak_trivial: 22.049
  llm_corr: 0.485
  llm_hr_raw_mae: 8.547
  llm_scale: 0.759
  n_heldout: 58.0
  n_train: 159.0
  paired_delta_bpm: 4.209
  ratio_llm_over_head: 1.97
  ridge_meanpool_hr_raw_mae: 5.924
  trivial_hr_raw_mae: 8.544
observed_effects:
- delta: 4.209
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: up
- delta: -2.353
  diagnostic_id: eiv_gen_between_sample_std
  direction: down
parent_experiment_id: exp-p1b-ppgfm-sensorfm-bar-readouts-20260905
prediction_match:
  overall: true
  per_diagnostic:
    eiv_splithonest_heldout_hr_raw_mae_bpm: true
provenance: {}
recipe_fingerprint:
  git_ref: track-p/raw-ppg@10d9063+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[p3-medgemma-soft-tokens-over-frozen-ppg-fm-tokens-match-an-attention-head]].
Branches from [[exp-p1b-ppgfm-sensorfm-bar-readouts-20260905]].
At commit `track-p/raw-ppg@10d9063+dirty`.

## Measurements
- [[eiv_splithonest_heldout_hr_raw_mae_bpm]]: before 4.338 → after 8.547  (Δ +4.209, up ✓ predicted up)
- [[eiv_gen_between_sample_std]]: before 10.713 → after 8.36  (Δ -2.353, down)

## Gates
- `g1-llm-within-seed-band-of-head`: **FAIL** — Identical 58 held-out PPG-carrying windows (val+test subjects of split_subjects.json), identical frozen Pulse-PPG tokens. Attention head hr_raw MAE 4.338 (trivial 8.544, skill +4.21, corr 0.858, scale 0.97); mean-pool RidgeCV 5.924. MedGemma-1.5-4b + LoRA over the same tokens through a trained projector: 8.547 (skill -0.003 -- at the always-median constant), corr 0.485, scale 0.76 (outside the [0.85, 1.35] band), 58/58 parsed, 18 distinct values, top-1 mass 0.21. Paired per-row: LLM - head = +4.209 bpm, bootstrap CI95 [+2.154, +6.275], head better on 65.5% of rows, ratio 1.97x -- the gate bar was head + 0.416. hr_peak: LLM 16.17 vs head 15.34, paired +0.83 [-3.45, +4.96] (no difference; both beat trivial 22.05). Head's random twin 5.334 (Pulse-PPG head beats twin by 1.0 bpm). logs/p3/paired_llm_vs_head.json, logs/p3/emission_pulse_ppg_heldout_summary.json, logs/p3_ppgfm_heads.json.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `claim_false`

The soft-token LLM path does not match a small head even on a pretrained, field-trained PPG tower: given identical frozen Pulse-PPG minute tokens and identical 159 training windows, MedGemma+LoRA emits hr_raw at the always-median constant (8.55 vs trivial 8.54, scale 0.76) while a ~2M attention head reads 4.34 (paired +4.21 bpm, CI95 [+2.15, +6.28]) -- the §2d.1 formatter finding reproduced on the tower the field cares about, closing the fair per-task probe-vs-LLM ablation PulseLM did not run. The 'engine' verdict is directional_only because the committed prediction was that the gate fails; read it as the claim being false. The hypothesis stays inconclusive only in the engine's bookkeeping; no soft-token arm is funded on this tower, the product path (head -> narrator) is unchanged, and the next LLM-side question would be a Flamingo-style cross-attention binding (OpenTSLM) rather than a longer prefix.

Transfers to: [[p1b-frozen-ppg-fm-embeddings-clear-the-sensorfm-bar-on-nurse-stress-free-living-and-wesad-lab]]

## Key numbers
- `ci95_hi`: 6.275
- `ci95_lo`: 2.154
- `gate_bar_bpm`: 0.416
- `head_better_frac`: 0.655
- `head_corr`: 0.858
- `head_hr_raw_mae`: 4.338
- `head_twin_hr_raw_mae`: 5.334
- `hr_peak_head`: 15.339
- `hr_peak_llm`: 16.165
- `hr_peak_trivial`: 22.049
- `llm_corr`: 0.485
- `llm_hr_raw_mae`: 8.547
- `llm_scale`: 0.759
- `n_heldout`: 58
- `n_train`: 159
- `paired_delta_bpm`: 4.209
- `ratio_llm_over_head`: 1.97
- `ridge_meanpool_hr_raw_mae`: 5.924
- `trivial_hr_raw_mae`: 8.544

## Artifacts
- `logs/p3/paired_llm_vs_head.json`
- `logs/p3/emission_pulse_ppg_heldout.json`
- `logs/p3/emission_pulse_ppg_heldout_summary.json`
- `logs/p3_ppgfm_heads.json`
- `outputs/p3_ppgfm_tokens/heldout_predictions_trained.json`
- `outputs/adapters/p3_pulse_ppg_c`
- `logs/p3/phase_c_pulse_ppg.log`
- `scratch/p3_ppgfm_tokens.py`
- `scratch/run_p3_ppgfm.sh`
- `configs/training.yaml#lora_proxy_12ch_ppgfm`

## Do not retry
- Do NOT re-run the LLM arm with a random-twin tower expecting to learn anything: the trained-tower LLM already reads hr_raw at the always-median constant (8.547 vs 8.544); a twin cannot read lower than chance and the head's twin (5.334) already prices the tower.
- Do NOT compare this 58-row hr_raw MAE (8.5 / 4.3) to the 128-row v41 numbers (2.843 / 0.812): different pool (PPG-carrying windows of six corpora, 31 of 58 from nurse_stress), different tokens, and hr_raw here must be ESTIMATED from a waveform rather than read off a channel that contains it.
- Do NOT read the LLM's hr_peak parity with the head (+0.83, CI crossing zero) as the LLM 'matching' on peak: both arms sit 6-7 bpm above the trivial 22.05 on a 58-row pool where the seed band is unmeasured.
- Do NOT run a P3 narrator rung (g3) on this tower until a head worth narrating exists: 4.34 bpm off a waveform is real skill but the product path already has 0.81 off the 12-channel tokens.

## Notes
The committed prediction (LLM >= 1.5x head) held: 1.97x. Both consumers got the identical frozen Pulse-PPG minute tokens and trained their own adapter on the identical 159 windows; the LLM path (512->192 Linear -> fusion -> projector -> 96 soft tokens -> MedGemma+LoRA, 2600 steps) ended at the always-median constant with scale 0.76, while a ~2M attention head on the same tokens reads 4.34 vs trivial 8.54 (twin 5.33). Per dataset the head wins everywhere except WESAD (3.9 vs 3.2, n=5): case 12.1 vs 1.1, cogwear 14.6 vs 4.0, exam 10.0 vs 4.5, nurse 7.6 vs 5.2. Between-sample std control: head 10.7, LLM 8.4 (true 11.0) -- the LLM is not mode-collapsed (18 distinct values, top-1 0.21); it is miscalibrated and wrong. Advisory gates g2 (state clause) and g3 (narrator fidelity) were not run: no state head exists on this tower to pair against, and the narrator's fidelity was measured on exp-n/n3 and does not depend on the tower. Instrument fixes on record: the scorer's slot regex accepted '107..' and crashed at summary -- now unparseable, counted against the arm; Phase C hung at exit in the W&B upload -- WANDB_MODE=offline. Recipe caveats: no Phase A/B for this tower (projector trained inside Phase C), geometry PL60/ST30 inherited from v41's phase_b config, hr_augment forced off (it would decorrelate the caption target from store-derived tokens). One seed.

#medgemma #cross-modal #readout #control #optihealth-eiv #matched-baseline
