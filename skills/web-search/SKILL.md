---
name: web-search
description: USE FOR web search via Search API. Returns ranked results with snippets, URLs, thumbnails. Supports freshness filters, SafeSearch, Goggles for custom ranking, pagination. Primary search endpoint. Requires BRAVE_SEARCH_API_KEY.
---

# Web Search (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **web search queries** when you need:
- Ranked search results with snippets
- URLs and page metadata
- Support for pagination, freshness filters
- Custom ranking via Goggles

**For grounded context with pre-extracted content**, use `grounding-context` instead - it returns relevant snippets without needing to visit pages.

## Quick Start (cURL)

### Basic Search
```bash
curl -s "https://api.search.brave.com/res/v1/web/search?q=python+web+frameworks" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### With Parameters
```bash
curl -s "https://api.search.brave.com/res/v1/web/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=rust programming tutorials" \
  --data-urlencode "country=us" \
  --data-urlencode "search_lang=en" \
  --data-urlencode "count=10" \
  --data-urlencode "safesearch=moderate" \
  --data-urlencode "freshness=pm"
```

### With Goggles (Custom Ranking)
```bash
curl -s "https://api.search.brave.com/res/v1/web/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=machine learning" \
  --data-urlencode 'goggles=$discard
$site=arxiv.org
$site=github.com'
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/web/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Search query |
| `country` | string | "US" | 2-char code | Search country code |
| `search_lang` | string | "en" | 2+ char code | Language for results |
| `ui_lang` | string | "en-US" | locale code | UI language (e.g., "en-US") |
| `count` | int | 20 | 1-20 | Results per page (web only) |
| `offset` | int | 0 | 0-9 | Page offset for pagination |
| `safesearch` | string | "moderate" | off/moderate/strict | Adult content filter |
| `freshness` | string | "" | pd/pw/pm/py or date range | Time filter |
| `text_decorations` | bool | true | - | Include highlight markers |
| `spellcheck` | bool | true | - | Auto-correct query |
| `result_filter` | string | null | comma-separated | Filter result types |
| `goggles` | string | null | URL or inline | Custom ranking filter |
| `extra_snippets` | bool | null | - | Get up to 5 extra snippets |
| `summary` | bool | null | - | Enable summarizer key |

### Freshness Values

| Value | Description |
|-------|-------------|
| `pd` | Past day (24 hours) |
| `pw` | Past week (7 days) |
| `pm` | Past month (31 days) |
| `py` | Past year (365 days) |
| `YYYY-MM-DDtoYYYY-MM-DD` | Custom date range |

### Result Filter Values

Filter types: `discussions`, `faq`, `infobox`, `news`, `query`, `summarizer`, `videos`, `web`, `locations`

```bash
# Only web and video results
curl "...&result_filter=web,videos"
```

## Response Format

```json
{
  "type": "search",
  "query": {
    "original": "python frameworks",
    "altered": "python web frameworks",
    "spellcheck_off": false
  },
  "web": {
    "type": "search",
    "results": [
      {
        "title": "Top Python Web Frameworks",
        "url": "https://example.com/python-frameworks",
        "description": "A comprehensive guide to Python web frameworks...",
        "age": "2 days ago",
        "language": "en",
        "meta_url": {
          "scheme": "https",
          "netloc": "example.com",
          "hostname": "example.com",
          "path": "/python-frameworks"
        },
        "thumbnail": {
          "src": "https://...",
          "width": 200,
          "height": 150
        },
        "extra_snippets": ["Additional excerpt 1...", "Additional excerpt 2..."]
      }
    ],
    "family_friendly": true
  },
  "videos": { ... },
  "news": { ... },
  "infobox": { ... }
}
```

## Goggles (Custom Ranking)

Goggles let you customize search ranking. Pass as URL or inline definition:

```bash
# Hosted goggle
--data-urlencode "goggles=https://raw.githubusercontent.com/brave/goggles-quickstart/main/goggles/hacker_news.goggle"

# Inline definition
--data-urlencode 'goggles=$discard,site=facebook.com
$discard,site=x.com
$site=docs.python.org,boost=3'
```

**Documentation**: https://search.brave.com/help/goggles

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **Pagination**: Use `offset` (0-9) with `count` to page through results
- **Count**: Only applies to web results, not other result types

## Related Skills

- `grounding-context`: Pre-extracted content for RAG/LLM (recommended for AI)
- `images-search`: Image-specific search
- `videos-search`: Video-specific search
- `news-search`: News-specific search
