<div align="center">

# 🔨 Mini Project: Deploy RAG Bot to Production

### Take your Knowledge Base QA Bot live with monitoring and CI/CD

![Tech](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Tech](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Tech](https://img.shields.io/badge/Render-46E3B7?style=flat-square&logo=render&logoColor=white)
![Tech](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![Tech](https://img.shields.io/badge/LangSmith-1C3C3C?style=flat-square)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > **Mini Project**

---

## 📋 Project Overview

Take the Phase 4 Knowledge Base QA Bot and make it **production-ready**: add a FastAPI backend, LangSmith tracing, Redis caching, rate limiting, deploy to Render (free tier), and set up GitHub Actions CI/CD.

---

## 🧠 Concepts Used

| Concept | Where It's Used |
|---------|----------------|
| **FastAPI Production** | CORS, auth, logging, health checks |
| **Cloud Deployment** | Render free tier with auto-deploy |
| **LangSmith** | LLM call tracing and debugging |
| **Caching** | Redis cache for identical queries |
| **Rate Limiting** | Per-user request limits |
| **CI/CD** | GitHub Actions test + deploy |

---

## 🏗️ Architecture

```
┌─────────────┐     ┌──────────────┐     ┌──────────┐
│   Client     │ ──→ │  FastAPI API  │ ──→ │  Ollama  │
│  (Browser)   │     │  (Render)    │     │  (LLM)   │
└─────────────┘     │              │     └──────────┘
                    │  ┌─────────┐ │     ┌──────────┐
                    │  │  Redis  │ │ ──→ │ ChromaDB │
                    │  │ (Cache) │ │     │ (Vectors)│
                    │  └─────────┘ │     └──────────┘
                    └──────────────┘
                           ↑
                    ┌──────────────┐
                    │   LangSmith  │
                    │  (Tracing)   │
                    └──────────────┘
```

---

## 🔧 Key Implementation

### Production FastAPI App

```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.middleware.cors import CORSMiddleware
import os

app = FastAPI(title="RAG QA Bot", version="1.0.0")

app.add_middleware(CORSMiddleware,
    allow_origins=[os.getenv("FRONTEND_URL", "http://localhost:3000")],
    allow_methods=["*"], allow_headers=["*"])

@app.get("/health")
async def health():
    return {"status": "healthy", "version": "1.0.0"}

@app.post("/api/v1/ask")
async def ask_question(question: str):
    # 1. Check cache
    cached = cache.get(question)
    if cached: return cached
    
    # 2. Query RAG pipeline (with LangSmith tracing)
    result = rag_pipeline.ask(question)
    
    # 3. Cache result
    cache.set(question, result)
    return result
```

### GitHub Actions Workflow

```yaml
name: Deploy RAG Bot
on:
  push:
    branches: [main]
jobs:
  test-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.11" }
      - run: pip install -r requirements.txt
      - run: pytest tests/ -v
      - run: curl -X POST "${{ secrets.RENDER_DEPLOY_HOOK }}"
        if: success()
```

---

## ▶️ How to Deploy

```bash
# 1. Push to GitHub
git push origin main

# 2. Connect to Render
# - New Web Service → Connect GitHub repo
# - Set environment variables (OPENAI_API_KEY, REDIS_URL)
# - Deploy!

# 3. Set up LangSmith
# - Set LANGCHAIN_API_KEY in Render env vars
# - View traces at smith.langchain.com
```

---

## 📝 What to Add to Your Resume

> **Production RAG QA Bot** — Deployed a production-ready RAG bot with FastAPI backend, LangSmith observability, Redis response caching (30% cost reduction), per-user rate limiting, and GitHub Actions CI/CD pipeline. Deployed to Render with Docker, auto-deploy on push to main. Includes health monitoring, structured logging, and CORS configuration.

---

<div align="center">

[← Topic 6](../topic-6-rate-limiting-caching/) · [🏠 Home](../../README.md)

**[Phase 7: Major Project →](../../phase-7-major-project/)**

</div>
