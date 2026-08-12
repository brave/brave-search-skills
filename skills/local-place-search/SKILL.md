---
name: local-place-search
description: USE FOR finding places in the physical world - businesses, POIs, street addresses, cities and streets. Results carry address, coordinates, rating, opening hours and phone, so basic details need no follow-up call. Standalone - no POI IDs or prior web search required; the IDs it returns work with local-pois and local-descriptions. Locate by coordinates or a location string, or omit both to search globally. Omit the query to browse an area. Max 100 results.
---

# Place Search (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
>
> **Plan**: Included in the **Search** plan (with `locations` option). See https://api-dashboard.search.brave.com/app/subscriptions/subscribe
>
> **Standalone**: Unlike `local-pois` and `local-descriptions`, this endpoint does **not** require POI IDs from a prior web search. You provide a location and an optional query directly.

## Quick Start (cURL)

### Search by Query + Coordinates

```bash
curl -s "https://api.search.brave.com/res/v1/local/place_search" \
  -H "Accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=coffee shops" \
  --data-urlencode "latitude=37.7749" \
  --data-urlencode "longitude=-122.4194" \
  --data-urlencode "radius=5000"
```

### Search by Query + Location String

```bash
curl -s "https://api.search.brave.com/res/v1/local/place_search" \
  -H "Accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=sushi restaurants" \
  --data-urlencode "location=tokyo japan" \
  --data-urlencode "country=JP" \
  --data-urlencode "search_lang=en"
```

### Browse General POIs (No Query)

```bash
curl -s "https://api.search.brave.com/res/v1/local/place_search" \
  -H "Accept: application/json" \
  -H "Accept-Encoding: gzip" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "latitude=48.8566" \
  --data-urlencode "longitude=2.3522" \
  --data-urlencode "radius=3000" \
  --data-urlencode "country=FR"
```

## Endpoint

