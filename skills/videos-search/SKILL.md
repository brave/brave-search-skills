---
name: videos-search
description: USE FOR video search via Search API. Returns videos with title, URL, thumbnail, duration, view count. Supports freshness filters and SafeSearch. Requires BRAVE_SEARCH_API_KEY.
---

# Video Search (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **video search** when you need:
- Video URLs and thumbnails
- Duration and view counts
- Publisher/channel information
- Freshness filtering for recent videos

## Quick Start (cURL)

### Basic Search
```bash
curl -s "https://api.search.brave.com/res/v1/videos/search?q=python+tutorial" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### With Parameters
```bash
curl -s "https://api.search.brave.com/res/v1/videos/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=machine learning explained" \
  --data-urlencode "country=us" \
  --data-urlencode "search_lang=en" \
  --data-urlencode "count=20" \
  --data-urlencode "freshness=pm"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/videos/search
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
| `safesearch` | string | "off" | off/moderate/strict | Adult content filter |
| `freshness` | string | "" | pd/pw/pm/py or date range | Time filter |
| `spellcheck` | bool | true | - | Auto-correct query |

### Freshness Values

| Value | Description |
|-------|-------------|
| `pd` | Past day (24 hours) |
| `pw` | Past week (7 days) |
| `pm` | Past month (31 days) |
| `py` | Past year (365 days) |
| `YYYY-MM-DDtoYYYY-MM-DD` | Custom date range |

## Response Format

```json
{
  "type": "videos",
  "query": {
    "original": "python tutorial",
    "spellcheck_off": false
  },
  "results": [
    {
      "type": "video_result",
      "title": "Python Tutorial for Beginners",
      "url": "https://www.youtube.com/watch?v=...",
      "description": "Learn Python programming from scratch...",
      "age": "6 months ago",
      "page_age": "2023-07-15T00:00:00",
      "thumbnail": {
        "src": "https://imgs.search.brave.com/...",
        "width": 320,
        "height": 180
      },
      "video": {
        "duration": "3:45:00",
        "views": "5.2M views",
        "creator": "freeCodeCamp",
        "publisher": "YouTube"
      },
      "meta_url": {
        "scheme": "https",
        "netloc": "www.youtube.com",
        "hostname": "www.youtube.com",
        "path": "/watch"
      }
    }
  ]
}
```

**Key Fields:**
- `video.duration`: Video length (H:MM:SS format)
- `video.views`: View count with formatting
- `video.creator`: Channel/creator name
- `video.publisher`: Platform (YouTube, Vimeo, etc.)
- `thumbnail.src`: Video thumbnail URL

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **SafeSearch**: Defaults to `off` for videos
- **Pagination**: Use `offset` (0-9) with `count`

## Related Skills

- `web-search`: General web search (includes video results)
- `images-search`: Image search
- `news-search`: News search
