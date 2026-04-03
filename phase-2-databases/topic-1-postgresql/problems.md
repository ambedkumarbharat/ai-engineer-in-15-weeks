<div align="center">

# 🧩 PostgreSQL — Practice Problems

![Problems](https://img.shields.io/badge/Problems-12-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > [PostgreSQL](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Design a Schema for AI Chat Application

**Problem:** Design and implement a PostgreSQL schema for an AI chatbot application with users, conversations, messages, and model configurations.

**Real-world AI use case:** Every AI chatbot (like ChatGPT) needs a database schema to store conversations, messages, user preferences, and token usage tracking.

**Concept being tested:** Table design, foreign keys, constraints, data types

**Hint:** Use `SERIAL PRIMARY KEY`, `REFERENCES` for foreign keys, and `CHECK` constraints for enums like message roles.

**Expected Output:**
```sql
-- Tables created: users, conversations, messages, model_configs
-- All foreign keys have ON DELETE CASCADE
-- Indexes on frequently queried columns
-- Check constraints on role ('user', 'assistant', 'system')
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### CRUD Operations with psycopg2

**Problem:** Implement a complete CRUD (Create, Read, Update, Delete) class for managing prompt templates in PostgreSQL using psycopg2.

**Real-world AI use case:** AI systems maintain libraries of prompt templates that are versioned and categorized. CRUD operations are the foundation of managing these.

**Concept being tested:** psycopg2 queries, parameterized queries, context managers

**Hint:** Use `%s` placeholders for parameterized queries (never f-strings!). Use `RETURNING` to get inserted data.

**Expected Output:**
```python
store = PromptStore(db_url)
template = store.create("summarizer", "Summarize: {text}", tags=["rag"])
print(template)  # {'id': 1, 'name': 'summarizer', 'template': 'Summarize: {text}', ...}
store.update(1, template="Summarize the following:\n{text}")
all_templates = store.list(tag="rag")
store.delete(1)
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Write Analytics Queries

**Problem:** Write 5 analytics queries for an AI platform: daily active users, average tokens per conversation, most used models, cost by tier, and user retention.

**Real-world AI use case:** AI platform teams track usage metrics constantly to understand costs, user engagement, and model performance.

**Concept being tested:** Aggregations, GROUP BY, JOINs, date functions

**Hint:** Use `DATE_TRUNC('day', created_at)` for daily grouping, `COUNT(DISTINCT user_id)` for unique users.

**Expected Output:**
```sql
-- Daily active users
SELECT DATE_TRUNC('day', created_at) AS day, COUNT(DISTINCT user_id) AS dau
FROM conversations GROUP BY day ORDER BY day DESC LIMIT 30;

-- Result: | day        | dau |
--         | 2024-01-15 | 432 |
--         | 2024-01-14 | 389 |
```

---

## Problem 04 🧩

**Difficulty:** 🟡 Medium

### SQLAlchemy ORM with Relationships

**Problem:** Build a complete SQLAlchemy ORM layer for an AI project management system with projects, experiments, and evaluation results.

**Real-world AI use case:** ML teams track experiments (different model configs) and their evaluation results. SQLAlchemy ORM makes this data easy to query and manipulate from Python.

**Concept being tested:** SQLAlchemy models, relationships, eager loading, queries

**Hint:** Use `relationship()` with `back_populates` and `cascade="all, delete-orphan"`. Use `joinedload()` to avoid N+1 queries.

**Expected Output:**
```python
project = Project(name="RAG Pipeline v2")
experiment = Experiment(name="chunk_size_500", config={"chunk_size": 500})
result = EvalResult(accuracy=0.92, latency_p95=1.8, cost_per_query=0.025)
experiment.results.append(result)
project.experiments.append(experiment)
db.add(project)
db.commit()

best = db.query(EvalResult).order_by(EvalResult.accuracy.desc()).first()
print(f"Best accuracy: {best.accuracy} from {best.experiment.name}")
```

---

## Problem 05 🧩

**Difficulty:** 🟡 Medium

### Database Migration Script

**Problem:** Write a Python migration system that tracks schema versions and applies migrations in order (like a mini Alembic).

**Real-world AI use case:** As AI systems evolve, the database schema changes. Migration systems ensure all environments (dev, staging, production) stay in sync.

**Concept being tested:** Schema versioning, DDL statements, migration patterns

**Hint:** Create a `migrations` table to track applied versions. Apply SQL files in order based on version numbers.

**Expected Output:**
```python
migrator = Migrator(db_url)
migrator.apply_pending()
# Applying migration 001_create_users.sql ... ✅
# Applying migration 002_create_conversations.sql ... ✅
# Applying migration 003_add_token_tracking.sql ... ✅
# All migrations applied. Current version: 003
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### Connection Pooling and Performance

**Problem:** Implement a connection pooling manager that handles concurrent database access, health checks, and connection recycling.

**Real-world AI use case:** AI APIs handle hundreds of concurrent requests. Without connection pooling, each request opens a new database connection, which is slow and can exhaust the connection limit.

**Concept being tested:** Connection pooling, SQLAlchemy engine config, concurrent access

**Hint:** Use SQLAlchemy's `pool_size`, `max_overflow`, `pool_recycle`, and `pool_pre_ping` parameters.

**Expected Output:**
```python
pool = ConnectionPool(db_url, pool_size=10, max_overflow=20)
stats = pool.get_stats()
print(stats)
# {'pool_size': 10, 'checked_out': 3, 'overflow': 0, 'checked_in': 7}
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### Full-Text Search for Knowledge Base

**Problem:** Implement full-text search in PostgreSQL for a knowledge base of documents, with ranking, highlighting, and relevance scoring.

**Real-world AI use case:** Before vector search became standard, full-text search was the primary way to find relevant documents. It's still used as a component in hybrid search (BM25 + vector).

**Concept being tested:** `tsvector`, `tsquery`, GIN indexes, `ts_rank`, `ts_headline`

**Hint:** Create a `tsvector` column, add a GIN index, and use `plainto_tsquery()` for user queries.

**Expected Output:**
```sql
SELECT title, 
       ts_rank(search_vector, query) AS rank,
       ts_headline('english', content, query) AS snippet
FROM documents, plainto_tsquery('english', 'machine learning models') AS query
WHERE search_vector @@ query
ORDER BY rank DESC LIMIT 5;

-- | title            | rank | snippet                          |
-- | ML Fundamentals  | 0.89 | <b>machine</b> <b>learning</b>... |
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Audit Trail System

**Problem:** Build an audit trail system that logs all changes to important tables (who changed what, when, old vs new values).

**Real-world AI use case:** Enterprise AI systems need audit trails for compliance (GDPR, SOC2). Knowing who changed model configs, prompt templates, or user data is legally required.

**Concept being tested:** Database triggers, JSONB, audit logging

**Hint:** Create an `audit_log` table with `table_name`, `operation`, `old_data`, `new_data` (JSONB), and a trigger function.

**Expected Output:**
```python
# After updating a user
audit_logs = get_audit_log(table="users", record_id=1)
print(audit_logs[0])
# {'table': 'users', 'operation': 'UPDATE', 'timestamp': '2024-01-15 10:30',
#  'old_data': {'tier': 'free'}, 'new_data': {'tier': 'pro'}, 'changed_by': 'admin'}
```

---

## Problem 09 🧩

**Difficulty:** 🔴 Hard

### Token Usage Billing System

**Problem:** Design and implement a complete token usage tracking and billing system with per-user quotas, real-time balance checking, and usage reports.

**Real-world AI use case:** Every AI SaaS product tracks token usage for billing. This requires accurate counting, concurrent safety (no double-spending), and efficient aggregate queries.

**Concept being tested:** Transactions, concurrent access, aggregation, advisory locks

**Hint:** Use `SELECT ... FOR UPDATE` to lock the user row while updating token counts. Use advisory locks for billing calculations.

**Expected Output:**
```python
billing = BillingSystem(db_url)
# Check balance
balance = billing.check_balance(user_id=1)
# {'tokens_remaining': 8500, 'tokens_used': 1500, 'tier': 'pro'}

# Record usage (thread-safe)
billing.record_usage(user_id=1, tokens=150, model="gpt-4", cost=0.003)
# True

# Usage report
report = billing.monthly_report(user_id=1, month="2024-01")
# {'total_tokens': 15000, 'total_cost': 4.50, 'by_model': {'gpt-4': 3.00, 'gpt-3.5': 1.50}}
```

---

## Problem 10 🧩

**Difficulty:** 🔴 Hard

### pgvector Integration for Embeddings

**Problem:** Set up the pgvector extension and implement similarity search for document embeddings stored directly in PostgreSQL.

**Real-world AI use case:** For applications with < 1M vectors, pgvector eliminates the need for a separate vector database, simplifying the architecture significantly.

**Concept being tested:** pgvector extension, vector operations, IVFFlat/HNSW indexes, hybrid queries

**Hint:** Use `CREATE EXTENSION vector;`, define columns as `vector(384)`, and use `<=>` (cosine distance) operator.

**Expected Output:**
```sql
-- Find similar documents
SELECT content, 1 - (embedding <=> '[0.1, 0.2, ...]') AS similarity
FROM documents
WHERE 1 - (embedding <=> '[0.1, 0.2, ...]') > 0.7
ORDER BY embedding <=> '[0.1, 0.2, ...]'
LIMIT 5;

-- | content          | similarity |
-- | AI fundamentals  | 0.95       |
-- | ML basics        | 0.88       |
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### Database Partitioning for Time-Series AI Logs

**Problem:** Implement table partitioning on a messages/logs table to efficiently handle millions of records with time-based queries.

**Real-world AI use case:** AI systems generate millions of log entries. Without partitioning, queries on old data slow down the entire system. Partitioning by month keeps queries fast.

**Concept being tested:** Table partitioning, partition pruning, maintenance

**Hint:** Use `PARTITION BY RANGE (created_at)` and create monthly partitions. Automate partition creation with a function.

**Expected Output:**
```sql
-- Query automatically uses only relevant partition
EXPLAIN SELECT * FROM messages WHERE created_at >= '2024-01-01' AND created_at < '2024-02-01';
-- Seq Scan on messages_2024_01 (only scans January partition!)
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### Async PostgreSQL with asyncpg

**Problem:** Build an async database layer using `asyncpg` with connection pooling, prepared statements, and batch operations for a high-throughput AI API.

**Real-world AI use case:** High-traffic AI APIs need async database access to avoid blocking the event loop. `asyncpg` is 3-5x faster than psycopg2 for async workloads.

**Concept being tested:** asyncpg, async connection pools, prepared statements, batch inserts

**Hint:** Use `asyncpg.create_pool()` and `pool.acquire()` as an async context manager. Use `executemany()` for batch inserts.

**Expected Output:**
```python
db = AsyncDB("postgresql://user:pass@localhost/ai_platform")
await db.connect()

# Batch insert 1000 messages
messages = [{"conv_id": 1, "role": "user", "content": f"Message {i}"} for i in range(1000)]
await db.batch_insert_messages(messages)
# Inserted 1000 messages in 0.23s (vs 8.5s with sequential inserts)

# Concurrent queries
results = await asyncio.gather(
    db.get_user(1),
    db.get_conversations(1),
    db.get_usage_stats(1),
)
# All 3 queries in 0.05s (vs 0.15s sequential)
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 2](../README.md) · [🏠 Home](../../README.md)

</div>
