---
name: news-search
description: USE FOR news search via Search API. Returns news articles with title, URL, description, age, thumbnail, source. Supports freshness and date range filtering. Requires BRAVE_SEARCH_API_KEY.
---

# News Search (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **news search** when you need:
- Recent news articles
- Publication dates and sources
- Freshness filtering for breaking news
- News-specific content (not general web pages)

## Quick Start (cURL)

### Basic Search
```bash
curl -s "https://api.search.brave.com/res/v1/news/search?q=artificial+intelligence" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Recent News (Past 24 Hours)
```bash
curl -s "https://api.search.brave.com/res/v1/news/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=technology news" \
  --data-urlencode "country=us" \
  --data-urlencode "freshness=pd" \
  --data-urlencode "count=20"
```

### Date Range Filter
```bash
curl -s "https://api.search.brave.com/res/v1/news/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=climate summit" \
  --data-urlencode "freshness=2024-01-01to2024-01-31"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/news/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Search query |
| `country` | string | "US" | 2-char code | Search country |
| `search_lang` | string | "en" | 2+ char code | Language preference |
| `ui_lang` | string | "en-US" | locale code | UI language |
| `count` | int | 20 | 1-50 | Number of results |
| `offset` | int | 0 | 0-9 | Page offset |
| `safesearch` | string | "strict" | off/moderate/strict | Adult content filter |
| `freshness` | string | "" | pd/pw/pm/py or date range | Time filter |
| `spellcheck` | bool | true | - | Auto-correct query |
| `extra_snippets` | bool | null | - | Get additional excerpts |

### Freshness Values

| Value | Description |
|-------|-------------|
| `pd` | Past day (24 hours) - ideal for breaking news |
| `pw` | Past week (7 days) |
| `pm` | Past month (31 days) |
| `py` | Past year (365 days) |
| `YYYY-MM-DDtoYYYY-MM-DD` | Custom date range |

## Response Format

```json
{
  "type": "news",
  "query": {
    "original": "artificial intelligence",
    "spellcheck_off": false
  },
  "results": [
    {
      "type": "news_result",
      "title": "New AI Breakthrough Announced",
      "url": "https://news.example.com/ai-breakthrough",
      "description": "Researchers have announced a major advancement in...",
      "age": "2 hours ago",
      "page_age": "2024-01-15T14:30:00",
      "source": {
        "name": "Tech News Daily",
        "url": "https://news.example.com",
        "favicon": "https://news.example.com/favicon.ico"
      },
      "thumbnail": {
        "src": "https://imgs.search.brave.com/...",
        "width": 200,
        "height": 150
      },
      "meta_url": {
        "scheme": "https",
        "netloc": "news.example.com",
        "hostname": "news.example.com",
        "path": "/ai-breakthrough"
      },
      "extra_snippets": ["Additional context...", "More details..."]
    }
  ]
}
```

**Key Fields:**
- `age`: Human-readable time since publication
- `page_age`: ISO timestamp of publication
- `source.name`: Publisher/news outlet name
- `source.favicon`: Publisher favicon URL

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **SafeSearch**: Defaults to `strict` for news
- **Breaking news**: Use `freshness=pd` for latest articles
- **Pagination**: Use `offset` (0-9) with `count`

## Related Skills

- `web-search`: General web search (includes news in results)
- `grounding-context`: Get content with extracted text
