# API Key Setup

## Getting Your API Key

1. Go to https://api.search.brave.com
2. Sign up or log in
3. Navigate to your dashboard
4. Create or copy your API key

## Configuration by Agent

### Claude Code

Add to `~/.claude/settings.json`:

```json
{
  "env": {
    "BRAVE_SEARCH_API_KEY": "your-api-key-here"
  }
}
```

### Cursor

Add to your workspace `.cursorrules` or global settings:

```
BRAVE_SEARCH_API_KEY=your-api-key-here
```

Or set in your shell profile (`~/.bashrc`, `~/.zshrc`):

```bash
export BRAVE_SEARCH_API_KEY="your-api-key-here"
```

### Windsurf / Cline / Aider

Set environment variable in your shell profile:

```bash
export BRAVE_SEARCH_API_KEY="your-api-key-here"
```

### GitHub Codex

Add to repository secrets or your `.env` file:

```
BRAVE_SEARCH_API_KEY=your-api-key-here
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

Brave Search API offers different plans with varying rate limits and features:

| Feature | Free | Pro | Enterprise |
|---------|------|-----|------------|
| Web Search | Yes | Yes | Yes |
| Grounding Context | Yes | Yes | Yes |
| Deep Research | No | Yes | Yes |
| Summarizer | No | Yes | Yes |
| Local POIs | No | Yes | Yes |
| URL Submit | No | Yes | Yes |
| Rate Limits | 1 req/sec | Higher | Custom |

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
