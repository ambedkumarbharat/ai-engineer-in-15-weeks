<div align="center">

# 🔨 Mini Project: Personal Knowledge Base QA Bot

### Upload documents, ask questions, get cited answers — full RAG pipeline

![Tech](https://img.shields.io/badge/Streamlit-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![Tech](https://img.shields.io/badge/ChromaDB-FF6F61?style=flat-square)
![Tech](https://img.shields.io/badge/Ollama-000000?style=flat-square)
![Tech](https://img.shields.io/badge/LangChain-1C3C3C?style=flat-square)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **Mini Project**

---

## 📋 Project Overview

Build a **Streamlit web app** where users can upload PDF/text files, ask questions, and receive answers with **source citations**. The complete RAG pipeline: document loading → chunking → embedding → storage → retrieval → generation.

---

## 🧠 Concepts Used

| Concept | Where It's Used |
|---------|----------------|
| **Document Loaders** | PDF and text file parsing |
| **Chunking** | RecursiveCharacterTextSplitter |
| **Embeddings** | sentence-transformers (free) |
| **Vector Store** | ChromaDB (persistent) |
| **RAG Pipeline** | Retrieve → Context → Generate |
| **LLM** | Ollama (free, local) |
| **UI** | Streamlit web interface |

---

## 🏗️ File Structure

```
knowledge-base-qa/
├── app.py                  # Streamlit main app
├── rag_pipeline.py         # RAG pipeline class
├── document_processor.py   # Document loading & chunking
├── requirements.txt
├── chroma_data/            # Persistent vector store
└── README.md
```

---

## 🔧 Key Implementation

```python
# rag_pipeline.py
import chromadb
from sentence_transformers import SentenceTransformer
from langchain.text_splitter import RecursiveCharacterTextSplitter
import requests

class RAGPipeline:
    def __init__(self):
        self.embedder = SentenceTransformer("all-MiniLM-L6-v2")
        self.chroma = chromadb.PersistentClient(path="./chroma_data")
        self.collection = self.chroma.get_or_create_collection("knowledge_base")
        self.splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
    
    def ingest_document(self, text: str, source: str):
        chunks = self.splitter.split_text(text)
        embeddings = self.embedder.encode(chunks).tolist()
        ids = [f"{source}::chunk_{i}" for i in range(len(chunks))]
        metas = [{"source": source, "chunk_idx": i} for i in range(len(chunks))]
        self.collection.add(documents=chunks, embeddings=embeddings, ids=ids, metadatas=metas)
        return len(chunks)
    
    def ask(self, question: str, top_k: int = 5) -> dict:
        query_emb = self.embedder.encode(question).tolist()
        results = self.collection.query(query_embeddings=[query_emb], n_results=top_k)
        
        context = "\n\n---\n\n".join(results["documents"][0])
        sources = list(set(m["source"] for m in results["metadatas"][0]))
        
        prompt = f"""Answer based on the context below. Cite sources.
If unsure, say "I don't have enough information."

Context:
{context}

Question: {question}
Answer:"""
        
        # Use Ollama (free)
        response = requests.post("http://localhost:11434/api/chat", json={
            "model": "llama3",
            "messages": [{"role": "user", "content": prompt}],
            "stream": False,
        })
        
        return {
            "answer": response.json()["message"]["content"],
            "sources": sources,
            "chunks_used": len(results["documents"][0]),
        }
```

```python
# app.py
import streamlit as st
from rag_pipeline import RAGPipeline
from PyPDF2 import PdfReader

st.set_page_config(page_title="📚 Knowledge Base QA", layout="wide")
st.title("📚 Personal Knowledge Base QA Bot")

@st.cache_resource
def get_pipeline():
    return RAGPipeline()

pipeline = get_pipeline()

# Sidebar — Upload documents
with st.sidebar:
    st.header("📄 Upload Documents")
    files = st.file_uploader("Upload PDF or TXT", type=["pdf", "txt"], accept_multiple_files=True)
    if files and st.button("📥 Ingest Documents"):
        for file in files:
            if file.name.endswith(".pdf"):
                reader = PdfReader(file)
                text = "\n".join(page.extract_text() for page in reader.pages)
            else:
                text = file.read().decode("utf-8")
            chunks = pipeline.ingest_document(text, file.name)
            st.success(f"✅ {file.name}: {chunks} chunks indexed")
    st.metric("Documents in DB", pipeline.collection.count())

# Main — Ask questions
question = st.text_input("🔍 Ask a question about your documents:")
if question:
    with st.spinner("Searching and generating answer..."):
        result = pipeline.ask(question)
    st.markdown(f"### 💡 Answer\n{result['answer']}")
    st.markdown(f"**📎 Sources:** {', '.join(result['sources'])}")
    st.caption(f"Used {result['chunks_used']} chunks for context")
```

---

## ▶️ How to Run

```bash
pip install streamlit chromadb sentence-transformers PyPDF2 requests
ollama pull llama3
streamlit run app.py
```

---

## 🚀 How to Extend

1. Add conversation memory (multi-turn QA)
2. Add hybrid search (BM25 + vector)
3. Add re-ranking with cross-encoder
4. Add document deletion and re-indexing
5. Add evaluation dashboard with test questions

---

## 📝 What to Add to Your Resume

> **Personal Knowledge Base QA Bot** — Built a full-stack RAG application using Streamlit that allows users to upload PDFs/text files and ask questions with cited answers. Implemented complete RAG pipeline: document parsing (PyPDF2) → recursive chunking (500 chars, 50 overlap) → embedding (sentence-transformers, 384-dim) → ChromaDB vector storage → top-5 retrieval → Ollama LLM generation with source citations. Zero API cost using local models.

---

<div align="center">

[← Topic 6: Hybrid Search](../topic-6-hybrid-search/) · [🏠 Home](../../README.md)

**[Phase 5: Agentic AI →](../../phase-5-agentic-ai/)**

</div>
