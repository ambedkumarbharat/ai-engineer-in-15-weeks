<div align="center">

# 🐳 Topic 5: Docker

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-1_Foundations-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > **Docker**

---

## 🎯 Why This Matters for AI Systems Engineering

Docker is **non-negotiable** for AI systems. Every AI service — your RAG pipeline, agent orchestrator, embedding server — runs in containers. Docker ensures:
- **Reproducibility** → "It works on my machine" is solved forever
- **Isolation** → Each service (API, database, model server) runs independently
- **Deployment** → Deploy the exact same container to dev, staging, and production
- **Scaling** → Run multiple instances behind a load balancer
- **Dependencies** → Package Python, CUDA, system libraries together

---

## 📚 Core Concepts

### 1. Dockerfile for Python AI Applications

```dockerfile
# Dockerfile
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# Install system dependencies (some AI libraries need these)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements first (Docker layer caching!)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# Run the application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Build and run:**
```bash
docker build -t ai-api:latest .
docker run -d -p 8000:8000 --name ai-api ai-api:latest
curl http://localhost:8000/health
```

### 2. Multi-Stage Build (Smaller Images)

```dockerfile
# Stage 1: Build
FROM python:3.11-slim AS builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --target=/install -r requirements.txt

# Stage 2: Runtime (smaller image)
FROM python:3.11-slim AS runtime

WORKDIR /app

# Copy installed packages from builder
COPY --from=builder /install /usr/local/lib/python3.11/site-packages/

# Copy application code
COPY . .

# Non-root user (security best practice)
RUN adduser --disabled-password --no-create-home appuser
USER appuser

EXPOSE 8000
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 3. Docker Compose for Multi-Service AI Systems

```yaml
# docker-compose.yml
version: '3.8'

services:
  # FastAPI application
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:password@postgres:5432/aidb
      - REDIS_URL=redis://redis:6379
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    volumes:
      - ./src:/app/src  # Hot reload during development
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  # PostgreSQL
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: aidb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d aidb"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis (caching + rate limiting)
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # ChromaDB (vector database)
  chromadb:
    image: chromadb/chroma:latest
    ports:
      - "8001:8000"
    volumes:
      - chroma_data:/chroma/chroma

volumes:
  postgres_data:
  redis_data:
  chroma_data:
```

**Commands:**
```bash
# Start all services
docker compose up -d

# View logs
docker compose logs -f api

# Stop everything
docker compose down

# Remove volumes too (clean slate)
docker compose down -v
```

### 4. Essential Docker Commands

```bash
# Image management
docker build -t my-app:v1.0 .          # Build image with tag
docker images                            # List images
docker rmi my-app:v1.0                  # Remove image
docker image prune -a                    # Remove unused images

# Container management
docker run -d -p 8000:8000 --name api my-app   # Run container
docker ps                                # List running containers
docker ps -a                             # List all containers
docker stop api                          # Stop container
docker rm api                            # Remove container
docker logs -f api                       # Follow logs
docker exec -it api bash                 # Shell into container
docker inspect api                       # Container details

# Resource management
docker stats                             # Live resource usage
docker system df                         # Disk usage
docker system prune -a --volumes         # Clean everything (⚠️ careful!)

# Networking
docker network ls                        # List networks
docker network create ai-network         # Create network
docker run --network ai-network my-app   # Run on specific network
```

### 5. Environment Variables & Secrets

```yaml
# docker-compose.yml — Using .env file
services:
  api:
    build: .
    env_file:
      - .env                # Load from .env file
    environment:
      - DEBUG=false          # Override specific vars
      - LOG_LEVEL=info
```

```bash
# .env file
OPENAI_API_KEY=sk-your-key-here
ANTHROPIC_API_KEY=sk-ant-your-key
DATABASE_URL=postgresql://user:pass@postgres:5432/db
REDIS_URL=redis://redis:6379
```

### 6. Debugging Docker Containers

