---
created_at: '2026-09-01T09:14:23+00:00'
former_ids: []
id: v40roster-changes-only-the-dataset-filter
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:14:23+00:00'
topic_tags: []
---

## Claim
v40roster is a clean one-variable arm: it changes dataset_filter alone, so any deployment effect is attributable to the wider Phase C training population.

## Method
Read the dataset_filter recorded inside outputs/checkpoints/v28affect01/phase_a/phase_config.json (the checkpoint's own written config, not the profile that names it) and set-differenced it against the v40 Phase C roster; then weighted the uncovered dataset's window count by its sampler weight to size the confound. No GPU.

## Evidence
ALMOST -- THERE IS A SECOND, SMALL CONFOUND THAT THE ONE-VARIABLE CHECK CANNOT SEE, AND IT IS RECORDED HERE BEFORE THE ARM RUNS RATHER THAN DISCOVERED IN THE RESULT.

v40roster runs Phase C off the incumbent's frozen Phase B checkpoint while widening the Phase C roster. verify_one_variable.py compares two PROFILES and correctly reports diff=['dataset_filter']. It cannot see that the frozen encoder has its own training roster, so it cannot ask whether Phase C will draw corpora the encoder never encoded.

CHECKED AGAINST THE CHECKPOINT'S OWN WRITTEN CONFIG (scratch/_v40_encoder_coverage_check.py), not the profile it names:
  outputs/checkpoints/v28affect01/phase_a/phase_config.json -> 19 datasets
  v40 Phase C roster                                        -> 20 datasets
  datasets Phase C would draw that Phase A NEVER SAW        -> 1: nurse_stress

So 18 of the 19 added corpora WERE seen by the encoder -- dapper, case, k_emocon, all five POPANE studies, wesad and the rest are all in the Phase A roster. The confound is confined to nurse_stress alone.

HOW BIG IS IT: 102 of 9,530 roster windows = 1.07% by count, and 0.77% of the sampler mass at weight 1.0 (lifesnaps 36.2%, pmdata 16.4%, dapper 13.0% dominate). A 0.77% share of an emitter's training mass cannot plausibly move dense hr_raw MAE by the k=2 band of 1.513 bpm.

WHY nurse_stress IS THERE AT ALL, and why removing it would be worse: the v11 block keeps nurse_stress in Phase C but excludes it from Phase A/B on purpose -- its state labels are shift-schedule-carried (clock-alone AUC 0.6358) and its giant windows soften a within-window null, so the nurse-free pool is the standard for anything STATE-scored, while Phase C is json-slot supervised. Dropping it to make the encoder roster match would make v40 a 19-dataset arm compared against 20-dataset precedent, i.e. trade a 0.77% confound for a different-roster confound.

DECISION: the arm runs as queued, and any result is reported with this stated. If v40 FAILS its gates, the failure cannot be blamed on nurse_stress at 0.77% without a further arm; if it PASSES, the confound is irrelevant by construction.

METHOD NOTE: 'one variable' verified between two config files is not the same as 'one variable' in the experiment. A frozen upstream artefact carries its own data law, and the check has to reach into the checkpoint, not just the YAML.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
