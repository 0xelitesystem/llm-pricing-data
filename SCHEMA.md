# Schema

Each provider file is a JSON array. Each element is a model entry with the following fields.

## Required fields

| Field | Type | Description |
|---|---|---|
| `model_id` | string | The identifier the API expects. Used to call the model. |
| `display_name` | string | Human-readable name. |
| `vendor` | string | Lowercase vendor slug. Use `meta-via-X` when a hosted provider serves a model. |
| `input_per_million_tokens_usd` | number | Cost per million input tokens, in USD. |
| `output_per_million_tokens_usd` | number | Cost per million output tokens, in USD. |
| `context_window` | integer | Maximum input + output tokens. |
| `max_output_tokens` | integer | Maximum tokens the model can return in one response. |
| `training_data_cutoff` | string | YYYY-MM. The latest month included in training data. |
| `modalities` | string[] | One or more of: `text`, `image`, `audio`, `video`. |
| `tool_use` | boolean | Does the model support function calling / tool use? |
| `vision` | boolean | Can the model accept images as input? |
| `available_via` | string[] | Where this model can be used (`api`, `chatgpt`, `claude.ai`, `amazon-bedrock`, `azure-openai`, `google-vertex`, `groq`, `together-ai`, etc.). |
| `deprecated` | boolean | True if the vendor has announced deprecation. |
| `deprecation_date` | string or null | YYYY-MM-DD. When the model will stop being available. |
| `notes` | string | Free-form notes (one sentence). |
| `last_updated` | string | YYYY-MM-DD. When this entry was last verified. |
| `source` | string | URL of the official pricing page or announcement. |

## Optional fields

When relevant, entries may include any of:

| Field | Type | Description |
|---|---|---|
| `cached_input_per_million_tokens_usd` | number | Cost per million tokens for cached / repeated context. |
| `batch_input_per_million_tokens_usd` | number | Cost per million tokens when using batch / async endpoints. |
| `batch_output_per_million_tokens_usd` | number | Output cost in batch mode. |
| `tier_pricing` | object | Pricing that varies with input length. See below. |
| `image_input_per_image_usd` | number | Per-image cost for vision (separate from token cost). |
| `audio_input_per_minute_usd` | number | Per-minute cost for audio input. |
| `audio_output_per_minute_usd` | number | Per-minute cost for audio output. |
| `embedding_dimensions` | integer | If this is an embedding model: vector size. |
| `aliases` | string[] | Other model IDs that point to this same model (e.g. dated snapshots). |

### `tier_pricing`

Some providers price differently when input exceeds a threshold (Google Gemini doubles the rate above 128K tokens, for example). Format:

```json
"tier_pricing": [
  {
    "input_threshold_tokens": 128000,
    "input_per_million_tokens_usd": 1.25,
    "output_per_million_tokens_usd": 5.00
  },
  {
    "input_threshold_tokens": null,
    "input_per_million_tokens_usd": 2.50,
    "output_per_million_tokens_usd": 10.00
  }
]
```

The first entry's threshold means "if input is ≤ this many tokens." The last entry's threshold of `null` is the catch-all bucket for anything above prior thresholds.

## Validation rules

- Numbers must be non-negative
- `model_id` must be unique within a file
- `last_updated` must not be in the future
- `deprecation_date`, when set, must not be before `last_updated`
- `source` must be a URL (not a description)

## Currency

All prices are in USD. If a provider quotes in another currency, convert at the rate stated on their pricing page on the day of update.

## Why per-million-tokens

Most providers now quote per-million pricing. For older sources that quote per-1K, multiply by 1000. Stating prices per-million keeps the numbers human-readable (no leading zeros).
