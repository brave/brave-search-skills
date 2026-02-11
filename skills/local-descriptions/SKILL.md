---
name: local-descriptions
description: USE FOR getting POI text descriptions via Search API. Returns detailed business descriptions for local places. Chain after local-pois for full details. Requires BRAVE_SEARCH_API_KEY.
---

# Local Descriptions (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **POI descriptions** when you need:
- Detailed text descriptions of businesses
- Additional context about local places
- Supplementary information beyond basic POI data

**Workflow**: Use with `local-pois` for complete local business information.

## Quick Start (cURL)

### Get POI Description
```bash
curl -s "https://api.search.brave.com/res/v1/local/descriptions?ids=loc4FNMQJNOOCVHEB7UBOLN354ZYIDIYJ3RPRETERRY=" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Multiple POIs
```bash
curl -s "https://api.search.brave.com/res/v1/local/descriptions" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "ids=loc4FNMQJNOOCVHEB7UBOLN354ZYIDIYJ3RPRETERRY=" \
  --data-urlencode "ids=loc4ABCDEF123456789="
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/local/descriptions
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `ids` | string[] | **required** | 1-20 IDs | POI IDs from search results |

## Response Format

```json
{
  "type": "local_descriptions",
  "results": [
    {
      "id": "loc4FNMQJNOOCVHEB7UBOLN354ZYIDIYJ3RPRETERRY=",
      "description": "A cozy neighborhood cafe known for its artisanal coffee and homemade pastries. Founded in 2015, this family-owned establishment sources beans from local roasters and offers a rotating menu of seasonal specialties. Popular for remote work with free WiFi and plenty of outlets.",
      "source": "Business Profile"
    }
  ]
}
```

**Key Fields:**
- `id`: POI identifier matching the request
- `description`: Detailed text description of the business
- `source`: Where the description originated

## Getting POI IDs

POI IDs come from web search results with location data:

```bash
# 1. Search for local businesses
curl -s "https://api.search.brave.com/res/v1/web/search?q=restaurants+san+francisco&result_filter=locations" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"

# 2. Extract POI IDs from locations.results[].id
# 3. Use those IDs with local/pois and local/descriptions
```

## Complete Local Workflow

```bash
# Step 1: Search for local businesses
SEARCH_RESULT=$(curl -s "https://api.search.brave.com/res/v1/web/search?q=coffee+shops+downtown" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}")

# Step 2: Extract POI ID (example with jq)
POI_ID=$(echo $SEARCH_RESULT | jq -r '.locations.results[0].id')

# Step 3: Get full POI details
curl -s "https://api.search.brave.com/res/v1/local/pois?ids=$POI_ID" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"

# Step 4: Get description
curl -s "https://api.search.brave.com/res/v1/local/descriptions?ids=$POI_ID" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **ID validity**: POI IDs expire after 8 hours
- **Plan requirement**: Requires `locations` option in your plan
- **Availability**: Not all POIs have descriptions

## Related Skills

- `local-pois`: Get POI details (hours, ratings, address)
- `web-search`: Search for POIs (use `result_filter=locations`)
