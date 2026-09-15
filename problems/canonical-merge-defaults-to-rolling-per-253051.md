---
created_at: '2026-08-07T16:28:03+00:00'
former_ids:
- canonical-merge-defaults-to-rolling-per-user-z-score-while-the-phase-c-target-builder-assumes-population-norm
id: canonical-merge-defaults-to-rolling-per-253051
paper_refs: []
problem_tags: []
status: parked
target: null
title: Canonical merge defaults to rolling per-user z-score while the Phase C target
  builder assumes population norm
topic_tags: []
updated_at: '2026-09-01T07:45:31+00:00'
updates:
- at: '2026-09-01T07:45:31+00:00'
  text: "PARKED 2026-09-01: THE DEFECT IS GONE, MEASURED WITH THE RECORD'S OWN NON-CIRCULAR
    TEST (scratch/_norm_contract_audit.py).\n\nThe record warns that de-normalising
    with the pinned stats and checking the values look physiological is CIRCULAR --
    z*iqr+median reproduces the pinned centre for any z-scored channel -- and supplies
    the valid test instead: DISPERSION. Under population norm an individual subject
    spans only a fraction of the unit IQR; under rolling per-user norm each subject
    is rescaled to IQR ~1. I used that test, not the circular one.\n\nMEASURED on
    the current [14041,1440,12] bundle, per-subject IQR / population IQR, subjects
    with >=200 present cells:\n  channel                       ratio    (record measured,
    defect live)\n  hr_mean (control, norm=none)   0.78     0.78\n  hrv_rmssd                      0.46
    \    1.01\n  respiratory_rate               0.33     0.97\n  skin_temperature
    \              0.77     1.02\nThe control is unchanged at 0.78, which is what
    makes the comparison meaningful -- the instrument reads the same on the channel
    neither normaliser touches. All three z-scored channels have collapsed from ~1.0
    to 0.33-0.77, i.e. individual subjects now span a fraction of the population spread.
    That is the population-norm signature.\n\nINDEPENDENT CONFIRMATION that did not
    exist when the record was written: merge_manifest.json now carries a `normalization`
    block declaring mode 'population', a stats_sha256 (24e731a6...), and a per-channel
    policy with hr_mean and hrv_rmssd both 'population' and the sleep/time block 'none'.
    So the bundle now STATES its contract rather than leaving it to be inferred, which
    is the structural fix for a declared-but-unenforced contract.\n\nCONSEQUENCE:
    the Phase C target builder's inverse raw = z*iqr+median is VALID on this bundle,
    so hrv/rr/temp values printed in captions and emitted in slots are in real physiological
    units. That matters directly for the current campaign -- v35wide added hrv/rr/temp
    dispersion clauses to the caption, and had this defect still been live those clauses
    would have been per-user z-scores wearing physiological unit labels.\n\nPARKED
    rather than resolved: a data-contract report with no hypothesis attached, and
    'resolved' requires a confirmed one. scratch/_norm_contract_audit.py is the one-command
    re-test after any future merge; run it BEFORE trusting a caption that prints hrv,
    rr or temp."
---

## Description
The canonical merger's `use_population_norm` defaults to False (merger.py:42) and the merge CLI
requires an explicit `--use-population-norm` (cli_data.py:519). When it is off, the merger instead applies
ZScoreNormalizer.normalize_rolling -- a ROLLING PER-USER z-score (merger.py:566 and 626) -- to every channel
whose schema `normalization` is not "none".

But the Phase C target builder documents the opposite contract, verbatim (datasets.py:1327):
    "``sample_tensor`` values are assumed to be population-normalized (z-scored); they are denormalised
     here using ``population_stats``"
and then executes  raw(ch) = z * iqr[ch] + median[ch]  (datasets.py:1216-1224, and the same inverse in
_build_json_slots_target). That inverse is valid ONLY if PopulationNormalizer.apply_inplace actually ran.
Nothing asserts it. This is a DECLARED-BUT-UNENFORCED CONTRACT -- the same defect class as the min_valid
mask contract (canonical-mask-marks-degenerate-zero-channels-as-present-20260805), which was declared in
schema.py and read nowhere.

MEASURED ON THE CURRENT TENSOR (data/canonical/X_canonical.npy, 2026-08-07 17:23, n=13923).
Anton confirms the merge was run WITHOUT --use-population-norm.
Affected channels are exactly those with schema normalization="z-score":
  ch1 hrv_rmssd, ch10 respiratory_rate, ch11 skin_temperature.