```http
GET https://api.search.brave.com/res/v1/local/place_search
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

## Parameters

### Location (optional but recommended)

Providing a geographic anchor improves precision. You can use coordinates (`latitude` + `longitude`) or a `location` string. Omitting both is allowed when a `q` is given — results are sourced globally and may be less precise. Omitting all three (`q`, `latitude`/`longitude`, and `location`) returns HTTP 422.

| Parameter | Type | Required | Default | Description |
|--|--|--|--|--|
| `latitude` | float | Conditional | — | Latitude (-90.0 to 90.0). Required together with `longitude` |
| `longitude` | float | Conditional | — | Longitude (-180.0 to 180.0). Required together with `latitude` |
| `location` | string | No | — | Location string, alternative to coordinates. US: `<city> <state> <country>` (e.g., `san francisco ca united states`). Non-US: `<city> <country>` (e.g., `tokyo japan`). Case-insensitive, no commas needed. English or the most popular local language works best |

### Search

| Parameter | Type | Required | Default | Description |
|--|--|--|--|--|
| `q` | string | No | — | Free-text query (e.g., `coffee shops`, `pizza`). Fully optional — if omitted, returns general POIs in the given area |

### Additional Options

| Parameter | Type | Required | Default | Description |
|--|--|--|--|--|
| `radius` | float | No | — | Search radius **bias** around the provided coordinates, in meters. Not a hard cutoff — results may extend beyond it. No upper limit |
| `count` | int | No | `20` | Total items returned across **all** buckets (1–100), not just `results` — an address query can spend the whole budget on `addresses`/`streets` |
| `geoloc` | string | No | — | User geolocation as `<latitude>x<longitude>` (e.g., `40.7128x-74.0060`), used to compute `distance` |
| `country` | string | No | `US` | Search country (2-letter country code or `ALL`) |
| `search_lang` | string | No | `en` | Language for search results (2+ char language code) |
| `ui_lang` | string | No | `en-US` | UI language (locale code, e.g., `en-US`) |
| `units` | string | No | `metric` | Measurement units: `metric` or `imperial` |
| `safesearch` | string | No | `strict` | Safe search level: `off`, `moderate`, or `strict` |
| `spellcheck` | bool | No | `true` | Whether to apply spellcheck to the query |

## Response Format

### Top-Level Fields

| Field | Type | Description |
|--|--|--|
| `type` | string | Always `"locations"` |
| `results` | array | List of `LocationResult` objects (individual POIs) |
| `cities` | array | Matched cities, `type: "city"` — see Geographic Place Fields |
| `countries` | array | Matched countries, `type: "country"` |
| `regions` | array | Matched regions, `type: "region"` |
| `neighborhoods` | array | Matched neighborhoods, `type: "neighborhood"` |
| `addresses` | array | List of `AddressResult` objects with `type: "address"` — specific street + number locations |
| `streets` | array | List of `AddressResult` objects with `type: "street"` — entire streets |
| `mixed` | array | `ResultReference` ordering hints describing how to interleave the buckets on a SERP |
| `location` | object? | Resolved location info |
| `location.coordinates` | [float, float] | `[latitude, longitude]` of the resolved center |
| `location.name` | string | Resolved location name (e.g., `"Helsinki"`) |
| `location.country` | string | Two-letter country code (e.g., `"FI"`) |

Treat a missing bucket as empty. For typical POI-style queries only `results` is populated, so clients that don't render rich SERPs can ignore the rest — except for address- or street-shaped queries, which can return an empty `results` and put every match in `addresses`/`streets`.

### LocationResult Fields

Each item in `results` is a `LocationResult`:

| Field | Type | Description |
|--|--|--|
| `type` | string | Always `"location_result"` |
| `title` | string | Business/POI name |
| `url` | string | Canonical URL |
| `description` | string? | Short description or category label (e.g., `"Coffee Shop"`) |
| `provider_url` | string | Provider page URL |
| `id` | string? | Opaque POI identifier (valid ~8 hours, usable with `local-pois` and `local-descriptions`) |
| `coordinates` | [float, float]? | `[latitude, longitude]` |
| `postal_address` | object | `displayAddress`, plus optional `streetAddress`, `addressLocality`, `addressRegion`, `postalCode`, `country` |
| `contact.telephone` | string? | Phone number |
| `contact.email` | string? | Email address |
| `rating.ratingValue` | float? | Average rating |
| `rating.bestRating` | float? | Max possible rating |
| `rating.reviewCount` | int? | Number of reviews |
| `rating.is_tripadvisor` | bool | Whether the rating comes from Tripadvisor |
| `opening_hours.current_day` | object[]? | Today's hours (`abbr_name`, `full_name`, `opens`, `closes`) |
| `opening_hours.days` | object[][]? | Hours for each day of the week |
| `categories` | string[] | Business categories (default `[]`) |
| `price_range` | string? | Price indicator, e.g. `$`, `$$`, `$$ - $$$` |
| `serves_cuisine` | string[]? | Cuisine types (restaurants) |
| `distance.value` | float? | Distance from the search location |
| `distance.units` | string? | Distance unit |
| `icon_category` | string? | Icon category slug (e.g., `cafe`) |
| `thumbnail.src` | string? | Thumbnail image URL |
| `thumbnail.original` | string? | Original image URL |
| `pictures.results` | object[]? | Additional images (`src`, `original`) |
| `profiles` | object[]? | External profiles (`name`, `url`, `long_name`, `img`) |
| `timezone` | string? | IANA timezone (e.g., `America/Los_Angeles`) |
| `zoom_level` | int | Suggested map zoom level (default `7`) |

### Geographic Place Fields (`cities`, `countries`, `regions`, `neighborhoods`)

All four buckets share one shape, differing only by the `type` identifier. The published spec names
them `CityResult` / `CountryResult` / `RegionResult` / `NeighborhoodResult`.

| Field | Type | Description |
|--|--|--|
| `type` | string | Bucket identifier: `city`, `country`, `region`, or `neighborhood` |
| `name` | string | Place name |
| `country` | string | Country code of the place |
| `coordinates` | [float, float] | `[latitude, longitude]` |
| `thumbnail.src` | string | Primary image URL |

### AddressResult Fields (`addresses` and `streets`)

Same model is used for both buckets. Items in `addresses` have `type: "address"` (street + number); items in `streets` have `type: "street"` (entire street).

| Field | Type | Description |
|--|--|--|
| `type` | string | `"address"` (in `addresses`) or `"street"` (in `streets`) |
| `name` | string | Display name of the address or street |
| `coordinates` | [float, float] | `[latitude, longitude]` |
| `pois` | object[] | `LocationResult` objects located **at** this address/street |
| `pois_nearby` | object[] | `LocationResult` objects located **nearby** |
| `zoom_level` | int | Suggested map zoom level (default `15`) |
| `distance.value` | float? | Distance from the search location |
| `distance.units` | string? | Distance unit |
| `postal_address` | object? | `displayAddress`, `streetAddress`, `addressLocality`, `addressRegion`, `country` |

### Mixed Ordering (`mixed`)

`mixed` is an ordered list of `ResultReference` objects telling clients how to interleave items from the different buckets on a single SERP.

| Field | Type | Description |
|--|--|--|
| `type` | string | Bucket to draw from: `results`, `cities`, `countries`, `regions`, `neighborhoods`, `addresses`, or `streets` |
| `index` | int? | 0-based index of the item within that bucket. May be `null` when `all` is `true` |
| `all` | bool | When `true`, all remaining items from the named bucket should be placed at this position |

Clients that only render POIs can ignore `mixed` entirely and read `results` directly.

### Example Response

```json
{
  "type": "locations",
  "results": [
    {
      "type": "location_result",
      "title": "Blue Bottle Coffee",
      "url": "https://yelp.com/biz/blue-bottle-coffee-sf",
      "provider_url": "",
      "id": "loc4CQWMJWLD4VBEBZ62XQLJTGK6YCJEEJDNAAAAAAA=",
      "description": "Coffee Shop",
      "postal_address": {
        "type": "PostalAddress",
        "displayAddress": "315 Linden St, San Francisco, CA 94102"
      },
      "contact": { "telephone": "+15106533394" },
      "rating": {
        "ratingValue": 4.3,
        "bestRating": 5.0,
        "reviewCount": 1024,
        "is_tripadvisor": true
      },
      "opening_hours": {
        "current_day": [
          { "abbr_name": "Tue", "full_name": "Tuesday", "opens": "07:00", "closes": "18:00" }
        ],
        "days": [
          [{ "abbr_name": "Mon", "full_name": "Monday", "opens": "07:00", "closes": "18:00" }]
        ]
      },
      "coordinates": [37.7763, -122.4215],
      "categories": [],
      "serves_cuisine": ["Cafe", "Coffee Shop"],
      "price_range": "$$",
      "icon_category": "cafe",
      "thumbnail": {
        "src": "https://example.com/thumb.jpg",
        "original": "https://example.com/original.jpg"
      },
      "zoom_level": 7
    }
  ],
  "cities": [],
  "countries": [],
  "regions": [],
  "neighborhoods": [],
  "addresses": [],
  "streets": [],
  "mixed": [
    { "type": "results", "index": 0, "all": false }
  ],
  "location": {
    "coordinates": [37.7749, -122.4194],
    "name": "San Francisco",
    "country": "US"
  }
}
```

For a query that matches a city name, the response additionally surfaces a city entry in `cities`:

```json
{
  "cities": [
    {
      "type": "city",
      "name": "San Francisco",
      "country": "US",
      "coordinates": [37.7749, -122.4194],
      "thumbnail": { "src": "https://example.com/sf.jpg" }
    }
  ],
  "mixed": [
    { "type": "cities", "index": 0, "all": false }
  ]
}
```

## Enriching Results with POI Details and Descriptions

POI `id` values from `results` can be passed to sibling endpoints for richer data:

```bash
# Get full POI details (hours, reviews, photos, web result mentions)
curl -s "https://api.search.brave.com/res/v1/local/pois" -G \
  --data-urlencode "ids=loc4CQWMJWLD4VBEBZ62XQLJTGK6YCJEEJDNAAAAAAA=" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"

# Get AI-generated descriptions
curl -s "https://api.search.brave.com/res/v1/local/descriptions" -G \
  --data-urlencode "ids=loc4CQWMJWLD4VBEBZ62XQLJTGK6YCJEEJDNAAAAAAA=" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

## Use Cases

- **Map-based exploration**: Search for POIs within a visible map viewport using coordinates + radius. No prior query needed.
- **Location-aware apps**: Build "nearby" features — pass device GPS coordinates and a query to find relevant businesses.
- **Travel planning**: Search for attractions, restaurants, and hotels by location string (e.g., `paris france`) without needing exact coordinates.

## Notes

- **Finds places, not pages**: This endpoint searches a geographic index of physical places. Use web search for general information retrieval.
- **Choosing a radius**: A tighter radius (below ~20 km) gives more focused results. Raise it to reach specific or well-known places further afield; for common category searches (e.g., `restaurants`), the default bias or tighter works best.
