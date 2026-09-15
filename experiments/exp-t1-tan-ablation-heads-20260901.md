---
artifacts:
- logs/t1_llm_ablation_heads.json
- logs/t1_extract.log
- logs/t1_train_heads.log
- scratch/t1_llm_ablation.py
- outputs/t1_tokens/
closed: true
config_note: 'Arms A1/A2/A3 only (CPU); A0, the LLM arm, is a separate GPU unit and
  its number is not in this record. Frozen tower from outputs/adapters/v28affect01_c/sensor_modules
  (encoder+channel_fusion+cross_modal_projector, sensor_slots 96, patch_pool_mode
  avg, projector_format rmsnorm_scale_v3, PL120/ST60, d_model 192, d_llm 2560, input_dim
  24 with_presence_v1) -- byte-identical to what the incumbent''s LLM was fed, because
  band04 freezes all three. Tokens extracted PRE positional codebook (the codebook
  is built and trained inside Phase C, so it is the consumer''s). Train roster: data/canonical,
  band04 filters + split=train, 5,910 windows / 178 subjects. Eval: data/eval_v3_1min,
  split-only val+test, 1,485 rows / 32 subjects (lifesnaps 906, pmdata 514, big_ideas
  31, dryad_ifh_affect 28, mmash 6). AttnReadout d=256, 4 learnable queries, 4 heads,
  3000 steps, batch 64, AdamW lr 1e-3 cosine, smooth-L1 on targets standardised on
  TRAIN only. A2 = mean-pool + RidgeCV over 12 alphas. A3 = identical head on a torch.manual_seed(0)
  random-init tower of the same geometry. Scored with emission_diversity_audit.py''s
  own summarise().'
cost:
  gpu_hours: 0.0
  wall_hours: 1.3
crash_reason: ''
created_at: '2026-09-01T20:32:32+00:00'
diagnostics_measured:
- after: 1.121
  before: 6.8815
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
- after: 1.0285
  before: 1.0
  diagnostic_id: eiv_gen_between_sample_std
do_not_retry:
- Do NOT quote 1.121 bpm as a deployment or generalisation figure. The CONSUMER honoured
  split=train, but Phase A and Phase B did not (split=None in all 96 profiles bar
  6), so the frozen tower was fit on these very subjects. The comparison between consumers
  is honest; the absolute number is not.
- 'Do NOT compare 1.121 against the incumbent''s 2.969 as though it were a matched
  contrast. They differ on three axes at once: training split (5,910 split-honest
  rows vs 7,592 leaked), scored rows (all 1,485 vs a 128-row draw of which 125 are
  in-sample), and objective (direct numeric regression vs token cross-entropy on JSON).
  The matched arm is eiv-t1llm and its paired readout is scratch/t1_pair_llm_vs_head.py.'
- 'Do NOT build a bigger readout head on this interface expecting gains. Cross-attention
  beats mean-pool + RidgeCV by 0.040 bpm on 1,485 rows. This is E28''s finding (Ridge
  beat GBT and MLP on frozen LM states, CI95 entirely above zero) reproducing one
  layer upstream, and the same conclusion follows: the cap here is information, not
  readout capacity.'
- Do NOT cite the head's hr_peak skill of +4.840 as beating the trivial baseline in
  a promotable sense. Its std_ratio is 0.636, outside the [0.85, 1.35] band, and this
  project's own rule is to read calibration before MAE. It is the best hr_peak number
  on record and it is directional only.
