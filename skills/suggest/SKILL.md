---
name: suggest
description: USE FOR query autocomplete/suggestions via Search API. Fast (<100ms). Returns suggested queries as user types. Supports rich suggestions with entity info. Requires BRAVE_SEARCH_API_KEY.
---

# Suggest / Autocomplete (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **query autocomplete** when you need:
- Fast suggestions as users type (<100ms)
- Query completion hints
- Rich suggestions with entity information (paid feature)

## Quick Start (cURL)

### Basic Suggestions
```bash
curl -s "https://api.search.brave.com/res/v1/suggest/search?q=how+to+" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### With Parameters
```bash
curl -s "https://api.search.brave.com/res/v1/suggest/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=python" \
  --data-urlencode "country=us" \
  --data-urlencode "lang=en" \
  --data-urlencode "count=10"
```

### Rich Suggestions (requires paid plan)
```bash
curl -s "https://api.search.brave.com/res/v1/suggest/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=elon" \
  --data-urlencode "rich=true"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/suggest/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Partial query to complete |
| `country` | string | "US" | 2-char code | Search country hint |
| `lang` | string | "en" | 2+ char code | Language hint |
| `count` | int | 5 | 1-20 | Number of suggestions |
| `rich` | bool | false | - | Include rich entity info (paid) |

## Response Format

### Standard Suggestions
```json
{
  "type": "suggest",
  "query": {
    "original": "how to "
  },
  "results": [
    {
      "query": "how to learn python",
      "is_entity": false
    },
    {
      "query": "how to cook rice",
      "is_entity": false
    },
    {
      "query": "how to screenshot on mac",
      "is_entity": false
    }
  ]
}
```

### Rich Suggestions (with `rich=true`)
```json
{
  "type": "suggest",
  "query": {
    "original": "elon"
  },
  "results": [
    {
      "query": "elon musk",
      "is_entity": true,
      "title": "Elon Musk",
      "description": "American entrepreneur and business magnate",
      "img": "https://imgs.search.brave.com/...",
      "url": "https://en.wikipedia.org/wiki/Elon_Musk"
    },
    {
      "query": "elon musk net worth",
      "is_entity": false
    }
  ]
}
```

**Rich Fields (when available):**
- `is_entity`: Whether suggestion is an entity (person, place, etc.)
- `title`: Entity title
- `description`: Brief entity description
- `img`: Entity image URL
- `url`: Entity reference URL

## Notes

- **Latency**: Designed for <100ms response times
- **Rate limits**: Check your API plan
- **Rich suggestions**: Requires paid autosuggest subscription
- **Country/lang**: These are hints, not strict filters

## Related Skills

- `spellcheck`: Spell correction for queries
- `web-search`: Full search results
