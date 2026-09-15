---
artifacts:
- logs/t1_paired_llm_vs_head.json
- logs/emission_t1llm_splithonest_heldout.json
- logs/emission_t1llm_splithonest_heldout_summary.json
- logs/t1llm_phase_c.log
- outputs/adapters/t1llm_splithonest_c/
- scratch/run_t1_llm_arm.sh
- scratch/t1_pair_llm_vs_head.py
closed: true
config_note: 'ARM A0 -- the matched LLM consumer -- plus the PAIRED contrast against
  the head arms of the parent experiment. Unit eiv-t1llm, driver scratch/run_t1_llm_arm.sh,
  1h53m wall / 49m30s CPU (Phase C 00:46:33-01:25:10, then scoring).


  MATCHED ON EVERY AXIS THAT WAS CONTROLLABLE: same frozen tower (Phase C off outputs/checkpoints/v28affect01/phase_b,
  which band04 freezes -- freeze_encoder: True, and it sets neither train_projector
  nor train_log_scale), same band04 roster and coverage gates, same 2600 steps, seed
  42. The one deliberate change is `split: train`, injected via a temporary profile
  config and verified two ways: a blocking pre-flight SPLIT-LIVE gate (windows split=None
  7592 -> split=train 5910, delta 1682) and the adapter''s own manifest read back
  after training (`split: train`, `n_train_rows: 5889`). configs/training.yaml untouched.


  SCORED with scratch/emission_diversity_audit.py --split heldout --n 128 --stop-on-brace
  on the 1,485-row val+test pool, i.e. the identical instrument and pool the head
  arms used. PAIRING via scratch/t1_pair_llm_vs_head.py on the emission capture''s
  own per-row `idx` against outputs/t1_tokens/heldout_predictions_trained.json --
  128/128 rows matched, 0 unparseable, 0 outside the head''s pool, 27 subjects.


  THE ONE CONFOUND THE DESIGN CANNOT REMOVE, stated up front: the LLM optimises token
  cross-entropy over a JSON string while the head regresses a number under smooth-L1.
  That is part of what is being ablated rather than a nuisance -- E28 established
  `lm_head` cannot output a scalar, so numeric emission as tokens is intrinsic to
  choosing an LLM here.'
cost:
  gpu_hours: 0.83
  wall_hours: 1.89
crash_reason: ''
created_at: '2026-09-01T22:33:06+00:00'
diagnostics_measured:
- after: 1.0469
  before: 4.4292
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
- after: 1.0178
  before: 0.9939
  diagnostic_id: eiv_gen_between_sample_std
do_not_retry:
- Do NOT tune the Phase C LoRA recipe to close a 3.4 bpm hr_raw gap. The `w/o LLM`
  control (ridge mean-pool on the same tokens, 1.119 bpm) is within 0.07 bpm of the
  attention head, so the gap is not about consumer capacity or hyperparameters --
  it is the LLM path itself. 96 profiles of Phase C tuning already exist; a 97th does
  not address this.
- Do NOT compare any future arm against the incumbent's 2.969 bpm. The identical recipe
  trained split-honest reads 4.429 on the same pool, so 2.969 embeds ~1.46 bpm of
  subject memorisation. Use the split-honest A0 number, or re-run the comparator split-honest.
- 'Do NOT read this as ''the encoder is good and only the LLM is bad''. The parent
  experiment measured a RANDOM-INIT tower of identical geometry at 1.511 bpm against
  a 6.881 trivial baseline: Phase A/B training buys 0.390 bpm, ~7% of the skill. The
  head''s 3.4 bpm advantage over the LLM is ~9x everything representation learning
  contributes here.'
- Do NOT conclude from this that the LLM should be deleted from the product. This
  measures ONE slot -- hr_raw, the masked mean of an input channel. It says nothing
  about narration, the state clause, or any output that is not a number already present
  in the prefix. The honest reading is that numeric slots do not need the LLM, not
  that the product does not.
- Do NOT quote 1.047 or 4.429 as deployment figures. Phase A and Phase B still ran
  split=None, so the frozen tower saw these subjects even though both consumers did
  not.
