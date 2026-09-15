---
created_at: '2026-08-05T10:28:41+00:00'
former_ids:
- canonical-mask-marks-degenerate-zero-channels-as-present-20260805
id: canonical-m-mask-marks-all-zero-889451
paper_refs: []
problem_tags:
- optihealth-eiv
- data-contract-violation
- masking
status: parked
target: null
title: Canonical M_mask marks all-zero channels as PRESENT, producing garbage training
  targets
topic_tags: []
updated_at: '2026-09-01T07:43:59+00:00'
updates:
- at: '2026-09-01T07:43:59+00:00'
  text: 'PARKED 2026-09-01: BOTH DEFECTS ARE GONE FROM THE CURRENT BUNDLE. Measured
    directly off X_canonical + M_mask, no re-merge required to check (scratch/_mask_contract_audit.py).
    This matters because the record''s own conclusion was that fixing it ''requires
    a re-merge, which invalidates cached canonical artifacts'' -- and re-merges run
    on Anton''s other machine, so leaving this open schedules work that is already
    done.


    CLAIM 1 -- absence encoded as present-and-zero, hr_peak=0 targets, ''72.0% of
    ALL bradycardic training targets were this garbage''. Now measured on the [14041,1440,12]
    bundle across all 21 datasets: the mean fraction of PRESENT hr cells holding a
    sub-30 bpm value is 0.0000 in every single dataset, and 0 of 14,010 windows have
    their entire present HR below 30. dryad_ifh_affect, the corpus the record measured
    at zero-fraction mean 0.687 with 0/1500 days zero-free, now reads 0.0000 on 4,506
    windows. The degenerate population is empty.


    CLAIM 2 -- the four sleep_prob_* channels sum to ~0.031 per step instead of ~1.0,
    ''roughly 30x too small everywhere''. Measured over 265,089 fully-observed sleep
    steps: per-step sum mean 1.0000, p5 1.0000, p50 1.0000, p95 1.0000, and 100.00%
    of steps are within 0.05 of 1.0. They are exact probabilities now.


    WHAT THIS DOES NOT SAY. It does not say the code-level contract was repaired:
    schema.py''s FeatureChannel.min_valid / max_valid may still be declared-and-unread,
    and if so a future source that encodes non-wear as literal 0 could reintroduce
    the same defect silently. What is established is that no such source is in the
    CURRENT bundle. The train-time guard the record mentions (min_valid_hr_bpm=30.0
    in InstructionDataset, pinned by tests/test_hr_bucket_marginal.py::TestZeroHrContamination)
    remains as the backstop and should not be removed on the strength of this measurement.


    CONSEQUENCE FOR ANALYSES THAT CITED IT. The record notes the contamination inflated
    evaluation -- a degenerate model scored eiv_gen_peak_corr +0.680 pooled against
    +0.086 on full-coverage rows, because the bimodal 0-vs-120 bpm truth made the
    correlation easy. Any number quoted from a pre-fix bundle carries that inflation;
    numbers measured on this bundle do not, which is one more reason the current campaign''s
    captures are not comparable with pre-rebuild ones.


    PARKED rather than resolved: a data-contract report with no hypothesis attached,
    and ''resolved'' requires a confirmed one. scratch/_mask_contract_audit.py is
    the one-command re-test if a new source is ever merged.'
---

## Description
=== ROOT CAUSE LOCALISED IN CODE 2026-08-07. Unpreregistered; problem evidence, no calibration weight. ===

THE DEFECT IS A DECLARED-BUT-UNENFORCED CONTRACT, NOT A PARSING BUG.

src/data/canonical/schema.py:40 defines FeatureChannel.min_valid / max_valid, and line 49 sets
hr_mean min_valid=30, max_valid=220. GREP RESULT: those two fields are read NOWHERE outside schema.py.
The only `min_valid*` hits in src/ are `min_valid_hr_bpm` in the TRAINING path (cli_train.py:660,
pipeline.py:2596, datasets.py:1954-2059), which is a different and much coarser workaround.

MECHANISM. The merger is correct on its own terms: merger.py:507-509 and 740-742 both build the mask as
`present = ~np.isnan(values)`. NaN therefore maps to missing, exactly as intended. But 0.0 IS NOT NaN, so any
source that encodes non-wear / no-reading as a literal 0 passes straight through as a PRESENT observation of
0 bpm. The dryad processor is not at fault: _process_sensor_file uses pd.to_numeric(errors="coerce") and drops
all-NaN rows, which is correct handling. The zeros are in the source and nothing maps them out.

EVIDENCE THAT IT IS NON-WEAR ZERO-FILL AND NOT SENSOR NOISE (n=1500 dryad days):
  present steps/day        median 96   (the mask claims a full day)
  NON-ZERO present/day     median 32   (real HR coverage 0.333, i.e. ~8h of wear)
  zero fraction/day        mean 0.687, median 0.667, p5 0.542, p95 0.938; 0/1500 days are zero-free
  zeros contiguous         94.0% of adjacent zero pairs -> ONE SOLID BLOCK, not scattered dropout
  non-zero values          mean 56.6, median 55.0, p99 93.0 bpm -> physiological where real
