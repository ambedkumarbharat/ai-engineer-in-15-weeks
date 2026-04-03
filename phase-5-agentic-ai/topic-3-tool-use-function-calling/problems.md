<div align="center">

# 🧩 Tool Use & Function Calling — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > [Tool Use](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Basic Tool Definition**
Define 3 tools with LangChain's `@tool` decorator: web search, calculator, and date/time. Include Pydantic schemas for input validation.

## Problem 02 🧩 **🟢 Easy** — **Tool Execution Handler**
Build a handler that receives LLM tool call responses, matches them to registered tools, executes them, and returns results.

## Problem 03 🧩 **🟢 Easy** — **OpenAI Function Calling**
Use OpenAI's function calling API to build an assistant that can look up user info and check order status using two defined functions.

## Problem 04 🧩 **🟡 Medium** — **Database Query Tool**
Build a tool that converts natural language to SQL, executes it safely (read-only), and returns formatted results. Prevent SQL injection.

## Problem 05 🧩 **🟡 Medium** — **API Integration Tool**
Build a tool that calls an external REST API (e.g., weather, news). Handle authentication, rate limiting, error responses, and response parsing.

## Problem 06 🧩 **🟡 Medium** — **File System Tools**
Build a set of file tools: read file, write file, list directory, search files. Add safety constraints (no access outside allowed directories).

## Problem 07 🧩 **🟡 Medium** — **Tool Chain**
Build a system where tools can call other tools: search → extract data → save to database. Handle nested tool execution.

## Problem 08 🧩 **🔴 Hard** — **Dynamic Tool Discovery**
Build a system where agents can discover and use new tools at runtime by reading tool documentation from a registry.

## Problem 09 🧩 **🔴 Hard** — **Tool Reliability Layer**
Add reliability to tools: retry failed calls, cache successful results, circuit breaker for down services, timeout handling.

## Problem 10 🧩 **🔴 Hard** — **Custom MCP-Style Tool Server**
Build a tool server that exposes tools over HTTP. Agents discover available tools via `/tools` endpoint and call them via `/execute`.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 5](../README.md)

</div>
