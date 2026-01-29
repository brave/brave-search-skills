---
name: sapi-spellcheck
description: USE BEFORE search to fix typos via Search API. Returns corrected query with confidence scores. Improves search quality by fixing common misspellings. Requires BRAVE_SEARCH_API_KEY.
---

# Spellcheck (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **spell correction** when you need:
- Fix typos before searching
- Verify query spelling
- Get corrected query suggestions

**Note**: Most search endpoints have `spellcheck=true` by default, so you typically don't need to call this separately. Use this for standalone spell checking or when you need the correction details.

## Quick Start (cURL)

### Basic Spellcheck
```bash
curl -s "https://api.search.brave.com/res/v1/spellcheck/search?q=artifical+inteligence" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### With Parameters
```bash
curl -s "https://api.search.brave.com/res/v1/spellcheck/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=programing langauge" \
  --data-urlencode "country=us" \
  --data-urlencode "lang=en"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/spellcheck/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Query to spell check |
| `country` | string | "US" | 2-char code | Country hint |
| `lang` | string | "en" | 2+ char code | Language hint |

## Response Format

```json
{
  "type": "spellcheck",
  "query": {
    "original": "artifical inteligence",
    "altered": "artificial intelligence",
    "spellcheck_off": false
  },
  "results": [
    {
      "original": "artifical",
      "corrected": "artificial",
      "confidence": 0.95
    },
    {
      "original": "inteligence",
      "corrected": "intelligence",
      "confidence": 0.98
    }
  ]
}
```

**Key Fields:**
- `query.original`: The input query
- `query.altered`: The corrected query
- `results`: Array of individual corrections with confidence scores
- `confidence`: 0.0-1.0 indicating correction confidence

### No Corrections Needed

When the query is already correct:
```json
{
  "type": "spellcheck",
  "query": {
    "original": "artificial intelligence",
    "altered": "artificial intelligence",
    "spellcheck_off": false
  },
  "results": []
}
```

## Notes

- **Timeout**: Recommended 10s (fast endpoint)
- **Rate limits**: Check your API plan
- **Built-in**: Search endpoints have spellcheck enabled by default
- **Language hint**: Helps with language-specific corrections

## Related Skills

- `sapi-suggest`: Query autocomplete
- `sapi-web-search`: Web search (has built-in spellcheck)
- `brave-dev-spellcheck`: Internal version (VPN required)
