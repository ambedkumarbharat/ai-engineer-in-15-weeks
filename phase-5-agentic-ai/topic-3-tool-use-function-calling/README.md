<div align="center">

# 🛠️ Topic 3: Tool Use & Function Calling

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow) ![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **Tool Use & Function Calling**

---

## 🎯 Why This Matters

Tools give agents **superpowers** — ability to search the web, query databases, run code, send emails, and interact with any API. Function calling is how LLMs communicate which tools to use and with what arguments.

---

## 📚 Core Concepts

### 1. Custom Tools with LangChain

```python
from langchain_core.tools import tool
from pydantic import BaseModel, Field

class SearchInput(BaseModel):
    query: str = Field(description="Search query")
    max_results: int = Field(default=5, description="Max number of results")

@tool(args_schema=SearchInput)
def search_knowledge_base(query: str, max_results: int = 5) -> str:
    """Search the internal knowledge base for relevant documents."""
    # In production: query ChromaDB or Pinecone
    return f"Found {max_results} results for '{query}'"

@tool
def get_current_weather(city: str) -> str:
    """Get the current weather for a city."""
    # In production: call weather API
    return f"Weather in {city}: 25°C, sunny"

@tool
def run_python_code(code: str) -> str:
    """Execute Python code and return the output."""
    try:
        result = eval(code)
        return f"Output: {result}"
    except Exception as e:
        return f"Error: {e}"

# Bind tools to LLM
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4").bind_tools([search_knowledge_base, get_current_weather, run_python_code])

response = llm.invoke("What's the weather in Mumbai and what's 25 * 4?")
print(response.tool_calls)
# [{'name': 'get_current_weather', 'args': {'city': 'Mumbai'}},
#  {'name': 'run_python_code', 'args': {'code': '25 * 4'}}]
```

### 2. Tool Execution Loop

```python
async def execute_tools(llm_response, tools_map: dict) -> list[str]:
    """Execute all tool calls from an LLM response."""
    results = []
    for tool_call in llm_response.tool_calls:
        tool_fn = tools_map[tool_call["name"]]
        result = tool_fn.invoke(tool_call["args"])
        results.append(f"{tool_call['name']}: {result}")
    return results
```

### 3. Best Practices for Tool Design

| Principle | Description |
|-----------|-------------|
| **Clear names** | `search_knowledge_base` not `search` |
| **Good descriptions** | LLM reads these to decide when to use the tool |
| **Typed arguments** | Use Pydantic schemas for validation |
| **Error handling** | Return error messages, don't raise exceptions |
| **Idempotent** | Same input → same output (when possible) |
| **Rate limited** | External API tools need rate limiting |

---

> 💡 **Pro Tip:** The tool description is the most important part — it's what the LLM reads to decide when to use the tool. Spend time writing clear, specific descriptions with examples of when to use each tool.

---

<div align="center">

[← Topic 2](../topic-2-langgraph/) · [📋 Phase 5](../README.md) · [Topic 4 →](../topic-4-multi-agent-crewai/)

</div>
