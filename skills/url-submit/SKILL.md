---
name: url-submit
description: USE FOR submitting URLs to Brave's index via Search API. Request crawling of new or updated pages. Returns 204 on success. Requires BRAVE_SEARCH_API_KEY.
---

# URL Submit (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **URL submission** when you need:
- Request indexing of new pages
- Notify Brave of updated content
- Submit URLs for crawling

**Note**: Submission is a request, not a guarantee of indexing. Brave's crawler will evaluate and index pages based on quality and relevance.

## Quick Start (cURL)

### Submit Single URL
```bash
curl -X POST "https://api.search.brave.com/res/v1/url/submit" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{"urls": ["https://example.com/new-page"]}'
```

### Submit Multiple URLs
```bash
curl -X POST "https://api.search.brave.com/res/v1/url/submit" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "urls": [
      "https://example.com/page1",
      "https://example.com/page2",
      "https://example.com/page3"
    ]
  }'
```

## Endpoint

```http
POST https://api.search.brave.com/res/v1/url/submit
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

**Content-Type**: `application/json`

## Request Body

```json
{
  "urls": [
    "https://example.com/page1",
    "https://example.com/page2"
  ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `urls` | string[] | Yes | Array of URLs to submit for indexing |

### URL Requirements

- Must be valid HTTP/HTTPS URLs
- Must be publicly accessible
- Should not be blocked by robots.txt for Brave
- Maximum URLs per request: Check your API plan limits

## Response

### Success (204 No Content)
```text
HTTP/1.1 204 No Content
```

A 204 response indicates the URLs were successfully submitted for crawling.

### Error Responses

```json
{
  "type": "error",
  "code": "INVALID_URL",
  "message": "One or more URLs are invalid"
}
```

| Code | Description |
|------|-------------|
| `INVALID_URL` | One or more URLs are malformed |
| `RATE_LIMITED` | Too many requests |
| `QUOTA_LIMITED` | Monthly quota exceeded |
| `OPTION_NOT_IN_PLAN` | URL submit not in your plan |

## Example with Error Handling

```bash
#!/bin/bash

RESPONSE=$(curl -s -w "\n%{http_code}" -X POST "https://api.search.brave.com/res/v1/url/submit" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{"urls": ["https://example.com/new-content"]}')

HTTP_CODE=$(echo "$RESPONSE" | tail -n1)
BODY=$(echo "$RESPONSE" | sed '$d')

if [ "$HTTP_CODE" = "204" ]; then
    echo "URLs submitted successfully"
elif [ "$HTTP_CODE" = "429" ]; then
    echo "Rate limited - try again later"
else
    echo "Error ($HTTP_CODE): $BODY"
fi
```

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **Plan requirement**: Requires `url_submit` option in your plan
- **No guarantee**: Submission doesn't guarantee indexing
- **Crawl timing**: Brave determines when to crawl submitted URLs
- **Duplicate handling**: Re-submitting existing URLs is safe (triggers re-crawl request)

## Best Practices

1. **Submit new content**: Best for pages Brave hasn't seen before
2. **Submit updated content**: Helps notify Brave of significant updates
3. **Batch submissions**: Submit multiple URLs in one request
4. **Quality content**: Focus on submitting quality, useful pages
5. **Respect rate limits**: Don't spam submissions

## Related Skills

- `web-search`: Search to verify if URLs are indexed
- `content`: Extract content from URLs
