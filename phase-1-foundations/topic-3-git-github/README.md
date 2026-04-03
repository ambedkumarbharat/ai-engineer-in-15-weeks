<div align="center">

# 🔀 Topic 3: Git & GitHub

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-2_Days-blue)
![Phase](https://img.shields.io/badge/Phase-1_Foundations-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > **Git & GitHub**

---

## 🎯 Why This Matters for AI Systems Engineering

Version control isn't just about code — in AI systems, you track **prompts**, **model configurations**, **evaluation results**, and **pipeline definitions**. Git is the backbone of:
- **Collaboration** → Multiple engineers working on the same AI pipeline
- **Reproducibility** → Knowing exactly which prompt version produced which results
- **CI/CD** → Auto-deploying AI services on every push
- **Experiments** → Branching for different model/prompt experiments

---

## 📚 Core Concepts

### 1. Git Fundamentals for AI Projects

```bash
# Initialize a new AI project
git init ai-rag-pipeline
cd ai-rag-pipeline

# Configure (do this once)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# The essential workflow
git status                    # Check what's changed
git add .                     # Stage all changes
git commit -m "feat: add RAG pipeline with chunking"  # Commit
git push origin main          # Push to GitHub
```

### 2. Branching Strategy for AI Projects

```bash
# Feature branch workflow
git checkout -b feature/add-embedding-cache
# ... make changes ...
git add .
git commit -m "feat: add Redis embedding cache"
git push origin feature/add-embedding-cache
# Create Pull Request on GitHub

# Experiment branches (great for AI)
git checkout -b experiment/gpt4-vs-claude
# ... test different models ...
git commit -m "experiment: GPT-4 achieves 85% accuracy vs Claude's 82%"

# Hotfix for production
git checkout -b hotfix/rate-limit-fix
git commit -m "fix: increase rate limit retry delay to 5s"
git push origin hotfix/rate-limit-fix
```

### 3. Commit Message Convention

```bash
# Format: <type>(<scope>): <description>
#
# Types for AI projects:
# feat     — new feature (new endpoint, new tool for agent)
# fix      — bug fix (fix token counting, fix chunk overlap)
# docs     — documentation (update API docs, add examples)
# refactor — code restructuring (refactor RAG pipeline)
# test     — add/update tests (add eval tests for RAG)
# perf     — performance improvement (cache embeddings)
# chore    — maintenance (update dependencies)
# experiment — ML experiment tracking

# Examples
git commit -m "feat(rag): add hybrid search with BM25 + vector"
git commit -m "fix(agent): handle timeout in web search tool"
git commit -m "perf(embeddings): add batch processing, 3x speedup"
git commit -m "experiment(prompts): test chain-of-thought vs direct"
```

### 4. `.gitignore` for AI Projects

```gitignore
# Python
__pycache__/
*.py[cod]
*.egg-info/
dist/
venv/
.venv/

# Environment
.env
.env.local
*.key

# AI/ML specific
models/
*.pt
*.pth
*.bin
*.onnx
*.gguf
embeddings_cache/
chroma_db/
vector_store/

# Data (large files)
data/raw/
data/processed/
*.csv
*.parquet
!data/sample/*.csv  # Keep sample data

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
logs/
*.log

# Docker
docker-compose.override.yml
```

### 5. GitHub Features for AI Projects

```markdown
# Pull Request Template (.github/pull_request_template.md)

## What does this PR do?
<!-- Brief description -->

## Type of change
- [ ] New feature (RAG, Agent, Pipeline)
- [ ] Bug fix
- [ ] Performance improvement
- [ ] Experiment results
- [ ] Documentation

## Evaluation Results (if applicable)
| Metric | Before | After |
|--------|--------|-------|
| Accuracy | | |
| Latency | | |
| Cost/query | | |

## Checklist
- [ ] Code follows project conventions
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No API keys committed
```

### 6. Git Collaboration Workflow

```bash
# Fork and clone workflow
git clone https://github.com/YOUR_USERNAME/ai-project.git
git remote add upstream https://github.com/ORIGINAL/ai-project.git

# Keep your fork updated
git fetch upstream
git checkout main
git merge upstream/main

# Create feature, push, and open PR
git checkout -b feature/new-embedding-model
# ... work ...
git push origin feature/new-embedding-model
# → Open PR on GitHub

# After PR review, squash and merge
git checkout main
git pull origin main
git branch -d feature/new-embedding-model
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Git & GitHub for Beginners** | 📹 YouTube | [FreeCodeCamp - Git Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk) |
| **Pro Git Book (Free)** | 📄 Book | [git-scm.com/book](https://git-scm.com/book/en/v2) |
| **GitHub Skills (Interactive)** | 🎮 Interactive | [skills.github.com](https://skills.github.com/) |
| **Conventional Commits** | 📄 Docs | [conventionalcommits.org](https://www.conventionalcommits.org/) |
| **Learn Git Branching** | 🎮 Interactive | [learngitbranching.js.org](https://learngitbranching.js.org/) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Committing `.env` files | Exposes API keys publicly | Add `.env` to `.gitignore` FIRST |
| Committing model files (GB+) | Bloats repo, can't undo easily | Use `.gitignore` for `*.pt`, `*.bin` |
| Giant commits with "update" message | Can't track what changed | Small, focused commits with clear messages |
| Working directly on `main` | Breaks CI/CD, no review | Always use feature branches |
| Not pulling before pushing | Merge conflicts | `git pull --rebase origin main` before push |
| Force-pushing shared branches | Destroys others' work | Only force-push your own feature branches |

---

## 📋 Quick Reference Cheatsheet

| Command | What It Does | When to Use |
|---------|-------------|------------|
| `git init` | Create new repo | Starting a project |
| `git clone <url>` | Download a repo | Getting existing project |
| `git status` | Show changes | Before every commit |
| `git add .` | Stage all changes | After making changes |
| `git commit -m "msg"` | Save snapshot | After staging |
| `git push origin main` | Upload to GitHub | After committing |
| `git pull origin main` | Download latest | Before starting work |
| `git checkout -b name` | Create branch | Starting new feature |
| `git merge branch` | Combine branches | After PR approval |
| `git log --oneline -10` | View history | Checking recent work |
| `git stash` | Save temp changes | Switching branches |
| `git diff` | Show changes | Reviewing before commit |
| `git reset --soft HEAD~1` | Undo last commit | Fix mistakes |

---

> 💡 **Pro Tip:** Use `git commit --amend` to fix the last commit message, and `git rebase -i HEAD~3` to clean up your last 3 commits before creating a PR. Clean commit history makes code review much easier.

---

<div align="center">

[← Topic 2: FastAPI Basics](../topic-2-fastapi-basics/) · [🏠 Home](../../README.md) · [📋 Phase 1](../README.md)

**[▶ Problems](problems.md)** · [Topic 4: Linux & Terminal →](../topic-4-linux-terminal/)

</div>
