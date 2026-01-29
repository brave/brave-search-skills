---
name: sapi-local-pois
description: USE FOR getting local business/POI details via Search API. Returns full business information including ratings, hours, contact info. Pass POI IDs from web search results. Requires BRAVE_SEARCH_API_KEY.
---

# Local POIs (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`

## When to Use This Skill

Use for **local business details** when you need:
- Full POI (Point of Interest) information
- Business hours, ratings, contact details
- Address and location data

**Workflow**: First search with `sapi-web-search` to get POI IDs from location results, then use this endpoint to get full details.

## Quick Start (cURL)

### Get POI Details
```bash
curl -s "https://api.search.brave.com/res/v1/local/pois?ids=loc4FNMQJNOOCVHEB7UBOLN354ZYIDIYJ3RPRETERRY=" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Multiple POIs with Location Headers
```bash
curl -s "https://api.search.brave.com/res/v1/local/pois" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -H "X-Loc-Lat: 37.7749" \
  -H "X-Loc-Long: -122.4194" \
  -G \
  --data-urlencode "ids=loc4FNMQJNOOCVHEB7UBOLN354ZYIDIYJ3RPRETERRY=" \
  --data-urlencode "ids=loc4ABCDEF123456789=" \
  --data-urlencode "units=imperial"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/local/pois
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `ids` | string[] | **required** | 1-20 IDs | POI IDs from search results |
| `search_lang` | string | "en" | 2+ char code | Language preference |
| `ui_lang` | string | "en-US" | locale code | UI language |
| `units` | string | null | metric/imperial | Measurement units |

### Location Headers (Optional)

| Header | Type | Range | Description |
|--------|------|-------|-------------|
| `X-Loc-Lat` | float | -90.0 to 90.0 | User latitude |
| `X-Loc-Long` | float | -180.0 to 180.0 | User longitude |

## Response Format

```json
{
  "type": "local_pois",
  "results": [
    {
      "id": "loc4FNMQJNOOCVHEB7UBOLN354ZYIDIYJ3RPRETERRY=",
      "name": "Cafe Example",
      "address": {
        "street": "123 Main Street",
        "city": "San Francisco",
        "state": "CA",
        "postal_code": "94102",
        "country": "US"
      },
      "phone": "+1 415-555-0123",
      "website": "https://cafe-example.com",
      "rating": {
        "value": 4.5,
        "count": 234,
        "provider": "Google"
      },
      "hours": {
        "monday": "7:00 AM - 9:00 PM",
        "tuesday": "7:00 AM - 9:00 PM",
        "wednesday": "7:00 AM - 9:00 PM",
        "thursday": "7:00 AM - 9:00 PM",
        "friday": "7:00 AM - 10:00 PM",
        "saturday": "8:00 AM - 10:00 PM",
        "sunday": "8:00 AM - 8:00 PM"
      },
      "categories": ["Cafe", "Coffee Shop", "Breakfast"],
      "price_range": "$$",
      "coordinates": {
        "latitude": 37.7749,
        "longitude": -122.4194
      },
      "distance": "0.3 mi"
    }
  ]
}
```

**Key Fields:**
- `id`: POI identifier (valid for 8 hours)
- `rating`: Average rating and review count
- `hours`: Operating hours by day
- `categories`: Business categories
- `price_range`: Price indicator ($, $$, $$$, $$$$)
- `distance`: Distance from user (if location headers provided)

## Getting POI IDs

POI IDs come from web search results with location data:

```bash
# 1. First, search for local businesses
curl -s "https://api.search.brave.com/res/v1/web/search?q=coffee+shops+near+me&result_filter=locations" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -H "X-Loc-Lat: 37.7749" \
  -H "X-Loc-Long: -122.4194"

# 2. Extract POI IDs from locations.results[].id
# 3. Use those IDs with this endpoint
```

## Notes

- **Timeout**: Recommended 30s
- **Rate limits**: Check your API plan
- **ID validity**: POI IDs expire after 8 hours
- **Plan requirement**: Requires `locations` option in your plan
- **Units**: Set `units=metric` for km/celsius, `units=imperial` for miles/fahrenheit

## Related Skills

- `sapi-local-descriptions`: Get text descriptions for POIs
- `sapi-web-search`: Search for POIs (use `result_filter=locations`)
- `brave-dev-local-places`: Internal local search (VPN required)
- `brave-dev-local-poi`: Internal POI details (VPN required)
