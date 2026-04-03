<div align="center">

# 🧩 Memory Systems — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > [Memory](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Conversation Buffer**
Build a conversation buffer that stores the last N messages and formats them for LLM context injection.

## Problem 02 🧩 **🟢 Easy** — **Summary Memory**
Build a memory system that summarizes old conversations when they exceed a token limit, keeping important info while discarding details.

## Problem 03 🧩 **🟡 Medium** — **Vector Memory Store**
Build a long-term memory using ChromaDB: store user facts, preferences, and past interactions. Retrieve relevant memories for each new query.

## Problem 04 🧩 **🟡 Medium** — **Entity Memory**
Build a memory that tracks entities (people, projects, technologies) mentioned in conversations. Maintain an entity knowledge graph.

## Problem 05 🧩 **🟡 Medium** — **Session Manager**
Build a session manager using Redis: create sessions, add messages, retrieve history, auto-expire after 24h, support multiple users.

## Problem 06 🧩 **🟡 Medium** — **Memory-Augmented RAG**
Combine conversation memory with RAG: inject both retrieved documents AND relevant past interactions into the prompt.

## Problem 07 🧩 **🔴 Hard** — **Episodic Memory System**
Build episodic memory that stores summaries of completed tasks. Before starting a new task, recall similar past tasks for guidance.

## Problem 08 🧩 **🔴 Hard** — **Adaptive Memory Manager**
Build a memory system that automatically decides which memories to keep, compress, or discard based on relevance and recency scoring.

## Problem 09 🧩 **🔴 Hard** — **Cross-Session Learning**
Build a system where an agent learns user preferences across sessions (without fine-tuning) by accumulating and indexing feedback.

## Problem 10 🧩 **🔴 Hard** — **Memory Evaluation**
Build an evaluation framework that tests memory accuracy: does the agent correctly recall facts from 5, 10, 50 messages ago?

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 5](../README.md)

</div>
