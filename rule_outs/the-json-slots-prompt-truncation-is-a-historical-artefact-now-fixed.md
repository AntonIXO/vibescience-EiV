---
created_at: '2026-09-01T07:52:03+00:00'
former_ids: []
id: the-json-slots-prompt-truncation-is-a-historical-artefact-now-fixed
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:52:03+00:00'
topic_tags: []
---

## Claim
The json_slots prompt truncation (327 tokens against a 256 budget) was a historical artefact of a hardcoded 256 budget and is fixed now that gemma_runtime auto-derives the budget from the model context window.

## Method
Tokenised prompts/json_slots_prompt.md with the model's own tokenizer and compared against the budget returned by resolve_prompt_token_budget for the live lora_proxy_12ch_wl_band04 profile, then instrumented resolve_model_context_tokens' inputs directly against the model config. Config and tokenizer only, no weights, no GPU.

## Evidence
STILL LIVE, AND THE ROOT CAUSE IS NOW LOCALISED. Measured 2026-09-01 on the exact profile every arm in this campaign uses (scratch/_prompt_budget_audit.py):
  prompts/json_slots_prompt.md   1,046 chars = 317 tokens
  effective_max_prompt_tokens    256
  -> 61 tokens silently cut, and the tail that is lost is:
     the 'temp' key definition, the entire 'state' key definition
     ("stress", "flow", "neutral", "burnout"), and the final instruction
     'Output ONLY the JSON object.'
So the model is trained and evaluated on a schema whose last two slot definitions and whose output-format instruction were never in the prompt. The `state` slot -- which several experiments have scored -- is defined ONLY in the truncated tail.

WHY THE AUTO-DERIVATION DID NOT HELP (scratch/_context_resolution_probe.py). resolve_prompt_token_budget falls back to auto_fallback_tokens=256 when resolve_model_context_tokens returns None, and for this model it does return None:
  config class Gemma3Config
  top level: max_position_embeddings / n_positions / max_seq_len / seq_length / model_max_length / sliding_window ALL None
  text_config (Gemma3TextConfig): max_position_embeddings 131072, sliding_window 1024
resolve_model_context_tokens (gemma_runtime.py:100-128) reads those attributes off `runtime.model.config` only, never off `config.text_config`. medgemma-1.5-4b-it is a MULTIMODAL config, so the language model's context sits one level down. The tokenizer's model_max_length is the int64 sentinel 1000000000000000019884624838656, which _normalize_context_length correctly rejects, leaving nothing.

SO THE MODEL CAN TAKE 131,072 TOKENS AND THE PIPELINE BUDGETS 256. The fix is ~3 lines in resolve_model_context_tokens: also scan `getattr(config, 'text_config', None)` for the same attributes. That would give text_budget = 131072 - 96 sensor slots - 16 reserve, an effective prompt of ~65k, and the 317-token prompt would fit with vast headroom.

SCOPE, STATED HONESTLY. This does NOT invalidate the campaign's comparisons: every arm from v28 through v37 was trained and scored under the same truncated prompt, so the comparison is internally consistent and the measured deltas stand. What it means is that the whole family may be operating below its ceiling, and that any experiment scoring the `state` slot was scoring a slot whose definition the model never saw.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
