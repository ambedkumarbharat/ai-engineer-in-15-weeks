<div align="center">

# 🔀 Topic 6: Hybrid Search

![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red)
![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **Hybrid Search**

---

## 🎯 Why This Matters

Pure vector search misses exact keyword matches. Pure keyword search misses semantic meaning. Hybrid search combines both for 10-20% better retrieval quality than either alone.

---

## 📚 Core Concepts

### 1. BM25 (Keyword Search)

```python
from rank_bm25 import BM25Okapi

documents = [
    "RAG combines retrieval with LLM generation for accurate answers",
    "Vector databases store embeddings for similarity search",
    "FastAPI is a modern Python web framework for APIs",
    "Docker containers package applications with dependencies",
]

tokenized_docs = [doc.lower().split() for doc in documents]
bm25 = BM25Okapi(tokenized_docs)

query = "retrieval augmented generation"
scores = bm25.get_scores(query.lower().split())
top_indices = sorted(range(len(scores)), key=lambda i: scores[i], reverse=True)

print("BM25 Results:")
for idx in top_indices[:3]:
    print(f"  [{scores[idx]:.2f}] {documents[idx][:60]}")
```

### 2. Reciprocal Rank Fusion (RRF)

```python
def reciprocal_rank_fusion(rankings: list[list[str]], k: int = 60) -> list[tuple[str, float]]:
    """Merge multiple ranked lists using RRF.
    
    RRF Score = Σ 1/(k + rank_i) for each ranking list
    """
    scores: dict[str, float] = {}
    
    for ranking in rankings:
        for rank, doc_id in enumerate(ranking):
            if doc_id not in scores:
                scores[doc_id] = 0.0
            scores[doc_id] += 1.0 / (k + rank + 1)
    
    # Sort by score descending
    return sorted(scores.items(), key=lambda x: x[1], reverse=True)

# Example
vector_results = ["doc_1", "doc_3", "doc_5", "doc_2", "doc_4"]
keyword_results = ["doc_3", "doc_1", "doc_6", "doc_5", "doc_2"]

fused = reciprocal_rank_fusion([vector_results, keyword_results])
print("Hybrid Results (RRF):")
for doc_id, score in fused[:5]:
    print(f"  {doc_id}: {score:.4f}")
```

### 3. Complete Hybrid Search Implementation

```python
class HybridSearch:
    def __init__(self, documents: list[dict]):
        self.documents = documents
        texts = [d["content"] for d in documents]
        
        # BM25 setup
        self.bm25 = BM25Okapi([t.lower().split() for t in texts])
        
        # Vector search setup (ChromaDB)
        self.chroma = chromadb.Client()
        self.collection = self.chroma.create_collection("hybrid")
        self.collection.add(
            documents=texts,
            ids=[d["id"] for d in documents],
        )
    
    def search(self, query: str, top_k: int = 5) -> list[dict]:
        # BM25 results
        bm25_scores = self.bm25.get_scores(query.lower().split())
        bm25_ranking = [self.documents[i]["id"] 
                       for i in sorted(range(len(bm25_scores)), 
                       key=lambda i: bm25_scores[i], reverse=True)]
        
        # Vector results
        vector_results = self.collection.query(query_texts=[query], n_results=top_k)
        vector_ranking = vector_results["ids"][0]
        
        # Fuse with RRF
        fused = reciprocal_rank_fusion([vector_ranking, bm25_ranking])
        return [{"id": doc_id, "score": score} for doc_id, score in fused[:top_k]]
```

---

> 💡 **Pro Tip:** Hybrid search consistently outperforms pure vector search in benchmarks. The improvement is especially large for queries with specific terms (product names, error codes, technical jargon) that vector search might miss.

---

<div align="center">

[← Topic 5](../topic-5-rag-evaluation/) · [📋 Phase 4](../README.md) · [🔨 Mini Project →](../mini-project/)

</div>
