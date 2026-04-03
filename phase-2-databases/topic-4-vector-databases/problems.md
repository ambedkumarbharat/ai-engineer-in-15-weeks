<div align="center">

# 🧩 Vector Databases — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > [Vector Databases](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Set Up ChromaDB and Add Documents
**Problem:** Initialize a persistent ChromaDB instance, create a collection, add 10 documents about different AI topics, and perform basic similarity search.
**Real-world AI use case:** This is the first step in building any RAG system — setting up a vector store and populating it with knowledge.
**Concept being tested:** ChromaDB setup, document addition, basic querying
**Hint:** Use `chromadb.PersistentClient()` and `collection.add()` with documents, metadatas, and ids.
**Expected Output:**
```python
results = collection.query(query_texts=["How do embeddings work?"], n_results=3)
# Returns 3 most relevant documents about embeddings
```

---

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Cosine Similarity Calculator
**Problem:** Implement cosine similarity from scratch, then compare N document embeddings to find the most and least similar pairs.
**Real-world AI use case:** Understanding similarity metrics is essential for tuning retrieval quality in RAG systems.
**Concept being tested:** Vector math, similarity metrics, pairwise comparison
**Hint:** cosine_similarity = dot(a,b) / (|a| * |b|). Use nested loops for pairwise comparison.
**Expected Output:**
```python
most_similar = find_most_similar(embeddings)
# ('doc_1', 'doc_3', similarity=0.95)  — Both about machine learning
least_similar = find_least_similar(embeddings)
# ('doc_2', 'doc_7', similarity=0.12)  — ML doc vs cooking doc
```

---

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Metadata Filtering
**Problem:** Add documents to ChromaDB with rich metadata (source, date, category, difficulty), then query with various metadata filters.
**Real-world AI use case:** RAG systems need to filter by source (only search company docs), date (recent docs only), or category (only technical docs).
**Concept being tested:** ChromaDB `where` filters, `$and`, `$or`, `$in` operators
**Hint:** Use `where={"$and": [{"source": "blog"}, {"year": {"$gte": 2023}}]}` syntax.
**Expected Output:**
```python
# Only search in recent blog posts about RAG
results = collection.query(
    query_texts=["retrieval augmented generation"],
    n_results=5,
    where={"$and": [{"source": "blog"}, {"topic": "rag"}]}
)
```

---

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Document CRUD with Embeddings
**Problem:** Build a complete document management system on ChromaDB: add, update (re-embed), delete, and search with automatic embedding generation.
**Real-world AI use case:** Knowledge bases need full CRUD capabilities. When a document is updated, its embedding must be regenerated and updated in the vector store.
**Concept being tested:** Full CRUD on vector collections, embedding updates, consistency
**Hint:** For updates, delete the old entry and add the new one with the same ID. ChromaDB also supports `collection.update()`.
**Expected Output:**
```python
kb = KnowledgeBase()
kb.add("doc_1", "Original content about RAG...", metadata={"source": "blog"})
kb.update("doc_1", "Updated content about advanced RAG techniques...")
results = kb.search("RAG techniques")  # Returns updated document
kb.delete("doc_1")
```

---

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Multi-Collection Search
**Problem:** Create separate ChromaDB collections for different content types (docs, FAQs, code snippets), implement search across all collections, and merge results by relevance.
**Real-world AI use case:** AI systems have different types of knowledge (documentation, code examples, FAQs). Searching across all types and ranking uniformly produces best results.
**Concept being tested:** Multiple collections, cross-collection search, result merging
**Hint:** Query each collection separately, then merge and sort by distance/score. Normalize scores across collections.
**Expected Output:**
```python
results = multi_search("How to implement caching?")
# [('code', 'redis_cache.py', 0.92), ('docs', 'Caching Guide', 0.88), ('faq', 'Cache vs DB', 0.75)]
```

