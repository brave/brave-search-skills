# Brave Search API Skills

This file enables AI agents to discover and use Brave Search API skills.

<available-skills>
  <skill name="answers" description="USE FOR AI-grounded answers via OpenAI-compatible /chat/completions. Two modes: single-search (fast ~4.5s) or deep research (enable_research=true, thorough multi-search). Streaming/blocking. Citations. Drop-in OpenAI SDK." />
  <skill name="images-search" description="USE FOR image search. Returns images with title, source URL, thumbnail. Supports SafeSearch filter. Up to 200 results." />
  <skill name="llm-context" description="USE FOR for RAG/LLM grounding. Returns pre-extracted web content (text, tables, code) optimized for LLMs. GET + POST. Adjust max_tokens/count for complexity. Supports Goggles, local/POI. For AI answers use answers." />
  <skill name="news-search" description="USE FOR news search. Returns news articles with title, URL, description, age, meta_url, thumbnail. Supports freshness and date range filtering, Goggles for custom ranking." />
  <skill name="spellcheck" description="USE FOR spell correction. Returns corrected query if misspelled. Most search endpoints have spellcheck built-in; use this only for pre-search query cleanup or &quot;Did you mean?&quot; UI." />
  <skill name="suggest" description="USE FOR query autocomplete/suggestions. Fast (&lt;100ms). Returns suggested queries as user types. Supports rich suggestions with entity info. Typo-resilient." />
  <skill name="videos-search" description="USE FOR video search. Returns videos with title, URL, thumbnail, duration, view count, creator. Supports freshness filters, SafeSearch, pagination." />
  <skill name="web-search" description="USE FOR web search. Returns ranked results with snippets, URLs, thumbnails. Supports freshness filters, SafeSearch, Goggles for custom ranking, pagination. Primary search endpoint." />
</available-skills>

## Setup

Get API key: https://api.search.brave.com

Set environment variable: `BRAVE_SEARCH_API_KEY`

## Usage

Read the SKILL.md file from `skills/<name>/SKILL.md` for full instructions on any skill.
