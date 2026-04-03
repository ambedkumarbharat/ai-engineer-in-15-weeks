<div align="center">

# 🐍 Topic 1: Python for AI

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-1_Foundations-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > **Python for AI**

---

## 🎯 Why This Matters for AI Systems Engineering

Python is the **lingua franca** of AI. Every LLM framework (LangChain, LlamaIndex), every ML library (PyTorch, TensorFlow), and every AI deployment tool uses Python. But AI Systems Engineering requires **modern Python** — not just basic scripts. You need to write code that is typed, async, and production-ready.

This topic focuses on the Python features you'll use **every single day** as an AI engineer:
- **Type hints** → LangChain and Pydantic require them everywhere
- **Async/await** → FastAPI and concurrent API calls to LLMs
- **Data classes & Pydantic** → Structured data throughout AI pipelines
- **Generators** → Processing large document sets without running out of memory
- **Context managers** → Managing database connections and file handles
- **Decorators** → Adding logging, caching, retry logic to AI pipelines

---

## 📚 Core Concepts

### 1. Type Hints (Essential for AI Frameworks)

Modern AI libraries like LangChain, Pydantic, and FastAPI **require** type hints. They're not optional — they power validation, serialization, and IDE support.

```python
# ✅ Modern Python with type hints
from typing import Optional

def analyze_sentiment(text: str, model: str = "default") -> dict[str, float]:
    """Analyze sentiment of text and return scores."""
    scores: dict[str, float] = {
        "positive": 0.0,
        "negative": 0.0,
        "neutral": 0.0,
    }
    # Your analysis logic here
    words = text.lower().split()
    positive_words = {"good", "great", "excellent", "amazing", "love"}
    negative_words = {"bad", "terrible", "awful", "hate", "horrible"}
    
    pos_count = sum(1 for w in words if w in positive_words)
    neg_count = sum(1 for w in words if w in negative_words)
    total = len(words) or 1
    
    scores["positive"] = round(pos_count / total, 2)
    scores["negative"] = round(neg_count / total, 2)
    scores["neutral"] = round(1 - scores["positive"] - scores["negative"], 2)
    
    return scores

# Usage
result = analyze_sentiment("This product is great and amazing")
print(result)
# Output: {'positive': 0.33, 'negative': 0.0, 'neutral': 0.67}
```

#### Advanced Type Hints You'll Use in AI Code

```python
from typing import TypeVar, Protocol, Callable, Literal

# Union types (Python 3.10+)
def process_input(data: str | list[str]) -> list[str]:
    if isinstance(data, str):
        return [data]
    return data

# Literal types — great for model selection
ModelName = Literal["gpt-4", "gpt-3.5-turbo", "claude-3"]

def call_llm(prompt: str, model: ModelName = "gpt-4") -> str:
    print(f"Calling {model} with prompt: {prompt[:50]}...")
    return f"Response from {model}"

# TypeVar for generic functions
T = TypeVar("T")

def batch_process(items: list[T], batch_size: int = 10) -> list[list[T]]:
    """Split items into batches — used everywhere in AI pipelines."""
    return [items[i:i + batch_size] for i in range(0, len(items), batch_size)]

# Usage
documents = ["doc1", "doc2", "doc3", "doc4", "doc5"]
batches = batch_process(documents, batch_size=2)
print(batches)
# Output: [['doc1', 'doc2'], ['doc3', 'doc4'], ['doc5']]
```

---

### 2. Data Classes & Pydantic Models

In AI systems, you pass structured data between components constantly. Data classes and Pydantic models are your bread and butter.

```python
from dataclasses import dataclass, field
from datetime import datetime

@dataclass
class Document:
    """Represents a document in a RAG pipeline."""
    content: str
    source: str
    doc_id: str = ""
    metadata: dict = field(default_factory=dict)
    created_at: datetime = field(default_factory=datetime.now)
    chunk_size: int = 500
    
    def __post_init__(self):
        if not self.doc_id:
            self.doc_id = f"doc_{hash(self.content) % 10000:04d}"
    
    @property
    def word_count(self) -> int:
        return len(self.content.split())
    
    def chunk(self) -> list[str]:
        """Split document into chunks for RAG processing."""
        words = self.content.split()
        chunks = []
        for i in range(0, len(words), self.chunk_size):
            chunk = " ".join(words[i:i + self.chunk_size])
            chunks.append(chunk)
        return chunks

# Usage
doc = Document(
    content="AI is transforming how we build software. " * 100,
    source="blog_post.md",
    metadata={"author": "AI Engineer", "category": "tutorial"}
)
print(f"Document ID: {doc.doc_id}")
print(f"Word count: {doc.word_count}")
print(f"Chunks: {len(doc.chunk())}")
```

