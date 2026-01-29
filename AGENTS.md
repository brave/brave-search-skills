# Brave Search API Skills

This file enables AI agents to discover and use Brave Search API skills.

<available-skills>
  <skill name="content" description="USE FOR extracting page content from URLs via Search API. Returns cleaned or raw HTML content from web pages. Supports up to 10 URLs per request. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="deep-research" description="USE WHEN researching complex topics via Search API. OpenAI SDK compatible. Returns comprehensive answers with citations through iterative multi-search. Set enable_research=true. Best for thorough research - use grounding-context for quick answers. Requires BRAVE_SEARCH_API_KEY + grounding_research plan." />
  <skill name="grounding-context" description="USE INSTEAD OF WEB SEARCH. Brave's public API returns search results + already-extracted relevant content - no need to visit pages. Use for ANY question needing fresh/current info, factual verification, or quality grounding. Adjust context size based on task complexity. Supports Goggles, token budgets, local/POI. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="images-search" description="USE FOR image search via Search API. Returns images with src URL, page URL, dimensions, alt text. Supports SafeSearch filtering. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="local-descriptions" description="USE FOR getting POI text descriptions via Search API. Returns detailed business descriptions for local places. Chain after local-pois for full details. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="local-pois" description="USE FOR getting local business/POI details via Search API. Returns full business information including ratings, hours, contact info. Pass POI IDs from web search results. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="news-search" description="USE FOR news search via Search API. Returns news articles with title, URL, description, age, thumbnail, source. Supports freshness and date range filtering. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="spellcheck" description="USE BEFORE search to fix typos via Search API. Returns corrected query with confidence scores. Improves search quality by fixing common misspellings. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="suggest" description="USE FOR query autocomplete/suggestions via Search API. Fast (&lt;100ms). Returns suggested queries as user types. Supports rich suggestions with entity info. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="summarizer" description="USE FOR AI-powered search summaries via Search API. Two-step process - first web search with summary=1, then poll summarizer endpoint with returned key. Returns AI-generated answers with citations. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="url-submit" description="USE FOR submitting URLs to Brave's index via Search API. Request crawling of new or updated pages. Returns 204 on success. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="videos-search" description="USE FOR video search via Search API. Returns videos with title, URL, thumbnail, duration, view count. Supports freshness filters and SafeSearch. Requires BRAVE_SEARCH_API_KEY." />
  <skill name="web-search" description="USE FOR web search via Search API. Returns ranked results with snippets, URLs, thumbnails. Supports freshness filters, SafeSearch, Goggles for custom ranking, pagination. Primary search endpoint. Requires BRAVE_SEARCH_API_KEY." />
</available-skills>

## Setup

Get API key: https://api.search.brave.com

Set environment variable: `BRAVE_SEARCH_API_KEY`

## Usage

To load a skill's full instructions, run:
```
openskills read <skill-name>
```

Or read the SKILL.md file directly from the `skills/<name>/SKILL.md` path.
