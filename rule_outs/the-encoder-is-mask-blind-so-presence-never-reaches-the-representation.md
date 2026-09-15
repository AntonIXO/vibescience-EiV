---
created_at: '2026-09-01T09:29:52+00:00'
former_ids: []
id: the-encoder-is-mask-blind-so-presence-never-reaches-the-representation
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:29:52+00:00'
topic_tags: []
---

## Claim
The encoder is mask-blind, so a masked cell is indistinguishable from a measured value at the population centre -- this is the surviving architectural gap from root cause 2 and deserves its own arm (add a mask input to TemporalEncoder).

## Method
Scanned encoder, channel-fusion and projector modules for mask/presence plumbing, counted the pipeline's call sites, and read the projector's guard clause. No GPU.

## Evidence
THE SENTENCE IS TRUE ABOUT ONE FUNCTION SIGNATURE AND FALSE ABOUT THE MODEL. Presence already reaches the representation -- just not at the encoder's first layer (scratch/_presence_reach_audit.py).

WHERE PRESENCE ENTERS:
  src/projectors/cross_modal.py   `patch_presence` [B,N] or [B,C,N] is an explicit
                                  forward argument (line 224), plus a
                                  `value_presence_fusion` module (line 275)
  src/models/channel_fusion.py    consumes channel_mask, 10 key_padding_mask uses
  src/training/pipeline.py        passes channel_mask 9 times, presence-ish kwargs 21 times

AND THE DECISIVE ONE: cross_modal.py:394 RAISES when presence is missing --
  if pres_c is None and not self.allow_presence_blind: ... "construct the
  projector with allow_presence_blind=True."
A guard that must be explicitly disabled exists only because presence is
normally REQUIRED. scratch/emission_diversity_audit.py:170 sets that flag only
for a deliberate ablation, described in its own comment as reproducing 'the
pre-2026-08-28 broken serving path'.

SO THE ARM I WAS ABOUT TO PROPOSE WOULD HAVE TESTED REDUNDANCY, not repair: it
would add a second presence pathway to a model that already carries one, and a
null result would have been read as 'presence does not matter' when the correct
reading is 'presence was already there'.

WHAT REMAINS TRUE AND WORTH KEEPING: at the FIRST LAYER specifically, FlexMLP is
nn.Linear over the channel axis with no mask term, so within that layer a
zero-filled cell is arithmetically identical to a measured population-median
cell. That is a statement about layer placement -- presence is injected later,
at fusion and projection -- not about the model being blind. The honest question
is whether EARLY presence beats LATE presence, which is a much narrower claim
than the synthesis implied and needs a different justification to be worth GPU.

METHOD NOTE: the synthesis inherited this claim from the coverage-dilution
record and I carried it forward twice today before checking the downstream
stack. Reading one signature is not reading the forward pass.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
