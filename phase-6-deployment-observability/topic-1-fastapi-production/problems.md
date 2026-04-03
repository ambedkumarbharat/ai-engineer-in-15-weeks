<div align="center">

# 🧩 FastAPI Production — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > [FastAPI Production](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Pydantic Settings**
Set up `pydantic-settings` for an AI API with environment variables: API keys, database URLs, model configs, debug mode. Load from `.env`.

## Problem 02 🧩 **🟢 Easy** — **Health Check Endpoint**
Build a `/health` endpoint that checks all dependencies (database, Redis, LLM provider) and returns status for each.

## Problem 03 🧩 **🟢 Easy** — **Structured Logging**
Replace all `print()` statements with structured JSON logging. Include timestamp, level, module, request_id, and latency.

## Problem 04 🧩 **🟡 Medium** — **API Key Authentication**
Implement API key authentication with middleware: validate keys against database, track usage per key, reject invalid keys with 401.

## Problem 05 🧩 **🟡 Medium** — **Error Handling Middleware**
Build comprehensive error handling: catch all exceptions, return sanitized error responses, log full stack traces, never expose internals.

## Problem 06 🧩 **🟡 Medium** — **Request/Response Logging**
Build middleware that logs every request (method, path, status, latency, request_id) and response (status, size) in structured JSON format.

## Problem 07 🧩 **🟡 Medium** — **Gunicorn Configuration**
Configure Gunicorn with multiple Uvicorn workers. Benchmark performance difference between 1, 2, 4, and 8 workers.

## Problem 08 🧩 **🔴 Hard** — **Graceful Shutdown**
Implement graceful shutdown: finish in-flight requests, close database connections, flush logs, then exit cleanly.

## Problem 09 🧩 **🔴 Hard** — **Background Task Queue**
Implement background tasks for long-running operations (document processing, embedding generation) with status polling endpoints.

## Problem 10 🧩 **🔴 Hard** — **API Versioning**
Implement API versioning (v1, v2) with backward compatibility, deprecation warnings, and migration guides.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 6](../README.md)

</div>
