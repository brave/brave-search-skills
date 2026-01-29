---
name: sapi-deep-research
description: USE WHEN researching complex topics via Search API. OpenAI SDK compatible. Returns comprehensive answers with citations through iterative multi-search. Set enable_research=true. Best for thorough research - use grounding-context for quick answers. Requires BRAVE_SEARCH_API_KEY + grounding_research plan.
---

# Deep Research (Search API)

> **Requires API Key**: Get one at https://api.search.brave.com
> Store in `~/.claude/settings.json`: `{"env": {"BRAVE_SEARCH_API_KEY": "your-key"}}`
> **Plan**: Requires `grounding_research` plan option

## When to Use This Skill

Use for **complex research questions** that benefit from multiple search iterations:
- Questions requiring synthesis from multiple sources
- Topics where initial search reveals knowledge gaps
- Research needing comprehensive coverage with citations

**For simple factual questions**, use `sapi-grounding-context` instead - it's faster and sufficient for single-search answers.

## How It Works

Deep Research performs **iterative multi-query research**:
1. Issues initial search query
2. Extracts facts from each source
3. Identifies knowledge blindspots
4. Generates follow-up queries to fill gaps
5. Repeats for up to 5 iterations (configurable)
6. Synthesizes final answer with inline citations

## Quick Start (cURL)

### Basic Research Query
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

### With Custom Research Limits
```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [{"role": "user", "content": "Compare quantum computing approaches"}],
    "model": "brave",
    "stream": true,
    "enable_research": true,
    "research_maximum_number_of_iterations": 3,
    "research_maximum_number_of_seconds": 120
  }'
```

## Endpoint

```http
POST https://api.search.brave.com/res/v1/chat/completions
```

**Authentication**: `X-Subscription-Token: <API_KEY>` header

**Response**: Server-Sent Events (SSE) stream when `stream: true`

**SDK Compatible**: Works with OpenAI SDK via `base_url="https://api.search.brave.com/res/v1"`

## OpenAI SDK Usage

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.search.brave.com/res/v1",
    api_key="your-brave-api-key",
    default_headers={"X-Subscription-Token": "your-brave-api-key"}
)

