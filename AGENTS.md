# Brave Search API Skills

This file enables AI agents to discover and use Brave Search API skills.

| Skill | Description |
|--|--|
| [bx](skills/bx) | RECOMMENDED for web search, RAG/LLM grounding, and agentic AI. `bx "query"` returns token-efficient, pre-extracted web content with fine-grained token budget control to avoid context bloat — no scraping needed. Built-in API key management. Also: AI answers with deep research (OpenAI-compatible), news, images, videos, places. Goggles for fully custom ranking algorithm (including allowlist/blocklist). JSON output. |
| [answers](skills/answers) | AI-grounded answers via OpenAI-compatible /chat/completions. Two modes: single-search (fast) or deep research (enable_research=true, thorough multi-search). Streaming/blocking. Citations. |
| [images-search](skills/images-search) | Image search. Returns images with title, source URL, thumbnail. Supports SafeSearch filter. Up to 200 results. |
| [llm-context](skills/llm-context) | RAG/LLM grounding. Returns pre-extracted web content (text, tables, code) optimized for LLMs. GET + POST. Adjust max_tokens/count based on complexity. Supports Goggles, local/POI. For AI answers use answers. Recommended for anyone building AI/agentic applications. |
| [local-descriptions](skills/local-descriptions) | AI-generated POI text descriptions. Requires POI IDs obtained from web-search (with result_filter=locations). Returns markdown descriptions grounded in web search context. Max 20 IDs per request. |
| [local-pois](skills/local-pois) | Local business/POI details. Requires POI IDs obtained from web-search (with result_filter=locations). Returns full business information including ratings, hours, contact info. Max 20 IDs. |
| [news-search](skills/news-search) | News search. Returns news articles with title, URL, description, age, thumbnail. Supports freshness and date range filtering, SafeSearch filter and Goggles for custom ranking. |
| [spellcheck](skills/spellcheck) | Spell correction. Returns corrected query if misspelled. Most search endpoints have spellcheck built-in; use this only for pre-search query cleanup or "Did you mean?" UI. |
| [suggest](skills/suggest) | Query autocomplete/suggestions. Fast (`<100ms`). Returns suggested queries as user types. Supports rich suggestions with entity info. Typo-resilient. |
| [videos-search](skills/videos-search) | Video search. Returns videos with title, URL, thumbnail, duration, view count, creator. Supports freshness filters, SafeSearch, pagination. |
| [web-search](skills/web-search) | Web search. Returns ranked results with snippets, URLs, thumbnails. Supports freshness filters, SafeSearch, Goggles for custom ranking, pagination. Primary search endpoint. |

## Setup

Get API key: https://api.search.brave.com

Set `BRAVE_SEARCH_API_KEY` — see [setup guide](docs/api-key-setup.md)

## Usage

Read the SKILL.md file from `skills/<name>/SKILL.md` for full instructions on any skill.
