<div align="center">

# 📐 Topic 4: Vector Databases

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-2_Databases-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > **Vector Databases**

---

## 🎯 Why This Matters for AI Systems Engineering

Vector databases are the **backbone of RAG systems**. They store and search **embeddings** — numerical representations of text, images, and other data. Every time you ask a chatbot a question and it retrieves relevant documents, a vector database is doing the heavy lifting.

Key capabilities:
- **Similarity search** → Find documents most similar to a query
- **Semantic understanding** → "car" matches "automobile" even though words differ
- **Metadata filtering** → Filter by source, date, category before vector search
- **Scalability** → Handle millions to billions of vectors

---

## 📚 Core Concepts

### 1. Understanding Embeddings

```python
# Embeddings convert text to numbers that capture meaning
# Similar texts → similar numbers (close in vector space)

# Simulating embeddings (in production, use OpenAI or sentence-transformers)
import hashlib
import math

def simple_embedding(text: str, dim: int = 384) -> list[float]:
    """Generate a simple embedding for demonstration."""
    # In production, use: openai.embeddings.create() or SentenceTransformer
    seed = hashlib.sha256(text.lower().encode()).digest()
    embedding = []
    for i in range(dim):
        byte_val = seed[i % len(seed)]
        embedding.append((byte_val / 255.0) * 2 - 1)  # Normalize to [-1, 1]
    # Normalize to unit vector
    magnitude = math.sqrt(sum(x**2 for x in embedding))
    return [x / magnitude for x in embedding]

def cosine_similarity(a: list[float], b: list[float]) -> float:
    """Compute cosine similarity between two vectors."""
    dot_product = sum(x * y for x, y in zip(a, b))
    mag_a = math.sqrt(sum(x**2 for x in a))
    mag_b = math.sqrt(sum(x**2 for x in b))
    return dot_product / (mag_a * mag_b)

# Demo
emb1 = simple_embedding("machine learning is great")
emb2 = simple_embedding("deep learning is powerful")
emb3 = simple_embedding("I like pizza")

print(f"ML vs DL similarity: {cosine_similarity(emb1, emb2):.3f}")   # Higher
print(f"ML vs Pizza similarity: {cosine_similarity(emb1, emb3):.3f}") # Lower
```

### 2. ChromaDB (Local, Free, Easy)

```python
import chromadb
from chromadb.utils import embedding_functions

# Initialize ChromaDB
client = chromadb.PersistentClient(path="./chroma_db")

# Use sentence-transformers for real embeddings
ef = embedding_functions.SentenceTransformerEmbeddingFunction(
    model_name="all-MiniLM-L6-v2"
)

# Create collection
collection = client.get_or_create_collection(
    name="knowledge_base",
    embedding_function=ef,
    metadata={"hnsw:space": "cosine"},  # Use cosine similarity
)

# Add documents
collection.add(
    documents=[
        "RAG combines retrieval with generation for better AI responses",
        "Vector databases store embeddings for similarity search",
        "LangChain is a framework for building LLM applications",
        "FastAPI is a modern Python web framework",
        "Docker containers package applications with their dependencies",
    ],
    metadatas=[
        {"topic": "rag", "difficulty": "intermediate"},
        {"topic": "databases", "difficulty": "intermediate"},
        {"topic": "frameworks", "difficulty": "beginner"},
        {"topic": "web", "difficulty": "beginner"},
        {"topic": "devops", "difficulty": "beginner"},
    ],
    ids=["doc_1", "doc_2", "doc_3", "doc_4", "doc_5"],
)

# Query — find similar documents
results = collection.query(
    query_texts=["How do I build a RAG pipeline?"],
    n_results=3,
    where={"difficulty": "intermediate"},  # Metadata filter
)

for doc, score, meta in zip(
    results["documents"][0], 
    results["distances"][0], 
    results["metadatas"][0]
):
    print(f"  [{meta['topic']}] {doc[:60]}... (distance: {score:.3f})")
```

### 3. Pinecone (Cloud, Managed, Scalable)

```python
from pinecone import Pinecone, ServerlessSpec

# Initialize Pinecone
pc = Pinecone(api_key="your-api-key")

# Create index
pc.create_index(
    name="ai-knowledge-base",
    dimension=384,
    metric="cosine",
    spec=ServerlessSpec(cloud="aws", region="us-east-1"),
)

# Connect to index
index = pc.Index("ai-knowledge-base")

# Upsert vectors
index.upsert(vectors=[
    {
        "id": "doc_1",
        "values": [0.1, 0.2, ...],  # 384-dim embedding
        "metadata": {
            "title": "RAG Tutorial",
            "source": "blog",
            "topic": "rag",
            "word_count": 1500,
        }
    },
    # ... more vectors
])

# Query with metadata filter
results = index.query(
    vector=[0.1, 0.2, ...],  # Query embedding
    top_k=5,
    filter={
        "topic": {"$eq": "rag"},
        "word_count": {"$gte": 500},
    },
    include_metadata=True,
)

for match in results["matches"]:
    print(f"  {match['id']}: score={match['score']:.3f} — {match['metadata']['title']}")
```

