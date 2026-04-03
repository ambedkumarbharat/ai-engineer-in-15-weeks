<div align="center">

# 🧩 Docker — Practice Problems

![Problems](https://img.shields.io/badge/Problems-12-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > [Docker](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Basic Dockerfile for a Python Script

**Problem:** Write a Dockerfile that packages a simple Python script that prints system info (Python version, OS, hostname, current time) and runs it.

**Real-world AI use case:** Understanding basic Dockerfiles is the first step to containerizing AI services. Every AI app deployment starts here.

**Concept being tested:** `FROM`, `WORKDIR`, `COPY`, `CMD`, building and running

**Hint:** Use `FROM python:3.11-slim`, `COPY script.py .`, `CMD ["python", "script.py"]`.

**Expected Output:**
```bash
$ docker build -t sysinfo .
$ docker run sysinfo
Python: 3.11.7
OS: Linux
Hostname: a1b2c3d4e5f6
Time: 2024-01-15 10:30:00
Container ID: a1b2c3d4e5f6
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### Dockerize a FastAPI App

**Problem:** Create a simple FastAPI application with 3 endpoints and Dockerize it with a proper Dockerfile including health check.

**Real-world AI use case:** This is the exact pattern for containerizing your AI API services.

**Concept being tested:** FastAPI + Docker, port mapping, health checks

**Hint:** Use `EXPOSE 8000`, `HEALTHCHECK`, and `uvicorn main:app --host 0.0.0.0 --port 8000`.

**Expected Output:**
```bash
$ docker build -t fastapi-app .
$ docker run -d -p 8000:8000 fastapi-app
$ curl http://localhost:8000/health
{"status": "healthy"}
$ curl http://localhost:8000/docs  # Swagger UI loads
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Docker Environment Variables

**Problem:** Create a Docker container that reads configuration from environment variables and uses sensible defaults.

**Real-world AI use case:** AI services need different configs for dev vs production (API keys, model names, temperatures). Environment variables handle this.

**Concept being tested:** `ENV`, `-e` flag, `.env` files, default values

**Hint:** Use `os.getenv("VAR", "default")` in Python and `-e VAR=value` when running the container.

**Expected Output:**
```bash
# Default config
$ docker run config-app
Model: gpt-3.5-turbo, Temperature: 0.7, Debug: false

# Custom config
$ docker run -e MODEL=gpt-4 -e TEMPERATURE=0.3 -e DEBUG=true config-app
Model: gpt-4, Temperature: 0.3, Debug: true
```

---

## Problem 04 🧩

**Difficulty:** 🟢 Easy

### .dockerignore File

**Problem:** Create a comprehensive `.dockerignore` file for an AI project and demonstrate the build size difference with and without it.

**Real-world AI use case:** Without `.dockerignore`, Docker copies your `venv/` (hundreds of MB), `.git/` (history), and model files into the build context, making builds slow and images bloated.

**Concept being tested:** `.dockerignore` patterns, build context optimization

**Hint:** Mirror your `.gitignore` plus add `venv/`, `.git/`, `*.pyc`, `__pycache__/`, `data/`, `models/`.

**Expected Output:**
```bash
# Without .dockerignore
$ docker build -t app-no-ignore .
Sending build context: 850 MB  # 😱

# With .dockerignore
$ docker build -t app-with-ignore .
Sending build context: 15 MB   # ✅
```

---

## Problem 05 🧩

**Difficulty:** 🟡 Medium

### Multi-Stage Build

**Problem:** Convert a basic Dockerfile into a multi-stage build that separates the build phase (installing dependencies) from the runtime phase (lean production image).

**Real-world AI use case:** Production AI images should be as small as possible for faster deployments and reduced attack surface. Multi-stage builds cut image size by 60-80%.

**Concept being tested:** Multi-stage builds, `AS`, `COPY --from`, layer optimization

**Hint:** Stage 1: install dependencies with `pip install --target=/install`. Stage 2: copy only the installed packages and app code.

**Expected Output:**
```bash
# Single-stage image
$ docker images | grep single
single-stage    latest    1.2GB

# Multi-stage image
$ docker images | grep multi
multi-stage     latest    250MB   # 80% smaller!
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### Docker Compose: FastAPI + PostgreSQL

**Problem:** Write a `docker-compose.yml` that sets up a FastAPI app connected to PostgreSQL with proper health checks, volumes, and dependency ordering.

**Real-world AI use case:** AI applications need databases. This is the foundational setup for any AI service that stores user data, conversation history, or evaluation results.

**Concept being tested:** Docker Compose, service dependencies, volumes, networking

**Hint:** Use `depends_on` with `condition: service_healthy`. PostgreSQL health check: `pg_isready -U user -d dbname`.

**Expected Output:**
```bash
$ docker compose up -d
Creating network "app_default"
Creating app_postgres_1 ... done
Creating app_api_1 ... done

$ docker compose ps
NAME              STATUS    PORTS
app_postgres_1    Up        5432/tcp
app_api_1         Up        0.0.0.0:8000->8000/tcp

$ curl http://localhost:8000/db-check
{"database": "connected", "tables": 3}
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### Docker Volumes for Data Persistence

**Problem:** Demonstrate named volumes, bind mounts, and tmpfs mounts. Show how data persists across container restarts with named volumes but is lost without them.

**Real-world AI use case:** Vector databases (ChromaDB), model caches, and PostgreSQL data must persist across container restarts. Understanding volume types is critical.

**Concept being tested:** Named volumes, bind mounts, data persistence lifecycle

**Hint:** Create a container that writes to `/data/counter.txt`, stop/remove it, start a new one, and check if the file persists with a named volume.

**Expected Output:**
```bash
# With named volume
$ docker run -v mydata:/data counter-app  # Writes counter=1
$ docker run -v mydata:/data counter-app  # Reads counter=1, writes counter=2
$ docker run -v mydata:/data counter-app  # Reads counter=2, writes counter=3

# Without volume
$ docker run counter-app  # Writes counter=1
$ docker run counter-app  # Writes counter=1 (data lost on each run!)
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Docker Networking Between Containers

**Problem:** Set up 3 containers on a custom Docker network where they can communicate by container name. Demonstrate service discovery.

**Real-world AI use case:** In an AI system, the API server calls the embedding service, which calls the model server. Docker networking enables this without hardcoding IPs.

**Concept being tested:** Docker networks, DNS resolution, inter-container communication

**Hint:** Create a network with `docker network create`, run containers with `--network`, and use container names as hostnames.

**Expected Output:**
```bash
$ docker network create ai-net
$ docker run -d --name embedding-svc --network ai-net embedding-app
$ docker run -d --name api-svc --network ai-net api-app

$ docker exec api-svc curl http://embedding-svc:8001/embed
{"embedding": [0.1, 0.2, 0.3], "source": "embedding-svc"}
```

---

## Problem 09 🧩

**Difficulty:** 🟡 Medium

### Container Resource Limits

**Problem:** Run containers with CPU and memory limits. Show what happens when an AI service exceeds its memory limit (OOM kill).

**Real-world AI use case:** Embedding models can consume gigabytes of RAM. Without memory limits, one service can crash the entire server by consuming all available memory.

**Concept being tested:** `--memory`, `--cpus`, resource limits, OOM kills

**Hint:** Use `docker run --memory=256m --cpus=1.0`. Create a Python script that intentionally consumes memory to trigger the OOM kill.

**Expected Output:**
```bash
# Running with limits
$ docker run --memory=256m --cpus=1.0 --name limited-app my-app
$ docker stats limited-app
CONTAINER    CPU    MEM USAGE / LIMIT
limited-app  15%    180MiB / 256MiB

# OOM kill demonstration
$ docker run --memory=128m memory-hog
# Container exits with code 137 (OOM killed)
$ docker inspect memory-hog --format='{{.State.OOMKilled}}'
true
```

---

## Problem 10 🧩

**Difficulty:** 🔴 Hard

### Full AI Stack with Docker Compose

**Problem:** Create a complete `docker-compose.yml` with 5 services: FastAPI, PostgreSQL, Redis, ChromaDB, and an embedding worker. Include health checks, volumes, networking, and environment configuration.

**Real-world AI use case:** This is the exact stack for a production RAG system — API gateway, relational DB, cache, vector DB, and background processing.

**Concept being tested:** Complex Docker Compose, multi-service orchestration, health checks, dependencies

**Hint:** Define a shared network, use `depends_on` with conditions, and separate `.env` files for sensitive config.

**Expected Output:**
```bash
$ docker compose up -d
[+] Running 5/5
 ✔ Container ai-stack-postgres   Healthy
 ✔ Container ai-stack-redis      Healthy  
 ✔ Container ai-stack-chromadb   Healthy
 ✔ Container ai-stack-worker     Started
 ✔ Container ai-stack-api        Healthy

$ curl http://localhost:8000/health
{
  "status": "healthy",
  "services": {
    "postgres": "connected",
    "redis": "connected",
    "chromadb": "connected",
    "worker": "running"
  }
}
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### Docker Image Optimization Challenge

**Problem:** Take a bloated 2GB AI service image and optimize it to under 300MB using multi-stage builds, slim base images, layer optimization, and dependency trimming.

**Real-world AI use case:** In CI/CD pipelines, every MB counts. Smaller images mean faster deploys, less storage cost, and reduced security attack surface.

**Concept being tested:** Image size optimization, layer caching, multi-stage builds, dependency analysis

**Hint:** Use `dive` (docker image analysis tool) to find large layers. Replace `python:3.11` with `python:3.11-slim`. Remove build tools after compilation.

**Expected Output:**
```bash
# Before optimization
$ docker images ai-app
REPOSITORY  TAG    SIZE
ai-app      v1     2.1GB

# After optimization  
$ docker images ai-app
REPOSITORY  TAG          SIZE
ai-app      v2-optimized 280MB

# Breakdown:
# - slim base image:     -800MB
# - multi-stage build:   -600MB
# - removed build tools: -200MB
# - pip --no-cache-dir:  -150MB
# - .dockerignore:       -70MB
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### CI/CD Docker Pipeline

**Problem:** Write a complete CI/CD pipeline (using a shell script simulating GitHub Actions) that: builds a Docker image, runs tests inside the container, tags with git commit hash, pushes to a registry, and deploys with zero downtime.

**Real-world AI use case:** Every AI company has automated deployment pipelines. Code changes are automatically built, tested, and deployed without human intervention.

**Concept being tested:** CI/CD concepts, Docker build/push, tagging strategy, blue-green deployment

**Hint:** Use `docker build -t app:$(git rev-parse --short HEAD)` for commit-based tagging. Implement blue-green deploy by running the new container before stopping the old one.

**Expected Output:**
```bash
$ bash cicd.sh
═══════════════════════════════════
  CI/CD Pipeline — ai-service
═══════════════════════════════════
  
📦 Build Phase:
  ✅ Docker image built: ai-service:abc1234
  ✅ Image size: 285MB
  
🧪 Test Phase:
  ✅ Unit tests: 24/24 passed
  ✅ Integration tests: 8/8 passed
  ✅ Health check: passed
  
🏷️ Tag Phase:
  ✅ Tagged: ai-service:abc1234
  ✅ Tagged: ai-service:latest
  
🚀 Deploy Phase:
  ✅ New container started (port 8001)
  ✅ Health check passed on new container
  ✅ Traffic switched to new container
  ✅ Old container stopped
  
✅ Deployment complete! abc1234 is live.
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 1](../README.md) · [🏠 Home](../../README.md)

</div>
