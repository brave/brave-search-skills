---
name: llm-context
description: USE FOR RAG/LLM grounding. Returns pre-extracted web content (text, tables, code) optimized for LLMs. GET + POST. Adjust max_tokens/count based on complexity. Supports Goggles, local/POI. For AI answers use answers.
---

# LLM Context

> **Requires API Key**: Get one at https://api.search.brave.com
> **Plan**: Available in the **Search** plan. See https://api.search.brave.com/app/subscriptions for details.

Brave LLM Context API delivers pre-extracted, relevance-ranked web content optimized for grounding LLM responses in real-time search results. Unlike traditional web search APIs that return links and snippets, LLM Context extracts the actual page content—text chunks, tables, code blocks, and structured data—so your LLM or AI agent can reason over it directly.

## LLM Context vs AI Grounding

| Feature | LLM Context (this) | AI Grounding (`answers`) |
|---------|---------------------|--------------------------------------|
| Output | Raw extracted content for YOUR LLM | End-to-end AI answers with citations |
| Interface | REST API (GET/POST) | OpenAI-compatible `/chat/completions` |
| Searches | Single search per request | Multi-search (iterative research) |
| Speed | Fast (<1s) | Slower |
| Plan | Search | Answers |
| Endpoint | `/res/v1/llm/context` | `/res/v1/chat/completions` |
| Best for | AI agents, RAG pipelines, tool calls | Chat interfaces, research mode |

## Endpoint

```http
GET  https://api.search.brave.com/res/v1/llm/context
POST https://api.search.brave.com/res/v1/llm/context
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

**Optional Headers**:
- `Accept-Encoding: gzip` — Enable gzip compression

## Quick Start

### GET Request
```bash
curl -s "https://api.search.brave.com/res/v1/llm/context?q=tallest+mountains+in+the+world" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### POST Request (JSON body)
```bash
curl -s --compressed -X POST "https://api.search.brave.com/res/v1/llm/context" \
  -H "accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"q": "tallest mountains in the world"}'
```

### With Goggles (Inline)
```bash
curl -s "https://api.search.brave.com/res/v1/llm/context" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=rust programming" \
  --data-urlencode 'goggles=$discard
$site=docs.rs
$site=rust-lang.org'
```

## Parameters

### Query Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `q` | string | **required** | 1-400 chars, max 50 words | Search query |
| `country` | string | `us` | 2-char code | Country for search results |
| `search_lang` | string | `en` | 2+ char code | Language preference |
| `count` | int | `20` | 1-50 | Max search results to consider |

### Context Size Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `maximum_number_of_urls` | int | `20` | 1-50 | Max URLs in response |
| `maximum_number_of_tokens` | int | `8192` | 1024-32768 | Approximate max tokens in context |
| `maximum_number_of_snippets` | int | `50` | 1-100 | Max snippets across all URLs |
| `maximum_number_of_tokens_per_url` | int | `4096` | 512-8192 | Max tokens per individual URL |
| `maximum_number_of_snippets_per_url` | int | `50` | 1-100 | Max snippets per individual URL |

### Filtering & Local Parameters

| Parameter | Type | Default | Options | Description |
|-----------|------|---------|---------|-------------|
| `context_threshold_mode` | string | `balanced` | `strict`/`balanced`/`lenient` | Relevance threshold for including content |
| `enable_local` | bool | `null` | `true`/`false`/`null` | Local recall control (see below) |
| `goggles` | string/list | `null` | - | Goggle URL or inline definition for custom re-ranking |

## Context Size Guidelines

| Task Type | count | max_tokens | Example |
|-----------|-------|------------|---------|
| Simple factual | 5 | 2048 | "What year was Python created?" |
| Standard queries | 20 | 8192 | "Best practices for React hooks" |
| Complex research | 50 | 16384 | "Compare AI frameworks for production" |

Larger context windows provide more information but increase latency and cost (of your inference). Start with defaults and adjust.

## Threshold Modes

| Mode | Behavior |
|------|----------|
| `strict` | Higher threshold — fewer but more relevant results |
| `balanced` | Default — good balance between coverage and relevance |
| `lenient` | Lower threshold — more results, may include less relevant content |

## Local Recall

The `enable_local` parameter controls location-aware recall:

| Value | Behavior |
|-------|----------|
| `null` (not set) | **Auto-detect** — local recall enabled when any location header is provided |
| `true` | **Force local** — always use local recall, even without location headers |
| `false` | **Force standard** — always use standard web ranking, even with location headers |

For most use cases, omit `enable_local` and let the API auto-detect from location headers.

## Location Headers

| Header | Type | Description |
|--------|------|-------------|
| `X-Loc-Lat` | float | Latitude (-90.0 to 90.0) |
| `X-Loc-Long` | float | Longitude (-180.0 to 180.0) |
| `X-Loc-City` | string | City name |
| `X-Loc-State` | string | State/region code (ISO 3166-2) |
| `X-Loc-State-Name` | string | State/region name |
| `X-Loc-Country` | string | 2-letter country code |
| `X-Loc-Postal-Code` | string | Postal code |

