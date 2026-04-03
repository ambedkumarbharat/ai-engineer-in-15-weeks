<div align="center">

# ⚡ Topic 2: FastAPI Basics

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-1_Foundations-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > **FastAPI Basics**

---

## 🎯 Why This Matters for AI Systems Engineering

FastAPI is the **#1 framework** for building AI system backends. Almost every AI startup uses FastAPI because:
- **Async by default** → Handle concurrent LLM API calls efficiently
- **Pydantic integration** → Automatic request/response validation
- **Auto-generated docs** → Swagger UI and ReDoc out of the box
- **Type-safe** → Catches bugs before they reach production
- **Fast** → Built on Starlette/ASGI for high performance

You will use FastAPI in **every single project** in this roadmap. It's the backbone of your AI applications.

---

## 📚 Core Concepts

### 1. Your First FastAPI Application

```python
# main.py
from fastapi import FastAPI

app = FastAPI(
    title="AI Systems API",
    description="My first FastAPI application for AI",
    version="1.0.0",
)

@app.get("/")
async def root():
    """Health check endpoint."""
    return {"status": "healthy", "service": "ai-systems-api"}

@app.get("/hello/{name}")
async def hello(name: str):
    """Greet a user by name."""
    return {"message": f"Hello, {name}! Welcome to AI Systems Engineering."}
```

**Run it:**
```bash
pip install fastapi uvicorn
uvicorn main:app --reload
```

**Visit:** http://localhost:8000/docs → Interactive Swagger UI 🎉

---

### 2. Request & Response Models with Pydantic

This is where FastAPI shines for AI systems — automatic validation of inputs and outputs.

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field
from datetime import datetime

app = FastAPI(title="LLM Gateway API")

# Request model
class LLMRequest(BaseModel):
    prompt: str = Field(..., min_length=1, max_length=10000, 
                       description="The prompt to send to the LLM")
    model: str = Field(default="gpt-4", description="Model to use")
    temperature: float = Field(default=0.7, ge=0.0, le=2.0)
    max_tokens: int = Field(default=1000, ge=1, le=4096)

    model_config = {
        "json_schema_extra": {
            "examples": [
                {
                    "prompt": "Explain retrieval augmented generation",
                    "model": "gpt-4",
                    "temperature": 0.7,
                    "max_tokens": 500
                }
            ]
        }
    }

# Response model
class LLMResponse(BaseModel):
    text: str
    model: str
    tokens_used: int
    processing_time: float
    timestamp: datetime = Field(default_factory=datetime.now)

@app.post("/generate", response_model=LLMResponse)
async def generate_text(request: LLMRequest):
    """Generate text using an LLM."""
    import time
    start = time.time()
    
    # Simulate LLM processing
    response_text = f"[Simulated response from {request.model}] " \
                    f"Processing prompt: '{request.prompt[:50]}...'"
    
    processing_time = time.time() - start
    
    return LLMResponse(
        text=response_text,
        model=request.model,
        tokens_used=len(request.prompt.split()) * 2,
        processing_time=processing_time,
    )
```

---

### 3. Path Parameters, Query Parameters & Request Body

```python
from fastapi import FastAPI, Query, Path
from enum import Enum

app = FastAPI()

class ModelProvider(str, Enum):
    openai = "openai"
    anthropic = "anthropic"
    ollama = "ollama"

# Path parameter
@app.get("/models/{provider}")
async def get_models(
    provider: ModelProvider = Path(..., description="LLM provider name")
):
    """Get available models for a provider."""
    models_db = {
        "openai": ["gpt-4", "gpt-3.5-turbo", "gpt-4-turbo"],
        "anthropic": ["claude-3-opus", "claude-3-sonnet", "claude-3-haiku"],
        "ollama": ["llama3", "mistral", "phi-3"],
    }
    return {"provider": provider, "models": models_db[provider.value]}

# Query parameters
@app.get("/search")
async def search_documents(
    query: str = Query(..., min_length=1, description="Search query"),
    top_k: int = Query(default=5, ge=1, le=100, description="Number of results"),
    threshold: float = Query(default=0.7, ge=0.0, le=1.0, description="Min similarity"),
    include_metadata: bool = Query(default=True),
):
    """Search documents by semantic similarity."""
    return {
        "query": query,
        "top_k": top_k,
        "threshold": threshold,
        "results": [
            {
                "content": f"Result {i+1} for '{query}'",
                "score": round(0.95 - i * 0.05, 2),
                "metadata": {"source": f"doc_{i}.pdf"} if include_metadata else None,
            }
            for i in range(min(top_k, 3))
        ],
    }
```

---

### 4. Error Handling

```python
from fastapi import FastAPI, HTTPException, Request
from fastapi.responses import JSONResponse
from pydantic import BaseModel

app = FastAPI()

class ErrorResponse(BaseModel):
    error: str
    detail: str
    status_code: int

class TokenLimitError(Exception):
    def __init__(self, tokens: int, limit: int):
        self.tokens = tokens
        self.limit = limit

