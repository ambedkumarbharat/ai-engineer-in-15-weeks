<div align="center">

# 🔄 Topic 4: RAG Pipeline Design

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **RAG Pipeline Design**

---

## 🎯 Why This Matters

This is where everything comes together. You'll design end-to-end RAG pipelines that ingest documents, retrieve relevant context, and generate accurate answers with citations.

---

## 📚 Core Concepts

### 1. The Complete RAG Pipeline

```
 Document Ingestion Pipeline:
 ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
 │  Load    │ →  │  Chunk   │ →  │  Embed   │ →  │  Store   │
 │ Documents│    │  Text    │    │  Chunks  │    │ in VecDB │
 └──────────┘    └──────────┘    └──────────┘    └──────────┘

 Query Pipeline:
 ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
 │  User    │ →  │ Embed    │ →  │ Retrieve │ →  │ Generate │
 │  Query   │    │  Query   │    │ Top-K    │    │ Answer   │
 └──────────┘    └──────────┘    └──────────┘    └──────────┘
```

### 2. Complete Implementation

```python
import chromadb
from sentence_transformers import SentenceTransformer
from langchain.text_splitter import RecursiveCharacterTextSplitter
from openai import OpenAI

class RAGPipeline:
    def __init__(self, collection_name: str = "knowledge_base"):
        self.embedder = SentenceTransformer("all-MiniLM-L6-v2")
        self.chroma = chromadb.PersistentClient(path="./rag_db")
        self.collection = self.chroma.get_or_create_collection(collection_name)
        self.splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
        self.llm = OpenAI()  # Or use Ollama
    
    def ingest(self, documents: list[dict[str, str]]):
        """Ingest documents into the pipeline."""
        all_chunks, all_ids, all_metas = [], [], []
        
        for doc in documents:
            chunks = self.splitter.split_text(doc["content"])
            for i, chunk in enumerate(chunks):
                all_chunks.append(chunk)
                all_ids.append(f"{doc['source']}::chunk_{i}")
                all_metas.append({"source": doc["source"], "chunk_idx": i})
        
        # Batch embed and store
        embeddings = self.embedder.encode(all_chunks).tolist()
        self.collection.add(
            documents=all_chunks, embeddings=embeddings,
            ids=all_ids, metadatas=all_metas,
        )
        print(f"✅ Ingested {len(all_chunks)} chunks from {len(documents)} documents")
    
    def query(self, question: str, top_k: int = 5) -> dict:
        """Query the RAG pipeline."""
        # Retrieve
        results = self.collection.query(
            query_embeddings=[self.embedder.encode(question).tolist()],
            n_results=top_k,
        )
        
        context = "\n\n---\n\n".join(results["documents"][0])
        sources = [m["source"] for m in results["metadatas"][0]]
        
        # Generate
        prompt = f"""Answer the question based on the provided context.
If the answer isn't in the context, say "I don't have enough information."
Always cite which source document your answer comes from.

Context:
{context}

Question: {question}

Answer:"""
        
        response = self.llm.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3,
        )
        
        return {
            "answer": response.choices[0].message.content,
            "sources": list(set(sources)),
            "chunks_used": len(results["documents"][0]),
        }

# Usage
pipeline = RAGPipeline()
pipeline.ingest([
    {"source": "rag_guide.md", "content": "RAG stands for Retrieval Augmented Generation..."},
    {"source": "embeddings.md", "content": "Embeddings are numerical representations..."},
])
result = pipeline.query("What is RAG?")
print(f"Answer: {result['answer']}")
print(f"Sources: {result['sources']}")
```

### 3. Advanced RAG Patterns

```python
# Pattern 1: Query Rewriting — Improve retrieval with LLM-refined queries
def rewrite_query(original_query: str, llm) -> list[str]:
    """Generate multiple search queries from the original question."""
    prompt = f"""Generate 3 different search queries that would help answer this question.
Return only the queries, one per line.

Question: {original_query}"""
    
    response = llm.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}],
    )
    return response.choices[0].message.content.strip().split("\n")

# Pattern 2: Contextual Compression — Only keep relevant parts of chunks
def compress_context(chunks: list[str], question: str, llm) -> list[str]:
    """Extract only the relevant sentences from each chunk."""
    compressed = []
    for chunk in chunks:
        prompt = f"Extract only the sentences relevant to this question: {question}\n\nText: {chunk}\n\nRelevant sentences:"
        response = llm.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}],
            max_tokens=200,
        )
        result = response.choices[0].message.content.strip()
        if result and result != "None":
            compressed.append(result)
    return compressed

# Pattern 3: Re-ranking — Score retrieved chunks by relevance
def rerank_chunks(chunks: list[str], query: str) -> list[tuple[str, float]]:
    """Re-rank chunks using cross-encoder for better precision."""
    from sentence_transformers import CrossEncoder
    reranker = CrossEncoder("cross-encoder/ms-marco-MiniLM-L-6-v2")
    pairs = [(query, chunk) for chunk in chunks]
    scores = reranker.predict(pairs)
    ranked = sorted(zip(chunks, scores), key=lambda x: x[1], reverse=True)
    return ranked
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **RAG from Scratch** | 📹 YouTube | [LangChain](https://www.youtube.com/watch?v=wd7TZ4w1mSw) |
| **Building RAG Applications** | 📄 Docs | [python.langchain.com](https://python.langchain.com/docs/tutorials/rag/) |
| **Advanced RAG Techniques** | 📄 Blog | [LlamaIndex Blog](https://www.llamaindex.ai/blog) |

> 💡 **Pro Tip:** The simplest RAG pipeline (embed → retrieve → generate) gets you 70% of the way. Adding query rewriting, re-ranking, and contextual compression gets you to 90%+. Always benchmark against a baseline before adding complexity.

---

<div align="center">

[← Topic 3](../topic-3-document-loaders-chunking/) · [📋 Phase 4](../README.md) · [Topic 5 →](../topic-5-rag-evaluation/)

</div>
