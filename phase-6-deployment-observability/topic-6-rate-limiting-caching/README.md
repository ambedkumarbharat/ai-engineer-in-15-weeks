<div align="center">

# 🛡️ Topic 6: Rate Limiting & Caching
![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red) ![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > **Rate Limiting & Caching**

---

## 🎯 Why This Matters

Without rate limiting, one user can exhaust your LLM budget. Without caching, you pay for the same LLM call repeatedly. Both are essential for production AI APIs.

---

## 📚 Core Concepts

### FastAPI Rate Limiting Middleware

`python
from fastapi import FastAPI, Request, HTTPException
import redis
import time

app = FastAPI()
r = redis.Redis(host="localhost", port=6379, decode_responses=True)

@app.middleware("http")
async def rate_limit_middleware(request: Request, call_next):
    client_ip = request.client.host
    key = f"ratelimit:{client_ip}"
    
    current = r.get(key)
    if current and int(current) >= 100:  # 100 requests per minute
        raise HTTPException(status_code=429, detail="Rate limit exceeded")
    
    pipe = r.pipeline()
    pipe.incr(key)
    pipe.expire(key, 60)
    pipe.execute()
    
    response = await call_next(request)
    return response
`

### LLM Response Caching

`python
import hashlib
import json

class LLMCache:
    def __init__(self, redis_client, ttl_hours: int = 24):
        self.redis = redis_client
        self.ttl = ttl_hours * 3600
    
    def _key(self, prompt: str, model: str, temp: float) -> str:
        content = f"{model}:{temp}:{prompt}"
        return f"llm:{hashlib.sha256(content.encode()).hexdigest()[:16]}"
    
    def get(self, prompt: str, model: str = "gpt-4", temp: float = 0.7):
        cached = self.redis.get(self._key(prompt, model, temp))
        return json.loads(cached) if cached else None
    
    def set(self, prompt: str, response: str, model: str = "gpt-4", temp: float = 0.7):
        self.redis.setex(self._key(prompt, model, temp), self.ttl, json.dumps({"response": response}))
`

---

> 💡 **Pro Tip:** Cache with `temperature=0` only. Non-deterministic responses (temp > 0) shouldn't be cached since users expect variety. For rate limiting, use per-user limits (API key) not per-IP (users behind NAT share IPs).

---

<div align="center">

[← Topic 5](../topic-5-cicd-github-actions/) · [📋 Phase 6](../README.md) · [🔨 Mini Project →](../mini-project/)

</div>
