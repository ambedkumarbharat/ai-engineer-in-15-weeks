<div align="center">

# 🔴 Topic 2: Redis

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-2_Databases-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > **Redis**

---

## 🎯 Why This Matters for AI Systems Engineering

Redis is the **performance layer** of AI systems. It's an in-memory data store used for:
- **Caching LLM responses** → Avoid re-calling expensive APIs for identical prompts (~$0.03/call → $0)
- **Rate limiting** → Prevent API abuse (e.g., 100 requests/minute per user)
- **Session management** → Track conversation state across requests
- **Pub/Sub** → Real-time event streaming between services
- **Embedding cache** → Cache computed embeddings to save cost and latency
- **Job queues** → Background task management with tools like Celery

Redis reduces your AI API costs by 30-70% through intelligent caching.

---

## 📚 Core Concepts

### 1. Redis Basics with Python

```python
import redis
import json
from datetime import timedelta

# Connect to Redis
r = redis.Redis(host='localhost', port=6379, db=0, decode_responses=True)

# Basic operations
r.set("model:default", "gpt-4")
r.set("model:fallback", "gpt-3.5-turbo")
print(r.get("model:default"))  # "gpt-4"

# Set with expiration (TTL)
r.setex("token:user_123", timedelta(hours=1), "jwt_token_here")
print(r.ttl("token:user_123"))  # seconds remaining

# Hashes — perfect for structured data
r.hset("user:42", mapping={
    "name": "AI Developer",
    "email": "dev@example.com",
    "tier": "pro",
    "tokens_used": "1500",
    "tokens_limit": "50000",
})
user = r.hgetall("user:42")
print(user)  # {'name': 'AI Developer', 'email': 'dev@example.com', ...}

# Increment counters atomically
r.hincrby("user:42", "tokens_used", 150)  # Add 150 tokens
```

### 2. LLM Response Caching

```python
import redis
import hashlib
import json
import time

class LLMCache:
    """Cache LLM responses to save cost and latency."""
    
    def __init__(self, redis_url: str = "redis://localhost:6379", ttl_hours: int = 24):
        self.redis = redis.from_url(redis_url, decode_responses=True)
        self.ttl = ttl_hours * 3600
        self.stats = {"hits": 0, "misses": 0}
    
    def _make_key(self, prompt: str, model: str, temperature: float) -> str:
        """Create a deterministic cache key."""
        content = f"{model}:{temperature}:{prompt}"
        hash_val = hashlib.sha256(content.encode()).hexdigest()[:16]
        return f"llm:cache:{hash_val}"
    
    def get(self, prompt: str, model: str = "gpt-4", temperature: float = 0.7) -> str | None:
        """Get cached response if available."""
        key = self._make_key(prompt, model, temperature)
        cached = self.redis.get(key)
        
        if cached:
            self.stats["hits"] += 1
            data = json.loads(cached)
            return data["response"]
        
        self.stats["misses"] += 1
        return None
    
    def set(self, prompt: str, response: str, model: str = "gpt-4", 
            temperature: float = 0.7, tokens_used: int = 0, cost: float = 0.0):
        """Cache an LLM response."""
        key = self._make_key(prompt, model, temperature)
        data = {
            "response": response,
            "model": model,
            "tokens_used": tokens_used,
            "cost": cost,
            "cached_at": time.time(),
        }
        self.redis.setex(key, self.ttl, json.dumps(data))
    
    @property
    def hit_rate(self) -> float:
        total = self.stats["hits"] + self.stats["misses"]
        return self.stats["hits"] / total if total > 0 else 0.0
    
    def get_savings(self) -> dict:
        """Calculate cost savings from caching."""
        return {
            "cache_hits": self.stats["hits"],
            "cache_misses": self.stats["misses"],
            "hit_rate": f"{self.hit_rate:.1%}",
            "estimated_savings": f"${self.stats['hits'] * 0.03:.2f}",
        }

# Usage
cache = LLMCache()

# First call — cache miss
prompt = "Explain RAG in simple terms"
response = cache.get(prompt)
if not response:
    response = "RAG stands for Retrieval Augmented Generation..."  # Actual LLM call
    cache.set(prompt, response, tokens_used=150, cost=0.003)

# Second call — cache hit!
response = cache.get(prompt)  # Returns cached response instantly
print(cache.get_savings())
```

