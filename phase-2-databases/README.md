<div align="center">

# 🗄️ Phase 2 — Databases

### Master the data layer that powers every AI system

![Duration](https://img.shields.io/badge/Duration-2_Weeks-blue)
![Topics](https://img.shields.io/badge/Topics-4-green)
![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Mini Project](https://img.shields.io/badge/Mini_Project-Multi--Database_User_Profile_System-orange)

</div>

---

> 🏠 [Home](../README.md) > **Phase 2 — Databases**

---

## 🎯 Why This Phase?

Every AI system needs persistent storage. You need **different databases for different jobs**:
- **PostgreSQL** → Structured data (users, configs, evaluation results)
- **Redis** → Caching (LLM responses, embeddings), rate limiting, sessions
- **MongoDB** → Unstructured data (conversation logs, document metadata)
- **Vector Databases** → Similarity search (embeddings, RAG retrieval)

Knowing which database to use and when is what separates a junior from a senior AI engineer.

---

## 📋 Topics Checklist

- [ ] [📘 Topic 1: PostgreSQL](topic-1-postgresql/)
- [ ] [📘 Topic 2: Redis](topic-2-redis/)
- [ ] [📘 Topic 3: MongoDB](topic-3-mongodb/)
- [ ] [📘 Topic 4: Vector Databases](topic-4-vector-databases/)
- [ ] [🔨 Mini Project: Multi-Database User Profile System](mini-project/)

---

## 📊 Topic Overview

| # | Topic | Duration | Key Skills | Difficulty |
|---|-------|----------|-----------|------------|
| 1 | [PostgreSQL](topic-1-postgresql/) | 3 days | Schema design, queries, indexing, SQLAlchemy | 🟢 Beginner |
| 2 | [Redis](topic-2-redis/) | 3 days | Caching, pub/sub, rate limiting, sessions | 🟢 Beginner |
| 3 | [MongoDB](topic-3-mongodb/) | 3 days | CRUD, aggregation, PyMongo, Motor | 🟡 Intermediate |
| 4 | [Vector Databases](topic-4-vector-databases/) | 3 days | Embeddings, ChromaDB, Pinecone, similarity search | 🟡 Intermediate |

---

## 🔨 Mini Project Preview

<table>
<tr>
<td>

### 📦 Multi-Database User Profile System

Build a **FastAPI application** that connects to **all 4 database types** — PostgreSQL for user profiles, Redis for caching and sessions, MongoDB for activity logs, and ChromaDB for semantic preference search.

**Tech Stack:** Python · FastAPI · PostgreSQL · Redis · MongoDB · ChromaDB · Docker

**[→ View Project Details](mini-project/)**

</td>
</tr>
</table>

---

## 🧭 Learning Path

```
Week 1: PostgreSQL (3 days) → Redis (3 days)
         └── Practice: Build user table, implement caching

Week 2: MongoDB (3 days) → Vector DBs (2 days) → Mini Project (2 days)
         └── Build: Multi-Database User Profile System
```

---

## ✅ Phase Completion Criteria

Before moving to Phase 3, make sure you can:

- [ ] Design a PostgreSQL schema with proper indexing for an AI application
- [ ] Implement caching and rate limiting with Redis
- [ ] Store and query unstructured data with MongoDB
- [ ] Perform similarity search with a vector database
- [ ] Explain **when** to use each database type
- [ ] Complete the mini project connecting all 4 databases

---

<div align="center">

[← Phase 1: Foundations](../phase-1-foundations/) · [🏠 Home](../README.md) · [Phase 3: LLM Fundamentals →](../phase-3-llm-fundamentals/)

</div>