#### Pydantic — The AI Engineer's Best Friend

```python
from pydantic import BaseModel, Field, field_validator

class LLMRequest(BaseModel):
    """Structured request to an LLM — used in FastAPI endpoints."""
    prompt: str = Field(..., min_length=1, max_length=10000)
    model: str = Field(default="gpt-4", description="Model to use")
    temperature: float = Field(default=0.7, ge=0.0, le=2.0)
    max_tokens: int = Field(default=1000, ge=1, le=4096)
    
    @field_validator("prompt")
    @classmethod
    def prompt_must_not_be_empty(cls, v: str) -> str:
        if not v.strip():
            raise ValueError("Prompt cannot be empty or whitespace")
        return v.strip()

class LLMResponse(BaseModel):
    """Structured response from an LLM."""
    text: str
    model: str
    tokens_used: int
    cost: float
    
    @property
    def cost_per_token(self) -> float:
        return self.cost / self.tokens_used if self.tokens_used else 0.0

# Usage — automatic validation
request = LLMRequest(
    prompt="Explain RAG in simple terms",
    temperature=0.5,
    max_tokens=500
)
print(request.model_dump_json(indent=2))

# This will raise a validation error
try:
    bad_request = LLMRequest(prompt="", temperature=3.0)
except Exception as e:
    print(f"Validation error: {e}")
```

---

### 3. Async/Await (Critical for AI Systems)

AI systems make many I/O calls — to LLM APIs, databases, and external services. Async code lets you handle these concurrently.

```python
import asyncio
from dataclasses import dataclass

@dataclass
class LLMCall:
    prompt: str
    model: str = "gpt-4"

async def call_llm(prompt: str, model: str = "gpt-4") -> str:
    """Simulate an async LLM API call."""
    print(f"  📤 Sending to {model}: {prompt[:40]}...")
    await asyncio.sleep(1)  # Simulates API latency
    return f"Response to: {prompt[:30]}..."

async def process_single(prompt: str) -> str:
    """Process one prompt sequentially."""
    return await call_llm(prompt)

async def process_batch(prompts: list[str]) -> list[str]:
    """Process multiple prompts concurrently — HUGE time savings."""
    tasks = [call_llm(prompt) for prompt in prompts]
    results = await asyncio.gather(*tasks)
    return list(results)

async def main():
    prompts = [
        "What is RAG?",
        "Explain embeddings",
        "How do AI agents work?",
        "What is LangChain?",
    ]
    
    # Sequential: ~4 seconds
    print("⏳ Sequential processing...")
    import time
    start = time.time()
    sequential_results = []
    for p in prompts:
        result = await process_single(p)
        sequential_results.append(result)
    seq_time = time.time() - start
    print(f"  ✅ Sequential took: {seq_time:.1f}s\n")
    
    # Concurrent: ~1 second
    print("⚡ Concurrent processing...")
    start = time.time()
    concurrent_results = await process_batch(prompts)
    conc_time = time.time() - start
    print(f"  ✅ Concurrent took: {conc_time:.1f}s")
    print(f"  🚀 Speedup: {seq_time/conc_time:.1f}x faster!")

asyncio.run(main())
```

#### Async Context Managers (For Database Connections)

```python
import asyncio
from contextlib import asynccontextmanager

@asynccontextmanager
async def get_db_connection(db_url: str = "postgresql://localhost/mydb"):
    """Simulate an async database connection."""
    print(f"  🔌 Connecting to {db_url}...")
    await asyncio.sleep(0.1)
    connection = {"url": db_url, "connected": True}
    try:
        yield connection
    finally:
        print(f"  🔌 Closing connection to {db_url}")
        connection["connected"] = False

async def query_user_data(user_id: int) -> dict:
    async with get_db_connection() as conn:
        print(f"  📊 Querying user {user_id} via {conn['url']}")
        await asyncio.sleep(0.1)  # Simulate query
        return {"user_id": user_id, "name": f"User_{user_id}"}

asyncio.run(query_user_data(42))
```

