---
name: bx-search
description: Use the Brave Search CLI (`bx`) to retrieve web context, AI-grounded answers, and specialized search results with JSON output optimized for LLM and agent workflows. Use when a task needs fresh web grounding, documentation lookup, troubleshooting research, or token-budgeted retrieval for RAG.
version: 1.0.0
license: MIT-0
metadata:
  {
    "openclaw": {
      "requires": {
        "bins": ["bx"],
        "env": ["BRAVE_SEARCH_API_KEY"],
      },
      "primaryEnv": "BRAVE_SEARCH_API_KEY",
      "homepage": "https://brave.com/search/api/",
      "install": [
        {
          "id": "download-linux-amd64",
          "kind": "download",
          "os": ["linux"],
          "bins": ["bx"],
          "label": "Install Brave Search CLI (GitHub)",
          "url": "https://github.com/brave/brave-search-cli/releases/download/v1.0.0/bx-1.0.0-linux-amd64"
        },
        {
          "id": "download-linux-arm64",
          "kind": "download",
          "os": ["linux"],
          "bins": ["bx"],
          "label": "Install Brave Search CLI (GitHub)",
          "url": "https://github.com/brave/brave-search-cli/releases/download/v1.0.0/bx-1.0.0-linux-arm64"
        },
        {
          "id": "download-darwin-arm64",
          "kind": "download",
          "os": ["darwin"],
          "bins": ["bx"],
          "label": "Install Brave Search CLI (GitHub)",
          "url": "https://github.com/brave/brave-search-cli/releases/download/v1.0.0/bx-1.0.0-darwin-arm64"
        },
        {
          "id": "download-windows-amd64",
          "kind": "download",
          "os": ["win32"],
          "bins": ["bx"],
          "label": "Install Brave Search CLI (GitHub)",
          "url": "https://github.com/brave/brave-search-cli/releases/download/v1.0.0/bx-1.0.0-windows-amd64.exe"
        }
      ]
    },
    "repo": "https://github.com/brave/brave-search-skills/tree/main/clawhub/bx-search",
    "issues": "https://github.com/brave/brave-search-skills/issues"
  }
---

# bx — Brave Search CLI