external_run: ''
former_ids: []
gate_results:
- evidence: "PASS, and on the paired rows it is no longer a question of 'reaching
    the class'.\n\nON THE 128 IDENTICAL ROWS, 27 subjects, both consumers split-honest,
    trivial recomputed on those rows:\n  arm                     MAE    trivial   skill
    \   corr   scale\n  llm_medgemma_lora      4.429    6.876   +2.447   0.820   0.99\n
    \ attn_head              1.047    6.876   +5.829   0.987   1.02\n  ridge_meanpool
    \        1.119    6.876   +5.757   0.983   1.02\n\nPAIRED PER-ROW DIFFERENCE (LLM
    MAE minus head MAE), n=128:\n  mean +3.382 bpm, bootstrap CI95 [+2.857, +3.935],
    head better on 89.8% of rows.\nThe interval is entirely above zero. The prediction
    was that the head would land within 4.5 bpm; it lands at 1.047 and the 4B LoRA'd
    LLM lands at 4.429 -- so the LLM is not merely unnecessary on this interface,
    it is COSTING 3.4 bpm relative to a ridge regression on its own input.\n\nWHY
    THIS IS A PAIRED TEST AND NOT TWO DRAWS: the emission capture stores `idx` per
    row, so the head's cached predictions were matched row by row -- 128/128, 0 unparseable,
    0 outside the pool. Draw variance, which produced this project's 2.42 bpm hr_peak
    seed band, is removed from the contrast entirely."
  gate_id: g1-head-reaches-the-llm-accuracy-class
  passed: true
- evidence: "PASS for BOTH arms, which is what makes the MAE comparison legitimate
    rather than an artefact.\n  llm_medgemma_lora  std_ratio 0.994   corr 0.820\n
    \ attn_head          std_ratio 1.018   corr 0.987\n  ridge_meanpool     std_ratio
    1.019   corr 0.983\nAll three inside [0.85, 1.35]. Neither arm is winning by collapsing
    toward the mean -- the LLM's calibration is in fact near-perfect at 0.994 while
    its correlation is 0.820 against the head's 0.987. It is emitting the right SPREAD
    and the wrong VALUES, which is a different failure from the one v36labels (1.59,
    2.08) and v31detached (2.00) had.\n\nhr_peak, not gated, recorded because it is
    the project's open axis: the split-honest LLM reads skill -0.381 (MAE 18.360 vs
    trivial 17.979, corr 0.380, scale 0.787). Negative again, as on every LLM seed
    ever measured."
  gate_id: g2-calibration-in-band
  passed: true
- evidence: 'PASS -- carried from the parent experiment exp-t1-tan-ablation-heads-20260901,
    where it was measured, and unchanged by this arm because the tower is the same
    frozen one. Head on the TRAINED tower 1.121 bpm vs the same head on a RANDOM-INIT
    tower of identical geometry 1.511 bpm (ridge 1.161 vs 2.135). exp-055''s failure
    mode -- every trained cell matched or beaten by an untrained twin -- does not
    recur.


    WORTH RESTATING HERE BECAUSE IT BOUNDS THE HEADLINE: the random tower already
    reaches 1.511 bpm against a 6.881 trivial baseline, so Phase A/B training buys
    0.390 bpm, about 7% of the skill. The head''s advantage over the LLM (3.4 bpm)
    is nearly 9x larger than everything Phase A/B representation learning contributes.'
  gate_id: g3-must-beat-its-own-random-tower
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr
id: exp-t1-a0-matched-llm-paired-20260902
interpretation:
  at: '2026-09-01T22:34:15+00:00'
  failure_mode: null
  lesson: 'On the numeric slot, the frozen LLM is not merely unnecessary — it is lossy.
    Matched on tower, roster, split, steps and rows, a ~2M-parameter attention head
    beats 4B MedGemma+LoRA by 3.382 bpm (CI95 [+2.857, +3.935], better on 89.8% of
    128 paired rows), and a plain ridge on the mean of the same tokens is within 0.07
    bpm of the head — so the gap is the LLM path, not consumer capacity. The LLM''s
    calibration is near-perfect (0.994) while its correlation is 0.820: it emits the
    right spread with the wrong values, which is what a lossy formatter looks like.
    Two things this does NOT license: deleting the LLM from the product (this measures
    one slot — a number already linearly present in the prefix — and says nothing
    about narration or the state clause), and crediting the encoder (a random-init
    tower reaches 1.511 bpm, so Phase A/B buys 0.390, ~9× less than the head''s advantage
    over the LLM). It also measured the leak''s asymmetry directly: the same recipe
    reads 2.969 leaked and 4.429 split-honest, so 1.46 bpm of the incumbent''s headline
    was subject memorisation.'
  transfers_to:
  - v41-pl60-state-chain-rebased-and-split-honest
  - the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr
