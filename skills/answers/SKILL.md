---
name: answers
description: "USE FOR AI-grounded answers via OpenAI-compatible /chat/completions. Two modes: single-search (fast) or deep research (enable_research=true, thorough multi-search). Streaming/blocking. Citations."
---

# Answers — AI Grounding

> **Requires API Key**: Get one at https://api.search.brave.com
> **Plan**: Available in the **Answers** plan. See https://api.search.brave.com/app/subscriptions for details.

## When to Use

| Use Case | Skill | Why |
|----------|-------|-----|
| Quick factual answer (raw context) | `llm-context` | Single search, returns raw context for YOUR LLM |
| Fast AI answer with citations | **`answers`** (single-search) | streaming, citations |
| Thorough multi-search deep research | **`answers`** (research mode) | Iterative deep research, synthesized cited answer |

**This endpoint** (`/res/v1/chat/completions`) supports two modes:
- **Single-search** (default): Fast AI-grounded answer from a single search. Supports `enable_citations`.
- **Research** (`enable_research=true`): Multi-iteration deep research with progress events and synthesized cited answer.

## Use Cases

- **Chat interface integration**: Drop-in OpenAI SDK replacement with web-grounded answers. Set `base_url="https://api.search.brave.com/res/v1"`.
- **Deep research / comprehensive topic research**: Use research mode (`enable_research=true`) for complex questions needing multi-source synthesis (e.g., "Compare approaches to nuclear fusion").
- **OpenAI SDK drop-in**: Same SDK, same streaming format — just change `base_url` and `api_key`. Works with both sync and async clients.
- **Cited answers**: Enable `enable_citations=true` in single-search mode for inline citation tags, or use research mode which automatically includes citations in its answer.

## Quick Start (cURL)

### Blocking (Single-Search)
```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [{"role": "user", "content": "What is the capital of France?"}],
    "model": "brave",
    "stream": false
  }'
```

### Streaming with Citations (Single-Search)
```bash
curl -X POST "https://api.search.brave.com/res/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "X-Subscription-Token: ${BRAVE_SEARCH_API_KEY}" \
  -d '{
    "messages": [{"role": "user", "content": "What are recent breakthroughs in fusion energy?"}],
    "model": "brave",
    "stream": true,
    "enable_citations": true
  }'
```

### Research Mode
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

**Authentication**: `X-Subscription-Token: <BRAVE_SEARCH_API_KEY>` header (or `Authorization: Bearer <BRAVE_SEARCH_API_KEY>`)

**SDK Compatible**: Works with OpenAI SDK via `base_url="https://api.search.brave.com/res/v1"`

## Two Modes

| Feature | Single-Search (default) | Research (`enable_research=true`) |
|---------|------------------------|----------------------------------|
| Speed | Fast | Slow |
| Searches | 1 | Multiple (iterative) |
| Streaming | Optional (`stream=true/false`) | **Required** (`stream=true`) |
| Citations | `enable_citations=true` (streaming only) | Built-in (in `<answer>` tag) |
| Progress events | No | Yes (`<progress>` tags) |
| Blocking response | Yes (`stream=false`) | No |

## Parameters

### Standard Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `messages` | array | **Yes** | - | Single user message (exactly 1 message) |
| `model` | string | **Yes** | - | Use `"brave"` |
| `stream` | bool | No | true | Enable SSE streaming |
| `country` | string | No | "us" | Search country code |
| `language` | string | No | "en" | Response language |
| `safesearch` | string | No | "moderate" | Search safety level (`off`, `moderate`, `strict`) |
| `max_completion_tokens` | int | No | null | Upper bound on completion tokens |
| `enable_citations` | bool | No | false | Include inline citation tags (single-search streaming only) |
| `web_search_options` | object | No | null | OpenAI-compatible; `search_context_size`: `low`, `medium`, `high` |

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

### Constraints (IMPORTANT)