A zero-dependency, token-efficient CLI for the [Brave Search API](https://brave.com/search/api/), built for AI agents and LLMs.

One binary, JSON in/out, no runtime dependencies. The default subcommand is `context` — bare `bx "query"` is equivalent to `bx context "query"`. It replaces search + scrape + extract in a single call with token-budgeted output — purpose-built for RAG and LLM grounding.

## Quick Start

**macOS/Linux**
```bash
curl -fsSL https://raw.githubusercontent.com/brave/brave-search-cli/main/scripts/install.sh | sh
```

**Windows (PowerShell)**
```powershell
powershell -ExecutionPolicy Bypass -c "irm https://raw.githubusercontent.com/brave/brave-search-cli/main/scripts/install.ps1 | iex"
```

```bash
bx config set-key YOUR_API_KEY    # get a key at https://api-dashboard.search.brave.com
bx "your search query"
bx --help                        # see all commands; bx <command> --help for flags
```

## Getting an API Key

1. Sign up at <https://api-dashboard.search.brave.com/register>
2. Choose a plan — all plans include **$5/month free credits** (~1,000 free queries). Different endpoints may require different plans.
3. Go to "API Keys" in the dashboard, generate a key (shown once — save it)

## Configuring the API Key

Three methods, in priority order:

| Priority | Method | Example |
|----------|--------|---------|
| 1 (highest) | `--api-key` flag | `bx --api-key KEY web "test"` |
| 2 | `BRAVE_SEARCH_API_KEY` env var | `export BRAVE_SEARCH_API_KEY=KEY` |
| 3 | Config file | `bx config set-key KEY` |

The config file is stored at `~/.config/brave-search/api_key` (Linux), `~/Library/Application Support/brave-search/api_key` (macOS), or `%APPDATA%\brave-search\api_key` (Windows).

**Security tip:** Prefer the env var or config file over `--api-key`, which is visible in process listings. Use `bx config set-key` without an argument to enter the key interactively, avoiding shell history.

## For AI Agents

**Use `context` by default.** It returns pre-extracted, relevance-scored web content ready for LLM prompt injection. One API call replaces the search → scrape → extract pipeline.

```bash
# RAG grounding with token budget
bx context "Python TypeError cannot unpack non-iterable NoneType" --max-tokens 4096

# Direct AI answer (OpenAI-compatible, streams by default)
bx answers "explain Rust lifetimes with examples"

# Raw web search when you need site: scoping or result filtering
bx web "site:docs.rs axum middleware" --count 5
```

### When to Use Which Command

| Your need | Command | Why |
|-----------|---------|-----|
| Look up docs, errors, code patterns | `context` | Pre-extracted text, token-budgeted |
| Get a synthesized explanation | `answers` | AI-generated, cites sources |
| Search a specific site (site:) | `web` | Supports search operators |
| Find discussions/forums | `web --result-filter discussions` | Forums often have solutions |
| Check latest versions/releases | `context` or `news --freshness pd` | Fresh info beyond training data |
| Research security vulnerabilities | `context` or `news` | CVE details, advisories |
| Boost/filter specific domains | `--goggles` on context/web/news | Custom re-ranking, no other API has this |

### Response Shapes

**`bx context`** — RAG/grounding (recommended)
```json
{
  "grounding": {
    "generic": [
      { "url": "...", "title": "...", "snippets": ["extracted content...", "..."] }
    ]
  }
}
```

**`bx answers --no-stream`** — AI answer (single response)
```json
{"choices": [{"message": {"content": "Rust lifetimes ensure references..."}}]}
```

**`bx answers`** — AI answer (streaming, one JSON chunk per line)
```json
{"choices": [{"delta": {"content": "R"}}]}
{"choices": [{"delta": {"content": "u"}}]}
{"choices": [{"delta": {"content": "s"}}]}
{"choices": [{"delta": {"content": "t"}}]}
{"choices": [{"delta": {"content": " "}}]}
```

**`bx web`** — Full search results
```json
{
  "web": { "results": [{"title": "...", "url": "...", "description": "..."}] },
  "news": { "results": [...] },
  "videos": { "results": [...] },
  "discussions": { "results": [...] }
}
```

### Agent Workflow Examples

**Debugging an error:**
```bash
bx "Python TypeError cannot unpack non-iterable NoneType" --max-tokens 4096
```

**Evaluating a dependency:**
```bash
bx context "reqwest crate security issues maintained 2026" --threshold strict
bx news "reqwest Rust crate" --freshness pm
```

**Corrective RAG loop:**
```bash
# 1. Broad search
bx "axum middleware authentication" --max-tokens 4096
# 2. Too general? Narrow down
bx "axum middleware tower layer authentication example" --threshold strict --max-tokens 4096
# 3. Still need synthesis? Ask for an answer
bx answers "how to implement JWT auth middleware in axum" --enable-research
```

**Checking for breaking changes before upgrading:**
```bash
bx context "Next.js 15 breaking changes migration guide" --max-tokens 8192
bx news "Next.js 15 release" --freshness pm
```

**Focused search with Goggles (custom re-ranking):**
```bash
bx "Python asyncio gather vs wait" \
  --goggles '$boost=3,site=docs.python.org
/docs/$boost=3
/blog/$downrank=2
$discard,site=geeksforgeeks.org
$discard,site=w3schools.com' --max-tokens 4096
```

**Token budget control:**
```bash
bx context "topic" --max-tokens 4096 --max-tokens-per-url 1024 --max-urls 5
```

**Non-streaming answers (for programmatic use):**
```bash
bx answers "compare SQLx and Diesel for Rust" --no-stream | jq '.choices[0].message.content'
```

**Answers stdin mode** — pass `-` to read a full JSON request body:
```bash
echo '{"messages":[{"role":"user","content":"review this code for security issues"}]}' | bx answers -
```

**Other commands:**
```bash
bx images "system architecture diagram microservices" | jq '.results[].thumbnail.src'
bx suggest "how to implement" --count 10 | jq '.results[].query'
bx places --location "San Francisco CA US" -q "coffee" | jq '.results[].title'
bx web "restaurants near me" --lat 37.7749 --long -122.4194 --city "San Francisco"
bx web "rust" --result-filter "web,discussions"
```

## Commands

| Command | Description | Output Shape |
|---------|-------------|--------------|
| `context` | **RAG/LLM grounding** — pre-extracted web content | `.grounding.generic[]` → `{url, title, snippets[]}` |
| `answers` | **AI answers** — OpenAI-compatible, streaming | `.choices[0].delta.content` (stream) |
| `web` | Full web search — all result types | `.web.results[]`, `.news.results[]`, etc. |
| `news` | News articles with freshness filters | `.results[]` → `{title, url, age}` |
| `images` | Image search (up to 200 results) | `.results[]` → `{title, url, thumbnail.src}` |
| `videos` | Video search with duration/views | `.results[]` → `{title, url, video.duration}` |
| `places` | Local place/POI search (200M+ POIs) | `.results[]` → `{title, postal_address, contact}` |
| `suggest` | Autocomplete/query suggestions | `.results[]` → `{query}` |
| `spellcheck` | Spell-check a query | `.results[0].query` |
| `pois` | POI details by ID | (use IDs from `places`) |
| `descriptions` | AI-generated POI descriptions | `.results[].description` |
| `config` | Manage API key | `set-key`, `show-key`, `path` |

## Goggles — Custom Search Re-Ranking

Brave Goggles let you define custom re-ranking rules for search results. Boost domains, URL paths, or content patterns; downrank noise; discard SEO spam — from simple domain allow/deny lists to complex multi-rule ranking profiles. No other search API offers this. Supported on `context`, `web`, and `news`.

### Domain Shortcuts — `--include-site` / `--exclude-site`

For the common case of restricting to or excluding specific domains, use the convenience flags (available on `context`, `web`, `news`):

```bash
# Only include results from these domains (allowlist)
bx "rust axum" --include-site docs.rs --include-site github.com

# Exclude specific domains
bx web "rust tutorial" --exclude-site w3schools.com --exclude-site medium.com
```

These generate Goggles rules internally. For more advanced re-ranking (boosting, path patterns, wildcards), use `--goggles` directly. The three flags are mutually exclusive.

### Why Agents Should Use Goggles

- **Domain & path targeting**: Boost, downrank, or discard by domain (`$site=`) or URL path (`/docs/$boost=5`) — fine-grained control with wildcards
- **Better than `site:`**: Brave converts `site:` operators to Goggles internally — explicit Goggles unlock the full DSL (hundreds of rules, path patterns, boost/downrank strengths) without bloating the query
- **Clean queries**: A single `--goggles` parameter replaces long `site:X OR site:Y` chains, saving tokens
- **Reusable**: Host a `.goggle` file on GitHub and share across agents, CI, and teams
- **Community-maintained**: Leverage existing Goggles like [Tech Blogs](https://raw.githubusercontent.com/brave/goggles-quickstart/main/goggles/tech_blogs.goggle)

### Inline Rules (zero setup)

```bash
# Allowlist — only include results from trusted domains
bx context "Python asyncio patterns" \
  --goggles '$discard
$site=docs.python.org
$site=peps.python.org'

# Path-based boosting — prefer /docs/ over /blog/ across all sites
bx context "axum middleware tower" \
  --goggles '/docs/$boost=5
/api/$boost=3
/blog/$downrank=3' --max-tokens 4096

# Ecosystem focus — boost Rust sources for crate research
bx context "serde custom deserializer" \
  --goggles '$boost=5,site=docs.rs
$boost=5,site=crates.io
$boost=3,site=github.com' --max-tokens 4096

# Downrank blog spam in news results
bx news "npm security advisory" --freshness pd \
  --goggles '$downrank=5,site=medium.com'
```

### DSL Quick Reference

| Rule | Effect | Example |
|------|--------|---------|
| `$boost=N,site=DOMAIN` | Promote domain (N=1-10) | `$boost=3,site=docs.rs` |
| `$downrank=N,site=DOMAIN` | Demote domain (N=1-10) | `$downrank=5,site=medium.com` |
| `$discard,site=DOMAIN` | Remove domain entirely | `$discard,site=w3schools.com` |
| `/path/$boost=N` | Boost matching URL paths | `/docs/$boost=5` |
| `*pattern*$boost=N` | Wildcard URL matching | `*api*$boost=3` |
| Generic `$discard` | Allowlist mode — discard all unmatched | `$discard` (as first rule) |

Separate multiple rules with newlines. Full DSL + pattern syntax: [goggles-quickstart](https://github.com/brave/goggles-quickstart).

### From a File (`@file`) — ideal for agents

Agents can generate a `.goggle` file on the fly and reference it:

```bash
# Agent writes rules to a file, then uses it across multiple queries
cat > /tmp/rust.goggle << 'EOF'
$boost=5,site=docs.rs
$boost=5,site=crates.io
$boost=3,site=github.com
/blog/$downrank=3
$discard,site=w3schools.com
$discard,site=geeksforgeeks.org
EOF

bx context "axum middleware tower" --goggles @/tmp/rust.goggle --max-tokens 4096
bx context "serde custom deserializer" --goggles @/tmp/rust.goggle --max-tokens 4096
```

### From stdin (`@-`) — pipe generated rules

```bash
echo '$boost=5,site=docs.rs
$boost=3,site=github.com' | bx web "tokio runtime" --goggles @-
```

### Hosted Goggles (reusable, shareable)

Host a `.goggle` file on GitHub/GitLab, [submit it to Brave](https://search.brave.com/goggles/create), then reference by URL:

```bash
bx web "distributed systems" \
  --goggles 'https://raw.githubusercontent.com/brave/goggles-quickstart/main/goggles/tech_blogs.goggle'
```

Community Goggles: [brave/goggles-quickstart](https://github.com/brave/goggles-quickstart) | [Discover page](https://search.brave.com/goggles/discover)

## Exit Codes

| Code | Meaning | Agent action |
|------|---------|-------------|
| 0 | Success | Process results |
| 1 | Client error (bad request) | Fix query/parameters |
| 2 | Usage error (bad flags) | Fix CLI arguments (clap) |
| 3 | Auth/permission error (401/403) | Check API key or plan: `bx config show-key` |
| 4 | Rate limited (429) | Retry after delay |
| 5 | Server/network error | Retry with backoff |

Error output format (stderr):
```
error: rate limited (429) — Request rate limit exceeded for plan.
hint: retry after a short delay, or upgrade plan for higher rate limits
{"type":"ErrorResponse","error":{"code":"RATE_LIMITED","status":429,...}}
```
