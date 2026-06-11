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
  meta.json          (for hosted providers serving Llama)
  ...
```

Each provider file contains an array of model entries. Schema:

```json
{
  "model_id": "claude-opus-4-7",
  "display_name": "Claude Opus 4.7",
  "vendor": "anthropic",
  "input_per_million_tokens_usd": 15.00,
  "output_per_million_tokens_usd": 75.00,
  "context_window": 200000,
  "max_output_tokens": 8192,
  "training_data_cutoff": "2025-03",
  "modalities": ["text", "image"],
  "tool_use": true,
  "vision": true,
  "available_via": ["api", "claude.ai"],
  "deprecated": false,
  "deprecation_date": null,
  "notes": "Most capable Anthropic model as of 2026-05",
  "last_updated": "2026-05-01",
  "source": "https://www.anthropic.com/pricing"
}
```

Cached prompt input pricing, batch discounts, and other tier-specific pricing live in optional sub-fields (see `SCHEMA.md`).

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

const opus = anthropic.find(m => m.model_id === "claude-opus-4-7");
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

## License

MIT. Use the data however you want, including in commercial products.

## Related

- [prompt-cost-calculator](https://github.com/0xelitesystem/prompt-cost-calculator), calculator that consumes this data
- [byok-patterns](https://github.com/0xelitesystem/byok-patterns), reference implementations for BYOK products
- [prompt-templates](https://github.com/0xelitesystem/prompt-templates), production prompts
