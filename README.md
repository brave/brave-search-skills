# Brave Search API Skills

Official skills for using [Brave Search API](https://api.search.brave.com) with AI coding agents.

Works with **Claude Code**, **Cursor**, **GitHub Copilot**, **Codex**, **Gemini CLI**, **VS Code**, **Windsurf**, **OpenClaw**, **Cline**, **Goose**, **Amp**, **Roo Code**, and [27+ other agents](https://agentskills.io) that support the Agent Skills standard.

## Prerequisites

Get a Brave Search API key at https://api.search.brave.com

### Claude Code

Add to `~/.claude/settings.json` ([docs](https://docs.anthropic.com/en/docs/claude-code/settings)):

```json
{
  "env": {
    "BRAVE_SEARCH_API_KEY": "your-key"
  }
}
```

This makes the key available in all Claude Code sessions. For per-project use, add to `.claude/settings.local.json` (gitignored) with the same format.

### Cursor

Add to your shell profile (`~/.zshrc` or `~/.bashrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-key"
```

Then restart Cursor (launch from terminal or fully quit and reopen — reloading the window is not enough). Cursor inherits environment variables from your shell. You can also add skills via **Settings > Rules > Add Rule > Remote Rule** using the GitHub URL.

### Codex

Add to your shell profile (`~/.zshrc` or `~/.bashrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-key"
```

Or add to `~/.codex/config.toml` ([docs](https://developers.openai.com/codex/config-reference)):

```toml
[shell_environment_policy]
set = { BRAVE_SEARCH_API_KEY = "your-key" }
```

Then restart your terminal. Codex reads environment variables from the shell (CLI, app, and IDE extension).

### OpenClaw

Add to `~/.openclaw/.env` ([docs](https://docs.openclaw.ai/tools/skills)):

```
BRAVE_SEARCH_API_KEY=your-key
```

Or add to `~/.openclaw/openclaw.json` under the skill's config:

```json
{
  "skills": {
    "entries": {
      "brave-search": {
        "env": {
          "BRAVE_SEARCH_API_KEY": "your-key"
        }
      }
    }
  }
}
```

You can also set the environment variable in your shell profile.

### Other agents

Set the environment variable in your shell profile:

```bash
export BRAVE_SEARCH_API_KEY="your-key"
```

## Installation

All agents below support the [Agent Skills](https://agentskills.io) standard and read SKILL.md files from their skills directory.

### Claude Code

[Skills documentation](https://docs.anthropic.com/en/docs/claude-code/skills)

```bash
# Project-level (recommended)
git clone https://github.com/brave/brave-search-skills
cp -r brave-search-skills/skills/* .claude/skills/

# User-level (available in all projects)
cp -r brave-search-skills/skills/* ~/.claude/skills/
```

### Cursor

[Skills documentation](https://cursor.com/docs/context/skills)

Cursor natively reads skills from `.cursor/skills/`, `.claude/skills/`, and `.codex/skills/` at both project and user level.

```bash
# Project-level
cp -r brave-search-skills/skills/* .cursor/skills/

# User-level
cp -r brave-search-skills/skills/* ~/.cursor/skills/
```

You can also import skills directly from a GitHub repo URL via **Settings > Rules > Project Rules > Add Rule > Remote Rule** — no manual copying needed.

### GitHub Copilot

```bash
cp -r brave-search-skills/skills/* .github/skills/
```

### Codex

[Skills documentation](https://developers.openai.com/codex/skills)

Codex reads from `.agents/skills/` at repo, parent, root, and user levels. Skills work across the CLI, desktop app, and IDE extension.

```bash
# Project-level
cp -r brave-search-skills/skills/* .agents/skills/

# User-level
cp -r brave-search-skills/skills/* ~/.agents/skills/
```

### Windsurf

```bash
# Project-level
cp -r brave-search-skills/skills/* .windsurf/skills/

# User-level
cp -r brave-search-skills/skills/* ~/.codeium/windsurf/skills/
```

### OpenClaw

[Skills documentation](https://docs.openclaw.ai/tools/skills)

```bash
# User-level (shared across all agents)
cp -r brave-search-skills/skills/* ~/.openclaw/skills/
```

### Other Agents (Cline, Gemini CLI, Goose, Amp, Roo Code, etc.)

Copy skills to the agent's skills directory. All agents following the [Agent Skills](https://agentskills.io) standard read SKILL.md files from their skills folder.

### OpenSkills (Third-Party Universal Installer)

```bash
npx openskills install brave/brave-search-skills
```

See [openskills on GitHub](https://github.com/numman-ali/openskills) for details.

### Updating

Pull the latest changes and re-copy to your agent's skills directory:

```bash
cd brave-search-skills && git pull
# Then re-copy, e.g.:
cp -r skills/* ~/.claude/skills/    # Claude Code
cp -r skills/* .cursor/skills/      # Cursor
cp -r skills/* .agents/skills/      # Codex
cp -r skills/* ~/.openclaw/skills/  # OpenClaw
```

Or re-run the OpenSkills install command to overwrite with the latest version.

See the full list of compatible agents at [agentskills.io](https://agentskills.io).

## Available Skills

| Skill | Description | Endpoint | Best For |
|-------|-------------|----------|----------|
| **llm-context** | Pre-extracted web content for LLM grounding (GET/POST) | `/res/v1/llm/context` | RAG, AI agents — **recommended** |
| **answers** | AI-grounded answers, OpenAI SDK compatible | `/res/v1/chat/completions` | Chat interfaces, cited answers |
| **web-search** | Ranked web results with snippets and rich data | `/res/v1/web/search` | General search queries |
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
    "messages": [{"role": "user", "content": "What programming languages are trending in 2026?"}],
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
    "messages": [{"role": "user", "content": "How are developers building AI-powered search applications in 2026?"}],
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
- **Agent Skills Standard**: https://agentskills.io/specification
- **Claude Code Skills**: https://docs.anthropic.com/en/docs/claude-code/skills
- **Cursor Skills**: https://cursor.com/docs/context/skills
- **Codex Skills**: https://developers.openai.com/codex/skills
- **OpenClaw Skills**: https://docs.openclaw.ai/tools/skills

## License

MIT
