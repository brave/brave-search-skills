---
name: content
description: USE FOR extracting page content from URLs via Search API. Returns cleaned or raw HTML content from web pages. Supports up to 10 URLs per request. Requires BRAVE_SEARCH_API_KEY.
---

# Content Extraction (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **content extraction** when you need:
- Page content from specific URLs
- Cleaned HTML (boilerplate removed) or raw HTML
- Text content for analysis or processing

**For search + content together**, use `grounding-context` instead - it searches and returns pre-extracted relevant snippets in one call.

## Quick Start (cURL)

### Single URL
```bash
curl -s "https://api.search.brave.com/res/v1/content/search?urls=https://brave.com" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Multiple URLs
```bash
curl -s "https://api.search.brave.com/res/v1/content/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "urls=https://brave.com" \
  --data-urlencode "urls=https://search.brave.com" \
  --data-urlencode "mode=clean"
```

### Raw HTML Mode
```bash
curl -s "https://api.search.brave.com/res/v1/content/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "urls=https://example.com" \
  --data-urlencode "mode=raw"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/content/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `urls` | string[] | **required** | 1-10 URLs | URLs to fetch content from |
| `mode` | string | "clean" | clean/raw | Content processing mode |
| `search_lang` | string | "en" | 2+ char code | Language preference |
| `country` | string | "US" | 2-char code | Country for geo-targeted content |

### Mode Values

| Mode | Description |
|------|-------------|
| `clean` | HTML with unnecessary tags removed (navigation, ads, etc.) |
| `raw` | Raw HTML as fetched from the page |

## Response Format

```json
{
  "type": "content",
  "results": [
    {
      "url": "https://brave.com",
      "title": "Brave Browser | The browser that puts you first",
      "content": "<article>Main page content here...</article>",
      "fetched_at": "2024-01-15T10:30:00Z",
      "status": 200
    },
    {
      "url": "https://search.brave.com",
      "title": "Brave Search",
      "content": "<article>Search content here...</article>",
      "fetched_at": "2024-01-15T10:30:01Z",
      "status": 200
    }
  ]
}
```

**Key Fields:**
- `content`: Page content (clean or raw HTML based on mode)
- `title`: Page title
- `fetched_at`: When the content was retrieved
- `status`: HTTP status code from the fetch

### Error Handling

URLs that fail to fetch return with an error status:
```json
{
  "url": "https://example.com/404",
  "status": 404,
  "error": "Page not found"
}
```

## Notes

- **Timeout**: Recommended 60s (fetching can be slow)
- **Rate limits**: Check your API plan
- **URL limit**: Maximum 10 URLs per request
- **Clean mode**: Removes navigation, sidebars, footers, ads
- **Caching**: Results may be cached; freshness depends on Brave's crawl schedule

## Related Skills

- `grounding-context`: Search + content in one call (recommended for AI)
- `web-search`: Search for URLs first, then fetch content
