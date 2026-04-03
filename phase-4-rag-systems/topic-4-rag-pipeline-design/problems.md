<div align="center">

# 🧩 RAG Pipeline Design — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > [RAG Pipeline](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Basic RAG Pipeline
**Problem:** Build a minimal RAG pipeline: load 5 documents, chunk, embed with sentence-transformers, store in ChromaDB, query and generate answer with Ollama.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Source Citation System
**Problem:** Modify your RAG pipeline to include source citations in every answer. Format: "According to [source_name], ..."

## Problem 03 🧩
**Difficulty:** 🟡 Medium
### Query Rewriting
**Problem:** Implement query rewriting: use an LLM to generate 3 alternative phrasings of the user's question, search with all 3, merge results with RRF.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Contextual Compression
**Problem:** After retrieval, use an LLM to extract only the most relevant sentences from each chunk before passing to the generation step.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Conversational RAG
**Problem:** Build a multi-turn RAG chatbot that maintains conversation history and uses it to contextualize follow-up questions.

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### RAG with Re-Ranking
**Problem:** Add a cross-encoder re-ranking step after initial retrieval. Compare retrieval quality with and without re-ranking on 20 test queries.

## Problem 07 🧩
**Difficulty:** 🔴 Hard
### Multi-Source RAG
**Problem:** Build a RAG system that queries multiple sources (local docs, web search, database) and merges results intelligently.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Adaptive RAG
**Problem:** Build a RAG system that adapts its strategy based on query type: simple fact lookup → direct retrieval, complex analysis → multi-step RAG, conversational → memory + RAG.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### RAG Pipeline Optimizer
**Problem:** Build a system that automatically tunes RAG parameters (chunk_size, overlap, top_k, model) by running evaluations on a test set and finding optimal settings.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Production RAG API
**Problem:** Build a production-ready RAG FastAPI service with: ingestion endpoint, query endpoint, streaming, caching, rate limiting, and LangSmith tracing.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 4](../README.md)

</div>
