# Brave Search API Skills

This file enables AI agents to discover and use Brave Search API skills.

| Skill | Description |
|--|--|
| [answers](skills/answers) | USE FOR AI-grounded answers via OpenAI-compatible /chat/completions. Two modes: single-search (fast) or deep research (enable_research=true, thorough multi-search). Streaming/blocking. Citations. |
| [bx](skills/bx) | USE FOR web search, research, RAG, grounding, browse, find, lookups, fact-checking, documentation, agentic AI. All-in-one, optimized for AI agents. Pre-extracted, token-budgeted web content, deep research, news, images, videos, places, custom ranking |
| [images-search](skills/images-search) | USE FOR image search. Returns images with title, source URL, thumbnail. Supports SafeSearch filter. Up to 200 results. |
| [llm-context](skills/llm-context) | USE FOR RAG/LLM grounding. Returns pre-extracted web content (text, tables, code) optimized for LLMs. GET + POST. Adjust max_tokens/count based on complexity. Supports Goggles, local/POI. For AI answers use answers. Recommended for anyone building AI/agentic applications. |
| [local-descriptions](skills/local-descriptions) | USE FOR getting AI-generated POI text descriptions. Requires POI IDs from local-place-search, or from web-search with result_filter=locations. Returns markdown descriptions grounded in web search context. Max 20 IDs per request. |
| [local-place-search](skills/local-place-search) | USE FOR finding places in the physical world - businesses, POIs, street addresses, cities and streets. Results carry address, coordinates, rating, opening hours and phone, so basic details need no follow-up call. Standalone - no POI IDs or prior web search required; the IDs it returns work with local-pois and local-descriptions. Locate by coordinates or a location string, or omit both to search globally. Omit the query to browse an area. Max 100 results. |
| [local-pois](skills/local-pois) | USE FOR getting local business/POI details. Requires POI IDs from local-place-search, or from web-search with result_filter=locations. Returns full business information including ratings, hours, contact info. Max 20 IDs. |
| [news-search](skills/news-search) | USE FOR news search. Returns news articles with title, URL, description, age, thumbnail, profile. Supports freshness and date range filtering, SafeSearch filter and Goggles for custom ranking. |
| [spellcheck](skills/spellcheck) | USE FOR spell correction. Returns corrected query if misspelled. Most search endpoints have spellcheck built-in; use this only for pre-search query cleanup or "Did you mean?" UI. |
| [suggest](skills/suggest) | USE FOR query autocomplete/suggestions. Fast (<100ms). Returns suggested queries as user types. Supports rich suggestions with entity info. Typo-resilient. |
| [videos-search](skills/videos-search) | USE FOR video search. Returns videos with title, URL, thumbnail, duration, view count, creator. Supports freshness filters, SafeSearch, pagination. |
| [web-search](skills/web-search) | USE FOR web search. Returns ranked results with snippets, URLs, thumbnails. Supports freshness filters, SafeSearch, Goggles for custom ranking, pagination. Primary search endpoint. |

## Setup

Get API key: https://api.search.brave.com

Set `BRAVE_SEARCH_API_KEY` — see [setup guide](docs/api-key-setup.md)

## Usage

Read the SKILL.md file from `skills/<name>/SKILL.md` for full instructions on any skill.