Unaffected (schema normalization="none", skipped by BOTH normalisers):
  ch0 hr_mean, ch2-5 sleep_prob_*, ch6-9 time encodings.

DISCRIMINATING EVIDENCE (per-subject vs population dispersion). Under population norm an individual
subject spans only a FRACTION of the unit IQR; under per-user norm each subject is individually rescaled
to IQR ~1.
  channel            population IQR   median per-subject IQR   ratio
  hr_mean (control)         22.972                   17.819    0.78
  hrv_rmssd                  1.039                    1.046    1.01
  respiratory_rate           1.125                    1.094    0.97
  skin_temperature           0.988                    1.011    1.02
Per-subject MEDIANS are also ~0 for every subject (SD 0.05-0.18). Conclusion: rolling per-user z-score.
METHOD WARNING: "de-normalise with the pinned stats and check the values look physiological" is CIRCULAR
and must not be used as evidence -- z*iqr+median reproduces the pinned centre for ANY z-scored channel.
I made that error first and withdraw it.

CONSEQUENCES.
1. Chronic/between-subject signal in hrv/rr/temp is erased -- the exact failure the PopulationNormalizer
   docstring was written to avoid ("a chronically elevated HR collapses to z~0"). Between-subject ICC now:
   hrv 0.359, respiratory_rate 0.013, skin_temperature 0.433, vs hr_mean 0.755 (untouched control).
2. The Phase C json_slots targets FABRICATE those three slots: every subject is asserted to have median
   RMSSD exactly 61.0 ms, respiratory rate exactly 15.0 /min and skin temperature exactly 33.67 C (the
   pinned May-24 medians), with only their own within-day wobble scaled by an unrelated constant.
3. The prose caption builders are wrong the same way -- _describe_hrv formats ch1 as "{rmssd:.1f} ms" and
   its `if hrv < 20` branch now fires on essentially every sample.
4. hr_raw and hr_peak are CORRECT regardless, because ch0 is in the stats file's `excluded` list so its
   pinned stats are the identity (median 0.0, iqr 1.0) and z*1+0 = raw bpm. Sleep and time slots likewise.

NOT A NEW REGRESSION. docs/dcl-vs-infonce-medgemma-cache-v2.md:51 already records "Population normalization
was explicitly disabled because the canonical merge was run without --use-population-norm", so earlier
corpora were built the same way. The mismatch is long-standing and silent.

SCOPE OF DAMAGE TO CONCLUSIONS: every Phase C result to date is on the primary hr_raw/hr_peak metrics, which
are unaffected; and the strict-clean eval population has 0% presence on hrv/rr/temp anyway (recorded in
phase-c-annotation-target-is-86pct-template-20260806). So no existing headline number is invalidated. What
is invalidated is any claim about the hrv / respiratory_rate / skin_temperature slots.

PROPOSED FIX (two parts, both small):
 (a) re-merge with --use-population-norm; decide explicitly whether to also pass --recompute-stats (the
     pinned stats are from 2026-05-24 and were fit on raw-ms hrv, i.e. a corpus that no longer exists).
 (b) enforce the contract instead of documenting it: at InstructionDataset construction, assert that the
     z-score channels are consistent with population_stats (e.g. per-subject IQR / population IQR must be
     well below 1, or persist a `normalization: population|rolling` field in population_stats.json / a
     merge manifest and refuse to build json_slots targets when it says `rolling`).

=== ADDENDUM 2026-08-07: THE BIGGER NORMALIZATION DEFECT IS ON hr_mean, NOT ON hrv/rr/temp.
Unpreregistered; problem evidence, no calibration weight. ===

Anton asked "do we need any kind of data normalization?". Measured answer: YES, and the channel that
is MISSING it is hr_mean -- the one channel declared normalization="none".

TemporalEncoder feeds [B,T,C] straight into FlexMLP = Linear(input_dim->64)->GELU->Linear with NO input
normalization (models/flexmlp.py:11-15). The only input norm, `use_layernorm`, is FALSE in every 12ch
profile (encoders/temporal.py:35 is its sole guard). A Linear at init weights all channels equally, so
first-layer pre-activation variance splits as:
                      present-steps-only     as actually fed (zero-filled)
  hr_mean                   96.07%                    99.77%
  hrv_rmssd                  1.63%                     0.06%
  respiratory_rate           1.04%                     0.01%
  skin_temperature           0.44%                     0.02%
  4x time encodings          0.68%                     0.12%
  4x sleep_prob_*            0.14%                     0.00%
