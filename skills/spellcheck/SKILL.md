---
name: spellcheck
description: USE FOR spell correction. Returns corrected query if misspelled. Most search endpoints have spellcheck built-in; use this only for pre-search query cleanup or "Did you mean?" UI.
---

# Spellcheck

> **Requires API Key**: Get one at https://api.search.brave.com
> **Plan**: Available in the **Search** plan. See https://api.search.brave.com/app/subscriptions for details.

## Quick Start (cURL)

```bash
curl -s "https://api.search.brave.com/res/v1/spellcheck/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=artifical inteligence" \
  --data-urlencode "lang=en" \
  --data-urlencode "country=US"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/spellcheck/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header
**Plan**: Search

## Parameters

| Parameter | Type | Required | Default | Description |
|--|--|--|--|--|
| `q` | string | **Yes** | — | Query to spell check (1-400 chars, max 50 words) |
| `lang` | string | No | `en` | Language hint (e.g. `en`, `fr`, `de`, `es`, `pt-br`, `zh-hans`). 51 codes supported |
| `country` | string | No | `US` | Country hint (e.g. `US`, `GB`, `FR`, `DE`). 38 codes including `ALL` |

## Response Fields

| Field | Type | Description |
|--|--|--|
| `type` | string | Always `"spellcheck"` |
| `query.original` | string | The input query as submitted |
| `results` | array | Spell-corrected suggestions. May be empty when no correction is found |
| `results[].query` | string | A corrected version of the query |

## Example Response

```json
{
  "type": "spellcheck",
  "query": {
    "original": "artifical inteligence"
  },
  "results": [
    {
      "query": "artificial intelligence"
    }
  ]
}
```

## Use Cases

- **Pre-search query cleanup**: Check spelling before deciding which search endpoint to call
- **"Did you mean?" UI**: Show users a corrected suggestion before running the search
- **Batch query normalization**: Clean up query logs or user input in bulk

## Notes

- **Timeout**: Recommended 10s (fast endpoint)
- **Built-in alternative**: Web Search and LLM Context have `spellcheck=true` by default — use this standalone endpoint only when you need the correction before searching
- **Context-aware**: Corrections consider the full query context, not just individual words