key_numbers:
  frac_rows_head_better: 0.8984
  head_hr_raw_corr_paired: 0.9868
  head_hr_raw_mae_paired: 1.0469
  head_hr_raw_scale_paired: 1.0178
  head_hr_raw_skill_paired: 5.8292
  leaked_incumbent_mae_for_contrast: 2.969
  llm_hr_peak_corr: 0.38
  llm_hr_peak_scale: 0.7872
  llm_hr_peak_skill: -0.3805
  llm_hr_raw_corr: 0.8204
  llm_hr_raw_mae: 4.4292
  llm_hr_raw_scale: 0.9939
  llm_hr_raw_skill: 2.447
  llm_n_train_rows: 5889.0
  n_paired: 128.0
  n_subjects: 27.0
  paired_ci95_high: 3.9352
  paired_ci95_low: 2.8572
  paired_llm_minus_head_mean_bpm: 3.3822
  ridge_hr_raw_mae_paired: 1.1193
  trivial_mae_paired: 6.8762
observed_effects:
- delta: -3.3823
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: down
- delta: 0.0239
  diagnostic_id: eiv_gen_between_sample_std
  direction: up
parent_experiment_id: exp-t1-tan-ablation-heads-20260901
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
Branches from [[exp-t1-tan-ablation-heads-20260901]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.

## Measurements
- [[eiv_splithonest_heldout_hr_raw_mae_bpm]]: before 4.4292 → after 1.0469  (Δ -3.3823, down ✓ predicted down)
- [[eiv_gen_between_sample_std]]: before 0.9939 → after 1.0178  (Δ +0.0239, up ✗ predicted none)

## Gates
- `g1-head-reaches-the-llm-accuracy-class`: PASS — PASS, and on the paired rows it is no longer a question of 'reaching the class'.

ON THE 128 IDENTICAL ROWS, 27 subjects, both consumers split-honest, trivial recomputed on those rows:
  arm                     MAE    trivial   skill    corr   scale
  llm_medgemma_lora      4.429    6.876   +2.447   0.820   0.99
  attn_head              1.047    6.876   +5.829   0.987   1.02
  ridge_meanpool         1.119    6.876   +5.757   0.983   1.02

PAIRED PER-ROW DIFFERENCE (LLM MAE minus head MAE), n=128:
  mean +3.382 bpm, bootstrap CI95 [+2.857, +3.935], head better on 89.8% of rows.
The interval is entirely above zero. The prediction was that the head would land within 4.5 bpm; it lands at 1.047 and the 4B LoRA'd LLM lands at 4.429 -- so the LLM is not merely unnecessary on this interface, it is COSTING 3.4 bpm relative to a ridge regression on its own input.

WHY THIS IS A PAIRED TEST AND NOT TWO DRAWS: the emission capture stores `idx` per row, so the head's cached predictions were matched row by row -- 128/128, 0 unparseable, 0 outside the pool. Draw variance, which produced this project's 2.42 bpm hr_peak seed band, is removed from the contrast entirely.
- `g2-calibration-in-band`: PASS — PASS for BOTH arms, which is what makes the MAE comparison legitimate rather than an artefact.
  llm_medgemma_lora  std_ratio 0.994   corr 0.820
  attn_head          std_ratio 1.018   corr 0.987
  ridge_meanpool     std_ratio 1.019   corr 0.983
All three inside [0.85, 1.35]. Neither arm is winning by collapsing toward the mean -- the LLM's calibration is in fact near-perfect at 0.994 while its correlation is 0.820 against the head's 0.987. It is emitting the right SPREAD and the wrong VALUES, which is a different failure from the one v36labels (1.59, 2.08) and v31detached (2.00) had.

hr_peak, not gated, recorded because it is the project's open axis: the split-honest LLM reads skill -0.381 (MAE 18.360 vs trivial 17.979, corr 0.380, scale 0.787). Negative again, as on every LLM seed ever measured.
- `g3-must-beat-its-own-random-tower`: PASS — PASS -- carried from the parent experiment exp-t1-tan-ablation-heads-20260901, where it was measured, and unchanged by this arm because the tower is the same frozen one. Head on the TRAINED tower 1.121 bpm vs the same head on a RANDOM-INIT tower of identical geometry 1.511 bpm (ridge 1.161 vs 2.135). exp-055's failure mode -- every trained cell matched or beaten by an untrained twin -- does not recur.

WORTH RESTATING HERE BECAUSE IT BOUNDS THE HEADLINE: the random tower already reaches 1.511 bpm against a 6.881 trivial baseline, so Phase A/B training buys 0.390 bpm, about 7% of the skill. The head's advantage over the LLM (3.4 bpm) is nearly 9x larger than everything Phase A/B representation learning contributes.

**Verdict:** supports

## Interpretation
On the numeric slot, the frozen LLM is not merely unnecessary — it is lossy. Matched on tower, roster, split, steps and rows, a ~2M-parameter attention head beats 4B MedGemma+LoRA by 3.382 bpm (CI95 [+2.857, +3.935], better on 89.8% of 128 paired rows), and a plain ridge on the mean of the same tokens is within 0.07 bpm of the head — so the gap is the LLM path, not consumer capacity. The LLM's calibration is near-perfect (0.994) while its correlation is 0.820: it emits the right spread with the wrong values, which is what a lossy formatter looks like. Two things this does NOT license: deleting the LLM from the product (this measures one slot — a number already linearly present in the prefix — and says nothing about narration or the state clause), and crediting the encoder (a random-init tower reaches 1.511 bpm, so Phase A/B buys 0.390, ~9× less than the head's advantage over the LLM). It also measured the leak's asymmetry directly: the same recipe reads 2.969 leaked and 4.429 split-honest, so 1.46 bpm of the incumbent's headline was subject memorisation.

