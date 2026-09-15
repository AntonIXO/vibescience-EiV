---
created_at: '2026-09-01T09:05:54+00:00'
former_ids: []
id: labels-in-all-three-phases-is-unreachable-on-the-phase-c-roster
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:05:54+00:00'
topic_tags: []
---

## Claim
'Utilise all the labels in all three phases' cannot be achieved on the current Phase C roster, because Phase C draws from 6 datasets while the affect labels live in the other 13, and the lever is the ROSTER, which is dictated by the deployment population.

## Method
Read the roster's provenance in configs/training.yaml rather than assuming it, found the v11 corpus-growth block and its three wide-roster Phase C profiles, then parameterised the ceiling script by profile and re-measured per-channel window reach on lora_proxy_12ch_v11full. No GPU.

## Evidence
REFUTED BY MY OWN NEXT MEASUREMENT, AND THE PROJECT HAD ALREADY SOLVED IT. I checked whether the 6-dataset roster is forced by deployment or merely historical. It is historical, and a 20-dataset Phase C roster already exists in configs/training.yaml.

The v11 block (training.yaml:2929-2969) states the same finding I had just derived, in advance: 'the designated baseline lora_proxy_12ch_wl_band04 sees only 7,592 of 14,092 windows and drops 48.4% of all label-epochs, including SIX channels at exactly ZERO reach (valence 0/40,893, arousal 0/6,457, protocol_condition, exam_session_code, stress_raw, stimulus_class)'. Three Phase C profiles carry the wide roster: lora_proxy_12ch_v11full, _v12conly, _v13flat, each on 20 datasets.

RE-MEASURED THE CEILING ON THAT ROSTER (scratch/_phase_c_label_ceiling.py, now profile-parameterised):

  channel            band04 roster    v11full roster   sources on the wide roster
  stress_level         1,665 (21.9%)   1,780 (18.7%)   pmdata, nurse_stress, brain_wearable
  valence                  0 ( 0.0%)   1,298 (13.6%)   dapper, popane_s7/s5/s6/s3, k_emocon, case
  arousal                  0 ( 0.0%)     609 ( 6.4%)   dapper, k_emocon, case
  valence_recalled         0 ( 0.0%)     486 ( 5.1%)   dapper
  arousal_recalled         0 ( 0.0%)     486 ( 5.1%)   dapper
  stai_state             242 ( 3.2%)     257 ( 2.7%)   lifesnaps, wesad

So the channels I reported as structurally unreachable are reachable, and -- the part that actually matters -- they arrive from MULTIPLE sources. valence gets 7 sources instead of 0, arousal 3, stress_level 3 instead of 1. My objection to a Phase C label slot was that a single-dataset slot leaks provenance through its mere PRESENCE; on the wide roster that objection dissolves for exactly the channels worth carrying.

WHERE MY REASONING WENT WRONG: I measured the ceiling on ONE profile and generalised to 'Phase C'. The roster is a per-profile choice with 43 profiles in the file, and treating the incumbent's roster as a property of the phase turned a configuration into a law of nature. The same error class as reading a noise floor measured at MAE ~12 as a property of the metric rather than of that regime.

WHAT STANDS: the v11 block's own warning, which is sharper than mine. 'THE VALENCE AXIS NOW POOLS FOUR INSTRUMENTS ... exp-a2-affect-resupply-20260830 measured valence r=+0.122 against a WITHIN-DATASET null of +0.169 -- i.e. most of the apparent pooled correlation was between-dataset structure at TWO instruments. At four, with dapper supplying 418 of the subjects, that risk is higher, not lower. affect_weight is therefore an ARM (v11affect), never the default.' That is the real constraint, and it is about scoring discipline, not about reach.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
