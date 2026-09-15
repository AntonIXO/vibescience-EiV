---
arxiv_id_or_url: '2209.08754'
id: yang-2022-privileged-features-distillation-2209-08754
title: Toward Understanding Privileged Features Distillation in Learning-to-Rank (Yang,
  Sanghavi, Rahmanian, Bakus, Vishwanathan)
topic_tags:
- optihealth
- lupi
---

## Key claims
- NeurIPS 2022. Privileged Features Distillation (teacher sees privileged feature, student does not) beats no-distillation, pretrain-finetune, self-distillation AND Lopez-Paz generalised distillation on 3 public ranking sets + Amazon logs.
- NON-MONOTONE LAW: as the privileged feature's predictive power rises, student performance first rises then FALLS -- a very predictive privileged teacher has high-variance predictions, which become high-variance student targets. Proven for linear models, shown empirically.
- For EiV's EDA LUPI (privileged_events.parquet, 14 corpora, never used): the failure mode to pre-register is exactly this -- if eda_tonic is a near-perfect dataset indicator (CASE median 19.6 uS vs POPANE s4 2.06), the teacher is 'too predictive' of the wrong thing and the student inherits variance, not skin. Test conditional MI of EDA with the label GIVEN dataset before any distillation arm.
- Code: not stated. Modality: tabular ranking, not physiological -- the mechanism transfers, the numbers do not.

#optihealth #lupi