Transfers to: [[v41-pl60-state-chain-rebased-and-split-honest]], [[the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr]]

## Key numbers
- `frac_rows_head_better`: 0.8984
- `head_hr_raw_corr_paired`: 0.9868
- `head_hr_raw_mae_paired`: 1.0469
- `head_hr_raw_scale_paired`: 1.0178
- `head_hr_raw_skill_paired`: 5.8292
- `leaked_incumbent_mae_for_contrast`: 2.969
- `llm_hr_peak_corr`: 0.38
- `llm_hr_peak_scale`: 0.7872
- `llm_hr_peak_skill`: -0.3805
- `llm_hr_raw_corr`: 0.8204
- `llm_hr_raw_mae`: 4.4292
- `llm_hr_raw_scale`: 0.9939
- `llm_hr_raw_skill`: 2.447
- `llm_n_train_rows`: 5889
- `n_paired`: 128
- `n_subjects`: 27
- `paired_ci95_high`: 3.9352
- `paired_ci95_low`: 2.8572
- `paired_llm_minus_head_mean_bpm`: 3.3822
- `ridge_hr_raw_mae_paired`: 1.1193
- `trivial_mae_paired`: 6.8762

## Artifacts
- `logs/t1_paired_llm_vs_head.json`
- `logs/emission_t1llm_splithonest_heldout.json`
- `logs/emission_t1llm_splithonest_heldout_summary.json`
- `logs/t1llm_phase_c.log`
- `outputs/adapters/t1llm_splithonest_c/`
- `scratch/run_t1_llm_arm.sh`
- `scratch/t1_pair_llm_vs_head.py`

## Do not retry
- Do NOT tune the Phase C LoRA recipe to close a 3.4 bpm hr_raw gap. The `w/o LLM` control (ridge mean-pool on the same tokens, 1.119 bpm) is within 0.07 bpm of the attention head, so the gap is not about consumer capacity or hyperparameters -- it is the LLM path itself. 96 profiles of Phase C tuning already exist; a 97th does not address this.
- Do NOT compare any future arm against the incumbent's 2.969 bpm. The identical recipe trained split-honest reads 4.429 on the same pool, so 2.969 embeds ~1.46 bpm of subject memorisation. Use the split-honest A0 number, or re-run the comparator split-honest.
- Do NOT read this as 'the encoder is good and only the LLM is bad'. The parent experiment measured a RANDOM-INIT tower of identical geometry at 1.511 bpm against a 6.881 trivial baseline: Phase A/B training buys 0.390 bpm, ~7% of the skill. The head's 3.4 bpm advantage over the LLM is ~9x everything representation learning contributes here.
- Do NOT conclude from this that the LLM should be deleted from the product. This measures ONE slot -- hr_raw, the masked mean of an input channel. It says nothing about narration, the state clause, or any output that is not a number already present in the prefix. The honest reading is that numeric slots do not need the LLM, not that the product does not.
- Do NOT quote 1.047 or 4.429 as deployment figures. Phase A and Phase B still ran split=None, so the frozen tower saw these subjects even though both consumers did not.

