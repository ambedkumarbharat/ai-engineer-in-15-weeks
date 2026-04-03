<div align="center">

# 🧩 Ollama & Local LLMs — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > [Ollama](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Pull and Compare Models
**Problem:** Pull 3 models (llama3, mistral, phi3). Send the same 5 prompts to each. Compare response quality, speed, and response length.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### OpenAI-Compatible Client
**Problem:** Use the OpenAI Python client pointed at Ollama (`base_url="http://localhost:11434/v1"`). Prove the same code works with both Ollama and OpenAI by toggling a config.

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Custom Modelfile
**Problem:** Create a custom model using a Modelfile with a specialized system prompt for code review. Set temperature=0.3 and test it.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Streaming Chat Application
**Problem:** Build a CLI chat app with Rich that streams responses from Ollama, shows a spinner while generating, and displays response time and token count.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Model Benchmarking Tool
**Problem:** Build a benchmarking CLI that tests any Ollama model on: reasoning (5 questions), coding (5 questions), and knowledge (5 questions). Score with LLM-as-judge.

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Multi-Model Chat
**Problem:** Build a chat interface where users can switch models mid-conversation with `/model llama3`. Maintain conversation history across model switches.

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Ollama + LangChain Integration
**Problem:** Build a LangChain LCEL chain using Ollama as the LLM. Add memory, output parsing, and streaming. Compare behavior with OpenAI.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Local RAG with Ollama
**Problem:** Build a complete RAG pipeline using only free/local tools: Ollama for LLM, sentence-transformers for embeddings, ChromaDB for storage. Zero API cost.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Model Performance Monitor
**Problem:** Build a monitoring dashboard that tracks Ollama model performance over time: average latency, tokens/second, memory usage, and error rate.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Auto-Model Selector
**Problem:** Build a system that automatically selects the best Ollama model for a given task based on query complexity, required reasoning depth, and latency requirements.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 3](../README.md)

</div>
