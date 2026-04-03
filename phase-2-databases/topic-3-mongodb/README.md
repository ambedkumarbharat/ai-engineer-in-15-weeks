<div align="center">

# 🍃 Topic 3: MongoDB

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-2_Databases-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > **MongoDB**

---

## 🎯 Why This Matters for AI Systems Engineering

MongoDB stores **unstructured and semi-structured data** — the kind of data AI systems deal with constantly:
- **Conversation logs** → Messages with varying metadata per model
- **Document metadata** → PDFs, web pages, each with different attributes
- **Agent action logs** → Each agent step has different tool outputs
- **Experiment tracking** → Configs with nested, evolving schemas
- **User activity** → Click streams, search queries, feedback

MongoDB's flexible schema means you don't need to ALTER TABLE every time your AI pipeline produces a new type of metadata.

---

## 📚 Core Concepts

### 1. PyMongo Basics

```python
from pymongo import MongoClient
from datetime import datetime

# Connect
client = MongoClient("mongodb://localhost:27017")
db = client["ai_platform"]

# Collections (like tables, but schema-free)
conversations = db["conversations"]
documents = db["documents"]

# Insert a document
conversation = {
    "user_id": "user_42",
    "title": "RAG Discussion",
    "model": "gpt-4",
    "messages": [
        {"role": "user", "content": "What is RAG?", "timestamp": datetime.now()},
        {"role": "assistant", "content": "RAG stands for...", "tokens": 150, "timestamp": datetime.now()},
    ],
    "metadata": {
        "topic": "rag",
        "satisfaction_score": None,
        "tags": ["rag", "beginner"],
    },
    "total_tokens": 200,
    "created_at": datetime.now(),
}

result = conversations.insert_one(conversation)
print(f"Inserted: {result.inserted_id}")

# Query
conv = conversations.find_one({"user_id": "user_42"})
print(f"Title: {conv['title']}, Messages: {len(conv['messages'])}")

# Find with filters
recent = conversations.find(
    {"user_id": "user_42", "total_tokens": {"$gt": 100}},
    {"title": 1, "total_tokens": 1, "_id": 0},  # Projection
).sort("created_at", -1).limit(10)

for doc in recent:
    print(doc)
```

### 2. CRUD Operations

```python
from pymongo import MongoClient, UpdateOne
from bson import ObjectId

client = MongoClient("mongodb://localhost:27017")
db = client["ai_platform"]
docs = db["documents"]

# CREATE — Insert many
documents_to_insert = [
    {"title": f"Doc {i}", "content": f"Content {i}", "source": "upload", 
     "word_count": i * 100, "embedded": False, "created_at": datetime.now()}
    for i in range(5)
]
result = docs.insert_many(documents_to_insert)
print(f"Inserted {len(result.inserted_ids)} documents")

# READ — Various queries
# Find by ID
doc = docs.find_one({"_id": result.inserted_ids[0]})

# Find with operators
large_docs = list(docs.find({"word_count": {"$gte": 300}}))
unembedded = list(docs.find({"embedded": False}))

# UPDATE — Single and bulk
docs.update_one(
    {"_id": result.inserted_ids[0]},
    {"$set": {"embedded": True, "embedding_model": "text-embedding-3-small"}}
)

# Bulk update — mark all as embedded
docs.update_many(
    {"embedded": False},
    {"$set": {"embedded": True, "embedded_at": datetime.now()}}
)

# DELETE
docs.delete_one({"_id": result.inserted_ids[4]})
deleted = docs.delete_many({"word_count": {"$lt": 200}})
print(f"Deleted {deleted.deleted_count} documents")
```

### 3. Aggregation Pipeline

```python
# Aggregation — powerful data processing
pipeline = [
    # Stage 1: Match conversations from last 7 days
    {"$match": {
        "created_at": {"$gte": datetime(2024, 1, 8)},
    }},
    # Stage 2: Unwind messages array
    {"$unwind": "$messages"},
    # Stage 3: Group by model
    {"$group": {
        "_id": "$model",
        "total_conversations": {"$addToSet": "$_id"},
        "total_messages": {"$sum": 1},
        "total_tokens": {"$sum": "$messages.tokens"},
        "avg_tokens_per_message": {"$avg": "$messages.tokens"},
    }},
    # Stage 4: Project results
    {"$project": {
        "model": "$_id",
        "conversations": {"$size": "$total_conversations"},
        "total_messages": 1,
        "total_tokens": 1,
        "avg_tokens_per_message": {"$round": ["$avg_tokens_per_message", 1]},
    }},
    # Stage 5: Sort by usage
    {"$sort": {"total_tokens": -1}},
]

results = list(db["conversations"].aggregate(pipeline))
for r in results:
    print(f"{r['model']}: {r['total_messages']} msgs, {r['total_tokens']} tokens")
```

