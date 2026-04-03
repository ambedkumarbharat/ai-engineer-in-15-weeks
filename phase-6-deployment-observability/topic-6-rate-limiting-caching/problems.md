<div align="center">

# 🧩 Rate Limiting & Caching — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > [Rate Limiting](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Fixed Window Rate Limiter**
Build a fixed window rate limiter with Redis: 100 requests per minute per user. Return 429 with Retry-After header when exceeded.

## Problem 02 🧩 **🟢 Easy** — **Simple Response Cache**
Cache LLM responses in Redis with a deterministic key (hash of prompt + model + temperature). Set 1-hour TTL.

## Problem 03 🧩 **🟡 Medium** — **Sliding Window Rate Limiter**
Implement a sliding window rate limiter using Redis sorted sets. More accurate than fixed window at window boundaries.

## Problem 04 🧩 **🟡 Medium** — **Tiered Rate Limits**
Implement per-tier rate limits: free (10/min), pro (100/min), enterprise (1000/min). Look up user tier from database.

## Problem 05 🧩 **🟡 Medium** — **Cache Invalidation Strategy**
Build a cache system with smart invalidation: TTL-based expiry, manual invalidation API, and auto-invalidate when source documents change.

## Problem 06 🧩 **🟡 Medium** — **Rate Limit Dashboard**
Build a dashboard showing: current rate limit status per user, top consumers, requests blocked, and usage patterns.

## Problem 07 🧩 **🔴 Hard** — **Token Bucket Rate Limiter**
Implement a token bucket rate limiter that allows burst traffic while maintaining average rate. Compare with sliding window.

## Problem 08 🧩 **🔴 Hard** — **Multi-Level Cache**
Implement L1 (in-memory, 1min) → L2 (Redis, 1hr) → L3 (database) caching with cascading lookups and cache population.

## Problem 09 🧩 **🔴 Hard** — **Distributed Rate Limiting**
Build rate limiting that works across multiple API server instances sharing a single Redis backend.

## Problem 10 🧩 **🔴 Hard** — **Adaptive Rate Limiting**
Build a rate limiter that adjusts limits based on system load: reduce limits when LLM provider is slow, increase when system is healthy.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 6](../README.md)

</div>
