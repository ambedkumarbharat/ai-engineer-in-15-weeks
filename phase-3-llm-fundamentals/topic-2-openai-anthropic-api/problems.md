<div align="center">

# 🧩 OpenAI & Anthropic APIs — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > [APIs](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Basic Chat Completion
**Problem:** Write a function that takes a user message, calls the OpenAI API, and returns the response. Add error handling for rate limits, timeouts, and invalid API keys.
**Hint:** Use `try/except` blocks for `RateLimitError`, `APITimeoutError`, `AuthenticationError`.

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Streaming Response Handler
**Problem:** Implement streaming chat that displays tokens one by one (like ChatGPT). Track and display total tokens and estimated cost at the end.
**Hint:** Use `stream=True` and iterate over `chunk.choices[0].delta.content`.

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Multi-Model Wrapper
**Problem:** Build a unified LLM class that works with both OpenAI and Ollama using the same interface. Automatically fall back to Ollama if the OpenAI key is missing.
**Hint:** Both support the OpenAI client format — just change `base_url` for Ollama.

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Function Calling — Weather + Calculator
**Problem:** Create an LLM assistant with two tools (weather lookup, calculator). The LLM should decide which tool to call, execute it, and give the final answer.
**Hint:** Define tools with JSON schema, pass to `tools` parameter, handle `tool_calls` in response.

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Structured Output Extractor
**Problem:** Build a system that extracts structured data from unstructured text (job descriptions → JSON with title, skills, salary, location) using `response_format={"type": "json_object"}`.
**Hint:** Define a Pydantic model and include the JSON schema in the system prompt.

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Retry Logic with Exponential Backoff
**Problem:** Build a production-grade LLM caller with retry logic, exponential backoff, circuit breaker pattern, and request logging.
**Hint:** Retry on 429/500/503. Backoff: wait 2^attempt seconds. Circuit breaker: stop after 5 consecutive failures.

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Token Usage Tracker
**Problem:** Build a token usage tracker that logs every API call (model, tokens, cost, latency) and provides a summary dashboard showing daily cost and usage trends.
**Hint:** Store call logs in a list/SQLite. Calculate cost = input_tokens * input_price + output_tokens * output_price.

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Parallel LLM Calls
**Problem:** Implement async parallel LLM calls that process 50 prompts concurrently with a semaphore (max 10 concurrent), progress bar, error handling, and cost tracking.
**Hint:** Use `asyncio.Semaphore(10)` and `asyncio.gather()` with `return_exceptions=True`.

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Model Comparison Benchmark
**Problem:** Build a benchmark that compares GPT-3.5, GPT-4, and Ollama on 30 test prompts across quality, latency, cost, and consistency metrics.
**Hint:** Run each 3 times. Use LLM-as-judge (GPT-4) to score quality 1-10. Track p50/p95 latency.

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### API Gateway with Fallback Chain
**Problem:** Build an API gateway that routes LLM requests through a fallback chain: GPT-4 → GPT-3.5 → Ollama. Include caching, rate limiting, and cost optimization.
**Hint:** Try primary provider, on failure/timeout try next. Cache successful responses in Redis.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 3](../README.md)

</div>
