<div align="center">

# 🧩 Embeddings & Vector Search — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > [Embeddings](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Generate and Compare Embeddings
**Problem:** Generate embeddings for 10 sentences using sentence-transformers. Compute pairwise cosine similarity and identify the most/least similar pairs.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Embedding Dimensions Comparison
**Problem:** Embed the same 20 texts using models with different dimensions (384, 768, 1536). Compare quality (similarity rankings) vs storage size vs speed.

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Semantic Search Engine
**Problem:** Build a simple semantic search: embed 50 documents, embed a query, find top-5 most similar using cosine similarity. Compare results with keyword search.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Batch Embedding Pipeline
**Problem:** Build an efficient batch embedding pipeline that processes 1000 texts with progress tracking, error handling, and automatic retry.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Embedding Cache
**Problem:** Build a caching layer that stores computed embeddings in Redis/SQLite. On re-embed request, return cached version if text hasn't changed.

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Clustering with Embeddings
**Problem:** Embed 100 documents, cluster them using K-means, and automatically label each cluster based on its centroid's nearest documents.

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Duplicate Detection
**Problem:** Use embeddings to find near-duplicate documents in a corpus of 500 texts. Set a similarity threshold and report duplicate pairs.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Multi-Modal Embeddings
**Problem:** Combine text and metadata into a unified embedding. Create a weighted embedding that considers both content similarity and metadata similarity.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Embedding Drift Detection
**Problem:** Build a system that detects when new documents have significantly different embedding distributions than existing ones, signaling potential domain shift.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### ANN Index Comparison
**Problem:** Implement and benchmark 3 similarity search approaches: brute-force, FAISS IVF, and HNSW. Compare recall@10 and query latency at different dataset sizes.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 4](../README.md)

</div>
