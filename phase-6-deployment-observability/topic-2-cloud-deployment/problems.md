<div align="center">

# 🧩 Cloud Deployment — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > [Cloud Deployment](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Docker Production Image**
Build a multi-stage Docker image for your AI API. Optimize for small image size (< 300MB) and fast builds.

## Problem 02 🧩 **🟢 Easy** — **Docker Compose for Full Stack**
Create a docker-compose.yml that runs your API + PostgreSQL + Redis + ChromaDB. Use health checks and dependency ordering.

## Problem 03 🧩 **🟢 Easy** — **Environment Variable Management**
Set up `.env`, `.env.example`, and `.env.production` files. Validate all required env vars are set at startup.

## Problem 04 🧩 **🟡 Medium** — **Deploy to Render**
Deploy your AI API to Render's free tier. Set up auto-deploy from GitHub, configure environment variables, and verify health endpoint.

## Problem 05 🧩 **🟡 Medium** — **Deploy to Railway**
Deploy the same API to Railway. Compare setup experience, cold start time, and pricing with Render.

## Problem 06 🧩 **🟡 Medium** — **Custom Domain + HTTPS**
Set up a custom domain for your deployed API with HTTPS. Configure DNS records and verify SSL.

## Problem 07 🧩 **🔴 Hard** — **Zero-Downtime Deployment**
Implement zero-downtime deployment using rolling updates. Verify no requests are dropped during deployment.

## Problem 08 🧩 **🔴 Hard** — **Auto-Scaling Configuration**
Configure auto-scaling rules: scale up when CPU > 70%, scale down when CPU < 30%. Test with load testing.

## Problem 09 🧩 **🔴 Hard** — **Multi-Region Deployment**
Deploy the same API to 2 regions (US, EU). Set up geographic routing based on client location.

## Problem 10 🧩 **🔴 Hard** — **Disaster Recovery**
Set up database backups, snapshot recovery, and a runbook for recovering from: database failure, cache failure, and LLM provider outage.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 6](../README.md)

</div>