So each dryad day is roughly 8 hours of genuine recording, zero-padded to 24 hours and marked fully present.

WHY THE EXISTING WORKAROUND DOES NOT COVER IT. datasets.py drops a whole SAMPLE when its PEAK < min_valid_hr_bpm
(30). That removes fully-dead days -- the training log reports "Dropped 1933/14013" -- but it cannot touch a
PARTIALLY dead day, because a day with 69% zeros and 31% real readings still has a peak well above 30. That is
precisely dryad (68.7% zero) and lifesnaps (31.0% zero), both of which survive the drop while carrying garbage
steps into the encoder and into every target computed by averaging over "present" steps.

THE FIX, AND WHERE. Enforce the contract at mask construction, per channel, in the two places masks are built:
merger.py::_epoch_df_to_arrays (497-511) and the epoch-grid path (710-742). Replace `~np.isnan(values)` with
`~np.isnan(values) & (values >= ch.min_valid) & (values <= ch.max_valid)` where the channel declares them.
~5 lines. Requires a re-merge (CLAUDE.md).

HONEST STATEMENT OF WHAT THE FIX DOES AND DOES NOT BUY:
  * dryad: 96 present -> ~32 honest present, coverage 0.333. It becomes HONEST, not SCORABLE -- it still fails
    cov>0.9. The gain is that the encoder stops being fed ~64 fabricated zeros per day and that any
    coverage-based filter starts telling the truth about it. My earlier runbook note that this "unlocks 4423
    dryad days" for the scorable pool is WITHDRAWN; it unlocks them for honest training, not for scoring.
  * lifesnaps: removes 31.0% garbage from its 24 real points. This matters MORE than dryad because lifesnaps is
    75.6% of the Phase C training filter, and its present-step HR mean currently reads 53.4 bpm against pmdata's
    67.0 -- a bias created by averaging zeros into the daily mean, i.e. the hr_raw TARGET on three quarters of
    the training corpus is dragged low. This is a target-quality fix, not merely a masking fix.
  * pmdata: 0.72% zeros, essentially unaffected.

SEPARATE OBSERVATION, NOT A BUG: optihealth_supabase (n=285, the app's own data via
src/data/importers/optihealth_supabase.py, which has a SchemaContractError path) is the CLEANEST source in the
corpus -- median 79 present steps/day, median 0 zero-but-present, 172/285 days entirely zero-free, HR p1 39.6 /
median 53.5 / p99 117.9 -- and it is the ONLY dataset with hrv, respiratory_rate, skin_temperature AND the
sleep block all at 100% presence. It is also excluded from the Phase C dataset_filter and carries 0% labels.
It is the only cohort that could exercise the full 12-channel schema and it is currently unused.

--- ADDENDUM 2026-08-07: THE optihealth_supabase EXCLUSION IS AN ACCIDENT OF ORDERING, NOT A DECISION ---
Dates, from git:
  configs/training.yaml dataset_filter last touched   2026-06-26 (commit 8577b43)
  src/data/importers/optihealth_supabase.py ADDED     2026-07-27 (commit 2daf603)
  data/canonical re-merged (X_canonical, metadata)    2026-07-27
The Phase C allowlist is ONE MONTH OLDER than the data it excludes. Its comment in configs/training.yaml
justifies the filter entirely in terms of dryad ("...ample elevated signal without dryad's resting mass") and
never mentions supabase. There is NO quarantine note for supabase in configs/, the docs, or src/training/ --
it is a first-class supported ingestion path (`eiv data import-samurai`... i.e. `eiv data import-supabase`,
documented in README.md, with its own configs/imports/optihealth_supabase.yaml and a SchemaContractError
validation path). So it is excluded by OMISSION from an allowlist, not by any recorded judgement about it.
CAVEAT: absence of a recorded reason is not proof there was none -- this is the production app's own user data,
so a privacy or leakage rationale could exist off-record. Ask before including it.

ASYMMETRY THIS CREATES: only lora_proxy_12ch carries a dataset_filter. phase_a_5070 and phase_b_5070 have NONE
and train on the FULL corpus. So the frozen encoder/projector HAVE seen optihealth_supabase; only the Phase C
LM adaptation has not. The 12-channel representation was learned partly on this cohort while the LM was never
taught to VERBALISE it.
WHY IT MATTERS BEYOND ITS SIZE (285 days is too small to move training volume): it is the ONLY cohort with
hrv, respiratory_rate, skin_temperature and the sleep block all at 100% presence, and the strict-clean scoring
cohort has 0% of those four. Including it in Phase C is currently the only route to giving the LM real
supervision -- and any evaluation at all -- on the hrv/rr/temp slots. Cost is a one-line config change.