| Constraint | Error |
|------------|-------|
| `enable_research=true` requires `stream=true` | "Blocking response doesn't support 'enable_research' option" |
| `enable_research=true` incompatible with `enable_citations=true` | "Research mode doesn't support 'enable_citations' option" |
| `enable_citations=true` requires `stream=true` | "Blocking response doesn't support 'enable_citations' option" |

## OpenAI SDK Usage

### Blocking (Single-Search)
```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.search.brave.com/res/v1",
    api_key="your-brave-api-key",
)

response = client.chat.completions.create(
    model="brave",
    messages=[{"role": "user", "content": "What is the capital of France?"}],
    stream=False,
)
print(response.choices[0].message.content)
```

### Streaming with Citations (Single-Search)
```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.search.brave.com/res/v1",
    api_key="your-brave-api-key",
)

stream = client.chat.completions.create(
    model="brave",
    messages=[{"role": "user", "content": "What are the latest AI safety developments?"}],
    stream=True,
    extra_body={"enable_citations": True}
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

### Research Mode
```python
from openai import AsyncOpenAI

client = AsyncOpenAI(
    base_url="https://api.search.brave.com/res/v1",
    api_key="your-brave-api-key",
)

stream = await client.chat.completions.create(
    model="brave",
    messages=[{"role": "user", "content": "Compare quantum computing approaches"}],
    stream=True,
    extra_body={
        "enable_research": True,
        "research_maximum_number_of_iterations": 3,
        "research_maximum_number_of_seconds": 120
    }
)

async for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
```

## Response Format

### Blocking Response (`stream=false`, single-search only)

Standard OpenAI-compatible JSON:

```json
{
  "id": "chatcmpl-...",
  "object": "chat.completion",
  "choices": [{"message": {"role": "assistant", "content": "Paris is the capital..."}, "index": 0, "finish_reason": "stop"}],
  "usage": {"prompt_tokens": 10, "completion_tokens": 50, "total_tokens": 60}
}
```

### Streaming Response

SSE response with OpenAI-compatible chunks:

```text
data: {"id":"chatcmpl-...","object":"chat.completion.chunk","choices":[{"delta":{"content":"Based on"},"index":0}]}

data: {"id":"chatcmpl-...","object":"chat.completion.chunk","choices":[{"delta":{"content":" recent research"},"index":0}]}

data: [DONE]
```

### Streaming Tags by Mode

#### Single-Search (with `enable_citations=true`)

| Tag | Purpose |
|-----|---------|
| `<citation>` | Inline citation references |
| `<usage>` | JSON cost/billing data |

#### Research Mode

| Tag | Purpose | Keep? |
|-----|---------|-------|
| `<queries>` | Generated search queries | Debug |
| `<analyzing>` | URL counts (verbose) | Debug |
| `<thinking>` | URL selection reasoning | Debug |
| `<progress>` | Stats: time, iterations, queries, URLs analyzed, tokens | Monitor |
| `<blindspots>` | Knowledge gaps identified | **Yes** |
| `<answer>` | Final synthesized answer (only the final answer is emitted; intermediate drafts are dropped) | **Yes** |
| `<usage>` | JSON cost/billing data (injected by API gateway) | **Yes** |

### Usage Tag Format

The `<usage>` tag contains JSON-stringified cost and token data:

```text
<usage>{"X-Request-Requests":1,"X-Request-Queries":8,"X-Request-Tokens-In":15000,"X-Request-Tokens-Out":2000,"X-Request-Requests-Cost":0.005,"X-Request-Queries-Cost":0.032,"X-Request-Tokens-In-Cost":0.075,"X-Request-Tokens-Out-Cost":0.01,"X-Request-Total-Cost":0.122}</usage>
```

## Notes

- **Timeout**: Set client timeout to at least 30s for single-search, 300s (5 min) for research
- **Single message**: The `messages` array must contain exactly 1 user message
- **Cost monitoring**: Parse the `<usage>` tag from streaming responses to track costs
