<div align="center">

# 🔨 Mini Project: Multi-Database User Profile System

### Connect PostgreSQL, Redis, MongoDB & ChromaDB in a single FastAPI application

![Tech](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Tech](https://img.shields.io/badge/PostgreSQL-316192?style=flat-square&logo=postgresql&logoColor=white)
![Tech](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Tech](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white)
![Tech](https://img.shields.io/badge/ChromaDB-FF6F61?style=flat-square)
![Tech](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > **Mini Project**

---

## 📋 Project Overview

Build a **FastAPI application** that uses **four different databases**, each for its ideal use case:
- **PostgreSQL** → Store structured user profiles and account data
- **Redis** → Cache frequent queries + manage session tokens
- **MongoDB** → Store unstructured user activity logs
- **ChromaDB** → Store and search user preferences semantically

This teaches the critical skill of **choosing the right database for the right job**.

---

## 🧠 Concepts Used from This Phase

| Concept | Where It's Used |
|---------|----------------|
| **PostgreSQL** | User profiles, account tiers, token quotas |
| **Redis** | Profile caching (60s TTL), session tokens, rate limiting |
| **MongoDB** | Activity logs (searches, chats, page views) |
| **ChromaDB** | Semantic search over user preferences/interests |
| **SQLAlchemy** | ORM for PostgreSQL models |
| **Docker Compose** | All 4 databases + API in containers |

---

## 🏗️ File Structure

```
multi-db-profile-system/
├── src/
│   ├── __init__.py
│   ├── main.py                  # FastAPI app
│   ├── config.py                # Database connection configs
│   ├── routes/
│   │   ├── profiles.py          # PostgreSQL: user CRUD
│   │   ├── sessions.py          # Redis: session management
│   │   ├── activity.py          # MongoDB: activity logging
│   │   └── preferences.py      # ChromaDB: semantic preferences
│   ├── models/
│   │   ├── sql_models.py        # SQLAlchemy models
│   │   └── schemas.py           # Pydantic models
│   └── services/
│       ├── postgres_service.py
│       ├── redis_service.py
│       ├── mongo_service.py
│       └── chroma_service.py
├── docker-compose.yml           # All services
├── Dockerfile
├── requirements.txt
├── .env.example
└── README.md
```

---

## 🔧 Step-by-Step Build Instructions

### Step 1: Set Up Docker Compose

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    build: .
    ports: ["8000:8000"]
    env_file: .env
    depends_on:
      postgres: { condition: service_healthy }
      redis: { condition: service_healthy }
      mongo: { condition: service_started }
    volumes: ["./src:/app/src"]

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: profiles
    ports: ["5432:5432"]
    volumes: ["pg_data:/var/lib/postgresql/data"]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5

  mongo:
    image: mongo:7
    ports: ["27017:27017"]
    volumes: ["mongo_data:/data/db"]

  chromadb:
    image: chromadb/chroma:latest
    ports: ["8001:8000"]
    volumes: ["chroma_data:/chroma/chroma"]

volumes:
  pg_data:
  mongo_data:
  chroma_data:
```

### Step 2: Build Each Service Layer

**PostgreSQL Service** — User profiles with SQLAlchemy:
```python
# src/services/postgres_service.py
from sqlalchemy.orm import Session
from src.models.sql_models import User

class UserService:
    def __init__(self, db: Session):
        self.db = db
    
    def create_user(self, email: str, name: str, bio: str = "") -> User:
        user = User(email=email, name=name, bio=bio)
        self.db.add(user)
        self.db.commit()
        self.db.refresh(user)
        return user
    
    def get_user(self, user_id: int) -> User | None:
        return self.db.query(User).filter(User.id == user_id).first()
```

**Redis Service** — Caching + sessions:
```python
# src/services/redis_service.py
import redis
import json

class CacheService:
    def __init__(self, redis_url: str):
        self.redis = redis.from_url(redis_url, decode_responses=True)
    
    def cache_profile(self, user_id: int, profile: dict, ttl: int = 60):
        self.redis.setex(f"profile:{user_id}", ttl, json.dumps(profile))
    
    def get_cached_profile(self, user_id: int) -> dict | None:
        data = self.redis.get(f"profile:{user_id}")
        return json.loads(data) if data else None
```

**MongoDB Service** — Activity logs:
```python
# src/services/mongo_service.py
from pymongo import MongoClient
from datetime import datetime

class ActivityService:
    def __init__(self, mongo_url: str):
        self.client = MongoClient(mongo_url)
        self.db = self.client["profiles"]
        self.logs = self.db["activity_logs"]
    
    def log_activity(self, user_id: int, action: str, details: dict = None):
        self.logs.insert_one({
            "user_id": user_id, "action": action,
            "details": details or {}, "timestamp": datetime.now(),
        })
```

**ChromaDB Service** — Semantic preferences:
```python
# src/services/chroma_service.py
import chromadb

class PreferenceService:
    def __init__(self, chroma_url: str = "http://localhost:8001"):
        self.client = chromadb.HttpClient(host="chromadb", port=8000)
        self.collection = self.client.get_or_create_collection("user_preferences")
    
    def add_preference(self, user_id: int, preference: str):
        self.collection.add(
            documents=[preference],
            metadatas=[{"user_id": user_id}],
            ids=[f"pref_{user_id}_{hash(preference) % 10000}"],
        )
    
    def find_similar_users(self, preference: str, n: int = 5) -> list:
        results = self.collection.query(query_texts=[preference], n_results=n)
        return results
```

### Step 3: Create API Endpoints

```python
# src/main.py
from fastapi import FastAPI
from src.routes import profiles, sessions, activity, preferences

app = FastAPI(title="Multi-Database Profile System")
app.include_router(profiles.router, prefix="/api/v1")
app.include_router(sessions.router, prefix="/api/v1")
app.include_router(activity.router, prefix="/api/v1")
app.include_router(preferences.router, prefix="/api/v1")

@app.get("/health")
async def health():
    return {"status": "healthy", "databases": ["postgres", "redis", "mongo", "chroma"]}
```

---

## ▶️ How to Run

```bash
docker compose up --build -d
curl http://localhost:8000/health
curl http://localhost:8000/docs  # Swagger UI
```

---

## 🚀 How to Extend It

1. Add JWT authentication with Redis session storage
2. Add real-time notifications via Redis Pub/Sub
3. Add recommendation engine using ChromaDB similarity
4. Add usage analytics dashboard from MongoDB aggregations
5. Add PostgreSQL full-text search as fallback to vector search

---

## 📝 What to Add to Your Resume

> **Multi-Database User Profile System** — Architected a FastAPI microservice integrating 4 database technologies: PostgreSQL (user profiles, SQLAlchemy ORM), Redis (caching with 60s TTL, session management), MongoDB (unstructured activity logging with aggregation pipelines), and ChromaDB (semantic preference search using embeddings). Dockerized with Docker Compose for local development. Demonstrates understanding of database selection criteria for AI system architectures.

---

<div align="center">

[← Topic 4: Vector Databases](../topic-4-vector-databases/) · [🏠 Home](../../README.md) · [📋 Phase 2](../README.md)

**[Phase 3: LLM Fundamentals →](../../phase-3-llm-fundamentals/)**

</div>
