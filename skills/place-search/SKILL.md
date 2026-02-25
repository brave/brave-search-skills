---
name: place-search
description: USE FOR searching places/POIs by query and location. Returns businesses with ratings, hours, contact, address, coordinates. Accepts coordinates or location string. Up to 50 results. Single-step — no prior web search needed.
---

# Place Search

> **Requires API Key**: Get one at https://api.search.brave.com
>
> **Plan**: Included in the **Search** plan. See https://api.search.brave.com/app/subscriptions
>
> **Single-step**: Unlike `local-pois` and `local-descriptions`, this endpoint does **not** require POI IDs from a prior web search. Search directly by query and location.

## Quick Start (cURL)

### Basic Search
```bash
curl -s "https://api.search.brave.com/res/v1/local/place_search?q=coffee+shops&latitude=37.7749&longitude=-122.4194&radius=1000" \
  -H "Accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### With Location String
```bash
curl -s "https://api.search.brave.com/res/v1/local/place_search" \
  -H "Accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=sushi restaurants" \
  --data-urlencode "location=san francisco ca united states" \
  --data-urlencode "count=10" \
  --data-urlencode "units=imperial"
```

### Browse POIs in an Area (No Query)
```bash
curl -s "https://api.search.brave.com/res/v1/local/place_search?latitude=48.8566&longitude=2.3522&radius=500" \
  -H "Accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/local/place_search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

| Parameter | Type | Required | Default | Description |
|--|--|--|--|--|
| `q` | string | No | `""` | Query for POIs in an area. If omitted, returns general POIs in the given area. |
| `latitude` | float | No | — | Latitude (-90.0 to 90.0). Use with `longitude`. |
| `longitude` | float | No | — | Longitude (-180.0 to 180.0). Use with `latitude`. |
| `location` | string | No | — | Location string, alternative to lat/long (see format below) |
| `radius` | float | No | `10000` | Search radius in meters (0–20000) |
| `count` | int | No | `20` | Results to return (1–50) |
| `country` | string | No | `US` | Two-letter country code (ISO 3166-1 alpha-2) or `ALL` |
| `search_lang` | string | No | `en` | Language for results (2+ char language code) |
| `ui_lang` | string | No | `en-US` | UI language for response (locale code, e.g., `en-US`) |
| `units` | string | No | `metric` | `metric` (km) or `imperial` (miles) |
| `safesearch` | string | No | `strict` | `off`, `moderate`, or `strict` |
| `spellcheck` | bool | No | `true` | Auto-correct query before searching |
| `geoloc` | string | No | — | Optional geolocation token to refine results |

### Location String Format

The `location` parameter is an alternative to `latitude`/`longitude`:

- **US locations**: `<city> <state> <country name>` — e.g., `san francisco ca united states`
- **Non-US locations**: `<city> <country name>` — e.g., `tokyo japan`
- No commas or special characters needed; capitalization does not matter
- Multiple languages supported (`nueva york` works, but English or the local dominant language works best)

## Response Format

```json
{
  "type": "locations",
  "query": {
    "original": "coffee shops",
    "altered": null,
    "spellcheck_off": false,
    "show_strict_warning": false
  },
  "results": [
    {
      "type": "location_result",
      "title": "Blue Bottle Coffee",
      "url": "https://yelp.com/biz/blue-bottle-coffee-sf",
      "provider_url": "https://yelp.com/biz/blue-bottle-coffee-sf",
      "id": "loc4CQWMJWLD4VBEBZ62XQLJTGK6YCJEEJDNAAAAAAA=",
      "description": "Specialty coffee roaster and cafe",
      "postal_address": {
        "type": "PostalAddress",
        "displayAddress": "66 Mint St, San Francisco, CA 94103",
        "streetAddress": "66 Mint St",
        "addressLocality": "San Francisco",
        "addressRegion": "CA",
        "postalCode": "94103",
        "country": "US"
      },
      "contact": { "telephone": "+1 510-653-3394" },
      "thumbnail": {
        "src": "https://example.com/thumb.jpg",
        "original": "https://example.com/original.jpg"
      },
      "rating": {
        "ratingValue": 4.3,
        "bestRating": 5.0,
        "reviewCount": 587
      },
      "opening_hours": {
        "current_day": [
          { "abbr_name": "Wed", "full_name": "Wednesday", "opens": "07:00", "closes": "18:00" }
        ]
      },
      "coordinates": [37.7821, -122.4065],
      "distance": { "value": 0.8, "units": "km" },
      "categories": ["Coffee & Tea"],
      "price_range": "$$",
      "timezone": "America/Los_Angeles"
    }
  ],
  "location": {
    "coordinates": [37.7749, -122.4194],
    "name": "San Francisco",
    "country": "US"
  }
}
```

