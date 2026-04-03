<div align="center">

# 🧩 Document Loaders & Chunking — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > [Chunking](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Multi-Format Document Loader
**Problem:** Build a document loader that handles PDF, TXT, MD, and CSV files. Return a unified format: `{content, source, page_count, word_count}`.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Chunking Strategy Comparison
**Problem:** Take a 10-page document and chunk it with 3 different strategies (character, recursive, token-based). Compare chunk count, avg size, and boundary quality.

## Problem 03 🧩
**Difficulty:** 🟡 Medium
### Overlap Optimization
**Problem:** Experiment with overlap sizes (0, 25, 50, 100, 200 chars) on 5 documents. Measure retrieval quality (precision@5) for each overlap setting.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Web Scraper + Loader
**Problem:** Build a web scraper that loads content from URLs, cleans HTML to text, and chunks the result. Handle pagination and rate limiting.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Code-Aware Chunker
**Problem:** Build a chunker that understands code structure — splits at function/class boundaries rather than arbitrary character counts.

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Metadata-Enriched Chunks
**Problem:** Build a chunking pipeline that adds rich metadata to each chunk: position, section heading, page number, word count, has_code boolean.

## Problem 07 🧩
**Difficulty:** 🔴 Hard
### Semantic Chunking
**Problem:** Implement semantic chunking: split text where embedding similarity between consecutive sentences drops below a threshold.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Hierarchical Chunking
**Problem:** Build a 3-level hierarchical chunker: document → section → paragraph. Store parent-child relationships for hierarchical retrieval.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Batch Ingestion Pipeline
**Problem:** Build a production ingestion pipeline that processes 1000 documents: load → clean → chunk → embed → store. With progress tracking, error handling, and resume-on-failure.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Chunking Quality Evaluator
**Problem:** Build an evaluation tool that scores chunking quality: measures information completeness (no cut sentences), context preservation, and retrieval quality on test queries.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 4](../README.md)

</div>