```bash
# Check why a container failed
docker logs ai-api --tail 50            # Last 50 lines
docker inspect ai-api --format='{{.State.ExitCode}}'  # Exit code

# Interactive debugging
docker run -it --entrypoint bash my-app  # Start with bash instead of app
docker exec -it ai-api python -c "import langchain; print(langchain.__version__)"

# Check container's view of environment
docker exec ai-api env | grep API_KEY

# Resource issues
docker stats ai-api                      # CPU/memory usage
docker inspect ai-api --format='{{.HostConfig.Memory}}'  # Memory limit

# Network debugging
docker exec ai-api curl http://postgres:5432  # Test internal networking
docker network inspect bridge            # Network details
```

### 7. Docker for Development vs Production

```dockerfile
# Dockerfile.dev — Development (hot reload)
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
# Don't copy code — mount as volume for hot reload
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
```

```dockerfile
# Dockerfile.prod — Production (optimized)
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --target=/install -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /install /usr/local/lib/python3.11/site-packages/
COPY . .
RUN adduser --disabled-password appuser && chown -R appuser /app
USER appuser
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Docker in 100 Seconds** | 📹 YouTube | [Fireship - Docker](https://www.youtube.com/watch?v=Gjnup-PuquQ) |
| **Docker Full Course (3h)** | 📹 YouTube | [TechWorld with Nana](https://www.youtube.com/watch?v=3c-iBn73dDE) |
| **Docker Compose Tutorial** | 📹 YouTube | [NetworkChuck - Docker Compose](https://www.youtube.com/watch?v=DM65_JyGxCo) |
| **Docker Official Docs** | 📄 Docs | [docs.docker.com/get-started](https://docs.docker.com/get-started/) |
| **Docker Best Practices** | 📄 Docs | [docs.docker.com/develop/develop-images/dockerfile_best-practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) |
| **Play with Docker** | 🎮 Interactive | [labs.play-with-docker.com](https://labs.play-with-docker.com/) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Not using `.dockerignore` | Builds include venv, .git, etc. | Create `.dockerignore` like `.gitignore` |
| Copying code before requirements | Rebuilds dependencies on every code change | `COPY requirements.txt` first, then `COPY .` |
| Running as root | Security vulnerability | Add `USER appuser` in Dockerfile |
| Not using health checks | Container appears "running" even if app crashed | Add `HEALTHCHECK` instruction |
| Using `latest` tag in production | Can't reproduce exact builds | Always tag images with version/commit |
| Not cleaning apt cache | Bloated images | `rm -rf /var/lib/apt/lists/*` after `apt-get install` |
| Hardcoding secrets in Dockerfile | Secrets in image layer history | Use env vars or Docker secrets |

---

## 📋 Quick Reference Cheatsheet

| Command | What It Does | When to Use |
|---------|-------------|------------|
| `docker build -t name .` | Build image | After code changes |
| `docker run -d -p 8000:8000 name` | Run container | Starting a service |
| `docker compose up -d` | Start all services | Multi-service dev |
| `docker compose down` | Stop all services | Shutting down |
| `docker ps` | List containers | Check what's running |
| `docker logs -f name` | Follow logs | Debugging |
| `docker exec -it name bash` | Shell into container | Debugging |
| `docker stats` | Resource usage | Performance check |
| `docker system prune -a` | Clean everything | Free disk space |
| `docker compose logs -f` | All service logs | Multi-service debugging |

---

> 💡 **Pro Tip:** Use Docker Compose profiles to separate dev and test services. Add `profiles: ["test"]` to services you only need during testing, then run `docker compose --profile test up`. Also, use `.dockerignore` to exclude `.git`, `venv`, `__pycache__`, and `.env` from the build context — it dramatically speeds up builds.

---

<div align="center">

[← Topic 4: Linux & Terminal](../topic-4-linux-terminal/) · [🏠 Home](../../README.md) · [📋 Phase 1](../README.md)

**[▶ Problems](problems.md)** · [🔨 Mini Project →](../mini-project/)

</div>