---

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Embedding Model Comparison
**Problem:** Use 3 different embedding approaches (TF-IDF, sentence-transformers, OpenAI) on the same document set. Compare retrieval quality with precision@k and recall@k metrics.
**Real-world AI use case:** Choosing the right embedding model significantly impacts RAG quality. Systematic comparison helps make data-driven decisions.
**Concept being tested:** Embedding models, evaluation metrics, benchmarking
**Hint:** Create test queries with known relevant documents. Compute precision@k = (relevant in top-k) / k.
**Expected Output:**
```python
# Comparison results:
# | Model              | Precision@5 | Recall@5 | Avg Latency |
# | TF-IDF             | 0.60        | 0.45     | 5ms         |
# | MiniLM-L6          | 0.85        | 0.72     | 25ms        |
# | text-embedding-3   | 0.92        | 0.88     | 100ms       |
```

---

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Batch Ingestion Pipeline
**Problem:** Build a batch ingestion pipeline that reads documents, chunks them, generates embeddings, and upserts into ChromaDB — with progress tracking and error handling.
**Real-world AI use case:** Ingesting thousands of documents is a batch operation that must be efficient, resumable, and fault-tolerant.
**Concept being tested:** Batch processing, chunking, error handling, progress tracking
**Hint:** Process in batches of 100. Track progress with a counter. Log and skip failed documents.
**Expected Output:**
```python
pipeline = IngestionPipeline(collection, chunk_size=500, batch_size=100)
stats = pipeline.ingest("./documents/")
# Processed: 1500 docs → 4200 chunks → 4200 embeddings
# Errors: 3 (logged to errors.json)
# Time: 45.2s (93 docs/sec)
```

---

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Hybrid Search (Vector + Keyword)
**Problem:** Implement hybrid search combining vector similarity (ChromaDB) with keyword search (BM25), using Reciprocal Rank Fusion (RRF) to merge results.
**Real-world AI use case:** Hybrid search outperforms pure vector search by 10-20% on most benchmarks. It catches exact keyword matches that vector search might miss.
**Concept being tested:** BM25, vector search, RRF merging, search evaluation
**Hint:** Get top-k from both searches, then RRF score = Σ 1/(k + rank_i) for each result across both lists.
**Expected Output:**
```python
results = hybrid_search("FastAPI rate limiting middleware")
# Vector results:  ['rate_limiting.md', 'middleware.md', 'auth.md']
# Keyword results:  ['rate_limiting.md', 'fastapi_guide.md', 'middleware.md']
# Hybrid (RRF):    ['rate_limiting.md', 'middleware.md', 'fastapi_guide.md']  ← mejor
```

---

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Vector Store with Automatic Reindexing
**Problem:** Build a vector store wrapper that detects when the embedding model changes and automatically reindexes all documents with the new model.
**Real-world AI use case:** When upgrading embedding models (e.g., from `text-embedding-ada-002` to `text-embedding-3-small`), all vectors must be regenerated. Automatic reindexing prevents manual errors.
**Concept being tested:** Index versioning, migration, batch re-embedding
**Hint:** Store embedding model info in collection metadata. On init, compare current vs stored model. If different, trigger reindex.
**Expected Output:**
```python
store = SmartVectorStore("knowledge_base", embedding_model="all-MiniLM-L6-v2")
store.add(documents)  # Uses MiniLM

# Later, upgrade model:
store = SmartVectorStore("knowledge_base", embedding_model="all-mpnet-base-v2")
# ⚠️ Model changed! Reindexing 5000 documents...
# Progress: [████████████████████] 100% (5000/5000)
# ✅ Reindexing complete. Model: all-mpnet-base-v2
```

---

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Production Vector Search Service
**Problem:** Build a production-ready vector search FastAPI service with: collection management, batch operations, concurrent search, caching, and monitoring.
**Real-world AI use case:** This is the complete vector search microservice pattern used in production RAG systems.
**Concept being tested:** Everything combined — FastAPI, ChromaDB, caching, concurrency, monitoring
**Hint:** Add Redis caching for frequent queries, asyncio.Semaphore for concurrency control, and Prometheus-compatible metrics.
**Expected Output:**
```bash
POST /collections — Create collection
POST /documents — Add/update documents
POST /search — Search with filters
GET /stats — Collection statistics, cache hit rate, search latency p95
DELETE /documents/{id} — Remove document
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 2](../README.md) · [🏠 Home](../../README.md)

</div>