### 3. Rate Limiting with Sliding Window

```python
import redis
import time

class RateLimiter:
    """Token bucket rate limiter using Redis sorted sets."""
    
    def __init__(self, redis_url: str = "redis://localhost:6379"):
        self.redis = redis.from_url(redis_url, decode_responses=True)
    
    def is_allowed(self, user_id: str, max_requests: int = 10, 
                   window_seconds: int = 60) -> tuple[bool, dict]:
        """Check if a request is allowed under rate limits."""
        key = f"ratelimit:{user_id}"
        now = time.time()
        window_start = now - window_seconds
        
        pipe = self.redis.pipeline()
        # Remove old entries
        pipe.zremrangebyscore(key, 0, window_start)
        # Add current request
        pipe.zadd(key, {str(now): now})
        # Count requests in window
        pipe.zcard(key)
        # Set expiration
        pipe.expire(key, window_seconds)
        
        results = pipe.execute()
        request_count = results[2]
        
        allowed = request_count <= max_requests
        remaining = max(0, max_requests - request_count)
        
        return allowed, {
            "allowed": allowed,
            "remaining": remaining,
            "limit": max_requests,
            "window_seconds": window_seconds,
            "retry_after": window_seconds if not allowed else 0,
        }

# Usage
limiter = RateLimiter()
for i in range(12):
    allowed, info = limiter.is_allowed("user_42", max_requests=10, window_seconds=60)
    print(f"Request {i+1}: {'✅' if allowed else '❌'} | Remaining: {info['remaining']}")
```

### 4. Session Management

```python
import redis
import json
import secrets
from datetime import timedelta

class SessionManager:
    """Manage user sessions and conversation state."""
    
    def __init__(self, redis_url: str = "redis://localhost:6379"):
        self.redis = redis.from_url(redis_url, decode_responses=True)
        self.session_ttl = timedelta(hours=24)
    
    def create_session(self, user_id: str, data: dict = None) -> str:
        """Create a new session."""
        session_id = secrets.token_urlsafe(32)
        key = f"session:{session_id}"
        
        session_data = {
            "user_id": user_id,
            "created_at": str(time.time()),
            "conversation_history": [],
            **(data or {}),
        }
        
        self.redis.setex(key, self.session_ttl, json.dumps(session_data))
        return session_id
    
    def get_session(self, session_id: str) -> dict | None:
        """Retrieve session data."""
        key = f"session:{session_id}"
        data = self.redis.get(key)
        return json.loads(data) if data else None
    
    def add_message(self, session_id: str, role: str, content: str):
        """Add a message to conversation history."""
        session = self.get_session(session_id)
        if not session:
            raise ValueError("Session not found")
        
        session["conversation_history"].append({
            "role": role,
            "content": content,
            "timestamp": time.time(),
        })
        
        key = f"session:{session_id}"
        self.redis.setex(key, self.session_ttl, json.dumps(session))
    
    def destroy_session(self, session_id: str):
        """Delete a session."""
        self.redis.delete(f"session:{session_id}")

# Usage
import time
sessions = SessionManager()
sid = sessions.create_session("user_42", {"model": "gpt-4"})
sessions.add_message(sid, "user", "What is RAG?")
sessions.add_message(sid, "assistant", "RAG stands for...")
session = sessions.get_session(sid)
print(f"Messages: {len(session['conversation_history'])}")
```

### 5. Pub/Sub for Real-Time Events