---

### 4. Generators (Memory-Efficient Document Processing)

When processing thousands of documents for RAG, you can't load them all into memory. Generators process one item at a time.

```python
from pathlib import Path
from typing import Generator

def read_documents(directory: str) -> Generator[dict, None, None]:
    """Read documents lazily — handles millions of files without memory issues."""
    doc_dir = Path(directory)
    if not doc_dir.exists():
        return
    
    for file_path in doc_dir.rglob("*.txt"):
        content = file_path.read_text(encoding="utf-8", errors="ignore")
        yield {
            "content": content,
            "source": str(file_path),
            "size": len(content),
        }

def chunk_text(text: str, chunk_size: int = 500, overlap: int = 50) -> Generator[str, None, None]:
    """Split text into overlapping chunks — core RAG operation."""
    words = text.split()
    start = 0
    while start < len(words):
        end = start + chunk_size
        chunk = " ".join(words[start:end])
        yield chunk
        start += chunk_size - overlap

# Usage
text = "This is a sample document. " * 200
chunks = list(chunk_text(text, chunk_size=20, overlap=5))
print(f"Total chunks: {len(chunks)}")
print(f"First chunk: {chunks[0][:80]}...")
print(f"Overlap demo - end of chunk 1: ...{chunks[0][-30:]}")
print(f"Overlap demo - start of chunk 2: {chunks[1][:30]}...")
```

---

### 5. Decorators (Adding Cross-Cutting Concerns)

Decorators are perfect for adding retry logic, caching, logging, and timing to AI pipeline functions.

```python
import functools
import time
from typing import Callable, TypeVar

T = TypeVar("T")

def retry(max_attempts: int = 3, delay: float = 1.0):
    """Retry decorator — essential for flaky LLM API calls."""
    def decorator(func: Callable) -> Callable:
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            last_error = None
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    last_error = e
                    print(f"  ⚠️ Attempt {attempt}/{max_attempts} failed: {e}")
                    if attempt < max_attempts:
                        time.sleep(delay)
            raise last_error
        return wrapper
    return decorator

def timer(func: Callable) -> Callable:
    """Time any function — great for profiling AI pipelines."""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"  ⏱️ {func.__name__} took {elapsed:.3f}s")
        return result
    return wrapper

def cache_result(func: Callable) -> Callable:
    """Simple cache — avoid re-computing embeddings for same text."""
    cache: dict = {}
    @functools.wraps(func)
    def wrapper(*args):
        key = str(args)
        if key not in cache:
            cache[key] = func(*args)
            print(f"  💾 Cache MISS for {func.__name__}")
        else:
            print(f"  ✅ Cache HIT for {func.__name__}")
        return cache[key]
    return wrapper

# Usage
@timer
@retry(max_attempts=3, delay=0.5)
def call_llm_api(prompt: str) -> str:
    """Simulate an LLM call that sometimes fails."""
    import random
    if random.random() < 0.3:
        raise ConnectionError("API timeout")
    return f"LLM response to: {prompt[:30]}"

@cache_result
def compute_embedding(text: str) -> list[float]:
    """Simulate computing an embedding (expensive operation)."""
    time.sleep(0.1)  # Simulate computation
    return [hash(text) % 100 / 100.0 for _ in range(5)]

# Test the decorators
print("Testing retry + timer:")
try:
    result = call_llm_api("What is machine learning?")
    print(f"  Result: {result}\n")
except ConnectionError:
    print("  All attempts failed\n")

print("Testing cache:")
emb1 = compute_embedding("hello world")
emb2 = compute_embedding("hello world")  # Cache hit!
emb3 = compute_embedding("different text")  # Cache miss
```

---

### 6. Error Handling Patterns for AI Systems

