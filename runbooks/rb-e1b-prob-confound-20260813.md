---
created_at: '2026-08-13T13:56:27+00:00'
former_ids: []
gates:
- g1-dense-recovered-within-1bpm-of-2.89-all-seeds-blocking
- g2-supabase-median-below-4.5-blocking
- g3-floor-0-and-sub45-below-0.10-band-reported
hypothesis_id: e1b-prob08-not-band-law-owns-the-dense-cost
id: rb-e1b-prob-confound-20260813
notes: []
priority: 2
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
status: done
topic_tags:
- deployment-distribution
- optihealth
updated_at: '2026-08-13T19:53:35+00:00'
---

## Prompt
E1b — ISOLATE THE hr_augment_prob CONFOUND IN THE E1 WIN AND RECOVER DENSE ACCURACY.

CONTEXT (self-contained). Repo /root/optiHealth-EiV. E1 (exp-e1-band-mass-20260813, git 14e2ee4, closed 'supports') reshaped the hr_raw augment target law (aug_min_mean_bpm=45 + band_prob=0.5 + hr_augment_prob 0.4->0.8) and produced supabase MAE 4.16/2.86/3.89 (median 3.89, beats trivial 4.02; v7noatom family was 11.54/15.78/9.49), seed band 6.29->1.31, floor and P(emit<45) = 0.000. BUT its preregistered dense gate FAILED: dense-128 MAE 4.49/5.65/4.46 vs bar 3.89 (v7noatom 2.89), regression concentrated in upper terciles with POSITIVE bias on truth>80 rows (+1.15/+9.57/+3.98 bpm) — overestimation, not band-shrinkage. Two confounds ride along with the band mixture: hr_augment_prob doubled (0.4->0.8) and natural-target share halved (0.6->0.2).

ARM: profiles lora_proxy_12ch_wl_band04{,_s43,_s44}: byte-identical to lora_proxy_12ch_wl_band{...} except hr_augment_prob: 0.4 (keep band_prob 0.5, min_mean 45, same seeds 42/43/44). This restores natural-target share to 0.6 while keeping the reshaped augment law. CPU supply check first (scratch/e1_supply_gate.py with prob=0.4): expect P(band)~0.37, P(<45)~0.001 — the 0.5 supply bar does NOT apply here; the question is whether LOWER augment intensity retains the supabase win while recovering dense.

Train 3 seeds Phase C from outputs/checkpoints/v5c/phase_b (~7.5h total), score frozen supabase 200 + dense 128 (scratch/emission_diversity_audit.py), then scratch/score_e1_band.py (edit BASE to v9band family {4.16,2.86,3.89} for band-vs-band).

DECISION RULE: if dense recovers (within 1 bpm of 2.89 all seeds) AND supabase median stays < 4.5, the E1b recipe becomes the baseline candidate and prob-0.8 is ruled out as necessary; if dense does NOT recover, the dense cost belongs to the band mixture itself — accept the trade-off explicitly or attack band_prob (e.g. 0.3); if supabase regresses above 4.5, prob-0.8 was load-bearing and the E1 recipe stands with its measured dense cost.

## Preconditions
rb-deployment-band-mass-20260813 DONE. No code change needed — profiles only.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Run as [[e1b-prob08-not-band-law-owns-the-dense-cost]].
Intended gates: `g1-dense-recovered-within-1bpm-of-2.89-all-seeds-blocking`, `g2-supabase-median-below-4.5-blocking`, `g3-floor-0-and-sub45-below-0.10-band-reported`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#deployment-distribution #optihealth
