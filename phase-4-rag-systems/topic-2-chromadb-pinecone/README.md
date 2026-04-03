<div align="center">

# 🗄️ Topic 2: ChromaDB & Pinecone

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **ChromaDB & Pinecone**

---

## 🎯 Why This Matters

ChromaDB and Pinecone are the two most popular vector databases for RAG systems. ChromaDB is perfect for local development (free, easy); Pinecone is the go-to for production (managed, scalable).

---

## 📚 Core Concepts

### 1. ChromaDB — Local Development

```python
import chromadb
from chromadb.utils.embedding_functions import SentenceTransformerEmbeddingFunction

# Setup
client = chromadb.PersistentClient(path="./chroma_data")
embedding_fn = SentenceTransformerEmbeddingFunction(model_name="all-MiniLM-L6-v2")

collection = client.get_or_create_collection(
    name="knowledge_base",
    embedding_function=embedding_fn,
    metadata={"hnsw:space": "cosine"},
)

# Add documents
collection.add(
    documents=["RAG combines retrieval with generation", "Vector databases store embeddings"],
    metadatas=[{"source": "docs", "topic": "rag"}, {"source": "blog", "topic": "databases"}],
    ids=["doc_1", "doc_2"],
)

# Query
results = collection.query(query_texts=["How does RAG work?"], n_results=3,
    where={"topic": "rag"})
for doc, dist in zip(results["documents"][0], results["distances"][0]):
    print(f"  [{1-dist:.2f}] {doc[:80]}")
```

### 2. Pinecone — Production Scale

```python
from pinecone import Pinecone, ServerlessSpec

pc = Pinecone(api_key="your-key")

# Create index
pc.create_index("knowledge-base", dimension=384, metric="cosine",
    spec=ServerlessSpec(cloud="aws", region="us-east-1"))

index = pc.Index("knowledge-base")

# Upsert vectors
index.upsert(vectors=[
    {"id": "doc_1", "values": [0.1]*384, "metadata": {"source": "docs", "topic": "rag"}},
])

# Query with filters
results = index.query(vector=[0.1]*384, top_k=5,
    filter={"topic": {"$eq": "rag"}}, include_metadata=True)
```

### 3. When to Use Which

| Criteria | ChromaDB | Pinecone |
|----------|----------|----------|
| Cost | Free | Pay-per-use |
| Setup | `pip install` | API key |
| Scale | < 1M vectors | Billions |
| Best for | Development | Production |
| Hosting | Local/self | Managed cloud |

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **ChromaDB Docs** | 📄 Docs | [docs.trychroma.com](https://docs.trychroma.com/) |
| **Pinecone Docs** | 📄 Docs | [docs.pinecone.io](https://docs.pinecone.io/) |

> 💡 **Pro Tip:** Develop with ChromaDB locally, then deploy with Pinecone. The code changes are minimal since both follow similar patterns.

---

<div align="center">

[← Topic 1](../topic-1-embeddings-vector-search/) · [📋 Phase 4](../README.md) · [Topic 3 →](../topic-3-document-loaders-chunking/)

</div>
