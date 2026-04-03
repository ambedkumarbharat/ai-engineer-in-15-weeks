<div align="center">

# 🔄 Topic 2: LangGraph

![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red) ![Time](https://img.shields.io/badge/Time-3_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **LangGraph**

---

## 🎯 Why This Matters

LangGraph is the **production framework** for building stateful AI agents. It uses graph-based workflows with nodes (actions), edges (transitions), and state management — enabling complex, cyclical agent behaviors.

---

## 📚 Core Concepts

### 1. Basic LangGraph Workflow

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated
from operator import add

class AgentState(TypedDict):
    messages: Annotated[list, add]
    next_action: str

def research_node(state: AgentState) -> dict:
    """Research step — gather information."""
    return {"messages": ["Researched the topic"], "next_action": "analyze"}

def analyze_node(state: AgentState) -> dict:
    """Analysis step — process gathered info."""
    return {"messages": ["Analyzed findings"], "next_action": "report"}

def report_node(state: AgentState) -> dict:
    """Report step — generate final output."""
    return {"messages": ["Generated report"], "next_action": "end"}

def router(state: AgentState) -> str:
    """Route to next node based on state."""
    return state.get("next_action", "end")

# Build graph
graph = StateGraph(AgentState)
graph.add_node("research", research_node)
graph.add_node("analyze", analyze_node)
graph.add_node("report", report_node)

graph.set_entry_point("research")
graph.add_conditional_edges("research", router, {"analyze": "analyze", "end": END})
graph.add_conditional_edges("analyze", router, {"report": "report", "end": END})
graph.add_edge("report", END)

app = graph.compile()
result = app.invoke({"messages": [], "next_action": "research"})
print(result["messages"])
```

### 2. Agent with Tool Use

```python
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langchain_core.tools import tool

@tool
def search_web(query: str) -> str:
    """Search the web for information."""
    return f"Search results for: {query}"

@tool  
def write_file(filename: str, content: str) -> str:
    """Write content to a file."""
    with open(filename, "w") as f:
        f.write(content)
    return f"Written to {filename}"

tools = [search_web, write_file]
tool_node = ToolNode(tools)

# Use with LLM that supports tool calling
# The graph handles: LLM decides tool → execute tool → return result → LLM continues
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **LangGraph Docs** | 📄 Docs | [langchain-ai.github.io/langgraph](https://langchain-ai.github.io/langgraph/) |
| **LangGraph Course** | 📹 Course | [DeepLearning.AI](https://www.deeplearning.ai/short-courses/ai-agents-in-langgraph/) |

> 💡 **Pro Tip:** Think of LangGraph as a state machine for AI. Each node does one thing. Edges define transitions. This makes agents debuggable and testable — unlike free-form agent loops.

---

<div align="center">

[← Topic 1](../topic-1-ai-agents-fundamentals/) · [📋 Phase 5](../README.md) · [Topic 3 →](../topic-3-tool-use-function-calling/)

</div>