### 4. When to Use Which Vector Database

```
┌────────────────┬──────────────┬──────────────┬──────────────┐
│ Feature        │ ChromaDB     │ Pinecone     │ pgvector     │
├────────────────┼──────────────┼──────────────┼──────────────┤
│ Deployment     │ Local/Self   │ Cloud/Managed│ PostgreSQL   │
│ Cost           │ Free         │ Pay per use  │ Free (self)  │
│ Scale          │ < 1M vectors │ Billions     │ < 5M vectors │
│ Setup          │ pip install  │ API key      │ Extension    │
│ Persistence    │ Local files  │ Cloud        │ PostgreSQL   │
│ Metadata       │ ✅ Good      │ ✅ Excellent │ ✅ SQL joins  │
│ Hybrid search  │ ❌ No        │ ❌ No        │ ✅ FTS + vec  │
│ Best for       │ Prototyping  │ Production   │ Already use PG│
│ Python client  │ chromadb     │ pinecone     │ psycopg2     │
└────────────────┴──────────────┴──────────────┴──────────────┘

Rule of thumb:
• Learning/prototyping → ChromaDB
• Production with scale → Pinecone or Weaviate
• Already using PostgreSQL → pgvector
• Need hybrid search → pgvector or Weaviate
```

### 5. Metadata Filtering Patterns

```python
# ChromaDB filtering
results = collection.query(
    query_texts=["machine learning"],
    n_results=10,
    where={
        "$and": [
            {"topic": {"$eq": "ml"}},
            {"difficulty": {"$in": ["beginner", "intermediate"]}},
        ]
    },
    where_document={"$contains": "neural"},  # Filter on document text
)

# Pinecone filtering
results = index.query(
    vector=query_embedding,
    top_k=10,
    filter={
        "$and": [
            {"source": {"$eq": "arxiv"}},
            {"year": {"$gte": 2023}},
            {"citations": {"$gte": 50}},
        ]
    },
)
```

### 6. Managing Collections and Indexes

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_db")

# List collections
collections = client.list_collections()
for col in collections:
    print(f"Collection: {col.name}, Count: {col.count()}")

# Delete and recreate (for reindexing)
client.delete_collection("old_collection")

# Update document metadata
collection.update(
    ids=["doc_1"],
    metadatas=[{"topic": "rag", "difficulty": "advanced", "updated": True}],
)

# Delete specific documents
collection.delete(ids=["doc_3", "doc_4"])

# Delete by filter
collection.delete(where={"topic": "deprecated"})
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **ChromaDB Getting Started** | 📄 Docs | [docs.trychroma.com](https://docs.trychroma.com/) |
| **Vector Databases Explained** | 📹 YouTube | [Fireship - Vector Database](https://www.youtube.com/watch?v=klTvEwg3oJ4) |
| **Pinecone Documentation** | 📄 Docs | [docs.pinecone.io](https://docs.pinecone.io/) |
| **Sentence Transformers** | 📄 Docs | [sbert.net](https://www.sbert.net/) |
| **What are Embeddings?** | 📹 YouTube | [3Blue1Brown - Embeddings](https://www.youtube.com/watch?v=wjZofJX0v4M) |
| **pgvector Tutorial** | 📄 Docs | [github.com/pgvector/pgvector](https://github.com/pgvector/pgvector) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Wrong embedding dimension | Query fails silently | Match index dimension to model output |
| Not normalizing vectors | Similarity scores are wrong | Use cosine metric or normalize vectors |
| Storing raw text as embedding | Not how embeddings work | Use an embedding model to convert text |
| No metadata filtering | Retrieves irrelevant docs | Add source, date, topic metadata |
| One giant collection | Slow queries, mixed content | Separate collections by content type |
| Not persisting ChromaDB | Data lost on restart | Use `PersistentClient(path=...)` |

---

## 📋 Quick Reference Cheatsheet

| Operation | ChromaDB | Pinecone |
|-----------|----------|----------|
| Connect | `chromadb.PersistentClient()` | `Pinecone(api_key=...)` |
| Create collection | `client.create_collection()` | `pc.create_index()` |
| Add vectors | `collection.add()` | `index.upsert()` |
| Query | `collection.query()` | `index.query()` |
| Filter | `where={"key": "val"}` | `filter={"key": {"$eq": "val"}}` |
| Delete | `collection.delete(ids=[])` | `index.delete(ids=[])` |
| Count | `collection.count()` | `index.describe_index_stats()` |
| Update | `collection.update()` | `index.upsert()` (overwrite) |

---

> 💡 **Pro Tip:** Start with ChromaDB for learning and prototyping — it's free and runs locally with `pip install chromadb`. When you're ready for production, evaluate Pinecone (managed, easy), Weaviate (self-hosted, feature-rich), or pgvector (if you already use PostgreSQL). The code changes are minimal since most vector DB clients follow similar patterns.

---

<div align="center">

[← Topic 3: MongoDB](../topic-3-mongodb/) · [🏠 Home](../../README.md) · [📋 Phase 2](../README.md)

**[▶ Problems](problems.md)** · [🔨 Mini Project →](../mini-project/)

</div>
