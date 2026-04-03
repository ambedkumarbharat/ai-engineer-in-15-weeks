<div align="center">

# 🧩 Cost & Latency Optimization — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > [Cost Optimization](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Cost Calculator**
Build a cost calculator that takes prompt + model and returns estimated cost. Support 5 different models with current pricing.

## Problem 02 🧩 **🟢 Easy** — **Token Budget Manager**
Build a budget manager that tracks spending, alerts at 80% threshold, and blocks requests at 100% of daily budget.

## Problem 03 🧩 **🟡 Medium** — **Model Router**
Build a model router that classifies query complexity and routes to GPT-3.5 (simple), GPT-4 (complex), or Ollama (development).

## Problem 04 🧩 **🟡 Medium** — **Response Cache**
Build an LLM response cache with Redis. Measure cache hit rate and cost savings over 1000 queries.

## Problem 05 🧩 **🟡 Medium** — **Prompt Compression**
Build a prompt compressor that reduces token count by 20-30% while maintaining response quality. Measure quality vs savings.

## Problem 06 🧩 **🟡 Medium** — **Latency Profiler**
Build a profiler that breaks down latency into components: embedding time, retrieval time, LLM time, serialization time.

## Problem 07 🧩 **🔴 Hard** — **Streaming Latency Optimizer**
Compare time-to-first-token vs total latency across models. Implement streaming to minimize perceived latency.

## Problem 08 🧩 **🔴 Hard** — **Cost Prediction Model**
Build a model that predicts monthly LLM costs based on current usage patterns. Alert if projected spend exceeds budget.

## Problem 09 🧩 **🔴 Hard** — **Multi-Provider Arbitrage**
Build a system that routes requests to the cheapest provider (OpenAI, Anthropic, Ollama) that meets quality requirements.

## Problem 10 🧩 **🔴 Hard** — **Full Optimization Suite**
Combine caching + model routing + prompt compression + batching. Measure total cost reduction on a realistic workload of 10K queries.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 6](../README.md)

</div>
