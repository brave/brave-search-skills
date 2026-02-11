# Brave Search API Skills

Official skills for using [Brave Search API](https://api.search.brave.com) with AI coding agents.

Works with **Claude Code**, **Cursor**, **GitHub Copilot**, **Codex CLI**, **Gemini CLI**, **VS Code**, **Windsurf**, **Cline**, **Goose**, **Amp**, **Roo Code**, and [27+ other agents](https://agentskills.io) that support the Agent Skills standard.

## Prerequisites

Get a Brave Search API key at https://api.search.brave.com

Configure in your agent:

**Claude Code** - Add to `~/.claude/settings.json`:
```json
{
  "env": {
    "BRAVE_SEARCH_API_KEY": "your-key"
  }
}
```

**Other agents** - Set environment variable:
```bash
export BRAVE_SEARCH_API_KEY="your-key"
```

## Installation

All agents below support the [Agent Skills](https://agentskills.io) standard and read SKILL.md files from their skills directory.

### Claude Code

```bash
# Project-level (recommended)
git clone https://github.com/brave/brave-search-skills
cp -r brave-search-skills/skills/* .claude/skills/

# User-level (available in all projects)
cp -r brave-search-skills/skills/* ~/.claude/skills/
```

### Cursor

Cursor natively reads skills from `.cursor/skills/`, `.claude/skills/`, and `.codex/skills/` at both project and user level.

```bash
# Project-level
cp -r brave-search-skills/skills/* .cursor/skills/

# User-level
cp -r brave-search-skills/skills/* ~/.cursor/skills/
```

You can also add skills via **Settings > Rules > Add Rule > Remote Rule** using the GitHub URL.

### GitHub Copilot

```bash
cp -r brave-search-skills/skills/* .github/skills/
```

### Codex CLI

Codex reads from `.agents/skills/` at repo, parent, root, and user levels.

```bash
# Project-level
cp -r brave-search-skills/skills/* .agents/skills/

# User-level
cp -r brave-search-skills/skills/* ~/.agents/skills/
```

### Other Agents (Windsurf, Cline, Gemini CLI, Goose, Amp, Roo Code, etc.)

All Agent Skills-compatible agents read from standard skill directories. Copy skills to the agent's skills directory or reference `AGENTS.md`.

### OpenSkills (Universal Installer)

```bash
npx openskills install brave/brave-search-skills
```

### Updating

Pull the latest changes and re-copy, or re-run the OpenSkills install command:

```bash
cd brave-search-skills && git pull
# Then re-copy to your agent's skills directory
```

See the full list of compatible agents at [agentskills.io](https://agentskills.io).

## Available Skills

| Skill | Description | Endpoint | Best For |
|-------|-------------|----------|----------|
| **llm-context** | Pre-extracted web content for LLM grounding (GET/POST) | `/res/v1/llm/context` | RAG, AI agents — **recommended** |
| **answers** | AI-grounded answers, OpenAI SDK compatible | `/res/v1/chat/completions` | Chat interfaces, cited answers |
| **grounding-context** | Search + pre-extracted content (GET only) | `/res/v1/grounding/context` | LLM grounding, RAG |
| **web-search** | Ranked web results with snippets and rich data | `/res/v1/web/search` | General search queries |
| **deep-research** | Multi-iteration research with citations (streaming required) | `/res/v1/chat/completions` | Complex research questions |
| **images-search** | Image search with thumbnails (up to 200 results) | `/res/v1/images/search` | Finding images |
| **news-search** | News articles with freshness filtering | `/res/v1/news/search` | Current events, breaking news |
| **videos-search** | Video search with duration/views/creator | `/res/v1/videos/search` | Finding video content |
| **suggest** | Query autocomplete (<100ms response) | `/res/v1/suggest/search` | Search UX, query expansion |
| **spellcheck** | Spell correction for query cleanup | `/res/v1/spellcheck/search` | Query preprocessing |

## Quick Start

### LLM Context (Recommended for AI)

Returns search results with pre-extracted web content, optimized for LLM grounding:

```bash
curl -X GET "https://api.search.brave.com/res/v1/llm/context?q=search+API+for+grounding+LLMs" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Web Search

Standard search with snippets, URLs, and metadata:

```bash
curl -s "https://api.search.brave.com/res/v1/web/search?q=retrieval+augmented+generation+explained" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Answers (AI-Grounded)

OpenAI SDK-compatible endpoint for AI-grounded answers with citations.

Fast single-search (blocking):

```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [{"role": "user", "content": "What programming languages are trending in 2025?"}],
    "model": "brave",
    "stream": false
  }'
```

Research mode (streaming required):

```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [{"role": "user", "content": "How are developers building AI-powered search applications in 2025?"}],
    "model": "brave",
    "stream": true,
    "enable_research": true
  }'
```

## Goggles (Custom Ranking)

Brave's unique feature lets you filter, boost, or downrank results:

```bash
# Focus on specific domains only
curl -X GET "https://api.search.brave.com/res/v1/llm/context" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -G \
  --data-urlencode "q=rust programming" \
  --data-urlencode 'goggles=$discard
$site=docs.rs
$site=rust-lang.org'
```

Learn more: https://search.brave.com/help/goggles

## Documentation

- **API Reference**: https://api.search.brave.com/docs
- **Goggles Quickstart**: https://github.com/brave/goggles-quickstart
- **Rate Limits**: Check your API plan at https://api.search.brave.com

## Contributing

Issues and pull requests welcome at https://github.com/brave/brave-search-skills

## License

MIT
