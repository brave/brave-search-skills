# API Key Setup

## Getting Your API Key

1. Go to https://api.search.brave.com
2. Sign up or log in
3. Navigate to your dashboard
4. Create or copy your API key

## Configuration by Agent

> **Security tip:** Prefer agent-native config files over shell profile exports. Coding agents can access shell environment variables and profile files — scoped config limits exposure. Options below are listed from most to least secure.

### Claude Code

Add to `~/.claude/settings.json` ([docs](https://code.claude.com/docs/en/settings)):

```json
{
  "env": {
    "BRAVE_SEARCH_API_KEY": "your-api-key-here"
  }
}
```

For per-project use, add to `.claude/settings.local.json` (gitignored) with the same format.

### Cursor / Windsurf / Cline

**Option 1 — direnv** (directory-scoped, auto-loads/unloads):

```bash
# Install direnv (https://direnv.net), then in your project directory:
echo 'export BRAVE_SEARCH_API_KEY="your-api-key-here"' >> .envrc
direnv allow
```

**Option 2 — Shell profile** (`~/.bashrc`, `~/.zshrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-api-key-here"
```

### GitHub Copilot

**Option 1 — direnv** (directory-scoped, auto-loads/unloads):

```bash
# Install direnv (https://direnv.net), then in your project directory:
echo 'export BRAVE_SEARCH_API_KEY="your-api-key-here"' >> .envrc
direnv allow
```

**Option 2 — Shell profile** (`~/.bashrc`, `~/.zshrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-api-key-here"
```

### Codex CLI

**Option 1 — config.toml** ([docs](https://developers.openai.com/codex/config-reference)):

```toml
# ~/.codex/config.toml
[shell_environment_policy]
set = { BRAVE_SEARCH_API_KEY = "your-api-key-here" }
```

**Option 2 — Shell profile** (`~/.bashrc`, `~/.zshrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-api-key-here"
```

### OpenClaw

Add to `~/.openclaw/.env` ([docs](https://docs.openclaw.ai/tools/skills)):

```
BRAVE_SEARCH_API_KEY=your-api-key-here
```

Or add to `~/.openclaw/openclaw.json` under the skill's config:

```json
{
  "skills": {
    "entries": {
      "brave-search": {
        "env": {
          "BRAVE_SEARCH_API_KEY": "your-api-key-here"
        }
      }
    }
  }
}
```

### Docker / CI/CD

Pass as environment variable:

```bash
docker run -e BRAVE_SEARCH_API_KEY="your-key" your-image
```

Or in docker-compose.yml:

```yaml
services:
  app:
    environment:
      - BRAVE_SEARCH_API_KEY=${BRAVE_SEARCH_API_KEY}
```

## API Plans

Check current plans and pricing at https://api.search.brave.com

## Verifying Your Key

Test your API key with a simple curl:

```bash
curl -s "https://api.search.brave.com/res/v1/web/search?q=test" \
  -H "Accept: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  | jq '.web.results[0].title'
```

If you see a result title, your key is working.

## Troubleshooting

### "Invalid API Key" Error

- Verify the key is copied correctly (no extra spaces)
- Check the key hasn't expired
- Ensure you're using `X-Subscription-Token` header (not `Authorization`)

### "Rate Limited" Error

- Check your plan's rate limits
- Implement exponential backoff
- Consider upgrading your plan

### "Option Not in Plan" Error

- The endpoint you're calling requires a higher tier plan
- Check which features are included in your plan at https://api.search.brave.com
