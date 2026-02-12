# Brave Search API Skills

Official skills for using [Brave Search API](https://api.search.brave.com) with AI coding agents.

Works with **Claude Code**, **Cursor**, **GitHub Copilot**, **Codex**, **Gemini CLI**, **VS Code**, **Windsurf**, **OpenClaw**, **Cline**, **Goose**, **Amp**, **Roo Code**, and [many other agents](https://agentskills.io) that support the Agent Skills standard.

<div align="center">

[Prerequisites](#prerequisites) &nbsp;&middot;&nbsp; [Installation](#installation) &nbsp;&middot;&nbsp; [Available Skills](#available-skills) &nbsp;&middot;&nbsp; [Quick Start](#quick-start) &nbsp;&middot;&nbsp; [Goggles](#goggles-custom-ranking) &nbsp;&middot;&nbsp; [Documentation](#documentation)

</div>

## Prerequisites

Get a Brave Search API key at https://api.search.brave.com

> **Security tip:** Prefer agent-native config over shell profile exports. Coding agents can access environment variables — scoped configs limit exposure. See [API key setup](docs/api-key-setup.md) for all options.

### Claude Code

Add to `~/.claude/settings.json` ([docs](https://code.claude.com/docs/en/settings)):

```json
{
  "env": {
    "BRAVE_SEARCH_API_KEY": "your-key"
  }
}
```

This makes the key available in all Claude Code sessions. For per-project use, add to `.claude/settings.local.json` (gitignored) with the same format.

### Cursor

**Option 1 — direnv** (directory-scoped, auto-loads/unloads):

```bash
# Install direnv (https://direnv.net), then in your project directory:
echo 'export BRAVE_SEARCH_API_KEY="your-key"' >> .envrc
direnv allow
```

**Option 2 — Shell profile** (`~/.zshrc` or `~/.bashrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-key"
```

Then restart Cursor (launch from terminal or fully quit and reopen — reloading the window is not enough). Cursor inherits environment variables from your shell. You can also add skills via **Settings > Rules > Add Rule > Remote Rule** using the GitHub URL.

### Codex

**Option 1 — config.toml** ([docs](https://developers.openai.com/codex/config-reference)):

```toml
# ~/.codex/config.toml
[shell_environment_policy]
set = { BRAVE_SEARCH_API_KEY = "your-key" }
```

**Option 2 — Shell profile** (`~/.zshrc` or `~/.bashrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-key"
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

### Other agents

**Option 1 — direnv** (directory-scoped, auto-loads/unloads):

```bash
# Install direnv (https://direnv.net), then in your project directory:
echo 'export BRAVE_SEARCH_API_KEY="your-key"' >> .envrc
direnv allow
```

**Option 2 — Shell profile** (`~/.zshrc` or `~/.bashrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-key"
```

## Installation

All agents below support the [Agent Skills](https://agentskills.io) standard and read SKILL.md files from their skills directory.

### Claude Code

[Skills documentation](https://code.claude.com/docs/en/skills)

**Plugin marketplace** (auto-updates, no git needed):

```
/plugin marketplace add brave/brave-search-skills
/plugin install brave-search-skills@brave-search
```

**curl** (no git, no marketplace):

```bash
# User-level (available in all projects)
mkdir -p ~/.claude/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C ~/.claude/skills --strip-components=2 brave-search-skills-main/skills

# Project-level
mkdir -p .claude/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C .claude/skills --strip-components=2 brave-search-skills-main/skills
```

**Manual** (git clone + cp):

```bash
git clone https://github.com/brave/brave-search-skills
cp -r brave-search-skills/skills/* ~/.claude/skills/   # user-level
cp -r brave-search-skills/skills/* .claude/skills/      # project-level
```

### Cursor

[Skills documentation](https://cursor.com/docs/context/skills)

**Remote Rule** (no terminal needed):

Settings → Rules → Project Rules → Add Rule → Remote Rule → paste `https://github.com/brave/brave-search-skills`

**curl:**

```bash
# Project-level
mkdir -p .cursor/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C .cursor/skills --strip-components=2 brave-search-skills-main/skills

# User-level
mkdir -p ~/.cursor/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C ~/.cursor/skills --strip-components=2 brave-search-skills-main/skills
```

**Manual** (cp — requires git clone above):

```bash
cp -r brave-search-skills/skills/* .cursor/skills/      # project-level
cp -r brave-search-skills/skills/* ~/.cursor/skills/     # user-level
```

Cursor natively reads skills from `.cursor/skills/`, `.claude/skills/`, and `.codex/skills/` at both project and user level.

### GitHub Copilot

**curl:**

```bash
mkdir -p .github/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C .github/skills --strip-components=2 brave-search-skills-main/skills
```

**Manual** (cp — requires git clone above):

```bash
cp -r brave-search-skills/skills/* .github/skills/
```

### Codex

[Skills documentation](https://developers.openai.com/codex/skills)

**Skill installer** (built-in — ask Codex to install skills from `https://github.com/brave/brave-search-skills`).

**curl:**

```bash
# User-level
mkdir -p ~/.agents/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C ~/.agents/skills --strip-components=2 brave-search-skills-main/skills

# Project-level
mkdir -p .agents/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C .agents/skills --strip-components=2 brave-search-skills-main/skills
```

**Manual** (cp — requires git clone above):

```bash
cp -r brave-search-skills/skills/* ~/.agents/skills/    # user-level
cp -r brave-search-skills/skills/* .agents/skills/       # project-level
```

Codex reads from `.agents/skills/` at repo, parent, root, and user levels. Skills work across the CLI, desktop app, and IDE extension.

### Windsurf

**curl:**

```bash
# Project-level
mkdir -p .windsurf/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C .windsurf/skills --strip-components=2 brave-search-skills-main/skills

# User-level
mkdir -p ~/.codeium/windsurf/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C ~/.codeium/windsurf/skills --strip-components=2 brave-search-skills-main/skills
```

**Manual** (cp — requires git clone above):

```bash
cp -r brave-search-skills/skills/* .windsurf/skills/             # project-level
cp -r brave-search-skills/skills/* ~/.codeium/windsurf/skills/   # user-level
```

### OpenClaw

[Skills documentation](https://docs.openclaw.ai/tools/skills)

**curl:**

```bash
mkdir -p ~/.openclaw/skills && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C ~/.openclaw/skills --strip-components=2 brave-search-skills-main/skills
```

**Manual** (cp — requires git clone above):

```bash
cp -r brave-search-skills/skills/* ~/.openclaw/skills/
```

### Other Agents (Cline, Gemini CLI, Goose, Amp, Roo Code, etc.)

**curl** (adjust the target directory for your agent):

```bash
mkdir -p <skills-dir> && curl -sL https://github.com/brave/brave-search-skills/archive/main.tar.gz | tar xz -C <skills-dir> --strip-components=2 brave-search-skills-main/skills
```

Or copy skills from a git clone to the agent's skills directory. All agents following the [Agent Skills](https://agentskills.io) standard read SKILL.md files from their skills folder.

### OpenSkills (Third-Party Universal Installer)

```bash
npx openskills install brave/brave-search-skills
```

See [openskills on GitHub](https://github.com/numman-ali/openskills) for details.

### Updating

**Claude Code marketplace**: updates automatically, or run `/plugin marketplace update brave-search`.

**curl**: re-run the curl command above to overwrite with the latest version.

**git clone**: pull the latest changes and re-copy:

```bash
cd brave-search-skills && git pull
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
| **[llm-context](skills/llm-context/SKILL.md)** | Pre-extracted web content for LLM grounding (GET/POST) | `/res/v1/llm/context` | RAG, AI agents — **recommended** |
| **[answers](skills/answers/SKILL.md)** | AI-grounded answers, OpenAI SDK compatible | `/res/v1/chat/completions` | Chat interfaces, cited answers |
| **[web-search](skills/web-search/SKILL.md)** | Ranked web results with snippets and rich data | `/res/v1/web/search` | General search queries |
| **[images-search](skills/images-search/SKILL.md)** | Image search with thumbnails (up to 200 results) | `/res/v1/images/search` | Finding images |
| **[news-search](skills/news-search/SKILL.md)** | News articles with freshness filtering | `/res/v1/news/search` | Current events, breaking news |
| **[videos-search](skills/videos-search/SKILL.md)** | Video search with duration/views/creator | `/res/v1/videos/search` | Finding video content |
| **[local-pois](skills/local-pois/SKILL.md)** | Local business/POI details (ratings, hours, contact) | `/res/v1/local/pois` | Business info from POI IDs |
| **[local-descriptions](skills/local-descriptions/SKILL.md)** | AI-generated POI text descriptions | `/res/v1/local/descriptions` | POI summaries from POI IDs |
| **[suggest](skills/suggest/SKILL.md)** | Query autocomplete (<100ms response) | `/res/v1/suggest/search` | Search UX, query expansion |
| **[spellcheck](skills/spellcheck/SKILL.md)** | Spell correction for query cleanup | `/res/v1/spellcheck/search` | Query preprocessing |

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
- **Claude Code Skills**: https://code.claude.com/docs/en/skills
- **Cursor Skills**: https://cursor.com/docs/context/skills
- **Codex Skills**: https://developers.openai.com/codex/skills
- **OpenClaw Skills**: https://docs.openclaw.ai/tools/skills

## License

MIT