### 4. Async MongoDB with Motor

```python
import asyncio
from motor.motor_asyncio import AsyncIOMotorClient

async def main():
    # Async connection
    client = AsyncIOMotorClient("mongodb://localhost:27017")
    db = client["ai_platform"]
    logs = db["activity_logs"]
    
    # Async insert
    await logs.insert_one({
        "user_id": "user_42",
        "action": "search",
        "query": "how to implement RAG",
        "results_count": 5,
        "latency_ms": 234,
        "timestamp": datetime.now(),
    })
    
    # Async find
    cursor = logs.find({"user_id": "user_42"}).sort("timestamp", -1).limit(10)
    async for log in cursor:
        print(f"Action: {log['action']} — {log['latency_ms']}ms")
    
    # Async aggregation
    pipeline = [
        {"$group": {"_id": "$action", "count": {"$sum": 1}, "avg_latency": {"$avg": "$latency_ms"}}},
        {"$sort": {"count": -1}},
    ]
    async for result in logs.aggregate(pipeline):
        print(f"{result['_id']}: {result['count']} calls, avg {result['avg_latency']:.0f}ms")

asyncio.run(main())
```

### 5. Indexing for Performance

```python
from pymongo import ASCENDING, DESCENDING, TEXT

# Single field index
db["conversations"].create_index([("user_id", ASCENDING)])

# Compound index
db["conversations"].create_index([("user_id", ASCENDING), ("created_at", DESCENDING)])

# Text index for search
db["documents"].create_index([("content", TEXT), ("title", TEXT)])

# TTL index — auto-delete old logs after 30 days
db["activity_logs"].create_index("timestamp", expireAfterSeconds=30*24*3600)

# Text search
results = db["documents"].find(
    {"$text": {"$search": "machine learning embeddings"}},
    {"score": {"$meta": "textScore"}},
).sort([("score", {"$meta": "textScore"})]).limit(5)
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **MongoDB University (Free)** | 📄 Course | [learn.mongodb.com](https://learn.mongodb.com/) |
| **PyMongo Tutorial** | 📄 Docs | [pymongo.readthedocs.io](https://pymongo.readthedocs.io/) |
| **MongoDB Crash Course** | 📹 YouTube | [Traversy Media - MongoDB](https://www.youtube.com/watch?v=-56x56UppqQ) |
| **MongoDB Aggregation** | 📄 Docs | [mongodb.com/docs/manual/aggregation](https://www.mongodb.com/docs/manual/aggregation/) |
| **Motor (Async MongoDB)** | 📄 Docs | [motor.readthedocs.io](https://motor.readthedocs.io/) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| No indexes | Queries scan every document | Add indexes on query fields |
| Deeply nested documents | Hard to query and update | Flatten or use references |
| Not using projections | Returns entire large documents | Use `{field: 1}` projection |
| Ignoring write concerns | Data loss risk | Use `w="majority"` for important writes |
| Storing everything in one collection | Poor query performance | Separate by access pattern |
| Not using aggregation pipeline | Complex queries in Python (slow) | Use `$match`, `$group`, `$project` in DB |

---

## 📋 Quick Reference Cheatsheet

| Operation | PyMongo | Description |
|-----------|---------|-------------|
| Insert one | `col.insert_one(doc)` | Add single document |
| Insert many | `col.insert_many(docs)` | Add multiple documents |
| Find one | `col.find_one(filter)` | Get single document |
| Find many | `col.find(filter)` | Get cursor of documents |
| Update one | `col.update_one(filter, {"$set": ...})` | Update first match |
| Update many | `col.update_many(filter, update)` | Update all matches |
| Delete one | `col.delete_one(filter)` | Remove first match |
| Aggregate | `col.aggregate(pipeline)` | Run aggregation pipeline |
| Create index | `col.create_index(keys)` | Add performance index |
| Count | `col.count_documents(filter)` | Count matching docs |

---

> 💡 **Pro Tip:** Use MongoDB for data that doesn't have a fixed schema — agent action logs, experiment configs, user activity streams. For structured data with relationships (users, billing), stick with PostgreSQL. Many AI systems use both: PostgreSQL for core data, MongoDB for flexible logs and metadata.

---

<div align="center">

[← Topic 2: Redis](../topic-2-redis/) · [🏠 Home](../../README.md) · [📋 Phase 2](../README.md)

**[▶ Problems](problems.md)** · [Topic 4: Vector Databases →](../topic-4-vector-databases/)

</div>
