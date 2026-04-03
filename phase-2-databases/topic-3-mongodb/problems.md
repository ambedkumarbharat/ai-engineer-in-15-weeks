<div align="center">

# 🧩 MongoDB — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > [MongoDB](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Agent Activity Logger
**Problem:** Build a MongoDB-based activity logger for an AI agent that stores action type, input, output, tool used, duration, and timestamp. Implement insert, query by time range, and query by action type.
**Real-world AI use case:** AI agents perform many actions (web search, calculator, file write). Logging each action helps debug failures and optimize performance.
**Concept being tested:** Insert, find with filters, date queries
**Hint:** Use `datetime` objects directly — MongoDB handles them natively. Query with `{"timestamp": {"$gte": start, "$lte": end}}`.
**Expected Output:**
```python
logger.log("web_search", input="RAG tutorial", output="Found 5 results", duration_ms=450)
recent = logger.get_recent(minutes=30)
print(f"Actions in last 30 min: {len(recent)}")
```

---

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Document Metadata Store
**Problem:** Create a MongoDB collection for storing document metadata (filename, type, size, chunk count, embedding status, tags) with CRUD operations and tag-based filtering.
**Real-world AI use case:** RAG systems track every document's processing status — which are chunked, which are embedded, which failed.
**Concept being tested:** CRUD operations, array queries, `$in` operator
**Hint:** Use `{"tags": {"$in": ["rag", "tutorial"]}}` to filter by tags.
**Expected Output:**
```python
store.add_document("paper.pdf", tags=["research", "ml"], size_kb=1024)
ml_docs = store.find_by_tags(["ml"])
print(f"ML documents: {len(ml_docs)}")
```

---

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Conversation History Store
**Problem:** Store AI chat conversations with nested messages in MongoDB. Support adding messages, retrieving full history, and searching messages by content.
**Real-world AI use case:** Chat apps store conversations as documents with nested message arrays. MongoDB's flexible schema handles varying message metadata.
**Concept being tested:** Nested documents, `$push`, text search on nested fields
**Hint:** Use `$push` to append messages: `update_one(filter, {"$push": {"messages": new_msg}})`.
**Expected Output:**
```python
conv_id = store.create_conversation("user_42", "RAG Help")
store.add_message(conv_id, "user", "What is RAG?")
store.add_message(conv_id, "assistant", "RAG stands for Retrieval Augmented Generation...")
history = store.get_messages(conv_id)
print(f"Messages: {len(history)}")  # 2
```

---

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Aggregation Pipeline for Usage Analytics
**Problem:** Write aggregation pipelines to compute: daily message count, average tokens per model, top users by token usage, and hourly request distribution.
**Real-world AI use case:** Product teams analyze usage patterns to optimize costs, understand user behavior, and plan capacity.
**Concept being tested:** `$match`, `$group`, `$project`, `$sort`, `$bucket`
**Hint:** Use `$dateToString` for date grouping and `$unwind` before grouping on nested array fields.
**Expected Output:**
```python
daily_stats = analytics.daily_usage(days=7)
# [{'date': '2024-01-15', 'messages': 1234, 'tokens': 456789, 'unique_users': 89}, ...]
model_stats = analytics.by_model()
# [{'model': 'gpt-4', 'calls': 500, 'avg_tokens': 350}, ...]
```

---

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Schema Validation and Migration
**Problem:** Implement MongoDB schema validation rules and a migration system that evolves the schema as the AI application grows.
**Real-world AI use case:** As AI systems evolve, document schemas change. Validation prevents corrupt data, and migrations transform existing documents.
**Concept being tested:** JSON Schema validation, `collMod`, data migration
**Hint:** Use `db.create_collection("name", validator={"$jsonSchema": {...}})` for validation.
**Expected Output:**
```python
migrator.apply("001_add_embedding_field")
# Migrating: Adding 'embedding_status' to 4523 documents... ✅
```

