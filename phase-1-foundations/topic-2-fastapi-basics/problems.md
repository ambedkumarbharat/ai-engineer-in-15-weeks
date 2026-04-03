<div align="center">

# 🧩 FastAPI Basics — Practice Problems

![Problems](https://img.shields.io/badge/Problems-12-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > [FastAPI Basics](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Health Check with System Info

**Problem:** Create a FastAPI app with a `/health` endpoint that returns system information including Python version, OS, uptime, and memory usage.

**Real-world AI use case:** Every production AI service needs a health check endpoint for load balancers and monitoring systems to verify the service is running correctly.

**Concept being tested:** Basic GET endpoints, response models

**Hint:** Use `platform.python_version()`, `platform.system()`, and `time.time()` to calculate uptime from a startup timestamp.

**Expected Output:**
```json
{
  "status": "healthy",
  "python_version": "3.10.12",
  "os": "Linux",
  "uptime_seconds": 3625.4,
  "timestamp": "2024-01-15T10:30:00"
}
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### Text Statistics Endpoint

**Problem:** Build a POST endpoint `/analyze` that receives text and returns comprehensive statistics.

**Real-world AI use case:** Before sending text to an LLM, you need to know its length (for token estimation), language, and structure to choose the right model and parameters.

**Concept being tested:** POST endpoints, Pydantic request/response models

**Hint:** Use a `TextInput` model with the text field and a `TextStats` response model with all statistics.

**Expected Output:**
```json
{
  "word_count": 42,
  "sentence_count": 3,
  "character_count": 256,
  "avg_word_length": 5.1,
  "estimated_tokens": 56,
  "top_words": [["the", 5], ["is", 3], ["ai", 3]]
}
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Model Selection Endpoint with Enums

**Problem:** Create an endpoint that recommends an LLM model based on task type and budget constraints using Enum path parameters.

**Real-world AI use case:** AI systems often need model routing — choosing the cheapest model that can handle a given task quality requirement.

**Concept being tested:** Path parameters, Enums, query parameters

**Hint:** Define `TaskType` enum (summarization, code_generation, chat, analysis) and `Budget` enum (low, medium, high).

**Expected Output:**
```json
{
  "task": "code_generation",
  "budget": "medium",
  "recommended_model": "gpt-4-turbo",
  "estimated_cost_per_1k_tokens": 0.01,
  "alternatives": ["claude-3-sonnet", "deepseek-coder"]
}
```

---

## Problem 04 🧩

**Difficulty:** 🟢 Easy

### CRUD for Prompt Templates

**Problem:** Build a complete CRUD API for managing prompt templates stored in-memory (dict).

**Real-world AI use case:** AI applications manage libraries of prompt templates that are versioned, categorized, and reused across different features.

**Concept being tested:** GET, POST, PUT, DELETE endpoints, in-memory storage, status codes

**Hint:** Store templates in a dict by ID. Use `HTTPException(status_code=404)` for missing templates.

**Expected Output:**
```bash
POST /templates → {"id": "tmpl_001", "name": "summarizer", "template": "Summarize: {text}", "created_at": "..."}
GET /templates → [list of all templates]
GET /templates/tmpl_001 → single template
PUT /templates/tmpl_001 → updated template
DELETE /templates/tmpl_001 → {"deleted": true}
```

---

## Problem 05 🧩

**Difficulty:** 🟡 Medium

### File Upload with Processing Pipeline

**Problem:** Create an endpoint that accepts file uploads (`.txt`, `.md`, `.csv`), validates them, processes them, and returns structured results.

**Real-world AI use case:** RAG systems need document ingestion endpoints that accept files, validate formats, extract text, and prepare documents for embedding.

**Concept being tested:** File upload, content validation, async processing

**Hint:** Use `UploadFile` with content type checking. Read file content asynchronously with `await file.read()`.

**Expected Output:**
```json
{
  "filename": "research_paper.txt",
  "content_type": "text/plain",
  "size_kb": 15.3,
  "word_count": 2450,
  "preview": "First 200 characters of the document...",
  "chunks_estimate": 5,
  "status": "ready_for_embedding"
}
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### Dependency Injection Chain

**Problem:** Build an API with a chain of dependencies: API key validation → rate limiting → user identification → database session.

**Real-world AI use case:** Production AI APIs require layered security — verify the API key, check rate limits, identify the user, and then provide a database connection.

**Concept being tested:** FastAPI `Depends()`, dependency chaining, headers

**Hint:** Each dependency function can depend on another via `Depends()`. Use `Header()` for API key extraction.

**Expected Output:**
```bash
# Without API key
POST /generate → 401 {"detail": "X-API-Key header required"}

# With invalid key
POST /generate (X-API-Key: bad) → 401 {"detail": "Invalid API key"}

# With valid key, rate limited
POST /generate (X-API-Key: valid) → 429 {"detail": "Rate limit exceeded"}

# Success
POST /generate (X-API-Key: valid) → 200 {"user": "user_123", "result": "..."}
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### Background Task Pipeline

**Problem:** Create an endpoint that starts a long-running document processing pipeline as a background task and provides a status-checking endpoint.

**Real-world AI use case:** Processing large documents through RAG pipelines (chunking → embedding → indexing) takes minutes. Background tasks prevent API timeouts.

**Concept being tested:** `BackgroundTasks`, job tracking, status endpoints

**Hint:** Use a dict to track job states. The background task updates the job dict as it progresses through stages.

**Expected Output:**
```bash
# Start job
POST /process {"text": "Long document..."} → {"job_id": "job_001", "status": "queued"}

# Check status (during processing)
GET /jobs/job_001 → {"job_id": "job_001", "status": "embedding", "progress": 60}

# Check status (complete)
GET /jobs/job_001 → {"job_id": "job_001", "status": "completed", "chunks": 5, "time_seconds": 12.3}
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Middleware for Request Logging

**Problem:** Build custom middleware that logs every request with timing, status code, and adds request IDs.

**Real-world AI use case:** AI systems need request tracing for debugging, cost tracking (which requests use the most tokens), and performance monitoring.

**Concept being tested:** Middleware, request/response lifecycle, headers

**Hint:** Use `@app.middleware("http")` decorator. Add `X-Request-ID` header to responses. Use `time.time()` for duration.

**Expected Output:**
```
INFO: [req_abc123] POST /generate - 200 - 1.234s
INFO: [req_def456] GET /health - 200 - 0.002s
INFO: [req_ghi789] POST /embed - 422 - 0.015s (validation error)
```

---

## Problem 09 🧩

**Difficulty:** 🟡 Medium

### Streaming Response for LLM Output

**Problem:** Create an endpoint that simulates streaming LLM output using Server-Sent Events (SSE).

**Real-world AI use case:** ChatGPT-like interfaces stream tokens one at a time so users see responses in real-time instead of waiting for the complete response.

**Concept being tested:** `StreamingResponse`, async generators, SSE format

**Hint:** Use `StreamingResponse` with `media_type="text/event-stream"`. Yield `data: {json}\n\n` formatted chunks.

**Expected Output:**
```
data: {"token": "The", "index": 0}

data: {"token": " answer", "index": 1}

data: {"token": " to", "index": 2}

data: {"token": " your", "index": 3}

data: {"token": " question", "index": 4}

data: {"done": true, "total_tokens": 5}
```

---

## Problem 10 🧩

**Difficulty:** 🔴 Hard

### Multi-Model Router with Fallback

**Problem:** Build a FastAPI service that routes LLM requests to different model providers with automatic fallback on failure.

**Real-world AI use case:** Production AI systems need resilient model routing — if OpenAI is down, fall back to Anthropic. If both fail, use a local Ollama model.

**Concept being tested:** Complex dependency injection, error handling, factory pattern, fallback strategies

**Hint:** Create provider classes implementing a common interface. Use a router class that tries providers in order. Implement circuit breaker logic.

**Expected Output:**
```bash
# Primary provider (OpenAI) succeeds
POST /chat → {"provider": "openai", "model": "gpt-4", "response": "..."}

# Primary fails, fallback to secondary
POST /chat → {"provider": "anthropic", "model": "claude-3", "response": "...", "fallback": true}

# All fail
POST /chat → 503 {"detail": "All providers unavailable", "tried": ["openai", "anthropic", "ollama"]}
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### WebSocket Chat Endpoint

**Problem:** Build a WebSocket-based chat endpoint that maintains conversation history and supports multiple concurrent conversations.

**Real-world AI use case:** Real-time AI chat applications use WebSockets for bidirectional communication, allowing instant message delivery without polling.

**Concept being tested:** WebSockets, connection management, state management, async

**Hint:** Use `@app.websocket("/chat/{conversation_id}")`. Maintain a `ConnectionManager` class that tracks active connections.

**Expected Output:**
```python
# Client connects to ws://localhost:8000/chat/conv_001
# Client sends: {"message": "What is RAG?"}
# Server responds: {"role": "assistant", "content": "RAG stands for...", "conversation_id": "conv_001", "turn": 1}
# Client sends: {"message": "Give me an example"}
# Server responds: {"role": "assistant", "content": "Here's an example...", "conversation_id": "conv_001", "turn": 2}
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### Full API with Auth, Rate Limiting, and Caching

**Problem:** Build a production-ready API that combines authentication, per-user rate limiting, response caching, and structured logging.

**Real-world AI use case:** This is the complete API pattern used by AI startups. Every production LLM gateway needs these features to control costs, prevent abuse, and ensure performance.

**Concept being tested:** All FastAPI concepts combined — dependencies, middleware, background tasks, error handling

**Hint:** Layer the dependencies: `verify_jwt → check_rate_limit → get_cache_or_proceed → process → update_cache`. Use in-memory stores for simplicity.

**Expected Output:**
```bash
# Unauthenticated
POST /api/v1/generate → 401

# Authenticated, first request (cache miss)
POST /api/v1/generate → 200 {"cached": false, "response": "...", "tokens": 150, "cost": 0.003}

# Same request (cache hit)
POST /api/v1/generate → 200 {"cached": true, "response": "...", "tokens": 0, "cost": 0.0}

# Rate limited
POST /api/v1/generate → 429 {"detail": "Rate limit: 10/min exceeded", "retry_after": 45}

# Usage stats
GET /api/v1/usage → {"requests_today": 42, "tokens_used": 15000, "cost_usd": 0.45}
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 1](../README.md) · [🏠 Home](../../README.md)

</div>
