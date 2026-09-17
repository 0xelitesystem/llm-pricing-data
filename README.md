# llm-pricing-data

Machine-readable pricing for major LLM providers. JSON files, community-maintained, structured for programmatic use.

## Why

Every cost calculator, budget tracker, and provider-comparison page reinvents the same JSON. Instead of hardcoding stale numbers in 50 places, this repo is the source of truth that any of them can pull from (or fork-and-pin).

## Structure

```
data/
  anthropic.json
  openai.json
  google.json
  mistral.json
  ...
```

Each provider file contains an array of model entries. Schema:

```json
{
  "model_id": "claude-opus-5",
  "display_name": "Claude Opus 5",
  "vendor": "anthropic",
  "input_per_million_tokens_usd": 5.0,
  "output_per_million_tokens_usd": 25.0,
  "cached_input_per_million_tokens_usd": 0.5,
  "cache_write_5m_per_million_tokens_usd": 6.25,
  "cache_write_1h_per_million_tokens_usd": 10.0,
  "batch_input_per_million_tokens_usd": 2.5,
  "batch_output_per_million_tokens_usd": 12.5,
  "context_window": 1000000,
  "max_output_tokens": 128000,
  "training_data_cutoff": "2026-05",
  "modalities": ["text", "image"],
  "tool_use": true,
  "vision": true,
  "available_via": ["api", "amazon-bedrock", "google-vertex"],
  "deprecated": false,
  "deprecation_date": null,
  "notes": "Fast mode (speed: fast) is billed at $10 input / $50 output per MTok.",
  "last_updated": "2026-09-17",
  "source": "https://platform.claude.com/docs/en/about-claude/pricing"
}
```

Cached prompt input pricing, prompt-cache write rates, batch discounts, and other tier-specific pricing live in optional sub-fields (see `SCHEMA.md`).

A required field is `null` when the provider does not publish it. That is deliberate: a number nobody can check against a primary source does not belong in a pricing file, and an old number left in place is worse than an honest gap. If the PRICE cannot be verified, the entry is removed instead.

## Use the data

### Direct download

```bash
# Always-latest from main:
curl https://raw.githubusercontent.com/0xelitesystem/llm-pricing-data/main/data/anthropic.json

# Pin to a release (recommended for production):
curl https://raw.githubusercontent.com/0xelitesystem/llm-pricing-data/v2026.05.01/data/anthropic.json
```

### As a JSON module

```javascript
import anthropic from "https://cdn.jsdelivr.net/gh/0xelitesystem/llm-pricing-data@main/data/anthropic.json";

const opus = anthropic.find(m => m.model_id === "claude-opus-5");
const cost = (inputTokens / 1_000_000) * opus.input_per_million_tokens_usd
           + (outputTokens / 1_000_000) * opus.output_per_million_tokens_usd;
```

### In Python

```python
import json
import urllib.request

url = "https://raw.githubusercontent.com/0xelitesystem/llm-pricing-data/main/data/anthropic.json"
with urllib.request.urlopen(url) as r:
    pricing = json.load(r)
```

## Verified 2026-09-17

Every entry in `data/` was re-read against the provider's own pages on 2026-09-17:

- `anthropic.json`, [platform.claude.com pricing](https://platform.claude.com/docs/en/about-claude/pricing) and [models overview](https://platform.claude.com/docs/en/about-claude/models/overview)
- `openai.json`, [developers.openai.com pricing](https://developers.openai.com/api/docs/pricing) and [models](https://developers.openai.com/api/docs/models)
- `google.json`, [ai.google.dev Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)
- `mistral.json`, [mistral.ai API pricing](https://mistral.ai/pricing/api) and the [models overview](https://docs.mistral.ai/getting-started/models/models_overview/)

What changed: the Anthropic rows carried Opus at $15 / $75 per MTok, which is the retired Opus 4.1 price, not the price of any Opus the vendor currently sells; the current Opus line is $5 / $25. Models the providers no longer list were removed rather than left to rot, and `data/meta.json` (hosted Llama) went with them, because neither host published a model id and a price that could both be verified from a primary page. A PR that restores Llama pricing with a checkable source is welcome.

## Update cadence

Data is updated when:

- A provider announces a price change (community PRs welcome immediately)
- A new model is released
- A model is deprecated

Each file's `last_updated` field tells you when it was last verified. If a file is more than 60 days stale, expect drift.

## Versioning

Tagged releases (e.g. `v2026.05.01`) snapshot the entire repo on a date. Use these for production builds where you want pricing to be predictable.

The `main` branch always has the latest data and may change at any time.

## Contribute updates

When a provider changes pricing or releases a new model, open a PR. See [CONTRIBUTING.md](./CONTRIBUTING.md). The bar is intentionally low: change the number, update `last_updated`, link the source.

## What this is NOT

- **Not real-time.** This is JSON in a git repo. There's a lag between announcements and updates.
- **Not infrastructure pricing.** Includes per-token model costs only. Not GPU rental, not fine-tuning, not embeddings (yet).
- **Not authoritative.** Always confirm critical numbers against the provider's own pricing page before billing decisions.
- **Not an endorsement.** Listing a provider doesn't mean we vouch for them.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT. Use the data however you want, including in commercial products.

## Related

- [prompt-cost-calculator](https://github.com/0xelitesystem/prompt-cost-calculator), calculator that consumes this data
- [byok-patterns](https://github.com/0xelitesystem/byok-patterns), reference implementations for BYOK products
- [prompt-templates](https://github.com/0xelitesystem/prompt-templates), production prompts
