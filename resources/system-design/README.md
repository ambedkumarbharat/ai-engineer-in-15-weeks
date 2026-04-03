<div align="center">

# 🏗️ System Design

### Architecture Patterns for AI Systems

</div>

---

> 🏠 [Home](../../README.md) > [Resources](../README.md) > **System Design**

---

## 1. RAG System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    RAG System Architecture                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────┐     INGESTION PIPELINE                        │
│  │  Sources  │─→ Load → Clean → Chunk → Embed → Store      │
│  │ PDF/Web/DB│                                     ↓        │
│  └──────────┘                              ┌────────────┐   │
│                                            │ Vector DB  │   │
│  ┌──────────┐     QUERY PIPELINE           │ (ChromaDB) │   │
│  │  User    │─→ Embed Query ──→ Retrieve ──┘            │   │
│  │  Query   │        │              │                    │   │
│  └──────────┘        │         Top-K Chunks              │   │
│                      │              │                    │   │
│                      │    ┌─────────▼─────────┐          │   │
│                      │    │  Context Builder   │          │   │
│                      │    │  + Prompt Template │          │   │
│                      │    └─────────┬─────────┘          │   │
│                      │              │                    │   │
│                      │    ┌─────────▼─────────┐          │   │
│                      │    │    LLM (GPT-4)    │          │   │
│                      │    │   + Citations     │          │   │
│                      │    └─────────┬─────────┘          │   │
│                      │              │                    │   │
│                      │    ┌─────────▼─────────┐          │   │
│  ┌──────────┐        │    │  Response + Refs  │          │   │
│  │  Answer  │◀───────┘────│                   │          │   │
│  └──────────┘              └───────────────────┘          │   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Key Design Decisions

| Decision | Options | Recommendation |
|----------|---------|---------------|
| Chunk size | 200-2000 chars | 500 chars + 50 overlap for general use |
| Embedding model | MiniLM / OpenAI | MiniLM for dev, OpenAI for production |
| Vector DB | ChromaDB / Pinecone | ChromaDB < 1M docs, Pinecone > 1M |
| Top-K retrieval | 3-20 chunks | 5 is a good default |
| Search type | Vector / Hybrid | Hybrid (BM25 + vector + RRF) for best quality |
| LLM | GPT-3.5 / GPT-4 | Route: 3.5 for simple, 4 for complex |

---

## 2. AI Agent Architecture

```
┌─────────────────────────────────────────────────┐
│                 AI Agent System                   │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌────────────────────────────────────────────┐  │
│  │              ORCHESTRATOR                  │  │
│  │           (LangGraph / ReAct)              │  │
│  │                                            │  │
│  │  State: {messages, tools_used, plan}       │  │
│  │                                            │  │
│  │  ┌──────┐    ┌──────┐    ┌──────┐         │  │
│  │  │Think │ →  │ Act  │ →  │ Obs  │ → Loop  │  │
│  │  └──────┘    └──────┘    └──────┘         │  │
│  └────────────────┬───────────────────────────┘  │
│                   │                              │
│  ┌────────────────▼───────────────────────────┐  │
│  │              TOOL REGISTRY                 │  │
│  │                                            │  │
│  │  🔍 Web Search    📊 Calculator            │  │
│  │  📄 File I/O      🗄️ Database Query        │  │
│  │  🌐 API Call      📧 Email Send            │  │
│  │  🧮 Code Exec     📋 RAG Search            │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
│  ┌────────────────────────────────────────────┐  │
│  │              MEMORY SYSTEM                 │  │
│  │                                            │  │
│  │  Short-term: Conversation buffer (list)    │  │
│  │  Long-term: Vector DB (preferences)        │  │
│  │  Episodic: PostgreSQL (past tasks)         │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
│  ┌────────────────────────────────────────────┐  │
│  │              SAFETY LAYER                  │  │
│  │                                            │  │
│  │  Max steps: 10 │ Token budget: 50K         │  │
│  │  Timeout: 120s │ Human approval: on        │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
└─────────────────────────────────────────────────┘
```

---

