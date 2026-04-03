<div align="center">

# 🔌 Topic 2: OpenAI & Anthropic APIs

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-2_Days-blue)
![Phase](https://img.shields.io/badge/Phase-3_LLM_Fundamentals-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > **OpenAI & Anthropic APIs**

---

## 🎯 Why This Matters for AI Systems Engineering

These are the two most important LLM providers. You'll call their APIs thousands of times in production. Key skills:
- **Synchronous and streaming calls** → Different UX patterns
- **Function calling** → Let LLMs trigger your code
- **Structured output** → Get JSON, not just text
- **Error handling** → Rate limits, timeouts, retries
- **Cost management** → Track tokens and optimize spend

---

## 📚 Core Concepts

### 1. OpenAI API — Chat Completions

```python
from openai import OpenAI

client = OpenAI(api_key="sk-your-key-here")  # Or set OPENAI_API_KEY env var

# Basic completion
response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a helpful AI assistant specializing in software engineering."},
        {"role": "user", "content": "Explain RAG in 3 sentences."},
    ],
    temperature=0.7,
    max_tokens=200,
)

print(response.choices[0].message.content)
print(f"Tokens: {response.usage.total_tokens} (cost: ~${response.usage.total_tokens * 0.00003:.4f})")
```

### 2. Streaming Responses

```python
# Streaming — tokens arrive one at a time (ChatGPT-like experience)
stream = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Write a haiku about coding"}],
    stream=True,
)

full_response = ""
for chunk in stream:
    if chunk.choices[0].delta.content:
        token = chunk.choices[0].delta.content
        print(token, end="", flush=True)
        full_response += token

print(f"\n\nFull response: {full_response}")
```

### 3. Function Calling (Tool Use)

```python
import json

tools = [
    {
        "type": "function",
        "function": {
            "name": "search_documents",
            "description": "Search the knowledge base for relevant documents",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string", "description": "Search query"},
                    "top_k": {"type": "integer", "description": "Number of results", "default": 5},
                    "source_filter": {"type": "string", "description": "Filter by source", "enum": ["docs", "blog", "code"]},
                },
                "required": ["query"],
            },
        },
    },
]

response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Find me documents about RAG evaluation metrics"}],
    tools=tools,
    tool_choice="auto",
)

# Check if the model wants to call a function
if response.choices[0].message.tool_calls:
    tool_call = response.choices[0].message.tool_calls[0]
    function_name = tool_call.function.name
    arguments = json.loads(tool_call.function.arguments)
    print(f"Function: {function_name}")
    print(f"Arguments: {arguments}")
    # → Function: search_documents
    # → Arguments: {'query': 'RAG evaluation metrics', 'top_k': 5}
```

### 4. Structured Output with Response Format

```python
from pydantic import BaseModel

class SentimentResult(BaseModel):
    sentiment: str  # "positive", "negative", "neutral"
    confidence: float
    key_phrases: list[str]
    summary: str

response = client.chat.completions.create(
    model="gpt-4-turbo",
    messages=[
        {"role": "system", "content": "Analyze the sentiment of the given text. Return structured JSON."},
        {"role": "user", "content": "I absolutely love this new AI framework! It makes everything so much easier."},
    ],
    response_format={"type": "json_object"},
)

result = json.loads(response.choices[0].message.content)
print(json.dumps(result, indent=2))
```

### 5. Anthropic (Claude) API

```python
import anthropic

client = anthropic.Anthropic(api_key="sk-ant-your-key")

message = client.messages.create(
    model="claude-3-sonnet-20240229",
    max_tokens=1024,
    system="You are an expert AI systems engineer.",
    messages=[
        {"role": "user", "content": "Compare ChromaDB vs Pinecone for RAG systems."},
    ],
)

print(message.content[0].text)
print(f"Input tokens: {message.usage.input_tokens}")
print(f"Output tokens: {message.usage.output_tokens}")
```

### 6. Production Error Handling

```python
from openai import OpenAI, RateLimitError, APIError, APITimeoutError
import time

client = OpenAI()

def robust_llm_call(messages: list, model: str = "gpt-4", max_retries: int = 3) -> str:
    """Production-grade LLM call with retry logic."""
    for attempt in range(max_retries):
        try:
            response = client.chat.completions.create(
                model=model, messages=messages, timeout=30,
            )
            return response.choices[0].message.content
        
        except RateLimitError as e:
            wait_time = min(2 ** attempt * 5, 60)
            print(f"⚠️ Rate limited. Waiting {wait_time}s... (attempt {attempt+1})")
            time.sleep(wait_time)
        
        except APITimeoutError:
            print(f"⏰ Timeout. Retrying... (attempt {attempt+1})")
            time.sleep(2)
        
        except APIError as e:
            print(f"❌ API error: {e}")
            if attempt == max_retries - 1:
                raise
            time.sleep(5)
    
    raise RuntimeError("All retry attempts failed")
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **OpenAI API Docs** | 📄 Docs | [platform.openai.com/docs](https://platform.openai.com/docs) |
| **Anthropic Claude Docs** | 📄 Docs | [docs.anthropic.com](https://docs.anthropic.com/) |
| **OpenAI Cookbook** | 📄 GitHub | [github.com/openai/openai-cookbook](https://github.com/openai/openai-cookbook) |
| **DeepLearning.AI — ChatGPT Prompt Engineering** | 📹 Course | [deeplearning.ai](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/) |

---

## 📋 Quick Reference Cheatsheet

| Feature | OpenAI | Anthropic |
|---------|--------|-----------|
| Client | `OpenAI()` | `Anthropic()` |
| Chat | `client.chat.completions.create()` | `client.messages.create()` |
| System prompt | `messages` role="system" | `system` parameter |
| Streaming | `stream=True` | `stream=True` |
| Function calling | `tools` parameter | `tools` parameter |
| Structured output | `response_format` | `tool_use` pattern |
| Token counting | `tiktoken` library | API response metadata |

---

> 💡 **Pro Tip:** Always use **Ollama** (free, local) for development and testing. Switch to OpenAI/Anthropic only for production or when you need specific capabilities. This saves hundreds of dollars during development.

---

<div align="center">

[← Topic 1: How LLMs Work](../topic-1-how-llms-work/) · [📋 Phase 3](../README.md)

[Topic 3: Prompt Engineering →](../topic-3-prompt-engineering/)

</div>