- Do NOT assume Phase A/B representation learning is what makes the soft tokens readable.
  A random-init tower of identical geometry reaches 1.511 bpm against a 6.881 trivial
  baseline; training buys 0.390 bpm, about 7% of the skill. Any future arm justified
  by 'the encoder learns to represent HR' must beat its own random-init twin, not
  the trivial baseline.
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS, and by a margin I did not predict. Bar was <=4.5 bpm; measured
    hr_raw MAE 1.121 bpm on all 1,485 val+test rows, trivial always-predict-median
    6.881 on those same rows, skill +5.760 bpm, corr 0.986. That is 4.0x under the
    bar and 8x the 0.5 bpm mde.


    The SHALLOW FLOOR essentially ties it: mean-pool + RidgeCV reads 1.161 bpm (corr
    0.985). Attention buys 0.040 bpm, which is inside any reasonable noise for this
    pool. Per the multimodal-ai skill''s lecture-4 control and E28''s own finding
    that Ridge beat every non-linear readout on frozen LM states, this makes the conclusion
    STRONGER, not weaker: not only is the LLM unnecessary to reach this accuracy,
    so is the attention.


    hr_peak, not gated but recorded because it is the axis the project has never moved:
    attention head MAE 12.873 vs trivial 17.713, skill +4.840, corr 0.669. Every LLM
    seed ever measured has NEGATIVE hr_peak skill (incumbent -0.895 / -0.617 / -1.731).
    Its calibration is however OUT of band at std_ratio 0.636, so the hr_peak figure
    is not comparable in the way the hr_raw figure is, and is reported as directional
    only.'
  gate_id: g1-head-reaches-the-llm-accuracy-class
  passed: true
- evidence: 'PASS on the gated quantity. hr_raw emitted/true sd ratio 1.029 for the
    attention head and 1.042 for ridge -- both comfortably inside [0.85, 1.35], and
    both nearer 1.0 than any incumbent seed (1.08 / 0.96 / 1.04). Prediction distribution
    is not collapsed: 1,484 distinct values over 1,485 rows, top1_mass 0.0013, so
    the low MAE is not a regression-to-the-mean artefact.


    REPORTED AGAINST MYSELF: hr_peak is OUT of band at std_ratio 0.636 (ridge 0.621;
    random tower 0.553/0.375). The head under-expresses the excursion''s spread even
    while beating the trivial baseline on it, which is the same direction the whole
    project''s hr_peak problem points. The hr_peak skill of +4.840 must therefore
    be read as directional, not as a calibrated result.'
  gate_id: g2-calibration-in-band
  passed: true
- evidence: 'PASS, but narrowly enough that the margin is itself the finding. Same
    head, same rows, same seed, on a RANDOM-INIT tower of identical geometry: hr_raw
    MAE 1.511 vs the trained tower''s 1.121 -> trained minus random = -0.390 bpm.
    Ridge shows the same ordering and a wider gap: 1.161 trained vs 2.135 random (-0.974).
    So the encoder''s learned weights DO carry something a random projection does
    not, and exp-055''s failure mode (every trained cell matched or beaten by an untrained
    twin) does not recur here.


    WHAT THE MARGIN MEANS, and it is uncomfortable: the RANDOM tower already reaches
    1.511 bpm against a 6.881 trivial baseline, i.e. skill +5.370 of the trained tower''s
    +5.760. Training the encoder buys 0.390 bpm -- about 7% of the available skill.
    93% of the hr_raw readout is recoverable from a random projection of the same
    input through the same architecture. This gate passes on its stated terms and
    simultaneously bounds how much of the result belongs to learning at all.'
  gate_id: g3-must-beat-its-own-random-tower
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr
id: exp-t1-tan-ablation-heads-20260901
interpretation:
  at: '2026-09-01T20:49:50+00:00'
  failure_mode: null
  lesson: 'The 96 soft tokens already contain hr_raw at ~1.1 bpm MAE (corr 0.986,
    scale 1.03) on subjects the consumer never saw — and mean-pool + RidgeCV gets
    within 0.040 bpm of a cross-attention block, so on this interface even the attention
    is decoration. Whatever the LLM is doing for hr_raw, it is not extracting information:
    the information is linearly present in its own input. Two caveats bound the claim
    — a RANDOM-INIT tower of identical geometry already reaches 1.511 bpm, so Phase
    A/B training buys only 0.390 bpm (~7% of the skill), and Phase A/B still ran split=None
    so the tower saw these subjects. Any future arm justified by "the encoder learns
    to represent HR" must beat its own random-init twin rather than the trivial baseline.
    The matched LLM arm (eiv-t1llm, split-honest Phase C on the same frozen tower)
    supplies the paired contrast; until it lands, this establishes the floor, not
    the comparison.'
  transfers_to:
  - a-held-out-scored-chain-combining-every-079038
