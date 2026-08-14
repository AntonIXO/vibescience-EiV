---
created_at: '2026-08-11T21:19:03+00:00'
former_ids:
- with-phase-c-now-seeded-0b5aff2-three-runs-of-the-identical-control-configuratio
gates:
- blocking: true
  description: All three seeds complete with RC=0, produce a pinned hr_buckets manifest
    block with IDENTICAL cut points, and are scored on the same eval bundle and the
    same 48-sample draw. Without identical cut points the accuracies are not comparable
    and the band is meaningless.
  diagnostic_id: null
  id: gate-seed-band-measured
  mde: null
- blocking: true
  description: Report the measured band against the legacy 0.066 seed-spread figure
    that prior arms were judged with. If the measured band EXCEEDS it, the 0.066 figure
    must be retired in writing and every prior single-seed Phase-C conclusion re-labelled
    as underpowered rather than refuted.
  diagnostic_id: null
  id: gate-band-vs-legacy-spread
  mde: null
- blocking: true
  description: The best-performing seed must NOT be promoted as a result. This experiment
    measures a distribution; selecting its maximum and reporting it as an arm is exactly
    the error that produced the v4w best-run claim.
  diagnostic_id: null
  id: gate-no-post-hoc-best-seed
  mde: null
id: with-phase-c-now-seeded-0b5aff2-three-fae38a
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_phase_c_seed_band_range_bpm
  direction: up
  magnitude_note: PRIMARY. Range across seeds 42/43/44 of emitted hr_peak skill (bpm)
    predicted > 1.0, on the basis that the accidental v4w-vs-v5c replication of this
    same config already measured 2.673 bpm. Reported with all three per-seed values,
    not just the range.
- diagnostic_id: eiv_gen_bucket_acc_over_majority
  direction: up
  magnitude_note: Range across the three seeds of p4 hr_acc predicted > 0.03, the
    bar the v4c/v4w allowlist gate was scored against. Each seed's hr_acc must be
    quoted with its own denominator and the majority baseline on the identical scored
    subset.
- diagnostic_id: eiv_gen_between_sample_std
  direction: none
  magnitude_note: 'Distinct emitted hr_peak values and top-1 mass recorded per seed
    as the collapse companion. No directional prediction: this is here so a seed that
    silently mode-collapses (as v5d did, 6 distinct values with 130.0 on 11/16) is
    visible rather than hidden behind an unchanged hr_acc.'
problem_id: phase-c-ran-unseeded-for-the-project-s-27557c
problem_tags:
- optihealth-eiv
- run-to-run-variance
- matched-baseline
status: confirmed
supersedes: null
topic_tags:
- medgemma
- cross-modal
- baseline
- control
---

## Statement
With Phase C now seeded (0b5aff2), three runs of the IDENTICAL control configuration lora_proxy_12ch_wl differing ONLY in the training seed (42/43/44), all off the SAME v5c Phase-B checkpoint and scored on the SAME eval draw, will span a band LARGER than the effect sizes this project has previously treated as findings. Concretely: the seed-to-seed range of emitted hr_peak skill will exceed 1.0 bpm, and the seed-to-seed range of p4 hr_acc will exceed 0.03 -- the bar the v4c/v4w allowlist gate was scored against. If so, every single-seed Phase-C A/B in this project's history is underpowered by construction, and the minimum detectable effect for all future arms must be re-derived from this band rather than from the legacy 0.066 figure.

## Rationale
This is not a guess: v4w and v5c are an ACCIDENTAL replication of exactly this experiment. Byte-identical manifests (only final_loss differs, 1.2183 vs 0.5821), measured 2.673 bpm apart on emitted hr_peak skill and 0.712 bpm apart on hr_raw skill over 48 identical eval samples. That two-point spread already exceeds the 1.0 bpm threshold predicted here. This experiment converts an accident into a deliberate three-point measurement under the new seeding, which is the only way to state a minimum detectable effect. The alternative -- continuing single-seed arms -- cannot separate an intervention from a draw, which is precisely how H-1 (dryad, 0.0078 hr_acc) and the allowlist gate (+0.0249) were scored. PREDICTION MAY FAIL HONESTLY: if the band turns out SMALL (<1.0 bpm), that is a genuinely good outcome -- it would mean the v4w/v5c gap was driven by something other than seed (e.g. the corpus swap mid-run at 16:36:05 that landed inside v4c's Phase A window) and single-seed arms are more trustworthy than feared. Either result is decision-relevant.

## Plan
Three seeds of lora_proxy_12ch_wl (42 = the existing v5c adapter, reused unchanged; 43 and 44 via new profiles lora_proxy_12ch_wl_s43/_s44), all initialised from outputs/checkpoints/v5c/phase_b so the ONLY variable is the Phase-C seed. Driver: scratch/run_seed_band.sh. Per seed: Phase C (2600 steps) -> probe p2,p3,p4,p6 at 512 samples against the rebuilt data/eval_v3_1min -> scratch/emission_diversity_audit.py at n=48 with the identical rng seed for the sample draw. Report per-seed hr_peak skill, hr_raw skill, distinct emitted values, top-1 mass, hr_acc with denominator and majority baseline, then the range of each.

## Links
Tests problem [[phase-c-ran-unseeded-for-the-project-s-27557c]].
Predicts [[eiv_phase_c_seed_band_range_bpm]] (up).
Predicts [[eiv_gen_bucket_acc_over_majority]] (up).
Predicts [[eiv_gen_between_sample_std]] (none).
Gate `gate-seed-band-measured` (blocking): All three seeds complete with RC=0, produce a pinned hr_buckets manifest block with IDENTICAL cut points, and are scored on the same eval bundle and the same 48-sample draw. Without identical cut points the accuracies are not comparable and the band is meaningless.
Gate `gate-band-vs-legacy-spread` (blocking): Report the measured band against the legacy 0.066 seed-spread figure that prior arms were judged with. If the measured band EXCEEDS it, the 0.066 figure must be retired in writing and every prior single-seed Phase-C conclusion re-labelled as underpowered rather than refuted.
Gate `gate-no-post-hoc-best-seed` (blocking): The best-performing seed must NOT be promoted as a result. This experiment measures a distribution; selecting its maximum and reporting it as an arm is exactly the error that produced the v4w best-run claim.

#medgemma #cross-modal #baseline #control #optihealth-eiv #run-to-run-variance #matched-baseline