# Custom exception handler
@app.exception_handler(TokenLimitError)
async def token_limit_handler(request: Request, exc: TokenLimitError):
    return JSONResponse(
        status_code=429,
        content={
            "error": "Token limit exceeded",
            "detail": f"Prompt uses {exc.tokens} tokens, limit is {exc.limit}",
            "status_code": 429,
        }
    )

@app.post("/analyze")
async def analyze_text(text: str):
    """Analyze text with token limit check."""
    token_count = len(text.split())
    max_tokens = 100
    
    if token_count > max_tokens:
        raise TokenLimitError(tokens=token_count, limit=max_tokens)
    
    if not text.strip():
        raise HTTPException(
            status_code=400,
            detail="Text cannot be empty",
        )
    
    return {
        "text": text,
        "token_count": token_count,
        "analysis": "processed",
    }
```

---

### 5. Dependency Injection (The FastAPI Superpower)

```python
from fastapi import FastAPI, Depends, Header, HTTPException
from typing import Annotated

app = FastAPI()

# Simulated database
fake_db: dict = {}

# Dependency: API key validation
async def verify_api_key(x_api_key: str = Header(...)):
    """Validate API key from header."""
    valid_keys = {"key-123", "key-456", "test-key"}
    if x_api_key not in valid_keys:
        raise HTTPException(status_code=401, detail="Invalid API key")
    return x_api_key

# Dependency: Get database session
async def get_db():
    """Simulate database connection lifecycle."""
    db_session = {"connected": True, "data": fake_db}
    try:
        yield db_session
    finally:
        db_session["connected"] = False

# Dependency: Rate limiter (simplified)
class RateLimiter:
    def __init__(self, max_requests: int = 10):
        self.max_requests = max_requests
        self.request_count: dict[str, int] = {}
    
    async def __call__(self, api_key: str = Depends(verify_api_key)):
        count = self.request_count.get(api_key, 0)
        if count >= self.max_requests:
            raise HTTPException(status_code=429, detail="Rate limit exceeded")
        self.request_count[api_key] = count + 1
        return api_key

rate_limiter = RateLimiter(max_requests=100)

@app.post("/embed")
async def create_embedding(
    text: str,
    api_key: Annotated[str, Depends(rate_limiter)],
    db: Annotated[dict, Depends(get_db)],
):
    """Create an embedding with rate limiting and DB storage."""
    embedding = [hash(text + str(i)) % 1000 / 1000 for i in range(5)]
    
    # Store in DB
    doc_id = f"doc_{len(db['data'])}"
    db["data"][doc_id] = {"text": text, "embedding": embedding}
    
    return {
        "doc_id": doc_id,
        "embedding": embedding,
        "dimensions": len(embedding),
    }
```

---

### 6. File Upload (Essential for RAG)

```python
from fastapi import FastAPI, UploadFile, File, HTTPException
from pydantic import BaseModel

app = FastAPI()

class FileAnalysis(BaseModel):
    filename: str
    size_bytes: int
    content_preview: str
    word_count: int
    line_count: int

@app.post("/upload", response_model=FileAnalysis)
async def upload_document(
    file: UploadFile = File(..., description="Document to analyze")
):
    """Upload and analyze a document (for RAG ingestion)."""
    # Validate file type
    allowed_types = {
        "text/plain", "application/pdf", 
        "text/markdown", "text/csv",
    }
    if file.content_type not in allowed_types:
        raise HTTPException(
            status_code=400,
            detail=f"File type {file.content_type} not supported. "
                   f"Allowed: {allowed_types}",
        )
    
    # Read content
    content = await file.read()
    text = content.decode("utf-8", errors="ignore")
    
    return FileAnalysis(
        filename=file.filename or "unknown",
        size_bytes=len(content),
        content_preview=text[:200],
        word_count=len(text.split()),
        line_count=text.count("\n") + 1,
    )

@app.post("/upload-multiple")
async def upload_multiple_documents(
    files: list[UploadFile] = File(..., description="Multiple documents")
):
    """Upload multiple documents at once for batch processing."""
    results = []
    for file in files:
        content = await file.read()
        text = content.decode("utf-8", errors="ignore")
        results.append({
            "filename": file.filename,
            "size_bytes": len(content),
            "word_count": len(text.split()),
        })
    return {"files_processed": len(results), "results": results}
```

---

### 7. Background Tasks (For Long-Running AI Operations)

```python
from fastapi import FastAPI, BackgroundTasks
from pydantic import BaseModel
import time

app = FastAPI()

# In-memory job tracker
jobs: dict[str, dict] = {}

class JobResponse(BaseModel):
    job_id: str
    status: str
    message: str

def process_document_background(job_id: str, text: str):
    """Simulate long-running document processing."""
    jobs[job_id]["status"] = "processing"
    
    # Simulate chunking
    time.sleep(2)
    chunks = [text[i:i+100] for i in range(0, len(text), 100)]
    jobs[job_id]["chunks"] = len(chunks)
    
    # Simulate embedding generation
    time.sleep(3)
    jobs[job_id]["embeddings_generated"] = len(chunks)
    
    jobs[job_id]["status"] = "completed"

