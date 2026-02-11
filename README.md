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

| Skill | Description | Best For |
|-------|-------------|----------|
| **grounding-context** | Search + pre-extracted content | LLM grounding, RAG - **recommended** |
| **web-search** | Standard web search with snippets | General search queries |
| **deep-research** | Multi-iteration comprehensive research | Complex research questions |
| **images-search** | Image search with metadata | Finding images |
| **news-search** | News articles with freshness filtering | Current events, breaking news |
| **videos-search** | Video search with duration/views | Finding video content |
| **summarizer** | AI-powered search summaries | Quick answers with citations |
| **suggest** | Query autocomplete suggestions | Search UX, query expansion |
| **spellcheck** | Spell correction before search | Query preprocessing |
| **local-pois** | Local business/POI details | Business info, hours, ratings |
| **local-descriptions** | POI text descriptions | Detailed business descriptions |
| **url-submit** | Submit URLs to Brave's index | SEO, content indexing |
| **content** | Extract page content from URLs | Content analysis |

## Quick Start

### Grounding Context (Recommended for AI)

Returns search results with pre-extracted relevant content - no need to visit pages:

```bash
curl -X GET "https://api.search.brave.com/res/v1/grounding/context?q=latest+AI+news" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Web Search

Standard search with snippets, URLs, and metadata:

```bash
curl -s "https://api.search.brave.com/res/v1/web/search?q=python+frameworks" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}"
```

### Deep Research

Multi-iteration research with OpenAI SDK compatibility:

```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [{"role": "user", "content": "What are recent breakthroughs in fusion energy?"}],
    "model": "brave",
    "stream": true,
    "enable_research": true
  }'
```

## Goggles (Custom Ranking)

Brave's unique feature lets you filter, boost, or downrank results:

```bash
# Focus on specific domains only
curl "https://api.search.brave.com/res/v1/web/search" \
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