key_numbers:
  attention_minus_ridge_hr_raw: -0.04
  attn_head_hr_peak_mae: 12.8729
  attn_head_hr_peak_scale: 0.6363
  attn_head_hr_peak_skill: 4.8397
  attn_head_hr_raw_corr: 0.9864
  attn_head_hr_raw_mae: 1.121
  attn_head_hr_raw_scale: 1.0285
  attn_head_hr_raw_skill: 5.7604
  incumbent_llm_hr_raw_mae_LEAKED: 2.969
  n_heldout_rows: 1485.0
  n_train_rows: 5889.0
  random_tower_attn_hr_raw_mae: 1.5113
  random_tower_ridge_hr_raw_mae: 2.1353
  ridge_meanpool_hr_raw_mae: 1.1611
  ridge_meanpool_hr_raw_scale: 1.0415
  trained_minus_random_attn: -0.3903
  trained_minus_random_ridge: -0.9742
  trivial_hr_peak_mae: 17.7126
  trivial_mae_on_pool: 6.8815
observed_effects:
- delta: -5.7605
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: down
- delta: 0.0285
  diagnostic_id: eiv_gen_between_sample_std
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_between_sample_std: false
    eiv_splithonest_heldout_hr_raw_mae_bpm: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: supports
---

## Summary
Tests hypothesis [[the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.

## Measurements
- [[eiv_splithonest_heldout_hr_raw_mae_bpm]]: before 6.8815 → after 1.121  (Δ -5.7605, down ✓ predicted down)
- [[eiv_gen_between_sample_std]]: before 1.0 → after 1.0285  (Δ +0.0285, up ✗ predicted none)

## Gates
- `g1-head-reaches-the-llm-accuracy-class`: PASS — PASS, and by a margin I did not predict. Bar was <=4.5 bpm; measured hr_raw MAE 1.121 bpm on all 1,485 val+test rows, trivial always-predict-median 6.881 on those same rows, skill +5.760 bpm, corr 0.986. That is 4.0x under the bar and 8x the 0.5 bpm mde.

The SHALLOW FLOOR essentially ties it: mean-pool + RidgeCV reads 1.161 bpm (corr 0.985). Attention buys 0.040 bpm, which is inside any reasonable noise for this pool. Per the multimodal-ai skill's lecture-4 control and E28's own finding that Ridge beat every non-linear readout on frozen LM states, this makes the conclusion STRONGER, not weaker: not only is the LLM unnecessary to reach this accuracy, so is the attention.

hr_peak, not gated but recorded because it is the axis the project has never moved: attention head MAE 12.873 vs trivial 17.713, skill +4.840, corr 0.669. Every LLM seed ever measured has NEGATIVE hr_peak skill (incumbent -0.895 / -0.617 / -1.731). Its calibration is however OUT of band at std_ratio 0.636, so the hr_peak figure is not comparable in the way the hr_raw figure is, and is reported as directional only.
- `g2-calibration-in-band`: PASS — PASS on the gated quantity. hr_raw emitted/true sd ratio 1.029 for the attention head and 1.042 for ridge -- both comfortably inside [0.85, 1.35], and both nearer 1.0 than any incumbent seed (1.08 / 0.96 / 1.04). Prediction distribution is not collapsed: 1,484 distinct values over 1,485 rows, top1_mass 0.0013, so the low MAE is not a regression-to-the-mean artefact.

REPORTED AGAINST MYSELF: hr_peak is OUT of band at std_ratio 0.636 (ridge 0.621; random tower 0.553/0.375). The head under-expresses the excursion's spread even while beating the trivial baseline on it, which is the same direction the whole project's hr_peak problem points. The hr_peak skill of +4.840 must therefore be read as directional, not as a calibrated result.
- `g3-must-beat-its-own-random-tower`: PASS — PASS, but narrowly enough that the margin is itself the finding. Same head, same rows, same seed, on a RANDOM-INIT tower of identical geometry: hr_raw MAE 1.511 vs the trained tower's 1.121 -> trained minus random = -0.390 bpm. Ridge shows the same ordering and a wider gap: 1.161 trained vs 2.135 random (-0.974). So the encoder's learned weights DO carry something a random projection does not, and exp-055's failure mode (every trained cell matched or beaten by an untrained twin) does not recur here.

WHAT THE MARGIN MEANS, and it is uncomfortable: the RANDOM tower already reaches 1.511 bpm against a 6.881 trivial baseline, i.e. skill +5.370 of the trained tower's +5.760. Training the encoder buys 0.390 bpm -- about 7% of the available skill. 93% of the hr_raw readout is recoverable from a random projection of the same input through the same architecture. This gate passes on its stated terms and simultaneously bounds how much of the result belongs to learning at all.

**Verdict:** supports

## Interpretation
The 96 soft tokens already contain hr_raw at ~1.1 bpm MAE (corr 0.986, scale 1.03) on subjects the consumer never saw — and mean-pool + RidgeCV gets within 0.040 bpm of a cross-attention block, so on this interface even the attention is decoration. Whatever the LLM is doing for hr_raw, it is not extracting information: the information is linearly present in its own input. Two caveats bound the claim — a RANDOM-INIT tower of identical geometry already reaches 1.511 bpm, so Phase A/B training buys only 0.390 bpm (~7% of the skill), and Phase A/B still ran split=None so the tower saw these subjects. Any future arm justified by "the encoder learns to represent HR" must beat its own random-init twin rather than the trivial baseline. The matched LLM arm (eiv-t1llm, split-honest Phase C on the same frozen tower) supplies the paired contrast; until it lands, this establishes the floor, not the comparison.

Transfers to: [[a-held-out-scored-chain-combining-every-079038]]

## Key numbers
- `attention_minus_ridge_hr_raw`: -0.04
- `attn_head_hr_peak_mae`: 12.8729
- `attn_head_hr_peak_scale`: 0.6363
- `attn_head_hr_peak_skill`: 4.8397
- `attn_head_hr_raw_corr`: 0.9864
- `attn_head_hr_raw_mae`: 1.121
- `attn_head_hr_raw_scale`: 1.0285
- `attn_head_hr_raw_skill`: 5.7604
- `incumbent_llm_hr_raw_mae_LEAKED`: 2.969
- `n_heldout_rows`: 1485
- `n_train_rows`: 5889
- `random_tower_attn_hr_raw_mae`: 1.5113
- `random_tower_ridge_hr_raw_mae`: 2.1353
- `ridge_meanpool_hr_raw_mae`: 1.1611
- `ridge_meanpool_hr_raw_scale`: 1.0415
- `trained_minus_random_attn`: -0.3903
- `trained_minus_random_ridge`: -0.9742
- `trivial_hr_peak_mae`: 17.7126
- `trivial_mae_on_pool`: 6.8815

## Artifacts
- `logs/t1_llm_ablation_heads.json`
- `logs/t1_extract.log`
- `logs/t1_train_heads.log`
- `scratch/t1_llm_ablation.py`
- `outputs/t1_tokens/`

## Do not retry
- Do NOT quote 1.121 bpm as a deployment or generalisation figure. The CONSUMER honoured split=train, but Phase A and Phase B did not (split=None in all 96 profiles bar 6), so the frozen tower was fit on these very subjects. The comparison between consumers is honest; the absolute number is not.
- Do NOT compare 1.121 against the incumbent's 2.969 as though it were a matched contrast. They differ on three axes at once: training split (5,910 split-honest rows vs 7,592 leaked), scored rows (all 1,485 vs a 128-row draw of which 125 are in-sample), and objective (direct numeric regression vs token cross-entropy on JSON). The matched arm is eiv-t1llm and its paired readout is scratch/t1_pair_llm_vs_head.py.
- Do NOT build a bigger readout head on this interface expecting gains. Cross-attention beats mean-pool + RidgeCV by 0.040 bpm on 1,485 rows. This is E28's finding (Ridge beat GBT and MLP on frozen LM states, CI95 entirely above zero) reproducing one layer upstream, and the same conclusion follows: the cap here is information, not readout capacity.
- Do NOT cite the head's hr_peak skill of +4.840 as beating the trivial baseline in a promotable sense. Its std_ratio is 0.636, outside the [0.85, 1.35] band, and this project's own rule is to read calibration before MAE. It is the best hr_peak number on record and it is directional only.
- Do NOT assume Phase A/B representation learning is what makes the soft tokens readable. A random-init tower of identical geometry reaches 1.511 bpm against a 6.881 trivial baseline; training buys 0.390 bpm, about 7% of the skill. Any future arm justified by 'the encoder learns to represent HR' must beat its own random-init twin, not the trivial baseline.

## Notes
HEAD ARMS ONLY (A1/A2/A3). The matched LLM arm A0 is running as unit eiv-t1llm and lands in a CHILD experiment; the paired row-by-row contrast is not in this record and no sentence here asserts one.

=== THE NUMBER ===
On all 1,485 val+test rows, consumer trained under split=train:
  arm                       hr_raw MAE   trivial   skill    corr   scale
  attention head (trained)      1.121     6.881   +5.760   0.986   1.03
  ridge mean-pool (trained)     1.161     6.881   +5.720   0.985   1.04
  attention head (RANDOM)       1.511     6.881   +5.370   0.973   1.03
  ridge mean-pool (RANDOM)      2.135     6.881   +4.746   0.951   0.92
  --- for orientation only, NOT a matched comparison ---
  incumbent LLM 3-seed pooled   2.969     6.876   +3.876   0.910   1.08   <- and 125/128 of its rows are in-sample

=== THREE THINGS THIS SAYS, IN DESCENDING ORDER OF CONFIDENCE ===

1. THE 96 SOFT TOKENS ALREADY CONTAIN hr_raw TO ~1.1 bpm. That is not a claim about the LLM yet; it is a claim about the interface. corr 0.986, scale 1.03, 1,484 distinct predictions over 1,485 rows, on subjects the CONSUMER never saw. The information the LLM is asked to emit is sitting in its input, linearly readable.

2. ATTENTION IS DECORATION ON THIS INTERFACE. Mean-pool + RidgeCV is 0.040 bpm behind a cross-attention block. This is the multimodal-ai skill's lecture-4 control and E28's own result (Ridge beat every non-linear readout on frozen LM hidden states, CI95 of the difference entirely above zero) reproducing one layer earlier. It strengthens rather than weakens the ablation: the question "does the LLM add anything" is being asked against a floor that a linear model already reaches.

3. AND MOST OF IT IS NOT LEARNED. A RANDOM-INIT tower of identical geometry reaches 1.511 bpm (skill +5.370 of the trained tower's +5.760). Phase A and Phase B buy 0.390 bpm -- roughly 7% of the available skill. The gate passes, so exp-055's "trained is matched by untrained" failure does NOT recur here; but the margin bounds how much of this result belongs to representation learning at all, and the answer is: not much. Ridge separates the towers more clearly (1.161 vs 2.135, -0.974), which suggests the attention head is partly compensating for a worse representation.

=== hr_peak, REPORTED AGAINST MYSELF ===
The head reads hr_peak at MAE 12.873 vs trivial 17.713, skill +4.840, where every LLM seed on record is NEGATIVE (-0.895 / -0.617 / -1.731) and the project's standing summary is "no arm beats always-predict-median on hr_peak". But its calibration is OUT of band at std_ratio 0.636, so by this project's own rule -- read scale before MAE -- that skill is DIRECTIONAL ONLY and is not a calibrated result. It is the strongest hr_peak number in the project and it is not yet a promotable one.

=== WHAT IS STILL LEAKED, STATED BEFORE ANYONE QUOTES 1.121 ===
Phase A and Phase B ran with split=None on every arm to date, so the frozen tower was fit on these subjects even though the head was not. The consumer-side comparison is honest; the absolute 1.121 bpm is optimistic and is NOT a deployment figure. A fully honest number needs A and B retrained under split=train, which is a separate chain.

=== TASK SHAPE, so nobody over-reads the MAE ===
hr_raw is the masked MEAN of input channel 0 over the same window the tokens were computed from. This is a READOUT task, not forecasting -- consistent with the project's own "hr_mean reads back at R2 0.971-0.99 on frozen embeddings". The finding is not that 1.1 bpm is hard; it is that the LLM is emitting 2.97 bpm (in-sample, at that) for a quantity a ridge regression recovers at 1.16 bpm from the LLM's own input.

#optihealth-eiv #matched-baseline