@app.post("/process", response_model=JobResponse)
async def start_processing(
    text: str,
    background_tasks: BackgroundTasks,
):
    """Start async document processing."""
    job_id = f"job_{int(time.time())}"
    jobs[job_id] = {"status": "queued", "text_length": len(text)}
    
    background_tasks.add_task(process_document_background, job_id, text)
    
    return JobResponse(
        job_id=job_id,
        status="queued",
        message="Processing started. Check /status/{job_id} for updates.",
    )

@app.get("/status/{job_id}")
async def get_job_status(job_id: str):
    """Check the status of a processing job."""
    if job_id not in jobs:
        return {"error": "Job not found"}
    return {"job_id": job_id, **jobs[job_id]}
```

---

### 8. Application Lifespan (Startup/Shutdown)

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI

# Shared resources
resources: dict = {}

@asynccontextmanager
async def lifespan(app: FastAPI):
    """Manage application lifecycle — load models, connect DBs."""
    # Startup
    print("🚀 Starting up...")
    resources["model"] = "loaded_model_placeholder"
    resources["db"] = {"connected": True}
    resources["cache"] = {}
    print("✅ All resources loaded")
    
    yield  # Application runs here
    
    # Shutdown
    print("🛑 Shutting down...")
    resources.clear()
    print("✅ All resources cleaned up")

app = FastAPI(lifespan=lifespan, title="AI API with Lifespan")

@app.get("/health")
async def health():
    return {
        "status": "healthy",
        "model_loaded": "model" in resources,
        "db_connected": resources.get("db", {}).get("connected", False),
    }
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **FastAPI Official Tutorial** | 📄 Docs | [fastapi.tiangolo.com/tutorial](https://fastapi.tiangolo.com/tutorial/) |
| **FastAPI Full Course — 7 Hours** | 📹 YouTube | [FreeCodeCamp - FastAPI](https://www.youtube.com/watch?v=7t2alSnE2-I) |
| **FastAPI + ML Model Deployment** | 📹 YouTube | [Patrick Loeber - FastAPI ML](https://www.youtube.com/watch?v=kMOhYNK3pIk) |
| **Pydantic V2 Complete Guide** | 📄 Docs | [docs.pydantic.dev/latest/](https://docs.pydantic.dev/latest/) |
| **ASGI Explained** | 📄 Blog | [florimond.dev/en/posts/2019/08/introduction-to-asgi](https://florimond.dev/en/posts/2019/08/introduction-to-asgi-async-python-web/) |
| **FastAPI Best Practices** | 📄 GitHub | [zhanymkanov/fastapi-best-practices](https://github.com/zhanymkanov/fastapi-best-practices) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Using `def` instead of `async def` | Blocks the event loop on I/O | Use `async def` for endpoints with I/O |
| Not using Pydantic models | No validation, bad docs | Define request/response models |
| Returning raw dicts | No type safety, no auto-docs | Use `response_model` parameter |
| Ignoring status codes | Client can't distinguish errors | Use `HTTPException` with proper codes |
| Not using dependencies | Repeated code in every endpoint | Use `Depends()` for shared logic |
| Hardcoding configs | Can't change between environments | Use environment variables with Pydantic `Settings` |
| Not handling file upload errors | App crashes on bad files | Validate file type, size, and encoding |

---

## 📋 Quick Reference Cheatsheet

| Pattern | Code | When to Use |
|---------|------|------------|
| GET endpoint | `@app.get("/path")` | Read data |
| POST endpoint | `@app.post("/path")` | Create/process data |
| Path param | `def foo(id: int):` | Resource by ID |
| Query param | `def foo(q: str = Query(...)):` | Filtering, search |
| Request body | `def foo(data: MyModel):` | Complex input |
| Response model | `@app.post("/", response_model=M)` | Type-safe output |
| File upload | `file: UploadFile = File(...)` | Document ingestion |
| Dependency | `Depends(my_function)` | Shared logic (auth, DB) |
| Background task | `background_tasks.add_task(fn)` | Long operations |
| Exception | `raise HTTPException(status_code=400)` | Error responses |
| Lifespan | `@asynccontextmanager` | Startup/shutdown |
| CORS | `add_middleware(CORSMiddleware)` | Frontend access |

---

> 💡 **Pro Tip:** Always test your API using the built-in Swagger UI at `/docs`. It's interactive — you can send requests directly from the browser. For production, add proper CORS middleware, rate limiting, and authentication. FastAPI's dependency injection system is powerful enough to handle all of these cleanly.

---

<div align="center">

[← Topic 1: Python for AI](../topic-1-python-for-ai/) · [🏠 Home](../../README.md) · [📋 Phase 1](../README.md)

**[▶ Problems](problems.md)** · [Topic 3: Git & GitHub →](../topic-3-git-github/)

</div>
