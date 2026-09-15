---
direction: higher_better
id: eiv_heldout_hr_r2
name: eiv_heldout_hr_r2
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags: []
unit: r2
---

## Description
Subject-balanced R² of a grouped-CV Ridge probe predicting raw mean heart rate (bpm) on subjects excluded from representation training. This is the primary diagnostic.

*** 2026-09-01 ADMISSIBILITY AUDIT -- 5 OF 13 RECORDED VALUES ARE IMPOSSIBLE ***
R2 is bounded above by 1.0 by construction. Classified every value recorded under this id (scratch/_hr_r2_admissibility.py):

  IMPOSSIBLE (5):
    exp-1min-end-to-end-20260808                    902.32 -> 2822.00
    exp-1min-allowlist-20260808                     859.47 ->  902.32
    exp-1min-control-20260808                         0.00 ->  902.32
    exp-eiv-multipositive-spectral-entropy-pilot      5.2109
    exp-eiv-multipositive-dcl-pilot-v2                5.2051
  SUSPICIOUS (2): exp-014 / exp-015 at 0.9997 -- an R2 of 0.9997 on HELD-OUT SUBJECTS is not credible for this task and should be treated as unverified until its provenance is checked.
  ADMISSIBLE (6): the 2026-07-27 objective family, range -0.2031 .. 0.1122.

UNLIKE THE OTHER UNIT DEFECTS FOUND THE SAME DAY, NO CONVENTION RESCUES THESE. duplicate_recall5 at 35.0 is fine as a percent; peak_vram_gib at 113.7 is fine as MiB; effective_rank at 0.21 is fine as a normalised ratio. There is no reading under which R2 equals 2822 -- not a percentage (that would be 282200%), not a ratio, not a rescaling. The 902.32/2822 values are plausibly an RMSE or a loss in bpm² filed under the wrong id; that is a guess and is recorded as one.

CONSEQUENCE: this diagnostic's 18 evaluations cannot be pooled or compared as a group. The admissible subset spans -0.20 to 0.11, i.e. the honest reading is that held-out HR R2 has never been shown to be high on this project, and any claim resting on 0.9997 or above is unsupported.

A seed floor is being measured on four same-recipe ab_ctrl Phase-A checkpoints via the shipped `eiv probe` path. When it lands it will be valid FOR THAT PATH and must be registered with that scope -- it does not license comparisons against the historical values above.

*Unit:* `r2` — *higher_better*
