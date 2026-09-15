---
created_at: '2026-09-01T09:09:20+00:00'
former_ids: []
id: the-wide-roster-is-what-breaks-phase-c-deployment
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:09:20+00:00'
topic_tags: []
---

## Claim
The four wide-roster Phase C arms failing deployment at z +24 to +29 shows that giving Phase C the affect datasets is what breaks it -- i.e. the ROSTER is the cause, and 'labels in all three phases via the Phase C roster' is refuted.

## Method
Dict-diffed each wide-roster Phase C profile against lora_proxy_12ch_wl_band04, listing every differing key rather than trusting the arms' names or notes. No GPU.

## Evidence
MY OWN ATTRIBUTION WAS WRONG AND I AM CORRECTING IT. The failure is real and the numbers stand, but it cannot be attributed to the roster, because not one of those arms is a roster-only variation. Dict-diffed each against the incumbent Phase C profile (scratch/_wide_roster_variable_check.py):

  every one of v11full / v12conly / v13flat / v14long differs in FIVE keys:
    dataset_filter            6 -> 20 datasets (7 for v14long)
    hr_coverage_denominator   <absent> -> recording
    patch_pool_mode           <absent> -> masked_avg
    prompt_path               <absent> -> prompts/json_slots_prompt_fit256.md
    seed                      42 -> 43

So four confounds ride with the roster, and two of them are known to be load-bearing on their own:
  - prompt_path is a DIFFERENT PROMPT. The incumbent trains on the shipped 317-token json prompt truncated to 256; these arms train on a 250-token prompt that fits with all 13 keys and all 11 enum values intact. That is exactly the variable v38prompt is queued to test in isolation, and its effect is unmeasured.
  - seed 42 -> 43 alone spans the measured Phase-C seed floor, and the ab_ctrl family shows same-recipe seeds landing 2.562 to 10.168 bpm on dense.
  - patch_pool_mode changes how the projector pools, i.e. the representation the emitter reads.

WHAT SURVIVES: the four arms are catastrophically bad on deployment (dense MAE 21.3-24.6 against 2.851, emit/true scale 0.26-0.57 or 2.03-2.76, all outside the [0.85,1.35] domain where the floor is valid), and that is worth knowing. What is NOT established is WHY. 'The wide roster fails' and 'one of the other four keys fails' predict the same table.

THE CORRECTED STATEMENT: the wide-roster question is UNTESTED, not refuted. Testing it needs one arm differing from the incumbent in dataset_filter alone -- and that arm has never been run.

WHY I MADE THE ERROR, since it is the second of its kind today: I read the arms' names (v12conly = 'C-only frozen encoder', v13flat = 'flat weights', v14long = 'long pool') as descriptions of their ONLY change, and treated a shared outcome across four arms as evidence for the one property I had been thinking about. Four arms agreeing is not four independent tests when they share four confounds.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
