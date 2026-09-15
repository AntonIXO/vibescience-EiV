---
created_at: '2026-08-30T09:45:09+00:00'
former_ids: []
id: that-the-deployment-collapse-observed-in-every-553c9d
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-30T09:45:09+00:00'
topic_tags:
- deployment-distribution
- baseline
- control
- collapse
---

## Claim
That the deployment collapse observed in every post-12,547 chain (v11full corr -0.035, v12conly corr -0.097, dense MAE ~21.5) is an artifact of the rebuilt eval bundle or of the emission scorer, rather than a real property of the trained adapters.

## Method
Negative control on the instrument, run 2026-08-30 immediately after exp-v12 closed. One command, no training: scratch/emission_diversity_audit.py --adapter outputs/adapters/ab_ctrl_s43 --dataset optihealth_supabase --n 200 --batch-size 1, against data/eval_v3_1min as rebuilt from the current corpus. Artifact logs/emission_healthyref_newEval_supa.json.

## Evidence
ELIMINATED BY A NEGATIVE CONTROL THAT COSTS NO TRAINING. The known-healthy adapter outputs/adapters/ab_ctrl_s43 (the v10conly family anchor, recorded at MAE 2.836 / corr 0.739 on the OLD eval bundle) was re-scored on the CURRENT eval bundle -- rebuilt from the [14092,1440,12] corpus, source sha 3e51df88, 6,745 strict-clean rows -- through the same scratch/emission_diversity_audit.py at n=200, batch 1, rng seed 0. RC=0, zero tracebacks, parse_ok 200/200.

  hr_raw  MAE 2.692 vs trivial 3.954 -> skill +1.262, corr +0.7598, distinct 18, top1_mass 0.275
  hr_peak MAE 20.084 vs trivial 17.410 -> skill -2.674, corr +0.2444

The healthy adapter reads the NEW eval rows slightly BETTER than it read the old ones (2.692 vs 2.836 recorded; corr 0.760 vs 0.739). So the eval bundle, the strict-clean filter, the 200-row draw and the whole emission/parse path are intact.

CONSEQUENCE: the collapses recorded today are real model failures, not instrument artifacts, and they are directly comparable to the healthy family on this draw. v11full (corr -0.035, top1 0.805) and v12conly (corr -0.097, top1 0.470) sit against a same-instrument, same-draw healthy reference of corr +0.760, top1 0.275.

This also breaks half of the corpus/roster confound: the NEW corpus's eval rows are readable by an adapter trained on the OLD corpus, so 'the new corpus is unreadable' is false. What remains unseparated is what the new corpus TEACHES the C stage.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].

#deployment-distribution #baseline #control #collapse
