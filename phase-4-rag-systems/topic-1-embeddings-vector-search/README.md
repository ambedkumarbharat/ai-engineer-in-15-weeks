<div align="center">

# 🔢 Topic 1: Embeddings & Vector Search

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **Embeddings & Vector Search**

---

## 🎯 Why This Matters

Embeddings are the foundation of RAG. They convert text into numerical vectors that capture semantic meaning, enabling similarity search — the core retrieval mechanism in every RAG system.

---

## 📚 Core Concepts

### 1. What Are Embeddings?

```python
# Embeddings map text → dense vectors where similar text = close vectors
# "king" and "queen" are close; "king" and "pizza" are far apart

from sentence_transformers import SentenceTransformer
import numpy as np

model = SentenceTransformer("all-MiniLM-L6-v2")  # Free, fast, 384 dimensions

texts = [
    "How to build a RAG pipeline",
    "Retrieval augmented generation tutorial",
    "Best pizza recipe in Italy",
]

embeddings = model.encode(texts)
print(f"Shape: {embeddings.shape}")  # (3, 384)

# Cosine similarity
from sklearn.metrics.pairwise import cosine_similarity
sim_matrix = cosine_similarity(embeddings)
print(f"RAG vs RAG tutorial: {sim_matrix[0][1]:.3f}")  # ~0.85 (similar!)
print(f"RAG vs pizza: {sim_matrix[0][2]:.3f}")          # ~0.15 (different!)
```

### 2. Embedding Models Comparison

| Model | Dimensions | Speed | Quality | Cost |
|-------|-----------|-------|---------|------|
| `all-MiniLM-L6-v2` | 384 | ⚡ Fast | Good | Free |
| `all-mpnet-base-v2` | 768 | Medium | Better | Free |
| `text-embedding-3-small` | 1536 | Fast | Excellent | $0.02/1M tokens |
| `text-embedding-3-large` | 3072 | Medium | Best | $0.13/1M tokens |

### 3. OpenAI Embeddings

```python
from openai import OpenAI

client = OpenAI()

def get_embedding(text: str, model: str = "text-embedding-3-small") -> list[float]:
    response = client.embeddings.create(input=text, model=model)
    return response.data[0].embedding

# Batch embedding (more efficient)
def get_embeddings_batch(texts: list[str]) -> list[list[float]]:
    response = client.embeddings.create(input=texts, model="text-embedding-3-small")
    return [item.embedding for item in response.data]

embedding = get_embedding("What is retrieval augmented generation?")
print(f"Dimensions: {len(embedding)}")  # 1536
```

### 4. Similarity Search Algorithms

```python
import numpy as np

def cosine_similarity(a: np.ndarray, b: np.ndarray) -> float:
    """Most common for text embeddings."""
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

def euclidean_distance(a: np.ndarray, b: np.ndarray) -> float:
    """Good for dense, normalized vectors."""
    return np.linalg.norm(a - b)

def dot_product(a: np.ndarray, b: np.ndarray) -> float:
    """Fast, used when vectors are normalized."""
    return np.dot(a, b)

# Approximate Nearest Neighbor (ANN) — what vector DBs use internally
# Exact search: O(n) — check every vector
# ANN (HNSW, IVF): O(log n) — approximate but 100x faster
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **What are Embeddings?** | 📹 YouTube | [3Blue1Brown](https://www.youtube.com/watch?v=wjZofJX0v4M) |
| **Sentence Transformers Docs** | 📄 Docs | [sbert.net](https://www.sbert.net/) |
| **OpenAI Embeddings Guide** | 📄 Docs | [platform.openai.com](https://platform.openai.com/docs/guides/embeddings) |
| **FAISS Tutorial** | 📄 GitHub | [facebookresearch/faiss](https://github.com/facebookresearch/faiss) |

---

## 📋 Quick Reference Cheatsheet

| Concept | Description | When to Use |
|---------|-------------|------------|
| Cosine similarity | Angle between vectors (0-1) | Default for text |
| Euclidean distance | Straight-line distance | Normalized vectors |
| HNSW index | Graph-based ANN | Fast search, good recall |
| IVF index | Cluster-based ANN | Large datasets (>1M) |
| Dimensionality | Vector size (384-3072) | Higher = better quality, slower |

> 💡 **Pro Tip:** Start with `all-MiniLM-L6-v2` (free, 384 dims) for prototyping. Switch to OpenAI `text-embedding-3-small` for production quality. The quality difference is noticeable but the free model is surprisingly good for most use cases.

---

<div align="center">

[📋 Phase 4](../README.md) · [Topic 2: ChromaDB & Pinecone →](../topic-2-chromadb-pinecone/)

</div>
