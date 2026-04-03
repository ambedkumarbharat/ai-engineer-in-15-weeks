<div align="center">

# 🧩 Hybrid Search — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > [Hybrid Search](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### BM25 Implementation
**Problem:** Implement BM25 keyword search using `rank_bm25`. Index 50 documents and compare results with simple text matching on 10 queries.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### RRF Implementation
**Problem:** Implement Reciprocal Rank Fusion from scratch. Merge two ranked lists and verify the output is correct against expected results.

## Problem 03 🧩
**Difficulty:** 🟡 Medium
### Hybrid Search Pipeline
**Problem:** Build a complete hybrid search: BM25 for keyword ranking + ChromaDB for vector ranking + RRF for merging. Compare quality vs each alone.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Weight Tuning
**Problem:** Implement weighted hybrid search where you can adjust the balance between keyword and vector results (e.g., 0.3 BM25 + 0.7 vector). Find the optimal weight.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Query Analysis Router
**Problem:** Build a router that analyzes the query and decides the best search strategy: keyword-only (exact terms), vector-only (semantic), or hybrid.

## Problem 06 🧩
**Difficulty:** 🔴 Hard
### Cross-Encoder Re-Ranker
**Problem:** Add a cross-encoder re-ranking step after hybrid search. Benchmark quality improvement vs additional latency cost.

## Problem 07 🧩
**Difficulty:** 🔴 Hard
### Full-Text Search with PostgreSQL
**Problem:** Implement PostgreSQL full-text search with `tsvector` and combine it with ChromaDB vector search using RRF for true hybrid search.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Benchmark Suite
**Problem:** Build a comprehensive benchmark comparing: BM25-only, vector-only, hybrid, hybrid+rerank on a dataset of 500 documents and 50 test queries.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Multi-Language Hybrid Search
**Problem:** Build hybrid search that works across English and Hindi documents. Handle language-specific tokenization for BM25 and multilingual embeddings.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Production Hybrid Search Service
**Problem:** Build a production FastAPI hybrid search service with caching, query analytics, A/B testing support, and configurable search strategies.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 4](../README.md)

</div>
