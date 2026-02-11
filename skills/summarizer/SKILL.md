---
name: summarizer
description: USE FOR AI-powered search summaries via Search API. Two-step process - first web search with summary=1, then poll summarizer endpoint with returned key. Returns AI-generated answers with citations. Requires BRAVE_SEARCH_API_KEY.
---

# Summarizer (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **AI-generated summaries** when you need:
- AI answers based on search results
- Summaries with inline citations
- Follow-up question suggestions
- Entity information enrichments

**For research with multiple iterations**, use `deep-research` instead.
**For pre-extracted context**, use `grounding-context`.

## How It Works

The summarizer uses a **two-step process**:

1. **Search**: Call web search with `summary=true` to get a summary key
2. **Poll**: Use the summary key to retrieve the AI-generated summary

## Quick Start (cURL)

### Step 1: Search with Summary Key
```bash
curl -s "https://api.search.brave.com/res/v1/web/search" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=what causes northern lights" \
  --data-urlencode "summary=true"
```

Response includes:
```json
{
  "summarizer": {
    "key": "summarizer_key_abc123..."
  },
  "web": { ... }
}
```

### Step 2: Get Summary
```bash
curl -s "https://api.search.brave.com/res/v1/summarizer/search?key=summarizer_key_abc123..." \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

## Endpoints

### Web Search (with summary)
```http
GET https://api.search.brave.com/res/v1/web/search?summary=true
```

### Summarizer Search
```http
GET https://api.search.brave.com/res/v1/summarizer/search?key=<KEY>
```

### Summarizer Summary (detailed)
```http
GET https://api.search.brave.com/res/v1/summarizer/summary?key=<KEY>
```

### Summarizer Enrichments
```http
GET https://api.search.brave.com/res/v1/summarizer/enrichments?key=<KEY>
```

### Summarizer Follow-ups
```http
GET https://api.search.brave.com/res/v1/summarizer/followups?key=<KEY>
```

### Summarizer Entity Info
```http
GET https://api.search.brave.com/res/v1/summarizer/entity_info?key=<KEY>
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header (all endpoints)

## Summarizer Search Response

```json
{
  "type": "summarizer",
  "status": "complete",
  "title": "What Causes the Northern Lights",
  "summary": [
    {
      "type": "token",
      "data": "The northern lights, or aurora borealis, are caused by "
    },
    {
      "type": "token",
      "data": "charged particles from the sun colliding with gases in Earth's atmosphere",
      "references": [1, 2]
    },
    {
      "type": "token",
      "data": ". When solar wind particles reach Earth..."
    }
  ],
  "references": [
    {
      "index": 1,
      "url": "https://example.com/aurora-science",
      "title": "The Science of Auroras"
    },
    {
      "index": 2,
      "url": "https://example.com/northern-lights",
      "title": "Northern Lights Explained"
    }
  ],
  "followups": [
    "Where is the best place to see northern lights?",
    "When is the best time to see aurora borealis?",
    "What colors can the northern lights be?"
  ]
}
```

**Key Fields:**
- `status`: "complete", "processing", or "failed"
- `summary`: Array of tokens with optional reference indices
- `references`: Cited sources
- `followups`: Suggested follow-up questions

## Complete Workflow Example

```bash
#!/bin/bash

# Step 1: Search with summary enabled
SEARCH_RESULT=$(curl -s "https://api.search.brave.com/res/v1/web/search?q=how+does+photosynthesis+work&summary=true" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}")

# Extract summary key
SUMMARY_KEY=$(echo $SEARCH_RESULT | jq -r '.summarizer.key')

# Step 2: Poll for summary (may need to retry if status is "processing")
curl -s "https://api.search.brave.com/res/v1/summarizer/search?key=$SUMMARY_KEY" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

## Polling for Completion

The summary may take time to generate. Poll until `status` is `complete`:

```bash
while true; do
  RESULT=$(curl -s "https://api.search.brave.com/res/v1/summarizer/search?key=$SUMMARY_KEY" \
    -H "Accept: application/json" \
    -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}")

  STATUS=$(echo $RESULT | jq -r '.status')

  if [ "$STATUS" = "complete" ]; then
    echo $RESULT | jq '.summary'
    break
  elif [ "$STATUS" = "failed" ]; then
    echo "Summary generation failed"
    break
  fi

  sleep 1
done
```

## Notes

- **Timeout**: Recommended 120s for full workflow
- **Rate limits**: Check your API plan
- **Plan requirement**: Requires `summarizer` option in your plan
- **Polling**: Summary generation is async; poll until complete
- **Key expiry**: Summary keys expire after a short time

## Related Skills

- `grounding-context`: Pre-extracted context (faster, no polling)
- `deep-research`: Multi-iteration research
- `web-search`: Base search endpoint
