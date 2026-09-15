---
created_at: '2026-09-01T20:03:57+00:00'
former_ids: []
id: that-the-scorer-s-split-heldout-flag-makes-the-d88faa
problem_id: the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T20:03:57+00:00'
topic_tags: []
---

## Claim
That the scorer's `--split heldout` flag makes the 1,485-row pool a SUBJECT-HELD-OUT generalisation measurement, and therefore that the incumbent's 3-seed pooled 2.969 bpm (2.646 / 3.000 / 3.262) is "the first honest deployment number in the project" and "not in-sample". IT IS NOT. `--split heldout` filters the EVAL side only; no stage of the incumbent chain ever honoured the split, so 125 of the 128 rows in every one of those captures belong to subjects the model trained on. ALSO ELIMINATED: the framing carried in the vault and in NEXT.md that this defect is specific to `optihealth_supabase` -- supabase is merely the case where it was noticed. It is a property of 90 of 96 profiles and it applies to the whole held-out pool.

## Method
scratch/t0_heldout_leak_audit.py, CPU, no checkpoint loaded. (1) Read the `split` key of the three profiles in the incumbent's chain from configs/training.yaml. (2) Reproduce each profile's OWN admission over data/canonical/sample_metadata.json -- dataset_filter, then min_coverage, then min_hr_coverage on its declared hr_coverage_denominator -- to get the exact set of `dataset::subject_id` keys that stage could draw batches from. (3) Take the 128 rows the incumbent was ACTUALLY scored on (their `idx` is stored per-record in logs/emission_v28affect01*_heldout_dense.json), resolve each idx through data/eval_v3_1min/sample_metadata.json to its subject key, and intersect with each stage's roster. This measures the overlap on the rows that produced the number, rather than inferring it from the config.

## Evidence
=== THE SPLIT KEY IS ABSENT AT EVERY STAGE OF THE INCUMBENT CHAIN ===
  phase_a_12ch_v28affect01    split=None   9,428 windows / 1,582 subjects
  phase_b_12ch_v25bigbatch    split=None   9,428 windows / 1,582 subjects
  lora_proxy_12ch_wl_band04   split=None   7,592 windows /   226 subjects
`split` is a Phase C parameter defaulting to None = "the whole bundle"
(pipeline.py:3809-3811; cli_train.py:819 `effective_split = profile_values.get("split")`).
Exactly 6 of 96 profiles set it, all of them lora_proxy_12ch_{chat,json,jsonfit}_s4x
smoke/seed profiles. None of the incumbent's three.

=== THE DECLARED SPLIT IS LARGELY INSIDE THE TRAINING ROSTER ===
split_subjects.json: train 1,015 / val 134 / test 143 -> heldout 277 subjects.
  Phase A/B roster contains 241 of those 277 held-out subjects (87.0%).
  Phase C roster contains 48 of 226 of its own subjects marked val/test (21.2%).

=== AND ON THE ROWS THAT ACTUALLY PRODUCED THE NUMBER ===
All three incumbent seeds were scored on the SAME 128-row draw (rng seed 0 is fixed
in the scorer), 27 distinct subjects:
  rows whose subject was in the Phase A/B roster : 125/128 (97.7%)
  rows whose subject was in the Phase C roster   : 125/128 (97.7%)
Identical for v28affect01 s42, s43 and s44.
  by dataset: lifesnaps 71, pmdata 47, big_ideas_glycemic 5, dryad_ifh_affect 3, mmash 2

=== THE ONLY GENUINELY UNSEEN ROWS ARE 3 ===
`dryad_ifh_affect` is the one dataset present among held-out subjects and ABSENT
from the training roster (it is not in band04's 6-dataset dataset_filter). Those
3 rows are the entire honest-generalisation content of a 128-row capture -- and
they are also the cross-DEVICE slice Prajod & Andre 2022 (arXiv:2210.06225) says
is the regime where deep models fall behind feature models. n=3 supports nothing.

=== CONSEQUENCE FOR EVERY GATE WRITTEN AGAINST 2.969 ===
2.969 is an in-sample number at the subject level. It is not a floor for
generalisation and must not be cited as one. Relative comparisons between arms
that share this defect (every arm to date) remain valid, because the leak is
identical across them: the same 128 rows, the same rosters, the same draw.
Absolute deployment claims do not survive.

## Links
Rules out for problem [[the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all]].