## Response Fields

| Field | Type | Description |
|--|--|--|
| `type` | string | Always `"locations"` |
| `query.original` | string | Original query |
| `query.altered` | string? | Spellchecked query (null if no correction) |
| `query.spellcheck_off` | bool? | Whether spellcheck was disabled |
| `query.show_strict_warning` | bool? | True if strict safesearch hid relevant results |
| `location.coordinates` | [float, float] | Resolved `[latitude, longitude]` for the search area |
| `location.name` | string? | Resolved location name |
| `location.country` | string? | Two-letter country code of resolved location |
| `results[]` | array | List of location results |
| `results[].type` | string | Always `"location_result"` |
| `results[].title` | string | Business/POI name |
| `results[].url` | string | Canonical URL for the location |
| `results[].provider_url` | string | Provider page URL |
| `results[].id` | string? | Temporary POI ID (valid ~8 hours), usable with `local-pois` and `local-descriptions` |
| `results[].description` | string? | Short description |
| `results[].coordinates` | [float, float]? | `[latitude, longitude]` |
| `results[].zoom_level` | int | Map zoom level (default 7) |
| `results[].postal_address.displayAddress` | string | Formatted display address |
| `results[].postal_address.streetAddress` | string? | Street address |
| `results[].postal_address.addressLocality` | string? | City |
| `results[].postal_address.addressRegion` | string? | State/region |
| `results[].postal_address.postalCode` | string? | Postal/ZIP code |
| `results[].postal_address.country` | string? | Country code |
| `results[].contact.telephone` | string? | Phone number |
| `results[].contact.email` | string? | Email address |
| `results[].rating.ratingValue` | float? | Average rating (≥0) |
| `results[].rating.bestRating` | float? | Max possible rating |
| `results[].rating.reviewCount` | int? | Number of reviews |
| `results[].rating.is_tripadvisor` | bool | Whether rating is from Tripadvisor |
| `results[].opening_hours.current_day` | object[]? | Today's hours (`abbr_name`, `full_name`, `opens`, `closes`) |
| `results[].opening_hours.days` | object[][]? | Hours for each day of the week |
| `results[].distance.value` | float? | Distance from search coordinates |
| `results[].distance.units` | string? | Distance unit (`km` or `miles`) |
| `results[].categories` | string[] | Business categories (default `[]`) |
| `results[].price_range` | string? | Price indicator (`$`, `$$`, `$$$`, `$$$$`) |
| `results[].serves_cuisine` | string[]? | Cuisine types (restaurants) |
| `results[].thumbnail.src` | string? | Thumbnail image URL |
| `results[].thumbnail.original` | string? | Original image URL |
| `results[].profiles` | object[]? | External profiles (`name`, `url`, `long_name`, `img`) |
| `results[].reviews` | object? | Aggregated reviews (`results`, `viewMoreUrl`, `reviews_in_foreign_language`) |
| `results[].pictures.results` | object[]? | Photo thumbnails |
| `results[].action` | object? | Action to take — has `type` (string) and `url` (string) |
| `results[].timezone` | string? | IANA timezone (e.g., `America/Los_Angeles`) |
| `results[].timezone_offset` | int? | UTC timezone offset |
| `results[].results` | object[]? | Related web results (`LocationWebResult` with `meta_url`) |

## Use Cases

- **Direct place discovery**: Search for businesses/POIs by name or category without a two-step web-search-then-local-pois flow. Ideal when you already know the location and want matching places.
- **Area browsing**: Omit `q` to explore general POIs around given coordinates — useful for travel apps, map views, or "what's nearby" features.
- **Location-string convenience**: Use `location` instead of coordinates when you have a city/region name but no lat/long — the API resolves coordinates for you.
- **Radius-scoped search**: Constrain results to a specific radius (up to 20 km) for precise geographic coverage.
- **Enrichment pipeline**: Use returned `id` fields with `local-pois` and `local-descriptions` for additional details and AI-generated descriptions.

## Notes

- **No query required**: Omit `q` to get general POIs in an area — useful for browsing
- **Location resolution**: Provide either `latitude`+`longitude` or `location` string — not both needed
- **Radius**: Default 10 km, max 20 km (20000 meters)
- **High volume**: Up to 50 results per request (vs 20 for web search)
- **SafeSearch**: Defaults to `strict`
- **IDs are ephemeral**: `results[].id` expires after ~8 hours — use promptly with `local-pois`/`local-descriptions` if needed
- **Cache control**: Set `Cache-Control: no-cache` header to bypass cached results
