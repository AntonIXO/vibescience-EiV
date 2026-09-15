---
created_at: '2026-09-01T08:48:13+00:00'
former_ids: []
id: the-next-step-is-a-valence-clause-in-label-clauses
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:48:13+00:00'
topic_tags: []
---

## Claim
The next label-utilisation step is to add a valence clause to _label_clauses(), the caption builder that carried v36labels' six label channels -- valence being the largest channel that survives the shared-axis check (1,085 windows over six declared-range sources).

## Method
Cross-referenced each valence source dataset against the dataset_filter of both live profiles (phase_a_12ch_v36labels and lora_proxy_12ch_wl_band04), counted reachable windows per phase, and expressed the Phase A reach as a fraction of that roster's total windows. No GPU.

## Evidence
THE CHANNEL IS FINE; THE PHASE WAS WRONG, AND WRITING IT WHERE INTENDED WOULD HAVE MEASURED NOTHING.

Measured on the two live profiles (scratch/_valence_phase_reach.py):

  dataset      valence windows   on Phase A roster   on Phase C roster
  dapper                  548        YES (w 2.0)            no
  popane_s7               213        YES (w 4.0)            no
  popane_s5               199        YES (w 4.0)            no
  popane_s6               140        YES (w 4.0)            no
  popane_s3               137        YES (w 4.0)            no
  k_emocon                 31        YES (w 7.0)            no
  case                     30        YES (w 7.0)            no

  aligned valence windows reachable in PHASE A : 1,085
  aligned valence windows reachable in PHASE C : 0

The Phase C profile lora_proxy_12ch_wl_band04 draws from exactly six datasets -- lifesnaps, pmdata, optihealth_supabase, big_ideas_glycemic, mmash, dreamt -- and NOT ONE of valence's seven sources is among them. A valence clause added to the Phase C path would fire on zero windows: the arm would run its full ~40 minutes, cost the GPU, produce a clean no-change result, and mean nothing. A null that arises from an empty intersection is indistinguishable, in the report, from a null that arises from the mechanism failing.

WHY THE MISTAKE WAS AVAILABLE. _label_clauses() lives in SensorTextPairDataset (the Phase A/B pair dataset), while the Phase C dataset is InstructionDataset -- the same class boundary that produced a misleading determinism result on eiv_train_label_marginal_max earlier today, where the wrong class returned a perfectly consistent all-ones weight vector. Two different near-misses in one session on the same boundary.

WHERE IT ACTUALLY BELONGS: Phase A, whose roster carries all seven sources at weights 2.0-7.0. Reach is 1,085 of 9,428 roster windows = 11.5%. That is a real fraction -- for comparison, v36labels' existing label clauses reached 46.8% of its draw, and its measured effect was to fix JSON termination outright (81/128 -> 128/128).

CONSTRAINT ON THE ARM: the effect would land on the ENCODER, not on the Phase C emission, so it must be gated on geometry (eff_rank >= 7.0, the campaign's standing g0) and then carried through B and C to be scored on deployment. It is not a one-stage change, and its cost is a full A->B->C chain rather than the ~40-minute Phase C arm the original plan implied.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