hr_mean std 16.88 vs next-largest 2.20 => 7.7x in std, 59x in variance.

THE MISSING-DATA FILL COMPOUNDS IT. 45.7% of hr_mean cells are missing and filled with 0.0 RAW BPM.
Against present-step median 70.5 / sd 16.9 that fill sits at -4.18 SD -- an extreme outlier on nearly
half the tensor. Presence is ALREADY supplied separately (encoder_format_version=with_presence_v1,
input_dim = 2*12 = 24), so the swing is redundant magnitude, not information. It is what lifts hr_mean
from 96% to 99.8%.

POPULATION NORM FIXES BOTH AT ONCE: after standardisation the fill value 0.0 IS the channel median, so
"missing" becomes neutral. Verified on the channels that are already normalized -- fill 0.0 sits at
+0.00 SD for both hrv_rmssd and skin_temperature, vs -4.18 SD for raw hr_mean.

MECHANISM, STATED HONESTLY: this is CONDITIONING, not expressivity. A Linear can learn a small weight on
hr_mean. But dL/dW_c is proportional to x_c, so under one shared LR the hr_mean weights get ~8x
(present-only) to ~40x (as fed) larger gradients, from an init where the other 11 channels are already
numerically invisible.

CONSEQUENCE FOR TWO EXISTING RULE-OUTS -- flagged as a HYPOTHESIS to preregister, NOT a finding.
ro-sensor-slot-granularity-20260805 (23 slots -> 1 costs only 0.009) and ro-window-shortening-20260805
(increment exactly 0.0000) are both CONSISTENT with a prefix carrying essentially one scalar, which is
what a 96%-one-channel encoder input would produce. Both were measured under this exact input scaling,
so they are rule-outs CONDITIONAL ON IT. If normalization rebalances the input they deserve re-testing.
The causal claim is untested and the correspondence may be coincidence -- it must not be treated as a
retraction of either rule_out until measured.

CHEAP DISCRIMINATING TEST, NO RE-MERGE: run a Phase A arm with use_layernorm: true (one-line config
change). It isolates the input-scaling effect from the population-vs-rolling question.

## Updates
- **2026-09-01T07:45:31+00:00** — PARKED 2026-09-01: THE DEFECT IS GONE, MEASURED WITH THE RECORD'S OWN NON-CIRCULAR TEST (scratch/_norm_contract_audit.py).

The record warns that de-normalising with the pinned stats and checking the values look physiological is CIRCULAR -- z*iqr+median reproduces the pinned centre for any z-scored channel -- and supplies the valid test instead: DISPERSION. Under population norm an individual subject spans only a fraction of the unit IQR; under rolling per-user norm each subject is rescaled to IQR ~1. I used that test, not the circular one.

MEASURED on the current [14041,1440,12] bundle, per-subject IQR / population IQR, subjects with >=200 present cells:
  channel                       ratio    (record measured, defect live)
  hr_mean (control, norm=none)   0.78     0.78
  hrv_rmssd                      0.46     1.01
  respiratory_rate               0.33     0.97
  skin_temperature               0.77     1.02
The control is unchanged at 0.78, which is what makes the comparison meaningful -- the instrument reads the same on the channel neither normaliser touches. All three z-scored channels have collapsed from ~1.0 to 0.33-0.77, i.e. individual subjects now span a fraction of the population spread. That is the population-norm signature.

INDEPENDENT CONFIRMATION that did not exist when the record was written: merge_manifest.json now carries a `normalization` block declaring mode 'population', a stats_sha256 (24e731a6...), and a per-channel policy with hr_mean and hrv_rmssd both 'population' and the sleep/time block 'none'. So the bundle now STATES its contract rather than leaving it to be inferred, which is the structural fix for a declared-but-unenforced contract.

CONSEQUENCE: the Phase C target builder's inverse raw = z*iqr+median is VALID on this bundle, so hrv/rr/temp values printed in captions and emitted in slots are in real physiological units. That matters directly for the current campaign -- v35wide added hrv/rr/temp dispersion clauses to the caption, and had this defect still been live those clauses would have been per-user z-scores wearing physiological unit labels.

PARKED rather than resolved: a data-contract report with no hypothesis attached, and 'resolved' requires a confirmed one. scratch/_norm_contract_audit.py is the one-command re-test after any future merge; run it BEFORE trusting a caption that prints hrv, rr or temp.