---

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Change Streams for Real-Time Processing
**Problem:** Use MongoDB Change Streams to react to document changes in real-time — trigger embedding generation when a new document is inserted.
**Real-world AI use case:** When new documents are uploaded to the knowledge base, change streams automatically trigger the embedding pipeline.
**Concept being tested:** Change Streams, event-driven processing, cursor handling
**Hint:** Use `collection.watch()` to open a change stream. Filter by `operationType`.
**Expected Output:**
```python
# When a document is inserted:
# [INSERT] New document: paper.pdf → Triggering embedding pipeline...
# [UPDATE] Document paper.pdf embedded: True
```

---

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Motor Async CRUD with FastAPI
**Problem:** Build FastAPI endpoints that perform CRUD operations on MongoDB using Motor (async driver) with proper error handling and Pydantic models.
**Real-world AI use case:** Production AI APIs use async database drivers for non-blocking I/O. Motor + FastAPI is the standard async MongoDB stack.
**Concept being tested:** Motor async operations, FastAPI integration, Pydantic + MongoDB
**Hint:** Use `AsyncIOMotorClient` and `await` all database operations. Convert `ObjectId` to string in responses.
**Expected Output:**
```bash
POST /documents → {"id": "507f1f77bcf86cd799439011", "title": "RAG Guide", "status": "created"}
GET /documents → [list of documents]
GET /documents/507f1f77bcf86cd799439011 → single document
```

---

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Full-Text Search with Ranking
**Problem:** Implement a full-text search system with relevance scoring, faceted results, autocomplete suggestions, and search analytics.
**Real-world AI use case:** Before deploying vector search, many AI systems use MongoDB text search for keyword-based retrieval. It's faster and cheaper for exact-match queries.
**Concept being tested:** Text indexes, `$text`, `$meta`, faceted search, search scoring
**Hint:** Use `{"$text": {"$search": query}}` with `{"score": {"$meta": "textScore"}}` for ranking.
**Expected Output:**
```python
results = search.query("machine learning embeddings", facets=["category", "source"])
# {'results': [...], 'facets': {'category': {'tutorial': 5, 'paper': 3}}, 'total': 8}
```

---

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Time-Series Data with TTL Indexes
**Problem:** Design a time-series collection for AI system metrics (latency, token usage, error rates) with automatic cleanup, downsampling, and alerting.
**Real-world AI use case:** AI systems generate high-volume metrics data. TTL indexes auto-delete old data, and downsampling preserves historical trends at lower storage cost.
**Concept being tested:** TTL indexes, time-series collections, aggregation for downsampling
**Hint:** Use `expireAfterSeconds` TTL index. Create a downsampling aggregation that computes hourly/daily averages.
**Expected Output:**
```python
metrics.record("api_latency", value=234, tags={"model": "gpt-4", "endpoint": "/generate"})
# Raw data: kept for 7 days (TTL)
# Hourly averages: kept for 90 days
# Daily averages: kept forever
```

---

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Multi-Tenant Document Isolation
**Problem:** Design a multi-tenant MongoDB architecture where each organization's documents are isolated but share the same database. Implement tenant-aware queries, data isolation, and cross-tenant analytics for admins.
**Real-world AI use case:** SaaS AI platforms serve multiple organizations. Each tenant's documents, conversations, and embeddings must be isolated for security and compliance.
**Concept being tested:** Multi-tenancy patterns, query middleware, data isolation
**Hint:** Add `tenant_id` to every document. Create compound indexes starting with `tenant_id`. Build a middleware that injects `tenant_id` into every query.
**Expected Output:**
```python
# Tenant A can only see their data
tenant_a_docs = store.find(tenant_id="org_a", query={"status": "active"})
# Tenant B's data is invisible to Tenant A

# Admin can see cross-tenant stats
admin_stats = store.admin_stats()
# {'org_a': {'documents': 1500, 'tokens': 500000}, 'org_b': {'documents': 800, 'tokens': 250000}}
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 2](../README.md) · [🏠 Home](../../README.md)

</div>
