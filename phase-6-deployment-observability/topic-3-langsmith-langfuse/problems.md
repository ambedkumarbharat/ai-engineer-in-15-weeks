<div align="center">

# 🧩 LangSmith & Langfuse — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > [Observability](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **LangSmith Setup**
Set up LangSmith tracing for a LangChain project. Send 10 test traces and explore them in the LangSmith UI.

## Problem 02 🧩 **🟢 Easy** — **Custom Trace Metadata**
Add custom metadata to LangSmith traces: user_id, session_id, model, prompt_version. Query traces by metadata.

## Problem 03 🧩 **🟡 Medium** — **Prompt A/B Testing**
Use LangSmith to A/B test two prompt variants. Track quality metrics, latency, and cost for each variant.

## Problem 04 🧩 **🟡 Medium** — **Error Monitoring Dashboard**
Build alerts for: LLM failures, high latency (> 5s), high cost (> $0.10/call), and low quality scores using LangSmith data.

## Problem 05 🧩 **🟡 Medium** — **Langfuse Self-Host**
Deploy Langfuse locally with Docker. Integrate with your FastAPI app. Compare with LangSmith.

## Problem 06 🧩 **🟡 Medium** — **Evaluation Pipeline**
Build an automated evaluation pipeline: run 50 test queries daily, log to LangSmith, compare with baseline, alert on regression.

## Problem 07 🧩 **🔴 Hard** — **Cost Attribution**
Build a system that attributes LLM costs to specific features, users, and endpoints using trace data.

## Problem 08 🧩 **🔴 Hard** — **Real-Time Monitoring**
Build a real-time monitoring dashboard (Streamlit) showing: active traces, error rate, avg latency, token usage, cost per hour.

## Problem 09 🧩 **🔴 Hard** — **Trace Analysis Pipeline**
Build a pipeline that analyzes traces to find: slowest steps, most expensive operations, and failure patterns.

## Problem 10 🧩 **🔴 Hard** — **Custom Observability SDK**
Build a custom tracing SDK (without LangSmith/Langfuse) that logs all LLM calls to PostgreSQL with analysis queries.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 6](../README.md)

</div>
