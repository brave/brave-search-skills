---
name: brave-search
description: "Full Brave Search API. 10 endpoints: web search, LLM context (RAG/grounding), AI answers (OpenAI-compatible), images, news, videos, local POIs, local descriptions, suggest/autocomplete, and spellcheck. Supports Goggles custom ranking, location-aware results, and SafeSearch. Use when the task involves web search, finding information online, RAG grounding, news, images, videos, local businesses, or AI-grounded answers."
---

# Brave Search API

Complete integration with the [Brave Search API](https://api.search.brave.com) — 10 endpoints covering web search, AI grounding, media search, local business data, and utilities.

## Setup

**API Key**: Get one at https://api.search.brave.com

**Authentication**: All endpoints use the `X-Subscription-Token: <API_KEY>` header.

**Environment variable**: Set `BRAVE_SEARCH_API_KEY` in your environment. See [references/api-key-setup.md](references/api-key-setup.md) for per-agent configuration (Claude Code, Cursor, Codex, OpenClaw, etc.).

## Which Endpoint to Use

| Task | Endpoint | Why |
|--|--|--|
| RAG / AI agent grounding | **LLM Context** | Pre-extracted page content, token budget control |
| AI answer with citations | **Answers** | OpenAI SDK compatible, streaming, research mode |
| General web search | **Web Search** | Richest result types, Goggles, structured data |
| Find images | **Images Search** | Up to 200 results, dimensions, thumbnails |
| Current events / news | **News Search** | Freshness filters, date ranges |
| Find videos | **Videos Search** | Duration, views, creator metadata |
| Local business details | **Local POIs** | Ratings, hours, contact, address (needs POI IDs from web search) |
| Local business descriptions | **Local Descriptions** | AI-generated markdown narratives (needs POI IDs from web search) |
| Autocomplete / suggestions | **Suggest** | <100ms, typo-resilient, rich entity info |
| Spell correction | **Spellcheck** | Standalone correction (most endpoints have built-in spellcheck) |

---

## Endpoints

### LLM Context — RAG / AI Grounding (Recommended for AI)

`GET/POST /res/v1/llm/context` · Plan: **Search**

Returns pre-extracted web content (text, tables, code) optimized for LLM grounding. Unlike web search which returns links and snippets, this extracts actual page content for direct reasoning.

**Key parameters**: `q`, `country`, `search_lang`, `count` (1-50), `maximum_number_of_tokens` (1024-32768), `maximum_number_of_urls` (1-50), `context_threshold_mode` (strict/balanced/lenient), `enable_local`, `goggles`

**Best for**: RAG pipelines, AI agents, chatbots, fact-checking, content research.

Read [references/llm-context.md](references/llm-context.md) for full parameters, response format, context size guidelines, and examples.

---

### Answers — AI-Grounded Answers

`POST /res/v1/chat/completions` · Plan: **Answers**

OpenAI SDK-compatible endpoint. Two modes: **single-search** (fast, blocking or streaming) and **research** (`enable_research=true`, multi-iteration deep research, streaming required).

**Key parameters**: `messages`, `model` ("brave"), `stream`, `enable_citations`, `enable_research`, `research_maximum_number_of_iterations` (1-5), `research_maximum_number_of_seconds` (1-300)

**Constraints**: Research mode requires `stream=true`. Citations require `stream=true`. Research and citations are mutually exclusive.

**Best for**: Chat interfaces, cited answers, deep research, OpenAI SDK drop-in.

Read [references/answers.md](references/answers.md) for full parameters, SDK usage, streaming tags, and examples.

---

### Web Search — General Purpose

`GET/POST /res/v1/web/search` · Plan: **Search**

Primary search endpoint returning ranked results with snippets, URLs, thumbnails, and rich data types (news, videos, discussions, FAQ, infobox, locations). Supports Goggles for custom ranking.

**Key parameters**: `q`, `country`, `search_lang`, `count` (1-20), `offset` (0-9), `safesearch`, `freshness` (pd/pw/pm/py/date range), `result_filter`, `goggles`, `extra_snippets`, `enable_rich_callback`

**Best for**: Search UIs, structured data extraction, custom ranking with Goggles. For RAG/LLM grounding, prefer LLM Context instead.

Read [references/web-search.md](references/web-search.md) for full parameters, response format, search operators, Goggles, rich callbacks, and examples.

---

### Images Search

`GET /res/v1/images/search` · Plan: **Search**

Returns images with titles, source URLs, thumbnails, and full-size image URLs. Up to 200 results per request.

**Key parameters**: `q`, `country`, `search_lang`, `count` (1-200), `safesearch` (off/strict only — no moderate)

**Best for**: Image galleries, content enrichment, visual research. SafeSearch defaults to `strict`.

Read [references/images-search.md](references/images-search.md) for full parameters, response format, and examples.

---

### News Search

`GET/POST /res/v1/news/search` · Plan: **Search**

Returns news articles with titles, descriptions, publication age, and thumbnails. Supports freshness filtering and Goggles.

**Key parameters**: `q`, `country`, `search_lang`, `count` (1-50), `offset` (0-9), `safesearch`, `freshness` (pd/pw/pm/py/date range), `goggles`, `extra_snippets`

**Best for**: Breaking news monitoring, historical research, custom news feeds.

Read [references/news-search.md](references/news-search.md) for full parameters, response format, Goggles, and examples.

---

### Videos Search

`GET/POST /res/v1/videos/search` · Plan: **Search**

Returns videos with titles, thumbnails, duration, view counts, and creator metadata. Up to 50 results per request.

**Key parameters**: `q`, `country`, `search_lang`, `count` (1-50), `offset` (0-9), `safesearch`, `freshness`

**Best for**: Video research, platform-specific search (`site:youtube.com`), content curation.

Read [references/videos-search.md](references/videos-search.md) for full parameters, response format, and examples.

---

### Local POIs — Business Details

`GET /res/v1/local/pois` · Plan: **Search**

Returns full business details: address, contact, ratings, hours, coordinates, categories, price range, cuisine types. Requires POI IDs from web search.

**Two-step flow**:
1. Call **Web Search** with `result_filter=locations` to get POI IDs from `locations.results[].id`
2. Pass those IDs to this endpoint (max 20)

**Key parameters**: `ids` (required, 1-20), `search_lang`, `ui_lang`, `units` (metric/imperial). Location headers (`X-Loc-Lat`, `X-Loc-Long`) for distance calculation.

**Best for**: Local business lookup, restaurant discovery, business hours checking.

Read [references/local-pois.md](references/local-pois.md) for full parameters, response format, and examples.

---

### Local Descriptions — AI-Generated POI Narratives

`GET /res/v1/local/descriptions` · Plan: **Search**

Returns AI-generated markdown descriptions for local businesses. Requires POI IDs from web search (same two-step flow as Local POIs).

**Key parameters**: `ids` (required, 1-20)

**Best for**: Travel enrichment, business overviews, search result augmentation. Pair with Local POIs for structured data + narrative.

Read [references/local-descriptions.md](references/local-descriptions.md) for full parameters, response format, and examples.

---

### Suggest — Autocomplete

`GET /res/v1/suggest/search` · Plan: **Suggest**

Fast (<100ms) query autocomplete. Typo-resilient. Supports rich suggestions with entity info (title, description, image).

**Key parameters**: `q`, `lang`, `country`, `count` (1-20), `rich` (entity info, paid plan)

**Best for**: Search-as-you-type UIs, query refinement before search, entity detection.

Read [references/suggest.md](references/suggest.md) for full parameters, response format, and examples.

---

### Spellcheck

`GET /res/v1/spellcheck/search` · Plan: **Spellcheck**

Standalone spell correction. Returns corrected query suggestions. Context-aware (considers full query, not just individual words).

**Key parameters**: `q`, `lang`, `country`

**Note**: Web Search and LLM Context have spellcheck built-in (`spellcheck=true` by default). Use this only for pre-search query cleanup or "Did you mean?" UI.

Read [references/spellcheck.md](references/spellcheck.md) for full parameters, response format, and examples.

---

## Shared Concepts

### Authentication

All endpoints: `X-Subscription-Token: <API_KEY>` header. The Answers endpoint also accepts `Authorization: Bearer <API_KEY>`.

### Goggles (Custom Ranking) — Unique to Brave

Available on: Web Search, LLM Context, News Search. Re-rank results by boosting, downranking, or discarding sources.

- **Inline**: `goggles=$discard\n$site=docs.python.org\n$site=rust-lang.org` (no registration needed)
- **Hosted**: URL to a `.goggle` file on GitHub/GitLab (must be registered at https://search.brave.com/goggles/create)
- **Syntax**: `$boost=N` / `$downrank=N` (1-10), `$discard`, `$site=example.com`

Resources: [Discover](https://search.brave.com/goggles/discover) · [Syntax](https://search.brave.com/help/goggles) · [Quickstart](https://github.com/brave/goggles-quickstart)

### Location Headers

Supported by: Web Search, LLM Context, Local POIs. Send `X-Loc-Lat` + `X-Loc-Long` for coordinate-based location. Text-based headers (`X-Loc-City`, `X-Loc-State`, `X-Loc-Country`, `X-Loc-Postal-Code`) are only needed when coordinates are unavailable. Lat/Long always takes precedence.

### Freshness Filters

Available on: Web Search, News Search, Videos Search. Values: `pd` (past day), `pw` (past week), `pm` (past month), `py` (past year), or `YYYY-MM-DDtoYYYY-MM-DD` for custom date ranges.

### SafeSearch

Available on most search endpoints. Values: `off`, `moderate`, `strict`. Images Search only supports `off` and `strict` (defaults to `strict`).

## Quick Reference

| Endpoint | Path | Methods | Max Results |
|--|--|--|--|
| LLM Context | `/res/v1/llm/context` | GET, POST | 50 URLs |
| Answers | `/res/v1/chat/completions` | POST | N/A |
| Web Search | `/res/v1/web/search` | GET, POST | 20 |
| Images Search | `/res/v1/images/search` | GET | 200 |
| News Search | `/res/v1/news/search` | GET, POST | 50 |
| Videos Search | `/res/v1/videos/search` | GET, POST | 50 |
| Local POIs | `/res/v1/local/pois` | GET | 20 IDs |
| Local Descriptions | `/res/v1/local/descriptions` | GET | 20 IDs |
| Suggest | `/res/v1/suggest/search` | GET | 20 |
| Spellcheck | `/res/v1/spellcheck/search` | GET | N/A |