```python
import logging
from typing import Optional

logging.basicConfig(level=logging.INFO, format="%(levelname)s: %(message)s")
logger = logging.getLogger(__name__)

class AISystemError(Exception):
    """Base exception for AI system errors."""
    pass

class LLMError(AISystemError):
    """LLM-specific errors."""
    def __init__(self, message: str, model: str, prompt_tokens: int = 0):
        self.model = model
        self.prompt_tokens = prompt_tokens
        super().__init__(f"[{model}] {message}")

class RateLimitError(LLMError):
    """Rate limit exceeded."""
    def __init__(self, model: str, retry_after: float = 60.0):
        self.retry_after = retry_after
        super().__init__(f"Rate limited. Retry after {retry_after}s", model)

class DocumentProcessingError(AISystemError):
    """Error processing a document in the RAG pipeline."""
    pass

def safe_llm_call(prompt: str, model: str = "gpt-4") -> Optional[str]:
    """Production-grade LLM call with proper error handling."""
    try:
        # Simulate the call
        if len(prompt) > 5000:
            raise LLMError("Prompt too long", model, len(prompt.split()))
        if "rate" in prompt.lower():
            raise RateLimitError(model, retry_after=30.0)
        
        response = f"Response from {model}: processed '{prompt[:30]}...'"
        logger.info(f"Successfully called {model}")
        return response
    
    except RateLimitError as e:
        logger.warning(f"Rate limited on {e.model}. Retry after {e.retry_after}s")
        return None
    except LLMError as e:
        logger.error(f"LLM error: {e}")
        return None
    except Exception as e:
        logger.exception(f"Unexpected error: {e}")
        raise

# Usage
print(safe_llm_call("Tell me about AI"))
print(safe_llm_call("rate limit test"))
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Python Type Hints — Full Course** | 📹 YouTube | [ArjanCodes - Type Hints](https://www.youtube.com/watch?v=dgBCEB2jVU0) |
| **Async Python — Complete Tutorial** | 📹 YouTube | [Tech With Tim - Asyncio](https://www.youtube.com/watch?v=2IW-ZEui4h4) |
| **Python Pydantic Tutorial** | 📹 YouTube | [BugBytes - Pydantic V2](https://www.youtube.com/watch?v=502XOB0u8OY) |
| **Python Decorators in 15 min** | 📹 YouTube | [Tech With Tim - Decorators](https://www.youtube.com/watch?v=FsAPt_9Bf3U) |
| **Python Official Docs — Typing** | 📄 Docs | [docs.python.org/3/library/typing](https://docs.python.org/3/library/typing.html) |
| **Real Python — Generators** | 📄 Blog | [realpython.com/introduction-to-python-generators](https://realpython.com/introduction-to-python-generators/) |
| **Pydantic V2 Docs** | 📄 Docs | [docs.pydantic.dev](https://docs.pydantic.dev/) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Not using type hints | LangChain/Pydantic won't work properly | Add types to all function signatures |
| Using `dict` everywhere | No validation, hard to debug | Use Pydantic models or dataclasses |
| Sequential API calls | 4x slower when calling LLMs | Use `asyncio.gather()` for concurrent calls |
| Loading all docs into memory | Crashes on large datasets | Use generators for lazy loading |
| Bare `except:` clauses | Swallows real bugs | Catch specific exceptions |
| Not using `if __name__ == "__main__"` | Causes import side effects | Always guard main execution |
| Mutable default arguments | Shared state bugs | Use `field(default_factory=list)` |

---

## 📋 Quick Reference Cheatsheet

| Concept | Syntax | When to Use |
|---------|--------|------------|
| Type hints | `def foo(x: str) -> int:` | Every function |
| Optional | `x: str \| None = None` | Nullable parameters |
| Literal | `mode: Literal["fast", "slow"]` | Fixed choices |
| Dataclass | `@dataclass` | Simple data containers |
| Pydantic | `class M(BaseModel):` | API models with validation |
| Async function | `async def foo():` | I/O operations (API calls, DB) |
| Gather | `await asyncio.gather(*tasks)` | Concurrent async calls |
| Generator | `yield item` | Large data processing |
| Decorator | `@decorator` | Cross-cutting concerns |
| Context manager | `async with resource():` | Resource management |
| f-string | `f"Result: {var:.2f}"` | String formatting |
| Walrus | `if (n := len(x)) > 10:` | Assign + check |

---

> 💡 **Pro Tip:** When you start using LangChain and LangGraph, you'll notice they heavily use Pydantic models, type hints, and async patterns. Master these now, and frameworks will feel intuitive later. Spend an extra day here if needed — it pays off 10x in later phases.

---

<div align="center">

[🏠 Home](../../README.md) · [📋 Phase 1](../README.md)

**[▶ Problems](problems.md)** · [Topic 2: FastAPI Basics →](../topic-2-fastapi-basics/)

</div>
