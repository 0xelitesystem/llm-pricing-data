# Contributing Pricing Updates

The bar is intentionally low. Pricing updates need accuracy, not ceremony.

## To update an existing model's price

1. Edit the relevant file in `data/`
2. Update the price field(s)
3. Update `last_updated` to today
4. Confirm the `source` URL still points to the announcement or pricing page
5. Open a PR

PR title: `update pricing: vendor-name model-id`

In the PR description, paste a screenshot or link to the pricing page showing the new number, OR link to the official announcement. We need to be able to verify.

## To add a new model

1. Find the right vendor file (or create a new one if the vendor isn't listed)
2. Add a new entry following the [schema](./SCHEMA.md)
3. Open a PR

PR title: `add model: vendor-name model-id`

For new vendors, also update the README's "Structure" section to mention the new file.

## To deprecate a model

When a vendor announces deprecation:

1. Set `deprecated: true`
2. Set `deprecation_date` to the announced date
3. Update `notes` to mention the migration target if any
4. Update `last_updated`

Don't delete the entry. Keeping deprecated entries lets users with pinned old code see what's affected.

## To remove a deprecated model

Remove only after the deprecation date has passed AND the model is no longer callable. Open an issue first to confirm.

## Validation

Before opening a PR, run:

```bash
# Validate JSON syntax
for f in data/*.json; do
  python -m json.tool "$f" > /dev/null && echo "$f OK" || echo "$f INVALID"
done
```

A CI check will run the same validation on PRs (community contribution welcome to add this).

## What gets rejected

- Pricing changes without a source link
- Pricing changes that contradict the linked source
- New models added speculatively (e.g. announced but not generally available)
- Estimates or "roughly $X" pricing, only confirmed published prices
- Internal/preview/private-beta pricing that isn't publicly documented
- Removing entries instead of marking them deprecated

## Response time

Pricing PRs are usually merged within a day. The data is volatile; the goal is to keep the lag short.

## What about embedding models, fine-tuning, GPU rentals?

Out of scope for now. This repo is per-token pricing for inference of completion / chat models. If there's demand, separate files (`anthropic-embeddings.json`, etc.) can be added later.