## 3. Production AI API Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                 Production AI API (1000 QPS)                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Client → CDN → Load Balancer → API Servers (4x)           │
│                                     │                       │
│                    ┌────────────────┼───────────────────┐    │
│                    │                │                   │    │
│              ┌─────▼─────┐  ┌──────▼──────┐  ┌────────▼─┐  │
│              │  Redis    │  │  PostgreSQL │  │  ChromaDB│  │
│              │  Cache    │  │  (Users,    │  │  (Vectors│  │
│              │  + Rate   │  │   Billing)  │  │   Search)│  │
│              │  Limit    │  │             │  │          │  │
│              └───────────┘  └─────────────┘  └──────────┘  │
│                                                             │
│              ┌───────────────────────────────────────────┐   │
│              │           LLM Provider Layer              │   │
│              │                                           │   │
│              │  Model Router:                            │   │
│              │  Simple → GPT-3.5-turbo ($0.0005/1K)      │   │
│              │  Complex → GPT-4 ($0.03/1K)               │   │
│              │  Fallback → Ollama (free, local)           │   │
│              │                                           │   │
│              │  Features: retry, rate limit, streaming    │   │
│              └───────────────────────────────────────────┘   │
│                                                             │
│              ┌───────────────────────────────────────────┐   │
│              │           Observability                    │   │
│              │                                           │   │
│              │  LangSmith: LLM traces, prompt debugging  │   │
│              │  Prometheus: API metrics, latency          │   │
│              │  Structured Logs: JSON to CloudWatch/ELK  │   │
│              └───────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Scaling Strategies

| Strategy | What | Impact |
|----------|------|--------|
| **Horizontal scaling** | Add more API server instances | Handle more concurrent requests |
| **Redis caching** | Cache LLM responses | 30-70% fewer LLM calls |
| **Model routing** | Cheap model for simple queries | 40-80% cost reduction |
| **Async processing** | Non-blocking I/O | 5x more throughput per server |
| **Rate limiting** | Per-user request limits | Prevent abuse, control costs |
| **Queue-based processing** | Celery + Redis for heavy tasks | Decouple request from processing |

---

## 4. Multi-Agent System Architecture

```
┌─────────────────────────────────────────────────┐
│              Multi-Agent System (CrewAI)          │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌─────────────────────────────────────────┐     │
│  │            SUPERVISOR AGENT             │     │
│  │ Decomposes tasks, delegates, merges     │     │
│  └──────────┬──────────┬──────────────────┘     │
│             │          │                        │
│   ┌─────────▼────┐ ┌───▼──────────┐ ┌────────┐ │
│   │ RESEARCHER   │ │   ANALYST    │ │ WRITER │ │
│   │ Tools: Web   │ │ Tools: Calc, │ │ Tools: │ │
│   │ Search, RAG  │ │ DB Query     │ │ File   │ │
│   └──────────────┘ └──────────────┘ └────────┘ │
│                                                  │
│  Communication: Sequential or Hierarchical       │
│  Memory: Shared context between agents           │
│  Safety: Budget limits, approval gates           │
│                                                  │
└─────────────────────────────────────────────────┘
```

### When to Use Multi-Agent vs Single Agent

| Scenario | Recommendation |
|----------|---------------|
| Simple Q&A | Single agent or just RAG |
| Research + Write report | Multi-agent (researcher + writer) |
| Code generation + review | Multi-agent (coder + reviewer) |
| Customer support | Single agent with tools |
| Complex data pipeline | Multi-agent with supervisor |

---

## 5. Database Selection Guide

```
                    What kind of data?
                         │
              ┌──────────┼──────────┐
              │          │          │
         Structured  Semi-struct  Vectors
              │          │          │
         PostgreSQL   MongoDB    ChromaDB
              │          │       Pinecone
              │          │          │
         + Relations  + Flexible  + Similarity
         + ACID       + Nested    + Embeddings
         + SQL        + Schema    + ANN Search
         + Indexes    + Aggreg.   + Metadata
              │          │          │
         Users/Billing  Agent     RAG/Search
         Model Configs  Logs      Recommend.
         Evaluations    Activity  Clustering
              
         + Redis for: Caching, Rate Limiting, Sessions, Pub/Sub
```

---

<div align="center">

[📚 Resources](../README.md) · [🏠 Home](../../README.md)

</div>
