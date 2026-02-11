---
name: news-search
description: USE FOR news search. Returns news articles with title, URL, description, age, meta_url, thumbnail. Supports freshness and date range filtering, Goggles for custom ranking.
---

# News Search

> **Requires API Key**: Get one at https://api.search.brave.com
> **Plan**: Available in the **Search** plan. See https://api.search.brave.com/app/subscriptions for details.

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
POST https://api.search.brave.com/res/v1/news/search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

**Note**: Both GET and POST are supported. POST is useful for long queries or complex Goggles.

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Search query |
| `country` | string | "US" | 2-char code or "ALL" | Search country ("ALL" for worldwide) |
| `search_lang` | string | "en" | 2+ char code | Language preference |
| `ui_lang` | string | "en-US" | locale code | UI language |
| `count` | int | 20 | 1-50 | Number of results |
| `offset` | int | 0 | 0-9 | Page offset |
| `safesearch` | string | "strict" | off/moderate/strict | Adult content filter |
| `freshness` | string | "" | pd/pw/pm/py or date range | Time filter |
| `spellcheck` | bool | true | - | Auto-correct query |
| `extra_snippets` | bool | null | - | Up to 5 additional excerpts per result (requires a plan that includes extra snippets) |
| `goggles` | string or array | null | URL or inline | Custom ranking filter (requires plan with Goggles; repeat param for multiple) |
| `operators` | bool | true | - | Apply search operators |
| `include_fetch_metadata` | bool | false | - | Include fetch timestamps in results |

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
    "original": "artificial intelligence"
  },
  "results": [
    {
      "type": "news_result",
      "title": "New AI Breakthrough Announced",
      "url": "https://news.example.com/ai-breakthrough",
      "description": "Researchers have announced a major advancement in...",
      "age": "2 hours ago",
      "page_age": "2024-01-15T14:30:00",
      "page_fetched": "2024-01-15T15:00:00Z",
      "meta_url": {
        "scheme": "https",
        "netloc": "news.example.com",
        "hostname": "news.example.com",
        "favicon": "https://imgs.search.brave.com/favicon/news.example.com",
        "path": "/ai-breakthrough"
      },
      "thumbnail": {
        "src": "https://imgs.search.brave.com/..."
      }
    }
  ]
}
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Always `"news"` |
| `query.original` | string | The original search query |
| `query.altered` | string? | Spellcheck-corrected query (if changed) |
| `query.cleaned` | string? | Cleaned/normalized query from spellchecker |
| `query.spellcheck_off` | bool? | Whether spellcheck was disabled |
| `query.show_strict_warning` | bool? | True if strict safesearch blocked results |
| `query.search_operators` | object? | Applied search operators |
| `query.search_operators.applied` | bool | Whether operators were applied |
| `query.search_operators.cleaned_query` | string? | Query after operator processing |
| `query.search_operators.sites` | list[str]? | Domains from `site:` operators |
| `results[].type` | string | Always `"news_result"` |
| `results[].title` | string | Article title |
| `results[].url` | string | Source URL of the article |
| `results[].description` | string? | Article description/summary |
| `results[].age` | string? | Human-readable age (e.g. "2 hours ago") |
| `results[].page_age` | string? | Publication date from source (ISO datetime) |
| `results[].page_fetched` | string? | When page was last fetched (ISO datetime) |
| `results[].fetched_content_timestamp` | int? | Fetch timestamp (only with `include_fetch_metadata=true`) |
| `results[].meta_url.scheme` | string? | URL protocol scheme |
| `results[].meta_url.netloc` | string? | Network location |
| `results[].meta_url.hostname` | string? | Lowercased domain name |
| `results[].meta_url.favicon` | string? | Favicon URL |
| `results[].meta_url.path` | string? | URL path |
| `results[].thumbnail.src` | string | Served thumbnail URL |
| `results[].thumbnail.original` | string? | Original thumbnail URL |
| `results[].extra_snippets` | list[str]? | Up to 5 additional excerpts per result |

## Goggles (Custom Ranking) - Unique to Brave

Goggles let you **customize which news sources appear** - essential for balanced coverage.

| Use Case | Goggle Rules |
|----------|--------------|
| Boost wire services | `$site=reuters.com,boost=2` + `$site=apnews.com,boost=2` |
| Quality journalism only | `$discard` + `$site=bbc.com` + `$site=nytimes.com` + `$site=reuters.com` |
| No tabloids | `$discard,site=dailymail.co.uk` + `$discard,site=thesun.co.uk` |
| Local news focus | `$site=local-paper.com,boost=3` |

### How to Apply

**Hosted Goggle** — any HTTPS URL (GitHub raw URLs, self-hosted, or Brave-hosted):
```bash
--data-urlencode "goggles=https://raw.githubusercontent.com/user/goggles/main/quality-news.goggle"
```

**Inline Rules** — prefix lines with `$`:
```bash
--data-urlencode 'goggles=$site=reuters.com,boost=2
$site=apnews.com,boost=2
$discard,site=buzzfeed.com'
```

**Multiple Goggles** — pass the parameter multiple times:
```bash
--data-urlencode "goggles=https://example.com/goggle1.goggle" \
--data-urlencode "goggles=https://example.com/goggle2.goggle"
```

### Syntax Reference

| Action | Syntax | Effect |
|--------|--------|--------|
| Boost | `$boost=2` | Rank higher (1-10) |
| Downrank | `$downrank` | Rank lower |
| Discard | `$discard` | Remove completely |
| Site filter | `$site=bbc.com` | Target domain |

- **Discover goggles**: https://search.brave.com/goggles/discover
- **Syntax guide**: https://search.brave.com/help/goggles

## Search Operators

Use search operators to refine results:
- `site:bbc.com` - Limit to specific news site
- `"exact phrase"` - Match exact phrase
- `-exclude` - Exclude term

Set `operators=false` to disable operator parsing.

## Use Cases

- **Breaking news monitoring**: Use `freshness=pd` for the most recent articles on a topic.
- **Custom news feeds with Goggles**: Boost trusted sources (e.g. Reuters, AP) and discard tabloids — unique to Brave.
- **Historical news research**: Use `freshness=YYYY-MM-DDtoYYYY-MM-DD` to find articles from specific time periods.
- **Multilingual news**: Combine `country`, `search_lang`, and `ui_lang` for cross-locale results.
- **Data pipelines**: Set `include_fetch_metadata=true` for `fetched_content_timestamp` on each result.

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Per API plan (1-second sliding window)
- **SafeSearch**: Defaults to `strict`
- **Pagination**: Use `offset` (0-9) with `count`
- **Extra snippets**: Up to 5 additional excerpts when `extra_snippets=true`
