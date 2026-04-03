<div align="center">

# 🧩 ChromaDB & Pinecone — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > [ChromaDB & Pinecone](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### ChromaDB CRUD Operations
**Problem:** Create a ChromaDB collection, add 20 documents with metadata, query with text and filters, update 5 documents, delete 3 documents.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Metadata Filtering
**Problem:** Add 50 documents with rich metadata (source, date, category, difficulty). Write 10 different filter queries combining `$and`, `$or`, `$in`, `$gte`.

## Problem 03 🧩
**Difficulty:** 🟡 Medium
### Collection Manager
**Problem:** Build a collection management system: create/delete collections, list all collections with stats (count, size), backup/restore collection data.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Multi-Collection Search
**Problem:** Create 3 separate collections (docs, FAQs, code). Search across all 3, merge results by relevance, and return unified ranked results.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Incremental Ingestion
**Problem:** Build a pipeline that detects new/modified/deleted documents and updates ChromaDB incrementally (without re-embedding everything).

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### ChromaDB + FastAPI Service
**Problem:** Build a FastAPI vector search service with endpoints: POST /documents, POST /search, DELETE /documents/{id}, GET /stats.

## Problem 07 🧩
**Difficulty:** 🔴 Hard
### Migration: ChromaDB → Pinecone
**Problem:** Build a migration tool that exports all data from ChromaDB and imports it into Pinecone, preserving documents, embeddings, and metadata.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Benchmarking Suite
**Problem:** Benchmark ChromaDB: measure insert throughput, query latency at various collection sizes (1K, 10K, 100K), and recall@10 quality.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Namespaced Multi-Tenant Vector Store
**Problem:** Build a multi-tenant vector store where each tenant has isolated data but shares the same ChromaDB instance. Enforce tenant isolation.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Auto-Optimization System
**Problem:** Build a system that monitors ChromaDB query performance and automatically adjusts HNSW parameters (ef_construction, M) for optimal recall/speed tradeoff.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 4](../README.md)

</div>
