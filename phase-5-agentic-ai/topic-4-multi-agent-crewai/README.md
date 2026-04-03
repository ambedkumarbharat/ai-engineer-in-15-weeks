<div align="center">

# 👥 Topic 4: Multi-Agent with CrewAI

![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red) ![Time](https://img.shields.io/badge/Time-3_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **Multi-Agent with CrewAI**

---

## 🎯 Why This Matters

Complex tasks require multiple specialized agents working together — a researcher, an analyst, a writer. CrewAI makes multi-agent orchestration simple with role-based agents, task delegation, and collaborative workflows.

---

## 📚 Core Concepts

### 1. CrewAI Setup

```python
from crewai import Agent, Task, Crew, Process

# Define specialized agents
researcher = Agent(
    role="Senior Research Analyst",
    goal="Find comprehensive information about {topic}",
    backstory="You are an expert researcher with 10 years of experience analyzing technology trends.",
    verbose=True,
    allow_delegation=True,
)

writer = Agent(
    role="Technical Content Writer",
    goal="Write clear, engaging content about technical topics",
    backstory="You are a technical writer who makes complex AI topics accessible.",
    verbose=True,
)

editor = Agent(
    role="Content Editor",
    goal="Ensure content is accurate, well-structured, and error-free",
    backstory="You are a meticulous editor with expertise in technical content.",
    verbose=True,
)

# Define tasks
research_task = Task(
    description="Research {topic} thoroughly. Find key concepts, recent developments, and best practices.",
    expected_output="Comprehensive research notes with key findings, statistics, and sources.",
    agent=researcher,
)

writing_task = Task(
    description="Write a detailed blog post based on the research findings.",
    expected_output="Well-structured 1000-word blog post with introduction, main points, and conclusion.",
    agent=writer,
    context=[research_task],  # Depends on research output
)

editing_task = Task(
    description="Edit and polish the blog post for publication.",
    expected_output="Final, publication-ready blog post with corrections and improvements.",
    agent=editor,
    context=[writing_task],
)

# Create and run crew
crew = Crew(
    agents=[researcher, writer, editor],
    tasks=[research_task, writing_task, editing_task],
    process=Process.sequential,
    verbose=True,
)

result = crew.kickoff(inputs={"topic": "AI Agents in Production"})
print(result)
```

### 2. When to Use Multi-Agent

| Single Agent | Multi-Agent |
|-------------|-------------|
| Simple tasks | Complex, multi-step tasks |
| One skill needed | Multiple skills needed |
| Fast execution | Quality over speed |
| Low cost | Higher cost acceptable |
| Direct queries | Research + analysis + writing |

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **CrewAI Docs** | 📄 Docs | [docs.crewai.com](https://docs.crewai.com/) |
| **CrewAI Tutorial** | 📹 YouTube | [Matt Williams](https://www.youtube.com/watch?v=tnejrr-0a94) |

> 💡 **Pro Tip:** Don't use multi-agent when single-agent suffices. Each agent call is an LLM call = more latency + cost. Use multi-agent only when the task genuinely requires different expertise or perspectives.

---

<div align="center">

[← Topic 3](../topic-3-tool-use-function-calling/) · [📋 Phase 5](../README.md) · [Topic 5 →](../topic-5-memory-systems/)

</div>
