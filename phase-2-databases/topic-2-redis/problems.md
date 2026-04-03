<div align="center">

# 🧩 Redis — Practice Problems

![Problems](https://img.shields.io/badge/Problems-12-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > [Redis](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Basic Key-Value Store for Model Config

**Problem:** Use Redis to store and retrieve AI model configurations (model name, temperature, max_tokens, system prompt) with proper TTL management.

**Real-world AI use case:** AI systems store model configs in Redis for fast access. When configs change, old keys expire automatically.

**Concept being tested:** SET, GET, SETEX, TTL, hash operations

**Hint:** Use `hset` with `mapping` for structured config, `setex` for TTL.

**Expected Output:**
```python
config_store.save_config("production", {"model": "gpt-4", "temperature": "0.7"})
config = config_store.get_config("production")
print(config)  # {'model': 'gpt-4', 'temperature': '0.7'}
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### Simple Counter and Analytics

**Problem:** Build a Redis-based analytics tracker that counts API calls, tokens used, and errors per model with hourly granularity.

**Real-world AI use case:** Track real-time usage metrics without database writes. Redis counters are atomic and blazing fast.

**Concept being tested:** INCR, INCRBY, key naming conventions, expiration

**Hint:** Use keys like `stats:{model}:{hour}:calls` and `INCRBY` for atomic counters.

**Expected Output:**
```python
tracker.record_call("gpt-4", tokens=150, success=True)
tracker.record_call("gpt-4", tokens=200, success=False)
stats = tracker.get_hourly_stats("gpt-4")
print(stats)  # {'calls': 2, 'tokens': 350, 'errors': 1, 'success_rate': '50.0%'}
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Simple Cache-Aside Pattern

**Problem:** Implement the cache-aside pattern: check Redis first, if miss then fetch from "database" (simulated), store in Redis, and return.

**Real-world AI use case:** This is the most common caching pattern. Check cache → miss → compute expensive result → cache it → return.

**Concept being tested:** Cache-aside pattern, TTL, cache invalidation

**Hint:** `get()` from Redis → if None → fetch from source → `setex()` in Redis → return result.

**Expected Output:**
```python
# First call - cache miss
result = cache.get_user(42)  # Cache MISS - fetched from DB in 100ms
# Second call - cache hit
result = cache.get_user(42)  # Cache HIT - fetched from Redis in 1ms
```

---

## Problem 04 🧩

**Difficulty:** 🟡 Medium

### LLM Response Cache with Semantic Keys

**Problem:** Build a cache that stores LLM responses keyed by a hash of (prompt + model + temperature). Include cache statistics and invalidation.

**Real-world AI use case:** Same prompt sent to the same model should return cached response. This saves 30-70% on LLM API costs.

**Concept being tested:** Hash-based cache keys, JSON serialization, cache stats

**Hint:** Use `hashlib.sha256(f"{model}:{temp}:{prompt}")` as the cache key.

**Expected Output:**
```python
cache.set(prompt="What is RAG?", model="gpt-4", response="RAG is...", cost=0.03)
result = cache.get(prompt="What is RAG?", model="gpt-4")  # Hit!
print(cache.stats())  # {'hits': 1, 'misses': 0, 'hit_rate': '100%', 'savings': '$0.03'}
```

---

## Problem 05 🧩

**Difficulty:** 🟡 Medium

### Sliding Window Rate Limiter

**Problem:** Implement a sliding window rate limiter using Redis sorted sets that supports per-user, per-endpoint, and global rate limits.

**Real-world AI use case:** AI APIs need multi-level rate limiting — per user (100/min), per endpoint (/generate: 50/min), and global (10000/min).

**Concept being tested:** Sorted sets (ZADD, ZRANGEBYSCORE, ZCARD), pipelining

**Hint:** Use timestamp as score in a sorted set. Remove entries older than the window. Count remaining entries.

**Expected Output:**
```python
limiter = RateLimiter(redis_client)
for i in range(12):
    result = limiter.check("user_42", limit=10, window=60)
    print(f"Request {i+1}: {'✅' if result.allowed else '❌'} (remaining: {result.remaining})")
# Requests 1-10: ✅, Requests 11-12: ❌
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### Pub/Sub Event System for AI Pipeline

**Problem:** Build a pub/sub system where document processing events (uploaded, chunked, embedded, indexed) are published and multiple subscribers react to them.

**Real-world AI use case:** In event-driven AI architectures, services communicate via pub/sub. When a doc is uploaded, the embedding service subscribes and auto-processes it.

**Concept being tested:** Redis pub/sub, event-driven architecture, async subscribers

**Hint:** Use `pubsub.subscribe()` and `pubsub.listen()` for consuming. Publish JSON-formatted events.

**Expected Output:**
```python
# Publisher
publisher.emit("doc:uploaded", {"doc_id": "123", "size": 1024})
# Subscriber
# [doc:uploaded] Processing document 123 (1024 bytes)
# [doc:chunked] Document 123 split into 5 chunks
# [doc:embedded] Generated embeddings for 5 chunks
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### Session Store with Conversation History

**Problem:** Build a session manager that stores conversation history in Redis, supports message append, history retrieval, and automatic session expiry.

**Real-world AI use case:** Chat applications need to track conversation history. Redis provides fast access and automatic cleanup of idle sessions.

**Concept being tested:** Redis lists (LPUSH, LRANGE), session management, JSON storage

**Hint:** Use Redis lists with `rpush` for message append and `lrange` for retrieval. Store each message as JSON.

**Expected Output:**
```python
session = SessionStore()
sid = session.create("user_42")
session.add_message(sid, "user", "What is RAG?")
session.add_message(sid, "assistant", "RAG stands for...")
history = session.get_history(sid, last_n=10)
print(f"Messages: {len(history)}")  # 2
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Redis Pipeline for Batch Operations

**Problem:** Compare the performance of individual Redis operations vs pipelined batch operations when storing 1000 embeddings.

**Real-world AI use case:** When ingesting documents, you generate and cache hundreds of embeddings at once. Pipelines reduce round trips from 1000 to 1.

**Concept being tested:** Redis pipelines, batch operations, performance measurement

**Hint:** Use `r.pipeline()` context manager, queue all operations, then `pipe.execute()` at once.

**Expected Output:**
```python
# Without pipeline: 1000 operations → 1000 round trips
# Time: 2.5 seconds

# With pipeline: 1000 operations → 1 round trip  
# Time: 0.05 seconds (50x faster!)
```

---

## Problem 09 🧩

**Difficulty:** 🔴 Hard

### Distributed Lock for LLM API Calls

**Problem:** Implement a distributed lock using Redis to prevent duplicate LLM API calls when multiple workers process the same document concurrently.

**Real-world AI use case:** In distributed systems, two workers might try to embed the same document simultaneously. A lock ensures only one proceeds, saving cost.

**Concept being tested:** Redis SETNX, distributed locking, lock expiration, retry logic

**Hint:** Use `SET key value NX EX seconds` for atomic lock acquisition. Implement retry with exponential backoff.

**Expected Output:**
```python
lock = DistributedLock(redis_client)
async with lock.acquire("doc:embed:123", timeout=30):
    # Only one worker executes this
    embedding = await compute_embedding("document content")
# Lock automatically released
```

---

## Problem 10 🧩

**Difficulty:** 🔴 Hard

### Leaderboard and Priority Queue

**Problem:** Build a priority queue using Redis sorted sets for ordering LLM processing tasks by priority, deadline, and creation time.

**Real-world AI use case:** AI processing queues need priority ordering — premium users first, urgent documents first, FIFO within same priority.

**Concept being tested:** Sorted sets as priority queues, scoring strategies, atomic operations

**Hint:** Use composite scores: `priority * 1e12 + deadline_timestamp` to sort by priority first, then deadline.

**Expected Output:**
```python
queue = PriorityQueue(redis_client)
queue.enqueue("task_1", priority=1, deadline="2024-01-15T12:00:00")  # Low priority
queue.enqueue("task_2", priority=10, deadline="2024-01-15T11:00:00") # High priority
next_task = queue.dequeue()  # Returns task_2 (higher priority)
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### Redis Streams for Event Sourcing

**Problem:** Use Redis Streams to implement an event sourcing system for an AI pipeline where every state change is recorded as an event.

**Real-world AI use case:** Event sourcing in AI systems tracks every action (document uploaded, embedding generated, query served) for debugging, replay, and audit.

**Concept being tested:** Redis Streams (XADD, XREAD, XRANGE), consumer groups

**Hint:** Use `r.xadd()` to append events, `r.xread()` for consuming, and `r.xrange()` for replay.

**Expected Output:**
```python
stream = EventStream(redis_client, "ai-pipeline")
stream.emit("doc.uploaded", {"doc_id": "123", "source": "upload"})
stream.emit("doc.chunked", {"doc_id": "123", "chunks": 5})
events = stream.replay(since="1h")
print(f"Events in last hour: {len(events)}")
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### Multi-Tier Caching Strategy

**Problem:** Implement a multi-tier caching system: L1 (in-memory dict, 100ms TTL), L2 (Redis, 1h TTL), L3 (database). Track hit rates at each level.

**Real-world AI use case:** Production AI systems use multi-level caching. Hot data in memory, warm data in Redis, cold data in database. This achieves sub-millisecond latency for frequent queries.

**Concept being tested:** Multi-tier cache, cache coherence, performance optimization

**Hint:** Check L1 → L2 → L3 in order. On miss, populate all higher levels. Track hit/miss stats per level.

**Expected Output:**
```python
cache = MultiTierCache(redis_url, db_url)
cache.get("user:42")  # L1 MISS → L2 MISS → L3 HIT (populate L1 & L2)
cache.get("user:42")  # L1 HIT (instant!)
time.sleep(0.2)       # L1 evicts (100ms TTL)
cache.get("user:42")  # L1 MISS → L2 HIT (populate L1)
print(cache.stats())
# {'L1': {'hits': 1, 'misses': 2}, 'L2': {'hits': 1, 'misses': 1}, 'L3': {'hits': 1, 'misses': 0}}
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 2](../README.md) · [🏠 Home](../../README.md)

</div>
