---
created_at: '2026-09-01T09:54:09+00:00'
former_ids: []
id: the-v36-v28-seed-family-is-confounded-like-w6
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:54:09+00:00'
topic_tags: []
---

## Claim
The v36labels vs v28affect01 seed family is confounded the same way w6 was: its four cells carry four different git_sha values, so its verdict will be unattributable too.

## Method
Read all four cells' manifests for git_sha, diffed each consecutive hop restricted to src/, identified what each hop actually changes, verified the new parameter's default resolves in the FAMILY'S OWN PROFILE rather than only in the function signature, and read the live process's environ to confirm the flag is unset.

## Evidence
CHECKED CELL BY CELL, AND THE FAMILY IS CLEAN. This is the first time today the confound check came back negative, which is why it is worth recording -- the guard is not a machine for finding faults.

The four shas are real and they differ:
  v28affect01_c      c3f482746ef3  08-31 06:10  pool=avg
  v36labels_c        398bc4638be6  09-01 00:34  pool=avg
  v36labels_c_s43    cb8d7d7e1bbe  09-01 08:20  pool=avg
  v36labels_c_s44    a01cad4a1ef7  09-01 09:17  pool=avg

But a sha difference is only a confound if the intervening code REACHES the run. Read each hop:

  c3f4827 -> 398bc46 (+303 datasets.py): the v36labels treatment itself -- the label clauses. This is the INTENDED variable, not a confound.
  398bc46 -> cb8d7d7 (+43 datasets.py, +31 gemma_runtime.py): adds the sampler_coverage_strength parameter, defaulting to 0.0, plus an ORDER -> order rename. Verified the default HOLDS IN THE PROFILE, not merely in the signature: lora_proxy_12ch_wl_band04 and its _s43/_s44 variants do not set the key at all, so it resolves to 0.0. Only profile line 4180 sets 1.0, and that is v37cov, a different arm.
  cb8d7d7 -> a01cad4 (+72 datasets.py): my own scaled label clauses, behind EIV_SCALED_LABEL_CLAUSES and default-off.

Then confirmed on the LIVE process rather than by reading code: pid 345076 runs --profile lora_proxy_12ch_wl_band04_s43 and its environ carries NO EIV_ variables at all, so the scaled-clause flag is off in the run that is producing the control cell right now.

SO THE FAMILY'S FOUR CELLS DIFFER BY: the label-clause treatment (intended) and two dormant additions that are provably not active. The verdict will be attributable.

WHY THIS MATTERED ENOUGH TO CHECK MID-RUN: three of the four cells are already spent. Discovering a confound after the fourth would have cost the entire family, and the check cost four minutes while the GPU kept working. The w6 lesson was that the check must precede the claim; the cheaper version is that it should precede the RUN CLOSING, which is still possible while a cell is in flight.

NOTE THE PATTERN THAT MADE THIS SAFE: both dormant changes ship default-off, which is the campaign's standing rule for touching a recipe while an arm is in flight. That rule is what turns four different shas into a clean comparison instead of a dead family.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
