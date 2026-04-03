<div align="center">

# 🔨 Mini Project: AI Research Assistant Agent

### An autonomous agent that researches topics, summarizes findings, and saves reports

![Tech](https://img.shields.io/badge/LangGraph-FF6F00?style=flat-square)
![Tech](https://img.shields.io/badge/Ollama-000000?style=flat-square)
![Tech](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **Mini Project**

---

## 📋 Project Overview

Build an **AI Research Assistant** that takes a topic, searches the web, summarizes findings, and saves a structured markdown report. Uses LangGraph for orchestration with custom tools.

---

## 🧠 Concepts Used

| Concept | Where It's Used |
|---------|----------------|
| **LangGraph** | Agent workflow orchestration |
| **Tool Use** | Web search, calculator, file writer |
| **ReAct Pattern** | Think → Act → Observe loop |
| **Memory** | Maintain research context across steps |
| **Prompt Engineering** | System prompts for research quality |

---

## 🏗️ File Structure

```
research-assistant/
├── agent.py              # LangGraph agent definition
├── tools.py              # Custom tools (search, calc, file_write)
├── prompts.py            # System and task prompts
├── main.py               # CLI interface
├── reports/              # Generated research reports
├── requirements.txt
└── README.md
```

---

## 🔧 Key Implementation

```python
# tools.py
from langchain_core.tools import tool
import requests

@tool
def web_search(query: str) -> str:
    """Search the web for information about a topic.
    Use this to find facts, recent developments, and expert opinions."""
    # In production: use SerpAPI, Tavily, or Brave Search API
    # For free development: use DuckDuckGo
    from duckduckgo_search import DDGS
    results = DDGS().text(query, max_results=5)
    return "\n".join(f"- {r['title']}: {r['body']}" for r in results)

@tool
def save_report(filename: str, content: str) -> str:
    """Save a research report as a markdown file."""
    filepath = f"reports/{filename}"
    with open(filepath, "w") as f:
        f.write(content)
    return f"Report saved to {filepath}"

@tool
def calculator(expression: str) -> str:
    """Evaluate a mathematical expression."""
    return str(eval(expression))
```

```python
# agent.py
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated

class ResearchState(TypedDict):
    topic: str
    research_notes: list[str]
    report: str
    status: str

def research_step(state: ResearchState) -> dict:
    """Search and gather information."""
    results = web_search.invoke(state["topic"])
    return {"research_notes": [results], "status": "analyzing"}

def analyze_step(state: ResearchState) -> dict:
    """Analyze and summarize findings."""
    notes = "\n".join(state["research_notes"])
    # LLM summarization here
    summary = f"# Research Report: {state['topic']}\n\n{notes}"
    return {"report": summary, "status": "saving"}

def save_step(state: ResearchState) -> dict:
    """Save the final report."""
    filename = state["topic"].replace(" ", "_").lower() + ".md"
    save_report.invoke({"filename": filename, "content": state["report"]})
    return {"status": "complete"}

# Build graph
graph = StateGraph(ResearchState)
graph.add_node("research", research_step)
graph.add_node("analyze", analyze_step)
graph.add_node("save", save_step)
graph.set_entry_point("research")
graph.add_edge("research", "analyze")
graph.add_edge("analyze", "save")
graph.add_edge("save", END)

agent = graph.compile()
```

---

## ▶️ How to Run

```bash
pip install langgraph langchain-community duckduckgo-search
python main.py "AI Agents in Production 2024"
# → Researches → Summarizes → Saves reports/ai_agents_in_production_2024.md
```

---

## 📝 What to Add to Your Resume

> **AI Research Assistant Agent** — Built an autonomous research agent using LangGraph with a 3-stage pipeline (research → analyze → report). Integrated custom tools for web search (DuckDuckGo API), mathematical computation, and file I/O. Agent autonomously gathers information from multiple sources, synthesizes findings, and generates structured markdown reports. Demonstrates agent design, tool integration, and state management patterns.

---

<div align="center">

[← Topic 6](../topic-6-agent-evaluation/) · [🏠 Home](../../README.md)

**[Phase 6: Deployment →](../../phase-6-deployment-observability/)**

</div>