### Example: With Coordinates
```bash
curl -s "https://api.search.brave.com/res/v1/llm/context" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -H "X-Loc-Lat: 37.7749" \
  -H "X-Loc-Long: -122.4194" \
  -G \
  --data-urlencode "q=best coffee shops near me"
```

### Example: With Place Name
```bash
curl -s "https://api.search.brave.com/res/v1/llm/context" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -H "X-Loc-City: San Francisco" \
  -H "X-Loc-State: CA" \
  -H "X-Loc-Country: US" \
  -G \
  --data-urlencode "q=best coffee shops near me"
```

## Goggles (Custom Source Ranking)

Goggles let you **control which sources ground your LLM** — essential for RAG quality.

### RAG Use Cases

| Use Case | Goggle Rules |
|----------|--------------|
| Official docs only | `$discard\n$site=docs.python.org` |
| Exclude user content | `$discard,site=reddit.com\n$discard,site=stackoverflow.com` |
| Academic sources | `$discard\n$site=arxiv.org\n$site=.edu` |
| No paywalls | `$discard,site=medium.com` |

### Two Ways to Apply

#### 1. Hosted Goggle (URL)

Goggles need to first be registered at: https://search.brave.com/goggles/create.

```bash
--data-urlencode "goggles=https://raw.githubusercontent.com/user/goggles/main/official-docs.goggle"
```

#### 2. Inline Rules (No Hosting)
```bash
--data-urlencode 'goggles=$discard
$site=docs.python.org
$site=rust-lang.org'
```

### Syntax Quick Reference

| Action | Syntax | Effect |
|--------|--------|--------|
| Discard | `$discard` | Remove from results |
| Site match | `$site=example.com` | Boost results from this domain |
| Boost | `$boost=2` | Rank higher |
| Downrank | `$downrank` | Rank lower |

### Resources
- **Discover goggles**: https://search.brave.com/goggles/discover
- **Syntax guide**: https://search.brave.com/help/goggles
- **Quickstart**: https://github.com/brave/goggles-quickstart

## Response Format

### Standard Response

```json
{
  "grounding": {
    "generic": [
      {
        "url": "https://example.com/page",
        "title": "Page Title",
        "snippets": [
          "Relevant text chunk extracted from the page...",
          "Another relevant passage from the same page..."
        ]
      }
    ],
    "map": []
  },
  "sources": {
    "https://example.com/page": {
      "title": "Page Title",
      "hostname": "example.com",
      "age": ["Monday, January 15, 2024", "2024-01-15", "380 days ago"]
    }
  }
}
```

### Local Response (with `enable_local`)

```json
{
  "grounding": {
    "generic": [...],
    "poi": {
      "name": "Business Name",
      "url": "https://business.com",
      "title": "Title of business.com website",
      "snippets": ["Business details and information..."]
    },
    "map": [
      {
        "name": "Place Name",
        "url": "https://place.com",
        "title": "Title of place.com website",
        "snippets": ["Place information and details..."]
      }
    ]
  },
  "sources": {
    "https://business.com": {
      "title": "Business Name",
      "hostname": "business.com",
      "age": null
    }
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `grounding` | object | Container for all grounding content by type |
| `grounding.generic` | array | Array of URL objects with extracted content (main grounding data) |
| `grounding.generic[].url` | string | Source URL |
| `grounding.generic[].title` | string | Page title |
| `grounding.generic[].snippets` | array | Extracted smart chunks relevant to the query |
| `grounding.poi` | object/null | Point of interest data (only with local recall) |
| `grounding.poi.name` | string/null | Point of interest name |
| `grounding.poi.url` | string/null | POI source URL |
| `grounding.poi.title` | string/null | POI page title |
| `grounding.poi.snippets` | array/null | POI text snippets |
| `grounding.map` | array | Map/place results (only with local recall) |
| `grounding.map[].name` | string/null | Place name |
| `grounding.map[].url` | string/null | Place source URL |
| `grounding.map[].title` | string/null | Place page title |
| `grounding.map[].snippets` | array/null | Place text snippets |
| `sources` | object | Metadata for all referenced URLs, keyed by URL |
| `sources[url].title` | string | Page title |
| `sources[url].hostname` | string | Source hostname |
| `sources[url].age` | array/null | Page modification dates (when available) |

**Note**: Snippets may contain plain text OR JSON-serialized structured data (tables, schemas, code blocks). LLMs handle this mixed format well.

## Use Cases

- **AI Agents**: Give your agent a web search tool that returns ready-to-use content in a single call
- **RAG Pipelines**: Ground LLM responses in fresh, relevant web content
- **AI Assistants & Chatbots**: Provide factual answers backed by real sources
- **Question Answering**: Retrieve focused context for specific queries
- **Fact Checking**: Verify claims against current web content
- **Content Research**: Gather source material on any topic with one API call

## Best Practices

- **Token budget**: Start with defaults (`maximum_number_of_tokens=8192`, `count=20`). Reduce for simple lookups, increase for complex research.
- **Source quality**: Use Goggles to restrict to trusted sources. Set `context_threshold_mode=strict` when precision > recall.
- **Performance**: Use smallest `count` and `maximum_number_of_tokens` that meet your needs. For local queries, provide location headers.