Discovered while root-causing the Phase C hr_peak collapse (E23). M_mask.npy marks channels as PRESENT on samples where every present value is exactly 0.0 in raw units. Measured on the live lifesnaps+pmdata training filter (N=8774) via scratch/diag_e23e_channel_contamination.py and scratch/diag_e23f_sleep_zero.py:

  hr_mean          20.9% all-zero-but-present (1830; 27.6% of lifesnaps, 0% of pmdata)
  sleep_awake      41.6%
  sleep_light      41.6%
  sleep_deep       41.6%
  sleep_rem        41.6%   (3649 samples: 3343 lifesnaps + 306 pmdata, all four jointly zero)
  dow_sin          11.8%
  hrv / respiratory_rate / skin_temperature   0.0%  (these correctly encode absence as absent)

Two distinct consequences:

1. GARBAGE TARGETS. These samples pass min_coverage and reach _build_json_slots_target, which computes hr_peak = 0 bpm and, via hr_label_bucket(0,0), the label 'bradycardic'. 72.0% of ALL bradycardic training targets were this garbage. Removing them shifts the natural label marginal from 28.95/25.18/28.78/17.10 to 10.22/31.81/36.36/21.60, so the corpus statistics several prior analyses relied on were themselves contaminated. It also inflates evaluation: the resulting bimodal true distribution (0 bpm vs ~120 bpm) let a demonstrably degenerate model score eiv_gen_peak_corr +0.680 pooled while its full-coverage-only corr was +0.086.

2. SLEEP CHANNELS ARE NOT PROBABILITIES. On the NON-degenerate 58.4%, the four sleep_prob_* channels sum to mean 0.031 per step (p5 0.010, p95 0.042) instead of ~1.0 - roughly 30x too small everywhere, not merely zero on some samples. Consistent with the persistently negative probe deep_sleep_r2 (-0.127 on 12ch_v1_lora_E22fix) and with P4 deep_sleep_acc = 1.00 being trivially satisfied by always emitting ~0%.

A train-time filter (min_valid_hr_bpm=30.0 in InstructionDataset, guarded by tests/test_hr_bucket_marginal.py::TestZeroHrContamination) now drops the HR-degenerate samples, but that is a symptom patch on one channel. The real defect is in the canonical merge/mask contract: absence is encoded as present-and-zero rather than absent, and the sleep normalisation scale looks wrong independently of that. Fixing it properly requires a re-merge, which invalidates cached canonical artifacts - hence filed as its own problem rather than patched inline.

## Updates
- **2026-09-01T07:43:59+00:00** — PARKED 2026-09-01: BOTH DEFECTS ARE GONE FROM THE CURRENT BUNDLE. Measured directly off X_canonical + M_mask, no re-merge required to check (scratch/_mask_contract_audit.py). This matters because the record's own conclusion was that fixing it 'requires a re-merge, which invalidates cached canonical artifacts' -- and re-merges run on Anton's other machine, so leaving this open schedules work that is already done.

CLAIM 1 -- absence encoded as present-and-zero, hr_peak=0 targets, '72.0% of ALL bradycardic training targets were this garbage'. Now measured on the [14041,1440,12] bundle across all 21 datasets: the mean fraction of PRESENT hr cells holding a sub-30 bpm value is 0.0000 in every single dataset, and 0 of 14,010 windows have their entire present HR below 30. dryad_ifh_affect, the corpus the record measured at zero-fraction mean 0.687 with 0/1500 days zero-free, now reads 0.0000 on 4,506 windows. The degenerate population is empty.

CLAIM 2 -- the four sleep_prob_* channels sum to ~0.031 per step instead of ~1.0, 'roughly 30x too small everywhere'. Measured over 265,089 fully-observed sleep steps: per-step sum mean 1.0000, p5 1.0000, p50 1.0000, p95 1.0000, and 100.00% of steps are within 0.05 of 1.0. They are exact probabilities now.

WHAT THIS DOES NOT SAY. It does not say the code-level contract was repaired: schema.py's FeatureChannel.min_valid / max_valid may still be declared-and-unread, and if so a future source that encodes non-wear as literal 0 could reintroduce the same defect silently. What is established is that no such source is in the CURRENT bundle. The train-time guard the record mentions (min_valid_hr_bpm=30.0 in InstructionDataset, pinned by tests/test_hr_bucket_marginal.py::TestZeroHrContamination) remains as the backstop and should not be removed on the strength of this measurement.

CONSEQUENCE FOR ANALYSES THAT CITED IT. The record notes the contamination inflated evaluation -- a degenerate model scored eiv_gen_peak_corr +0.680 pooled against +0.086 on full-coverage rows, because the bimodal 0-vs-120 bpm truth made the correlation easy. Any number quoted from a pre-fix bundle carries that inflation; numbers measured on this bundle do not, which is one more reason the current campaign's captures are not comparable with pre-rebuild ones.

PARKED rather than resolved: a data-contract report with no hypothesis attached, and 'resolved' requires a confirmed one. scratch/_mask_contract_audit.py is the one-command re-test if a new source is ever merged.

#optihealth-eiv #data-contract-violation #masking
