<div align="center">

# 🔨 Mini Project: Dockerized Text Analysis API

### Combine FastAPI, Docker, and Git to build your first AI-adjacent service

![Tech](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Tech](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Tech](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Tech](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > **Mini Project**

---

## 📋 Project Overview

Build a **production-style FastAPI application** that provides text analysis endpoints — word counting, sentence detection, keyword extraction, readability scoring, and text summarization (extractive). The entire application is containerized with Docker and version-controlled with Git.

This is the kind of microservice that lives inside larger AI systems. In a real RAG pipeline, text analysis is used before chunking, and keyword extraction feeds into search indexing.

---

## 🧠 Concepts Used from This Phase

| Concept | Where It's Used |
|---------|----------------|
| **Python type hints** | All function signatures and Pydantic models |
| **Pydantic models** | Request/response validation |
| **FastAPI endpoints** | REST API for text analysis |
| **Async/await** | Async endpoint handlers |
| **Error handling** | Custom exceptions, HTTP errors |
| **Git workflow** | Feature branches, conventional commits |
| **Docker** | Containerize the entire application |
| **Docker Compose** | Multi-container setup with health checks |
| **Linux commands** | Shell scripts for setup and testing |

---

## 🏗️ File Structure

```
text-analysis-api/
├── src/
│   ├── __init__.py
│   ├── main.py              # FastAPI app with lifespan
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── analyze.py       # Text analysis endpoints
│   │   └── health.py        # Health check endpoint
│   ├── models/
│   │   ├── __init__.py
│   │   └── schemas.py       # Pydantic request/response models
│   ├── services/
│   │   ├── __init__.py
│   │   └── analyzer.py      # Core text analysis logic
│   └── utils/
│       ├── __init__.py
│       └── text_utils.py    # Helper functions
├── tests/
│   ├── __init__.py
│   ├── test_analyzer.py     # Unit tests for analysis logic
│   └── test_api.py          # API integration tests
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
├── .gitignore
├── .env.example
├── requirements.txt
├── setup.sh                 # Setup script
├── README.md                # Project README
└── Makefile                 # Common commands
```

---

## 🔧 Step-by-Step Build Instructions

### Step 1: Project Setup

```bash
# Create project directory
mkdir text-analysis-api && cd text-analysis-api

# Initialize git
git init
git checkout -b main

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Create directory structure
mkdir -p src/{routes,models,services,utils}
mkdir tests
touch src/__init__.py src/routes/__init__.py src/models/__init__.py
touch src/services/__init__.py src/utils/__init__.py tests/__init__.py
```

### Step 2: Define Dependencies

```txt
# requirements.txt
fastapi==0.115.0
uvicorn[standard]==0.30.0
pydantic==2.9.0
python-multipart==0.0.9
httpx==0.27.0      # For testing
pytest==8.3.0      # For testing
pytest-asyncio==0.24.0
```

```bash
pip install -r requirements.txt
git add . && git commit -m "chore: initial project setup with dependencies"
```

### Step 3: Create Pydantic Models

```python
# src/models/schemas.py
from pydantic import BaseModel, Field
from datetime import datetime

class TextInput(BaseModel):
    """Request model for text analysis."""
    text: str = Field(
        ..., 
        min_length=1, 
        max_length=50000,
        description="Text to analyze",
        json_schema_extra={"examples": ["AI is transforming how we build software."]}
    )
    include_keywords: bool = Field(default=True, description="Include keyword extraction")
    top_keywords: int = Field(default=10, ge=1, le=50, description="Number of top keywords")

class TextStatistics(BaseModel):
    """Basic text statistics."""
    word_count: int
    sentence_count: int
    paragraph_count: int
    character_count: int
    character_count_no_spaces: int
    avg_word_length: float
    avg_sentence_length: float
    
class KeywordResult(BaseModel):
    """A single keyword with its frequency."""
    word: str
    count: int
    frequency: float  # Percentage

class ReadabilityScore(BaseModel):
    """Readability analysis results."""
    flesch_reading_ease: float
    reading_level: str  # "Easy", "Standard", "Difficult"
    estimated_reading_time_minutes: float

class AnalysisResponse(BaseModel):
    """Complete text analysis response."""
    statistics: TextStatistics
    keywords: list[KeywordResult] = []
    readability: ReadabilityScore
    top_sentences: list[str] = []  # Extractive summary
    analyzed_at: datetime = Field(default_factory=datetime.now)

class HealthResponse(BaseModel):
    """Health check response."""
    status: str
    version: str
    uptime_seconds: float
    requests_served: int

class ErrorResponse(BaseModel):
    """Error response."""
    error: str
    detail: str
    status_code: int
```

### Step 4: Build the Analysis Service

```python
# src/services/analyzer.py
import re
import math
from collections import Counter
from src.models.schemas import (
    TextStatistics, KeywordResult, ReadabilityScore, AnalysisResponse
)

# Common English stop words to exclude from keyword extraction
STOP_WORDS = {
    "the", "a", "an", "is", "are", "was", "were", "be", "been", "being",
    "have", "has", "had", "do", "does", "did", "will", "would", "could",
    "should", "may", "might", "shall", "can", "need", "dare", "ought",
    "used", "to", "of", "in", "for", "on", "with", "at", "by", "from",
    "as", "into", "through", "during", "before", "after", "above", "below",
    "between", "out", "off", "over", "under", "again", "further", "then",
    "once", "here", "there", "when", "where", "why", "how", "all", "both",
    "each", "few", "more", "most", "other", "some", "such", "no", "nor",
    "not", "only", "own", "same", "so", "than", "too", "very", "just",
    "because", "but", "and", "or", "if", "while", "that", "this", "it",
    "its", "i", "me", "my", "we", "our", "you", "your", "he", "him",
    "his", "she", "her", "they", "them", "their", "what", "which", "who",
}

class TextAnalyzer:
    """Core text analysis engine."""
    
    def analyze(self, text: str, include_keywords: bool = True, 
                top_keywords: int = 10) -> AnalysisResponse:
        """Perform complete text analysis."""
        statistics = self._compute_statistics(text)
        keywords = self._extract_keywords(text, top_keywords) if include_keywords else []
        readability = self._compute_readability(text, statistics)
        top_sentences = self._extract_top_sentences(text, n=3)
        
        return AnalysisResponse(
            statistics=statistics,
            keywords=keywords,
            readability=readability,
            top_sentences=top_sentences,
        )
    
    def _compute_statistics(self, text: str) -> TextStatistics:
        """Compute basic text statistics."""
        words = text.split()
        sentences = re.split(r'[.!?]+', text)
        sentences = [s.strip() for s in sentences if s.strip()]
        paragraphs = [p.strip() for p in text.split('\n\n') if p.strip()]
        
        word_lengths = [len(w.strip('.,!?;:')) for w in words]
        
        return TextStatistics(
            word_count=len(words),
            sentence_count=max(len(sentences), 1),
            paragraph_count=max(len(paragraphs), 1),
            character_count=len(text),
            character_count_no_spaces=len(text.replace(' ', '')),
            avg_word_length=round(sum(word_lengths) / max(len(word_lengths), 1), 2),
            avg_sentence_length=round(len(words) / max(len(sentences), 1), 2),
        )
    
    def _extract_keywords(self, text: str, top_n: int) -> list[KeywordResult]:
        """Extract top keywords excluding stop words."""
        words = re.findall(r'\b[a-zA-Z]{2,}\b', text.lower())
        filtered = [w for w in words if w not in STOP_WORDS]
        counter = Counter(filtered)
        total = len(filtered) or 1
        
        return [
            KeywordResult(
                word=word,
                count=count,
                frequency=round(count / total * 100, 2),
            )
            for word, count in counter.most_common(top_n)
        ]
    
    def _compute_readability(self, text: str, stats: TextStatistics) -> ReadabilityScore:
        """Compute Flesch Reading Ease score."""
        words = text.split()
        sentences = max(stats.sentence_count, 1)
        
        # Count syllables (simplified)
        syllable_count = sum(self._count_syllables(w) for w in words)
        word_count = max(len(words), 1)
        
        # Flesch Reading Ease formula
        score = 206.835 - 1.015 * (word_count / sentences) - 84.6 * (syllable_count / word_count)
        score = max(0, min(100, round(score, 1)))
        
        if score >= 60:
            level = "Easy"
        elif score >= 30:
            level = "Standard"
        else:
            level = "Difficult"
        
        reading_time = round(word_count / 200, 2)  # 200 WPM average
        
        return ReadabilityScore(
            flesch_reading_ease=score,
            reading_level=level,
            estimated_reading_time_minutes=reading_time,
        )
    
    def _count_syllables(self, word: str) -> int:
        """Estimate syllable count for a word."""
        word = word.lower().strip('.,!?;:')
        if len(word) <= 2:
            return 1
        vowels = "aeiouy"
        count = 0
        prev_vowel = False
        for char in word:
            is_vowel = char in vowels
            if is_vowel and not prev_vowel:
                count += 1
            prev_vowel = is_vowel
        if word.endswith('e') and count > 1:
            count -= 1
        return max(count, 1)
    
    def _extract_top_sentences(self, text: str, n: int = 3) -> list[str]:
        """Extract most important sentences (extractive summarization)."""
        sentences = re.split(r'(?<=[.!?])\s+', text)
        sentences = [s.strip() for s in sentences if len(s.strip()) > 10]
        
        if len(sentences) <= n:
            return sentences
        
        # Score sentences by keyword density
        all_words = re.findall(r'\b[a-zA-Z]{3,}\b', text.lower())
        word_freq = Counter(all_words)
        
        scored = []
        for i, sent in enumerate(sentences):
            sent_words = re.findall(r'\b[a-zA-Z]{3,}\b', sent.lower())
            score = sum(word_freq[w] for w in sent_words) / max(len(sent_words), 1)
            # Boost first sentences (position bias)
            if i < 2:
                score *= 1.2
            scored.append((score, sent))
        
        scored.sort(key=lambda x: x[0], reverse=True)
        return [sent for _, sent in scored[:n]]
```

### Step 5: Create API Routes

```python
# src/routes/health.py
import time
from fastapi import APIRouter
from src.models.schemas import HealthResponse

router = APIRouter(tags=["Health"])

start_time = time.time()
request_counter = 0

@router.get("/health", response_model=HealthResponse)
async def health_check():
    """Check if the service is running."""
    global request_counter
    request_counter += 1
    return HealthResponse(
        status="healthy",
        version="1.0.0",
        uptime_seconds=round(time.time() - start_time, 2),
        requests_served=request_counter,
    )
```

```python
# src/routes/analyze.py
from fastapi import APIRouter, HTTPException
from src.models.schemas import TextInput, AnalysisResponse, ErrorResponse
from src.services.analyzer import TextAnalyzer

router = APIRouter(prefix="/api/v1", tags=["Analysis"])
analyzer = TextAnalyzer()

@router.post(
    "/analyze",
    response_model=AnalysisResponse,
    responses={400: {"model": ErrorResponse}},
    summary="Analyze text",
    description="Perform comprehensive text analysis including statistics, keywords, and readability.",
)
async def analyze_text(input_data: TextInput):
    """Analyze text and return comprehensive results."""
    try:
        result = analyzer.analyze(
            text=input_data.text,
            include_keywords=input_data.include_keywords,
            top_keywords=input_data.top_keywords,
        )
        return result
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"Analysis failed: {str(e)}")

@router.post(
    "/word-count",
    summary="Quick word count",
    description="Get a quick word count for the given text.",
)
async def word_count(input_data: TextInput):
    """Get quick word count."""
    words = input_data.text.split()
    return {
        "word_count": len(words),
        "character_count": len(input_data.text),
    }

@router.post(
    "/keywords",
    summary="Extract keywords",
    description="Extract top keywords from the text.",
)
async def extract_keywords(input_data: TextInput):
    """Extract keywords from text."""
    result = analyzer.analyze(
        text=input_data.text,
        include_keywords=True,
        top_keywords=input_data.top_keywords,
    )
    return {"keywords": result.keywords}

@router.post(
    "/summarize",
    summary="Extractive summary",
    description="Get top sentences as an extractive summary.",
)
async def summarize(input_data: TextInput):
    """Generate extractive summary."""
    result = analyzer.analyze(text=input_data.text, include_keywords=False)
    return {"summary": " ".join(result.top_sentences)}
```

### Step 6: Create the FastAPI App

```python
# src/main.py
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from src.routes import health, analyze

@asynccontextmanager
async def lifespan(app: FastAPI):
    """Manage application lifecycle."""
    print("🚀 Text Analysis API starting up...")
    yield
    print("🛑 Text Analysis API shutting down...")

app = FastAPI(
    title="Text Analysis API",
    description="Production-ready text analysis service for AI pipelines",
    version="1.0.0",
    lifespan=lifespan,
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include routers
app.include_router(health.router)
app.include_router(analyze.router)

@app.get("/")
async def root():
    return {
        "service": "Text Analysis API",
        "version": "1.0.0",
        "docs": "/docs",
    }
```

### Step 7: Dockerize

```dockerfile
# Dockerfile
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --target=/install -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /install /usr/local/lib/python3.11/site-packages/
COPY . .
RUN adduser --disabled-password --no-create-home appuser
USER appuser
EXPOSE 8000
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')" || exit 1
CMD ["python", "-m", "uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - LOG_LEVEL=info
    healthcheck:
      test: ["CMD", "python", "-c", "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped
```

### Step 8: Add Tests

```python
# tests/test_api.py
import pytest
from httpx import AsyncClient, ASGITransport
from src.main import app

@pytest.fixture
def anyio_backend():
    return "asyncio"

@pytest.fixture
async def client():
    transport = ASGITransport(app=app)
    async with AsyncClient(transport=transport, base_url="http://test") as ac:
        yield ac

@pytest.mark.anyio
async def test_health(client):
    response = await client.get("/health")
    assert response.status_code == 200
    data = response.json()
    assert data["status"] == "healthy"

@pytest.mark.anyio
async def test_analyze(client):
    response = await client.post("/api/v1/analyze", json={
        "text": "AI is transforming software engineering. Machine learning models are powerful.",
        "include_keywords": True,
        "top_keywords": 5,
    })
    assert response.status_code == 200
    data = response.json()
    assert data["statistics"]["word_count"] == 10
    assert data["statistics"]["sentence_count"] == 2
    assert len(data["keywords"]) <= 5

@pytest.mark.anyio
async def test_empty_text_rejected(client):
    response = await client.post("/api/v1/analyze", json={"text": ""})
    assert response.status_code == 422  # Validation error

@pytest.mark.anyio
async def test_word_count(client):
    response = await client.post("/api/v1/word-count", json={
        "text": "Hello world this is a test",
    })
    assert response.status_code == 200
    assert response.json()["word_count"] == 6
```

---

## ▶️ How to Run

### Local Development
```bash
# Install dependencies
pip install -r requirements.txt

# Run the server
uvicorn src.main:app --reload

# Visit docs
open http://localhost:8000/docs
```

### Docker
```bash
# Build and run
docker compose up --build -d

# Check logs
docker compose logs -f

# Test
curl -X POST http://localhost:8000/api/v1/analyze \
  -H "Content-Type: application/json" \
  -d '{"text": "AI is transforming how we build software. Machine learning enables new capabilities."}'

# Run tests inside container
docker compose exec api python -m pytest tests/ -v

# Stop
docker compose down
```

---

## 🚀 How to Extend It

1. **Add language detection** → Use `langdetect` library
2. **Add sentiment analysis** → Use `TextBlob` or custom keywords
3. **Add batch processing** → Accept multiple texts in one request
4. **Add file upload** → Accept `.txt`, `.md`, `.pdf` files
5. **Add caching** → Cache results for repeated texts with Redis
6. **Add rate limiting** → Limit requests per API key
7. **Add authentication** → JWT-based API key system

---

## 📝 What to Add to Your Resume

> **Dockerized Text Analysis API** — Built a production-ready RESTful API using FastAPI with endpoints for text statistics, keyword extraction, readability analysis, and extractive summarization. Containerized with Docker using multi-stage builds (250MB image), added health checks, CORS support, and comprehensive Pydantic validation. Tested with pytest achieving 95%+ coverage. Pushed to GitHub with conventional commits and CI-ready structure.

**Keywords:** FastAPI, Docker, REST API, Pydantic, Python, Text Processing, NLP, Containerization, Testing

---

<div align="center">

[← Topic 5: Docker](../topic-5-docker/) · [🏠 Home](../../README.md) · [📋 Phase 1](../README.md)

**[Phase 2: Databases →](../../phase-2-databases/)**

</div>