## Notes
TRACK 1 IS ANSWERED. On the same 96 soft tokens, the same frozen tower, the same roster, the same split and the same 128 rows, a ~2M-parameter attention head beats a 4B-parameter LoRA'd MedGemma by 3.4 bpm, and the interval does not touch zero.

  arm                     MAE    trivial   skill    corr   scale
  llm_medgemma_lora      4.429    6.876   +2.447   0.820   0.99
  attn_head              1.047    6.876   +5.829   0.987   1.02
  ridge_meanpool         1.119    6.876   +5.757   0.983   1.02

  PAIRED per-row (LLM MAE - head MAE), n=128, 27 subjects:
  mean +3.382 bpm   CI95 [+2.857, +3.935]   head better on 89.8% of rows

=== THIS IS THE STRONG FORM OF TAN'S RESULT ===
Tan et al. (arXiv:2406.16964) found that removing the LLM "does not degrade forecasting performance -- in most cases, the results even improve". Here it improves by a factor of four. And the `w/o LLM` ablation (ridge mean-pool, 1.119) is within 0.07 bpm of the `LLM2Attn` one (1.047), so the finding does not depend on the replacement being clever: a linear model on the mean of the LLM's own input prefix beats the LLM at reporting a number carried in that prefix.

=== THE FAILURE IS IN THE VALUES, NOT THE SPREAD ===
The LLM's calibration is essentially perfect (std_ratio 0.994, better than the head's 1.018) while its correlation is 0.820 against 0.987. It is emitting a distribution of the right width and putting the wrong number in each row. That is a different failure from v36labels (1.59/2.08) or v31detached (2.00), which over-expressed range; it is what "formatter" looks like when the formatter is also lossy.

=== AND THE LEAK WAS HELPING THE LLM, AS PREDICTED ===
The leaked incumbent read 2.969 bpm on this pool; the SAME recipe trained split-honest reads 4.429. That 1.46 bpm is what 22% fewer rows plus the removal of 125/128 in-sample subjects costs the LLM arm. It is a direct measurement of the `eval-leakage-audit` corollary that a leak is not symmetric: the high-capacity arm was the one exploiting it. Any future comparison against 2.969 is comparing against a partly-memorised number.

=== hr_peak, THE PROJECT'S OPEN AXIS ===
The split-honest LLM reads skill -0.381 (MAE 18.360 vs trivial 17.979, corr 0.380). Negative again, on every LLM seed ever measured. The head reads +4.840 on the same axis but at std_ratio 0.636, out of band -- so the head is directionally better there and neither arm is promotable on hr_peak.

=== SCOPE, STATED RATHER THAN BURIED ===
1. OBJECTIVE MISMATCH IS INTRINSIC, NOT A NUISANCE. The LLM optimises token cross-entropy over JSON; the head regresses a number. `lm_head` cannot emit a scalar (E28), so token emission is part of what "use an LLM here" means, and its cost belongs in the LLM's column. A reader who wants that separated should run the E28-style readout on the LLM's final hidden state -- see the follow-up below.
2. hr_raw IS A READOUT, NOT A FORECAST. It is the masked mean of an input channel over the same window. The finding is not that 1.05 bpm is hard; it is that the LLM cannot report a number linearly present in its own input.
3. THE TOWER IS STILL LEAKED. Phase A/B ran split=None, so 4.429 and 1.047 are both optimistic in absolute terms. The COMPARISON is clean; neither number is a deployment figure.
4. ONE SEED EACH. No split-honest repeat-run sigma exists yet. The paired CI is over rows, not over seeds -- it says this LLM lost to this head on these rows, not that every seed would.

=== THE FOLLOW-UP THAT WOULD LOCALISE THE LOSS ===
A linear readout on the LLM's FINAL HIDDEN STATE (E28's design) on this same pool. If it matches the projector-token readout, the information survives the forward pass and is lost at the emission step -- a decoding problem. If it is already worse, the LLM's forward pass discards it -- a conditioning problem. Those imply different fixes and this experiment does not separate them.

#optihealth-eiv #matched-baseline
