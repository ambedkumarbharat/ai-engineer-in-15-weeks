<div align="center">

# 🧩 AI Agents Fundamentals — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > [AI Agents](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Simple ReAct Agent**
Build a ReAct agent from scratch (no frameworks) with 2 tools: calculator and dictionary lookup. Agent should think, act, observe, and loop until it has an answer.

## Problem 02 🧩 **🟢 Easy** — **Tool Registry**
Build a tool registry that manages available tools: register, list, get by name, execute by name with arguments. Include tool descriptions for LLM consumption.

## Problem 03 🧩 **🟢 Easy** — **Agent Conversation Logger**
Build a logger that records every step of an agent's execution: thoughts, tool calls, tool results, and final answer. Export as markdown report.

## Problem 04 🧩 **🟡 Medium** — **Multi-Tool Agent**
Build an agent with 5 tools: web search, calculator, file reader, date/time, and weather. Let the LLM choose which tool to use based on the question.

## Problem 05 🧩 **🟡 Medium** — **Plan-and-Execute Agent**
Build an agent that first creates a step-by-step plan, then executes each step sequentially, adapting the plan if a step fails.

## Problem 06 🧩 **🟡 Medium** — **Agent with Error Recovery**
Build an agent that handles tool failures gracefully: retry with modified input, try alternative tools, or ask for clarification.

## Problem 07 🧩 **🟡 Medium** — **Reflection Agent**
Build an agent that generates an answer, then critiques its own answer, and improves it — repeating until quality score exceeds a threshold.

## Problem 08 🧩 **🔴 Hard** — **Agent Cost Controller**
Build an agent with a token budget. It must complete the task within the budget, choosing cheaper strategies when budget is low.

## Problem 09 🧩 **🔴 Hard** — **Parallel Tool Execution**
Build an agent that identifies independent tool calls and executes them in parallel using asyncio, reducing total execution time.

## Problem 10 🧩 **🔴 Hard** — **Agent Benchmark Suite**
Build a benchmark with 30 test tasks to evaluate an agent on: task completion rate, steps taken, cost, latency, and tool selection accuracy.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 5](../README.md)

</div>
