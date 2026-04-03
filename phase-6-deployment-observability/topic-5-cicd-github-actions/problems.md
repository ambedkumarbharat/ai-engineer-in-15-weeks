<div align="center">

# 🧩 CI/CD with GitHub Actions — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > [CI/CD](README.md) > **Problems**

---

## Problem 01 🧩 **🟢 Easy** — **Basic Test Workflow**
Create a GitHub Actions workflow that runs pytest on every push and pull request. Show pass/fail status badge in README.

## Problem 02 🧩 **🟢 Easy** — **Lint + Format Check**
Add ruff (linting) and black (formatting) checks to your CI pipeline. Block merge if either fails.

## Problem 03 🧩 **🟡 Medium** — **Docker Build + Push**
Build a workflow that builds a Docker image, tags it with the git commit SHA, and pushes to GitHub Container Registry.

## Problem 04 🧩 **🟡 Medium** — **Auto-Deploy on Merge**
Set up auto-deployment to Render when code is merged to main. Include a post-deploy health check.

## Problem 05 🧩 **🟡 Medium** — **Matrix Testing**
Run tests across multiple Python versions (3.10, 3.11, 3.12) and operating systems (Ubuntu, macOS) using a matrix strategy.

## Problem 06 🧩 **🟡 Medium** — **Secret Management**
Set up GitHub Secrets for API keys and database URLs. Use them in workflows without exposing in logs.

## Problem 07 🧩 **🔴 Hard** — **Staging + Production Pipeline**
Build a 2-stage pipeline: push to staging on PR, deploy to production on merge to main. Include smoke tests between stages.

## Problem 08 🧩 **🔴 Hard** — **RAG Evaluation in CI**
Add a CI step that runs RAG evaluation tests (50 test queries) and blocks merge if accuracy drops below 85%.

## Problem 09 🧩 **🔴 Hard** — **Rollback Automation**
Build a rollback workflow that can revert to the previous deployment if the health check fails after deploy.

## Problem 10 🧩 **🔴 Hard** — **Full DevOps Pipeline**
Build a complete pipeline: lint → type check → test → build Docker → push registry → deploy staging → smoke test → deploy production.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 6](../README.md)

</div>
