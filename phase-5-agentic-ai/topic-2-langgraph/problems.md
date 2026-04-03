<div align="center">

# 🧩 LangGraph — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > [LangGraph](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Linear Workflow**
Build a 3-node LangGraph: research → write → edit. Each node transforms the state and passes it forward.

## Problem 02 🧩 **🟢 Easy** — **State Management**
Build a LangGraph with a `TypedDict` state that accumulates messages, tracks step count, and records which nodes were visited.

## Problem 03 🧩 **🟡 Medium** — **Conditional Routing**
Build a graph with conditional edges: classify the query type → route to different handler nodes (code, math, general) → merge outputs.

## Problem 04 🧩 **🟡 Medium** — **Agent Loop with Tool Calls**
Build a LangGraph agent that loops: LLM decides tool → execute tool → check if done → loop or return. Use ToolNode for tool execution.

## Problem 05 🧩 **🟡 Medium** — **Human-in-the-Loop**
Build a LangGraph workflow that pauses for human approval before executing certain high-risk actions (sending email, deleting data).

## Problem 06 🧩 **🟡 Medium** — **Parallel Node Execution**
Use LangGraph's parallel execution to run multiple research tasks simultaneously and merge results.

## Problem 07 🧩 **🔴 Hard** — **Multi-Agent Supervisor**
Build a supervisor pattern: one supervisor agent delegates to specialized worker agents (researcher, coder, writer) and merges their outputs.

## Problem 08 🧩 **🔴 Hard** — **Persistent State**
Build a LangGraph agent with persistent checkpointing — can resume from any step after a crash or interruption.

## Problem 09 🧩 **🔴 Hard** — **Dynamic Graph Construction**
Build a system that constructs different LangGraph workflows based on user requirements (simple query → 3 nodes, complex → 7 nodes).

## Problem 10 🧩 **🔴 Hard** — **Production Agent Service**
Build a FastAPI service that runs LangGraph agents as background tasks with streaming status updates, cancellation, and result retrieval.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 5](../README.md)

</div>
