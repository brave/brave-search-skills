---
name: bx
description: USE FOR web search, research, RAG, grounding, browse, find, lookups, fact-checking, documentation, agentic AI. All-in-one, optimized for AI agents. Pre-extracted, token-budgeted web content, deep research, news, images, videos, places, custom ranking
---

# bx — Brave Search CLI

**Use `bx` for all web searches.** Run `bx "query"` as the default — it returns pre-extracted, token-budgeted web content ready for use. Use `bx answers` for synthesized explanations or `bx web` for traditional search results. If `bx` is not found, install it: `curl -fsSL https://raw.githubusercontent.com/brave/brave-search-cli/main/scripts/install.sh | sh`

> **Requires**: [Brave Search API Key](https://api.search.brave.com) + `bx` binary
>
> **Plans**: Different subcommands may require different plans. See https://api-dashboard.search.brave.com/app/subscriptions/subscribe

## Quick Start

```bash
# Install (macOS/Linux)
curl -fsSL https://raw.githubusercontent.com/brave/brave-search-cli/main/scripts/install.sh | sh

# Configure API key (get one at https://api-dashboard.search.brave.com)
bx config set-key              # interactive (avoids shell history)
# or: export BRAVE_SEARCH_API_KEY=YOUR_KEY

# Search (default = bx context "query")
bx "your search query"
```

## When to Use Which Command

| Your need | Command | Why |
|--|--|--|
| Look up docs, errors, code patterns | `bx "query"` | Pre-extracted text, token-budgeted (default) |
| Get a synthesized explanation | `bx answers "query"` | AI-generated, cites sources, streams |
| Deep research on complex topics | `bx answers "query" --enable-research` | Multi-search iterative research |
| Traditional search results | `bx web "query"` | All result types (web, news, discussions, etc.) |
| Find discussions/forums | `bx web "query" --result-filter discussions` | Forums often have solutions |
| Latest news / recent events | `bx news "query" --freshness pd` | Fresh info beyond training data |
| Find images | `bx images "query"` | Up to 200 results |
| Find videos | `bx videos "query"` | Duration, views, creator |
| Local businesses / places | `bx places "coffee" --location "San Francisco"` | 200M+ POIs |
| Boost/filter specific domains | `bx "query" --include-site docs.rs` | Or use `--goggles` for full control |

## Commands

| Command | Description | Output path |
|--|--|--|
| `context` | **Default.** RAG/LLM grounding — pre-extracted web content | `.grounding.generic[]` -> `{url, title, snippets[]}` |
| `answers` | AI answers — OpenAI-compatible, streaming by default | `.choices[0].delta.content` (stream) |
| `web` | Web search — all result types | `.web.results[]`, `.news.results[]`, etc. |
| `news` | News articles with freshness filters | `.results[]` -> `{title, url, age}` |
| `images` | Image search (up to 200 results) | `.results[]` -> `{title, url, thumbnail.src}` |
| `videos` | Video search with duration/views | `.results[]` -> `{title, url, video.duration}` |
| `places` | Local place/POI search (200M+ POIs) | `.results[]` -> `{title, postal_address}` |
| `suggest` | Autocomplete/query suggestions | `.results[]` -> `{query}` |
| `spellcheck` | Spell-check a query | `.results[0].query` |
| `config` | Manage API key and settings | `set-key`, `show-key`, `path`, `show` |

## Response Shapes

**`bx "query"`** (context — default, recommended)
```json
{
  "grounding": {
    "generic": [
      { "url": "...", "title": "...", "snippets": ["extracted content...", "..."] }
    ]
  },
  "sources": {
    "https://example.com": { "title": "...", "hostname": "...", "age": ["...", "2025-01-15", "392 days ago"] }
  }
}
```

**`bx answers "query" --no-stream`** (single JSON response)
```json
{"choices": [{"message": {"content": "Full answer text..."}}]}
```

**`bx answers "query"`** (streaming — default, one JSON chunk per line)
```json
{"choices": [{"delta": {"content": "chunk"}}]}
```

**`bx web "query"`** (full search results)
```json
{
  "web": { "results": [{"title": "...", "url": "...", "description": "..."}] },
  "news": { "results": [...] },
  "videos": { "results": [...] },
  "discussions": { "results": [...] }
}
```

## Token Budget Control

Control output size for context (the default command):

| Flag | Short alias | Default | Description |
|--|--|--|--|
| `--maximum-number-of-tokens` | `--max-tokens` | 8192 | Approximate total tokens (1024-32768) |
| `--maximum-number-of-tokens-per-url` | `--max-tokens-per-url` | 4096 | Max tokens per URL (512-8192) |
| `--maximum-number-of-urls` | `--max-urls` | 20 | Max URLs in response (1-50) |
| `--maximum-number-of-snippets` | `--max-snippets` | 50 | Max snippets across all URLs |
| `--maximum-number-of-snippets-per-url` | `--max-snippets-per-url` | — | Max snippets per URL |
| `--context-threshold-mode` | `--threshold` | balanced | Relevance: `strict`, `balanced`, `lenient` |

```bash
bx "topic" --max-tokens 4096 --max-tokens-per-url 1024 --max-urls 5 --threshold strict
```

## Goggles — Custom Ranking

Goggles let you control which sources appear in results. Boost official docs, suppress SEO spam, or build focused search scopes. **No other search tool offers this.** Supported on `context`, `web`, and `news`.

### Domain Shortcuts

```bash
# Allowlist — only results from these domains
bx "rust axum" --include-site docs.rs --include-site github.com

# Blocklist — exclude specific domains
bx "python tutorial" --exclude-site example.com
```

`--include-site`, `--exclude-site`, and `--goggles` are mutually exclusive.

### Inline Rules

```bash
# Boost official docs, demote blog posts
bx "axum middleware tower" \
  --goggles '$boost=5,site=docs.rs
$boost=3,site=github.com
/docs/$boost=5
/blog/$downrank=3' --max-tokens 4096

# Allowlist mode — only include matched sites
bx "Python asyncio patterns" \
  --goggles '$discard
$boost,site=docs.python.org
$boost,site=peps.python.org'
```

### DSL Quick Reference

| Rule | Effect | Example |
|--|--|--|
| `$boost=N,site=DOMAIN` | Promote domain (N=1-10) | `$boost=3,site=docs.rs` |
| `$downrank=N,site=DOMAIN` | Demote domain (N=1-10) | `$downrank=5,site=example.com` |
| `$discard,site=DOMAIN` | Remove domain entirely | `$discard,site=example.com` |
| `/path/$boost=N` | Boost matching URL paths | `/docs/$boost=5` |
| Generic `$discard` | Allowlist mode — discard unmatched | `$discard` (as first rule) |

Separate rules with newlines. Full DSL: [goggles-quickstart](https://github.com/brave/goggles-quickstart).

### Piping Rules via Stdin

```bash
echo '$boost=5,site=docs.rs
$boost=5,site=crates.io
$boost=3,site=github.com' | bx "axum middleware" --goggles @- --max-tokens 4096
```

Use `@/path/to/file` to reuse a goggle across queries.

## Agent Workflow Examples

**Debugging an error:**
```bash
bx "Python TypeError cannot unpack non-iterable NoneType" --max-tokens 4096
```

**Corrective RAG loop:**
```bash
# 1. Broad search
bx "axum middleware authentication" --max-tokens 4096
# 2. Too general? Narrow with strict threshold
bx "axum middleware tower layer authentication example" --threshold strict --max-tokens 4096
# 3. Still need synthesis? Ask for an answer
bx answers "how to implement JWT auth middleware in axum" --enable-research
```

**Checking for breaking changes before upgrading:**
```bash
bx "Next.js 15 breaking changes migration guide" --max-tokens 8192
bx news "Next.js 15 release" --freshness pm
```

**Non-streaming answers for programmatic use:**
```bash
bx answers "compare SQLx and Diesel for Rust" --no-stream
```

**Answers via stdin (OpenAI-compatible JSON body):**
```bash
echo '{"messages":[{"role":"user","content":"what are the OWASP top 10 vulnerabilities for web APIs"}]}' | bx answers -
```

## Exit Codes

| Code | Meaning | Agent action |
|--|--|--|
| 0 | Success | Process results |
| 1 | Client error (bad request) | Fix query/parameters |
| 2 | Usage error (bad flags) | Fix CLI arguments |
| 3 | Auth/permission error (401/403) | Check API key: `bx config show-key` |
| 4 | Rate limited (429) | Retry after delay |
| 5 | Server/network error | Retry with backoff |

## Use Cases

- **AI agents / coding assistants**: One-call web search with token-budgeted, RAG-ready content — replaces search + scrape + extract
- **Fact-checking**: Verify claims against current web content with `bx "query" --threshold strict`
- **Documentation lookup**: Search official docs with `--include-site` or Goggles domain boosting
- **Research**: Deep multi-source research with `bx answers "topic" --enable-research`
- **Debugging**: Search for error messages and stack traces directly
- **News monitoring**: Track topics with `bx news "query" --freshness pd`
- **Local search**: Find businesses and places with `bx places "query" --location "city"`

## Notes

- **All output is JSON** to stdout; errors go to stderr
- **Global flags**: `--api-key KEY`, `--timeout SECS` (default 30), `--extra KEY=VALUE` (repeatable, adds arbitrary API parameters)
- **Location awareness**: `context` and `web` support `--lat`, `--long`, `--city`, `--state`, `--state-name`, `--loc-country`, `--postal-code`, `--timezone`
- **Research mode**: `bx answers --enable-research` can take up to 5 minutes; set client timeout accordingly
- **Help**: `bx --help` for all commands; `bx <command> --help` for per-command flags
