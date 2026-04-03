<div align="center">

# 🏆 Phase 7 — Major Project

### Build Your Capstone Project: Full-Stack AI Application

![Duration](https://img.shields.io/badge/Duration-3_Weeks-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red)
![Type](https://img.shields.io/badge/Type-Capstone_Project-gold)

</div>

---

> 🏠 [Home](../README.md) > **Phase 7 — Major Project**

---

## 🎯 What You'll Build

A **full-stack AI application** that demonstrates everything you've learned. This is your **portfolio centerpiece** — the project you'll showcase in interviews and on your resume.

---

## 🚀 Project: AI-Powered Document Intelligence Platform

### Overview

Build a complete **Document Intelligence Platform** where users can:
1. **Upload** documents (PDFs, text files, web URLs)
2. **Ask questions** about their documents with cited answers
3. **Chat** with an AI assistant that has access to their knowledge base
4. **Analyze** documents for summaries, key topics, and insights
5. **Manage** their document collections with metadata and tags

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────┐
│                  FRONTEND (Streamlit)                  │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐ │
│  │ Upload  │  │  Chat   │  │ Search  │  │ Analyze │ │
│  │  Page   │  │  Page   │  │  Page   │  │  Page   │ │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘ │
└───────────────────────┬──────────────────────────────┘
                        │ HTTP/REST
┌───────────────────────┴──────────────────────────────┐
│                  BACKEND (FastAPI)                     │
│  ┌───────────┐  ┌───────────┐  ┌───────────────────┐ │
│  │ Document  │  │   RAG     │  │   Agent (LangGraph)│ │
│  │ Ingestion │  │  Pipeline │  │   Research + Tools │ │
│  │ Service   │  │  Service  │  │   Service          │ │
│  └───────────┘  └───────────┘  └───────────────────┘ │
└────────┬─────────────┬─────────────┬─────────────────┘
         │             │             │
┌────────┴──┐  ┌───────┴───┐  ┌─────┴─────┐  ┌───────┐
│ PostgreSQL│  │  ChromaDB  │  │   Redis   │  │Ollama │
│ (Users,   │  │  (Vectors, │  │ (Cache,   │  │(LLM)  │
│  Metadata)│  │  Embeddings│  │  Sessions) │  │       │
└───────────┘  └───────────┘  └───────────┘  └───────┘
```

---

## 📋 Feature Requirements

### Must Have (Week 1-2)
- [ ] User authentication (API keys or simple auth)
- [ ] Document upload (PDF, TXT, URL)
- [ ] Document processing pipeline (load → chunk → embed → store)
- [ ] RAG-based Q&A with source citations
- [ ] Chat interface with conversation history
- [ ] Health check and basic monitoring

### Should Have (Week 2-3)
- [ ] Document management (list, delete, update)
- [ ] Hybrid search (vector + keyword)
- [ ] Response caching with Redis
- [ ] Rate limiting per user
- [ ] Streaming responses
- [ ] Collection/folder organization

### Nice to Have (Week 3)
- [ ] Multi-model support (switch between Ollama models)
- [ ] Document analytics (word count, topics, summaries)
- [ ] Export chat history
- [ ] Agent mode (research + tools)
- [ ] LangSmith/Langfuse integration
- [ ] Docker Compose deployment

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Streamlit |
| **Backend** | FastAPI |
| **LLM** | Ollama (local, free) |
| **Embeddings** | sentence-transformers |
| **Vector DB** | ChromaDB |
| **SQL DB** | PostgreSQL |
| **Cache** | Redis |
| **Agent** | LangGraph |
| **Containerization** | Docker + Docker Compose |
| **CI/CD** | GitHub Actions |

---

## 🏗️ Suggested File Structure

```
document-intelligence-platform/
├── backend/
│   ├── src/
│   │   ├── main.py               # FastAPI app
│   │   ├── config.py             # Settings
│   │   ├── routes/
│   │   │   ├── documents.py      # Document CRUD
│   │   │   ├── chat.py           # Chat/QA endpoints
│   │   │   ├── search.py         # Search endpoints
│   │   │   └── health.py         # Health check
│   │   ├── services/
│   │   │   ├── ingestion.py      # Document processing
│   │   │   ├── rag.py            # RAG pipeline
│   │   │   ├── agent.py          # LangGraph agent
│   │   │   └── cache.py          # Redis caching
│   │   └── models/
│   │       ├── schemas.py        # Pydantic models
│   │       └── database.py       # SQLAlchemy models
│   ├── tests/
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/
│   ├── app.py                    # Streamlit app
│   ├── pages/
│   │   ├── 1_📄_Upload.py
│   │   ├── 2_💬_Chat.py
│   │   ├── 3_🔍_Search.py
│   │   └── 4_📊_Analytics.py
│   └── requirements.txt
├── docker-compose.yml
├── .github/workflows/ci.yml
├── .env.example
├── Makefile
└── README.md
```

---

## 📅 Week-by-Week Plan

### Week 1: Backend Foundation
- Day 1-2: Project setup, Docker Compose, database models
- Day 3-4: Document ingestion pipeline (load, chunk, embed, store)
- Day 5: RAG Q&A endpoint with citations

### Week 2: Features & Frontend
- Day 1-2: Chat endpoint with conversation history
- Day 3: Streamlit frontend (upload, chat, search pages)
- Day 4: Redis caching and rate limiting
- Day 5: Hybrid search, document management

### Week 3: Polish & Deploy
- Day 1-2: Agent mode with LangGraph
- Day 3: Testing (pytest, 80%+ coverage)
- Day 4: GitHub Actions CI/CD, LangSmith integration
- Day 5: Documentation, README, demo recording

---

## 📝 What to Add to Your Resume

> **Document Intelligence Platform** — Architected and built a full-stack AI-powered document management system. Backend: FastAPI with PostgreSQL (user/document metadata), ChromaDB (vector storage for 384-dim embeddings), Redis (response caching, rate limiting), and Ollama (local LLM inference). Features include: multi-format document ingestion (PDF/TXT/URL), recursive chunking (500 chars, 50 overlap), RAG-based Q&A with source citations, hybrid search (BM25 + vector with RRF), streaming chat with conversation history, and LangGraph-powered research agent. Frontend: Streamlit multi-page app. DevOps: Docker Compose, GitHub Actions CI/CD, LangSmith observability. Caching reduces LLM costs by 40%. Deployed on Render.

---

## ✅ Evaluation Criteria

Your project should demonstrate:

| Criteria | Weight | What We Look For |
|----------|--------|-----------------|
| **Functionality** | 30% | All must-have features working |
| **Code Quality** | 20% | Clean architecture, type hints, error handling |
| **RAG Quality** | 15% | Accurate answers with proper citations |
| **DevOps** | 15% | Docker, CI/CD, environment variables |
| **Documentation** | 10% | Clear README, API docs, architecture diagram |
| **Testing** | 10% | Unit tests, integration tests, 80%+ coverage |

---

<div align="center">

[← Phase 6: Deployment](../phase-6-deployment-observability/) · [🏠 Home](../README.md) · [Resources →](../resources/)

---

### 🎓 Congratulations!

If you've completed all 7 phases, you have the skills of an **AI Systems Engineer**.

Build. Ship. Iterate. 🚀

</div>
