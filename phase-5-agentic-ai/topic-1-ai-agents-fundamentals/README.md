<div align="center">

# 🤖 Topic 1: AI Agents Fundamentals

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **AI Agents Fundamentals**

---

## 🎯 Why This Matters

AI agents are LLMs that can **think**, **decide**, and **act**. Unlike chatbots that just respond, agents can use tools, make multi-step plans, and accomplish complex tasks autonomously.

---

## 📚 Core Concepts

### 1. The Agent Loop (ReAct Pattern)

```
┌─────────────────────────────────────┐
│          AGENT LOOP (ReAct)          │
│                                     │
│  1. OBSERVE  ← User gives task      │
│       ↓                             │
│  2. THINK    ← LLM reasons about    │
│       ↓        what to do next      │
│  3. ACT      ← Execute a tool       │
│       ↓                             │
│  4. OBSERVE  ← Get tool result      │
│       ↓                             │
│  5. THINK    ← Do I have the answer?│
│       ↓        Yes → Return answer  │
│       ↓        No  → Go to step 3   │
└─────────────────────────────────────┘
```

### 2. Building an Agent from Scratch

```python
import json
from openai import OpenAI

client = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")

# Define tools
def search_web(query: str) -> str:
    """Simulate web search."""
    results = {
        "RAG": "RAG combines retrieval with LLM generation for accurate answers.",
        "LangGraph": "LangGraph is a framework for building stateful AI agents.",
    }
    for key, val in results.items():
        if key.lower() in query.lower():
            return val
    return f"No results found for: {query}"

def calculator(expression: str) -> str:
    """Evaluate a math expression."""
    try:
        return str(eval(expression))
    except Exception as e:
        return f"Error: {e}"

TOOLS = {"search_web": search_web, "calculator": calculator}

TOOL_DESCRIPTIONS = """Available tools:
1. search_web(query: str) — Search the web for information
2. calculator(expression: str) — Evaluate math expressions

To use a tool, respond with JSON: {"tool": "tool_name", "input": "tool_input"}
To give the final answer, respond with: {"answer": "your final answer"}"""

def run_agent(task: str, max_steps: int = 5) -> str:
    """Run a simple ReAct agent."""
    messages = [
        {"role": "system", "content": f"You are a helpful agent. {TOOL_DESCRIPTIONS}"},
        {"role": "user", "content": task},
    ]
    
    for step in range(max_steps):
        response = client.chat.completions.create(
            model="llama3", messages=messages, temperature=0,
        )
        
        content = response.choices[0].message.content
        print(f"\n🤖 Step {step + 1}: {content[:100]}...")
        
        try:
            action = json.loads(content)
        except json.JSONDecodeError:
            return content  # Direct text response
        
        if "answer" in action:
            return action["answer"]
        
        if "tool" in action:
            tool_name = action["tool"]
            tool_input = action["input"]
            
            if tool_name in TOOLS:
                result = TOOLS[tool_name](tool_input)
                print(f"  🔧 {tool_name}({tool_input}) → {result}")
                messages.append({"role": "assistant", "content": content})
                messages.append({"role": "user", "content": f"Tool result: {result}"})
            else:
                messages.append({"role": "user", "content": f"Unknown tool: {tool_name}"})
    
    return "Max steps reached without an answer."

# Usage
answer = run_agent("What is RAG and how much is 15 * 23?")
print(f"\n📋 Final Answer: {answer}")
```

### 3. Agent Design Patterns

| Pattern | Description | When to Use |
|---------|-------------|------------|
| **ReAct** | Think → Act → Observe loop | General-purpose agents |
| **Plan-and-Execute** | Plan all steps first, then execute | Complex multi-step tasks |
| **Reflection** | Agent critiques its own output | Quality-sensitive tasks |
| **Router** | Route to specialized sub-agents | Multi-domain tasks |
| **Tool-only** | LLM decides which tool to call | Structured workflows |

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **AI Agents in LangGraph** | 📹 YouTube | [LangChain](https://www.youtube.com/watch?v=v9fkbTxPzs0) |
| **ReAct Paper** | 📄 Paper | [arxiv.org/abs/2210.03629](https://arxiv.org/abs/2210.03629) |
| **Building Agents from Scratch** | 📹 YouTube | [Sam Witteveen](https://www.youtube.com/watch?v=ySus5ZS0b94) |

> 💡 **Pro Tip:** Start with simple tool-calling agents before jumping to complex multi-agent systems. A well-designed single agent with good tools often outperforms a poorly-designed multi-agent system.

---

<div align="center">

[📋 Phase 5](../README.md) · [Topic 2: LangGraph →](../topic-2-langgraph/)

</div>
