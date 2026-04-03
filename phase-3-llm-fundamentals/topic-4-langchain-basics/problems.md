<div align="center">

# 🧩 LangChain Basics — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > [LangChain](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Basic LCEL Chain
**Problem:** Build a chain using LCEL (pipe operator) that takes a topic and generates a 3-sentence explanation. Use ChatPromptTemplate + ChatOpenAI + StrOutputParser.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Prompt Template Library
**Problem:** Create 5 reusable `ChatPromptTemplate` objects: summarizer, translator, code explainer, Q&A, and classifier. Invoke each with sample input.

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Output Parser — JSON
**Problem:** Use `JsonOutputParser` with a Pydantic model to extract structured data (name, skills, experience) from a job candidate description.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Conversation Memory
**Problem:** Build a chatbot with `RunnableWithMessageHistory` that remembers the last 10 messages. Support multiple sessions (different users).

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Multi-Step Chain
**Problem:** Build a 3-step chain: generate code → review code → improve code. Each step feeds its output to the next. Use `RunnablePassthrough.assign()`.

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Parallel Chain Execution
**Problem:** Build a chain that generates a summary AND extracts keywords in parallel using `RunnableParallel`, then merges results.

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Streaming Chain
**Problem:** Build a chain that streams output token by token. Display tokens with Rich library formatting. Track total tokens and latency.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Router Chain
**Problem:** Build a router that classifies the user's question type (code, math, general) and routes to a specialized prompt for each type.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Batch Processing Pipeline
**Problem:** Process 100 documents through a LangChain chain using `chain.batch()` with concurrency control (max 5). Track progress, errors, and total cost.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Custom Runnable
**Problem:** Create a custom `Runnable` class that wraps a caching layer around any LLM chain — check Redis before calling the chain, cache results after.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 3](../README.md)

</div>