# Streaming research
stream = client.chat.completions.create(
    model="brave",
    messages=[{"role": "user", "content": "What are the latest AI safety developments?"}],
    stream=True,
    extra_body={
        "enable_research": True,
        "research_maximum_number_of_iterations": 3,
        "research_maximum_number_of_seconds": 120
    }
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

## Request Parameters

### Standard Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `messages` | array | **required** | Chat messages array |
| `model` | string | **required** | Use "brave" |
| `stream` | bool | false | Enable SSE streaming |
| `country` | string | "us" | Search country code |
| `language` | string | "en" | Response language |
| `enable_entities` | bool | false | Include entity info (NOT compatible with research mode) |
| `enable_citations` | bool | false | Include inline citations (NOT compatible with research mode) |

### Research Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| `enable_research` | bool | false | - | **Enable research mode** |
| `research_allow_thinking` | bool | true | - | Enable extended thinking |
| `research_maximum_number_of_tokens_per_query` | int | 8192 | 1024-16384 | Max tokens per query |
| `research_maximum_number_of_queries` | int | 20 | 1-50 | Max total search queries |
| `research_maximum_number_of_iterations` | int | 4 | 1-5 | Max research iterations |
| `research_maximum_number_of_seconds` | int | 180 | 1-300 | Time budget (seconds) |
| `research_maximum_number_of_results_per_query` | int | 60 | 1-60 | Results per search query |

### Constraints

| Constraint | Reason |
|------------|--------|
| `stream` must be `true` | Research takes too long for blocking responses |
| `enable_citations` must be `false` | Research mode has its own citation format |
| `enable_entities` must be `false` | Not compatible with research mode |

**Error messages if violated:**
- "Research mode doesn't support 'enable_citations' option"
- "Research mode doesn't support 'enable_entities' option"
- "Blocking response doesn't support 'enable_research' option"

## Response Format (Streaming)

When `stream: true`, response is SSE with OpenAI-compatible chunks:

```text
data: {"id":"chatcmpl-...","object":"chat.completion.chunk","choices":[{"delta":{"content":"Based on"},"index":0}]}

data: {"id":"chatcmpl-...","object":"chat.completion.chunk","choices":[{"delta":{"content":" recent research"},"index":0}]}

data: [DONE]
```

### Research-Specific Events

In addition to standard chat chunks, research mode may include progress events:

```text
data: {"type":"progress","elapsed_seconds":15,"queries_completed":5}

data: {"type":"blindspots","blindspots":["error rates","scalability"]}

data: {"type":"sources","urls":["https://example.com/article1"]}
```

## Example: Full Research Request

```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [
      {"role": "user", "content": "What are the environmental impacts and benefits of different renewable energy sources? Compare solar, wind, hydro, and geothermal."}
    ],
    "model": "brave",
    "stream": true,
    "enable_research": true,
    "country": "us",
    "language": "en",
    "research_maximum_number_of_iterations": 4,
    "research_maximum_number_of_queries": 30,
    "research_maximum_number_of_seconds": 180
  }'
```

## Python Processing Example

```python
import httpx
import json

url = "https://api.search.brave.com/res/v1/chat/completions"
headers = {
    "Content-Type": "application/json",
    "X-Subscription-Token": os.environ["BRAVE_SEARCH_API_KEY"]
}
payload = {
    "messages": [{"role": "user", "content": "Latest developments in CRISPR gene editing"}],
    "model": "brave",
    "stream": True,
    "enable_research": True,
    "research_maximum_number_of_iterations": 3
}

with httpx.stream("POST", url, json=payload, headers=headers, timeout=300) as response:
    for line in response.iter_lines():
        if line.startswith("data: ") and line != "data: [DONE]":
            chunk = json.loads(line[6:])
            if "choices" in chunk and chunk["choices"][0]["delta"].get("content"):
                print(chunk["choices"][0]["delta"]["content"], end="", flush=True)
```

## Parsing Streaming Output

Content streams inside `choices[0].delta.content` with XML-like tags:

| Tag | Purpose | Keep? |
|-----|---------|-------|
| `<queries>` | Generated search queries | Debug |
| `<analyzing>` | URL counts (verbose) | No |
| `<thinking>` | URL selection (verbose) | No |
| `<progress>` | Stats: time, iterations | Monitor |
| `<insights>` | **Facts extracted per URL** | **Yes** |
| `<blindspots>` | **Knowledge gaps identified** | **Yes** |
| `<answer>` | **Final synthesized answer** | **Yes** |

### Extract Key Results (Recommended)
```bash
# Extract all key results from single request (save stream, then parse)
curl -s -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{"messages":[{"role":"user","content":"QUERY"}],"model":"brave","stream":true,"enable_research":true}' \
  --max-time 300 > /tmp/research.txt && \
grep -oP '<(answer|insights|blindspots)>\K[^<]+' /tmp/research.txt | \
jq -rs '{answer: .[0], insights: .[1], blindspots: .[2]}'
```

### With Progress Updates
```bash
curl ... | tee >(grep -oP '<progress>\K[^<]+' | jq -r '"⏳ \(.number_of_iterations) iter, \(.number_of_urls_analyzed) URLs"' 2>/dev/null) | \
  grep -oP '"answer":\s*"\K[^"]+(?="\}</answer>)'
```

## Plan Requirements

| Plan Option | Feature |
|-------------|---------|
| `grounding_single` | Single-search grounding (grounding-context) |
| `grounding_research` | Multi-search research mode (this skill) |

Check your plan at https://api.search.brave.com

## Notes

- **Timeout**: Set client timeout to at least 300s (5 min)
- **Streaming**: Recommended for research mode due to longer processing
- **Citations**: Research mode automatically includes citations in the `<answer>` tag. Do NOT use `enable_citations` with research mode (incompatible)
- **Rate limits**: Check your API plan for limits
- **Cost**: Research mode uses more resources than single-search

## Related Skills

- `sapi-grounding-context`: Fast single-search grounding (use for simple questions)
- `brave-dev-deep-research`: Internal version (requires VPN, no API key)
