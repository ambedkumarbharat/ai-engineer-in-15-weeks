<div align="center">

# 📋 Cheatsheets

### Quick reference cards for every technology in the roadmap

</div>

---

> 🏠 [Home](../../README.md) > [Resources](../README.md) > **Cheatsheets**

---

## 🐍 Python for AI

```python
# Type Hints
def process(text: str, chunks: list[str], config: dict[str, int] | None = None) -> list[float]: ...

# List/Dict Comprehensions
squares = [x**2 for x in range(10) if x % 2 == 0]
word_freq = {w: text.count(w) for w in set(text.split())}

# Dataclasses
from dataclasses import dataclass, field
@dataclass
class Chunk:
    content: str
    index: int
    metadata: dict = field(default_factory=dict)

# Async
import asyncio
async def fetch_all(urls: list[str]):
    tasks = [fetch(url) for url in urls]
    return await asyncio.gather(*tasks)

# Context Managers
from contextlib import contextmanager
@contextmanager
def timer(label: str):
    start = time.time()
    yield
    print(f"{label}: {time.time()-start:.2f}s")
```

---

## ⚡ FastAPI

```python
from fastapi import FastAPI, HTTPException, Depends, Query
from pydantic import BaseModel, Field

app = FastAPI(title="AI API")

class Input(BaseModel):
    text: str = Field(..., min_length=1, max_length=5000)

@app.post("/api/v1/analyze")
async def analyze(data: Input):
    return {"result": process(data.text)}

@app.get("/items/{id}")
async def get_item(id: int, q: str = Query(None)):
    return {"id": id, "query": q}

# Dependency injection
def get_db():
    db = SessionLocal()
    try: yield db
    finally: db.close()

@app.get("/users")
async def users(db = Depends(get_db)):
    return db.query(User).all()
```

---

## 🐘 PostgreSQL + SQLAlchemy

```python
# SQLAlchemy Model
from sqlalchemy import Column, Integer, String, ForeignKey, create_engine
from sqlalchemy.orm import declarative_base, relationship, sessionmaker

Base = declarative_base()
class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    email = Column(String, unique=True, index=True)
    conversations = relationship("Conversation", back_populates="user")

engine = create_engine("postgresql://user:pass@localhost/db", pool_size=10)
Session = sessionmaker(bind=engine)
```

```sql
-- Essential SQL
SELECT * FROM users WHERE tier = 'pro' ORDER BY created_at DESC LIMIT 10;
INSERT INTO users (email, name) VALUES ('a@b.com', 'Dev') RETURNING id;
UPDATE users SET tokens_used = tokens_used + 100 WHERE id = 1;
CREATE INDEX idx_users_email ON users(email);
```

---

## 🔴 Redis

```python
import redis
r = redis.Redis(host='localhost', port=6379, decode_responses=True)

r.set("key", "value")                    # SET
r.setex("key", 3600, "value")            # SET with TTL (1 hour)
r.get("key")                             # GET
r.hset("user:1", mapping={"name": "A"})  # Hash SET
r.hgetall("user:1")                      # Hash GET all
r.incr("counter")                        # Atomic increment
r.expire("key", 60)                      # Set TTL
r.delete("key")                          # DELETE

# Pipeline (batch operations)
pipe = r.pipeline()
pipe.set("a", 1)
pipe.set("b", 2)
pipe.execute()  # One round trip!
```

---

## 🍃 MongoDB

```python
from pymongo import MongoClient
client = MongoClient("mongodb://localhost:27017")
db = client["mydb"]
col = db["docs"]

col.insert_one({"title": "Doc", "tags": ["ai"]})           # Insert
col.find({"tags": "ai"}).sort("created", -1).limit(10)     # Query
col.update_one({"_id": id}, {"$set": {"status": "done"}})  # Update
col.delete_many({"status": "old"})                          # Delete
col.aggregate([{"$match": {}}, {"$group": {"_id": "$tag", "count": {"$sum": 1}}}])
```

---

## 📐 ChromaDB (Vector Database)

```python
import chromadb
client = chromadb.PersistentClient(path="./chroma_data")
col = client.get_or_create_collection("kb")

col.add(documents=["text"], ids=["id1"], metadatas=[{"source": "doc"}])
results = col.query(query_texts=["search"], n_results=5, where={"source": "doc"})
col.delete(ids=["id1"])
col.count()
```

---

## 🦜 LangChain / LCEL

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser, JsonOutputParser

llm = ChatOpenAI(model="gpt-4")
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are {role}."),
    ("user", "{input}"),
])

# LCEL chain
chain = prompt | llm | StrOutputParser()
result = chain.invoke({"role": "tutor", "input": "What is RAG?"})

# Batch
results = chain.batch([{"role": "tutor", "input": q} for q in questions])

# Stream
for chunk in chain.stream({"role": "tutor", "input": "Explain agents"}):
    print(chunk, end="")
```

---

## 🔄 LangGraph

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict

class State(TypedDict):
    messages: list[str]
    next: str

def node_a(state): return {"messages": ["from A"], "next": "b"}
def node_b(state): return {"messages": ["from B"], "next": "end"}
def router(state): return state["next"]

graph = StateGraph(State)
graph.add_node("a", node_a)
graph.add_node("b", node_b)
graph.set_entry_point("a")
graph.add_conditional_edges("a", router, {"b": "b", "end": END})
graph.add_edge("b", END)
app = graph.compile()
```

---

## 🐳 Docker

```dockerfile
# Multi-stage Dockerfile
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY . .
EXPOSE 8000
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```bash
docker build -t myapp .                    # Build
docker run -p 8000:8000 myapp              # Run
docker compose up -d                       # Compose up
docker compose down                        # Compose down
docker compose logs -f api                 # View logs
docker exec -it container_name bash        # Shell into container
```

---

## 🦙 Ollama

```bash
ollama pull llama3                         # Download model
ollama run llama3                          # Interactive chat
ollama list                                # List models
ollama rm model_name                       # Delete model
ollama create mymodel -f Modelfile         # Custom model
# API: http://localhost:11434/v1/chat/completions (OpenAI-compatible)
```

```python
from openai import OpenAI
client = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")
response = client.chat.completions.create(model="llama3", messages=[...])
```

---

## 🔧 Git

```bash
git init && git checkout -b main
git add . && git commit -m "feat: initial commit"
git push origin main
git checkout -b feature/new-feature
git merge feature/new-feature
git log --oneline -10
git stash && git stash pop
```

---

<div align="center">

[📚 Resources](../README.md) · [🏠 Home](../../README.md)

</div>
