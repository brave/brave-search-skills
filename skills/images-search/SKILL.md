---
name: sapi-images-search
description: USE FOR image search via Search API. Returns images with src URL, page URL, dimensions, alt text. Supports SafeSearch filtering. Requires BRAVE_SEARCH_API_KEY.
---

# Image Search (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **image search** when you need:
- Image URLs and thumbnails
- Image dimensions and metadata
- SafeSearch filtering for content moderation

## Quick Start (cURL)

### Basic Search
```bash
curl -s "https://api.search.brave.com/res/v1/images/search?q=mountain+landscape" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### With Parameters
```bash
curl -s "https://api.search.brave.com/res/v1/images/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=northern lights photography" \
  --data-urlencode "country=us" \
  --data-urlencode "search_lang=en" \
  --data-urlencode "count=20" \
  --data-urlencode "safesearch=strict"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/images/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Search query |
| `country` | string | "US" | 2-char code | Search country |
| `search_lang` | string | "en" | 2+ char code | Language preference |
| `count` | int | 50 | 1-200 | Number of results |
| `safesearch` | string | "strict" | off/strict | Adult content filter |
| `spellcheck` | bool | true | - | Auto-correct query |

### SafeSearch Values

| Value | Description |
|-------|-------------|
| `off` | No filtering (except illegal content) |
| `strict` | Drops all adult content |

## Response Format

```json
{
  "type": "images",
  "query": {
    "original": "mountain landscape",
    "spellcheck_off": false
  },
  "results": [
    {
      "type": "image_result",
      "title": "Beautiful Mountain Landscape",
      "url": "https://example.com/mountain-photo",
      "source": "example.com",
      "page_fetched": "2024-01-15T10:30:00Z",
      "thumbnail": {
        "src": "https://imgs.search.brave.com/...",
        "width": 200,
        "height": 150
      },
      "properties": {
        "url": "https://example.com/images/mountain.jpg",
        "width": 1920,
        "height": 1080,
        "format": "jpeg"
      },
      "meta_url": {
        "scheme": "https",
        "netloc": "example.com",
        "hostname": "example.com",
        "path": "/mountain-photo"
      }
    }
  ]
}
```

**Key Fields:**
- `properties.url`: Direct URL to the full-size image
- `properties.width/height`: Image dimensions
- `thumbnail.src`: Thumbnail URL (smaller, cached version)
- `url`: Page URL where image was found

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **SafeSearch**: Defaults to `strict` for images
- **Thumbnails**: Brave-hosted, cached thumbnails are provided

## Related Skills

- `sapi-web-search`: General web search
- `sapi-videos-search`: Video search
- `brave-dev-images-search`: Internal version (VPN required)