```python
import redis
import json
import threading

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# Publisher — sends events when things happen
def publish_event(channel: str, event_type: str, data: dict):
    """Publish an event to a Redis channel."""
    message = json.dumps({"type": event_type, "data": data, "timestamp": time.time()})
    r.publish(channel, message)

# Subscriber — listens for events
def subscribe_to_events(channel: str, callback):
    """Subscribe to events on a channel."""
    pubsub = r.pubsub()
    pubsub.subscribe(channel)
    
    for message in pubsub.listen():
        if message["type"] == "message":
            event = json.loads(message["data"])
            callback(event)

# Example: Document processing events
import time

def on_document_event(event):
    print(f"  📨 Event: {event['type']} — {event['data']}")

# Start subscriber in background thread
thread = threading.Thread(
    target=subscribe_to_events,
    args=("documents", on_document_event),
    daemon=True,
)
thread.start()

# Publish events
time.sleep(0.5)
publish_event("documents", "uploaded", {"doc_id": "doc_1", "size": 1024})
publish_event("documents", "embedded", {"doc_id": "doc_1", "chunks": 5})
publish_event("documents", "indexed", {"doc_id": "doc_1", "status": "ready"})
time.sleep(1)
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Redis Crash Course** | 📹 YouTube | [Traversy Media - Redis](https://www.youtube.com/watch?v=jgpVdJB2sKQ) |
| **Redis University (Free)** | 📄 Course | [university.redis.com](https://university.redis.com/) |
| **redis-py Documentation** | 📄 Docs | [redis-py.readthedocs.io](https://redis-py.readthedocs.io/) |
| **Redis Best Practices** | 📄 Docs | [redis.io/docs/management/optimization](https://redis.io/docs/management/optimization/) |
| **Redis in 100 Seconds** | 📹 YouTube | [Fireship - Redis](https://www.youtube.com/watch?v=G1rOthIU-uo) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| No TTL on cache keys | Memory fills up forever | Always set expiration with `setex` |
| Storing large objects | Redis is in-memory, expensive | Cache only what you need, not entire responses |
| Not using pipelines | Multiple round trips to Redis | Use `pipeline()` for batch operations |
| Plain text API keys | Inspectable with `redis-cli` | Hash sensitive data before storing |
| Single Redis for everything | Cache eviction deletes sessions | Use separate Redis databases or instances |
| Not handling connection failures | App crashes when Redis is down | Add try/except and fallback logic |

---

## 📋 Quick Reference Cheatsheet

| Command | Python | Use Case |
|---------|--------|----------|
| `SET key val` | `r.set("k", "v")` | Store a value |
| `GET key` | `r.get("k")` | Retrieve value |
| `SETEX key ttl val` | `r.setex("k", 3600, "v")` | Store with expiry |
| `DEL key` | `r.delete("k")` | Delete key |
| `HSET key field val` | `r.hset("k", "f", "v")` | Hash field |
| `HGETALL key` | `r.hgetall("k")` | Get all hash fields |
| `INCR key` | `r.incr("k")` | Atomic increment |
| `EXPIRE key seconds` | `r.expire("k", 60)` | Set TTL |
| `TTL key` | `r.ttl("k")` | Check remaining TTL |
| `ZADD key score member` | `r.zadd("k", {"m": 1.0})` | Sorted set add |
| `PUBLISH channel msg` | `r.publish("ch", "msg")` | Pub/sub publish |
| `KEYS pattern` | `r.keys("prefix:*")` | Find keys (⚠️ slow) |

---

> 💡 **Pro Tip:** In production, use `SCAN` instead of `KEYS` (which blocks Redis). Set up Redis Sentinel or Redis Cluster for high availability. For AI applications, a common pattern is: check Redis cache → if miss, call LLM → cache response. This alone can save 50%+ on LLM API costs.

---

<div align="center">

[← Topic 1: PostgreSQL](../topic-1-postgresql/) · [🏠 Home](../../README.md) · [📋 Phase 2](../README.md)

**[▶ Problems](problems.md)** · [Topic 3: MongoDB →](../topic-3-mongodb/)

</div>
