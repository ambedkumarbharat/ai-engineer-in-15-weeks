<div align="center">

# 🧩 Git & GitHub — Practice Problems

![Problems](https://img.shields.io/badge/Problems-12-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > [Git & GitHub](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Initialize an AI Project Repo

**Problem:** Create a new Git repository for an AI project with a proper `.gitignore`, `README.md`, and initial commit. The `.gitignore` must cover Python, AI/ML artifacts, environment files, and IDE files.

**Real-world AI use case:** Every AI project starts with proper repo initialization. A bad `.gitignore` can leak API keys or bloat the repo with model files.

**Concept being tested:** `git init`, `.gitignore`, initial commit structure

**Hint:** Create the `.gitignore` BEFORE the first commit. Include patterns for `*.pt`, `*.bin`, `.env`, `__pycache__/`, `chroma_db/`, `venv/`.

**Expected Output:**
```bash
$ git log --oneline
abc1234 Initial commit: project structure with .gitignore
$ cat .gitignore | head -5
# Python
__pycache__/
*.py[cod]
venv/
.env
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### Feature Branch Workflow

**Problem:** Starting from a main branch with an existing FastAPI app, create a feature branch, add a new `/health` endpoint, commit with a conventional commit message, and simulate a merge.

**Real-world AI use case:** Teams use feature branches for every change — adding new API endpoints, new RAG strategies, or new agent tools.

**Concept being tested:** Branching, committing, merging

**Hint:** Use `git checkout -b feature/health-endpoint`, make changes, commit, then `git checkout main && git merge feature/health-endpoint`.

**Expected Output:**
```bash
$ git log --oneline --graph
*   def5678 Merge branch 'feature/health-endpoint'
|\
| * abc1234 feat(api): add health check endpoint with system info
|/
* 789abcd Initial commit
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Write Meaningful Commit Messages

**Problem:** Given 6 code changes described in plain English, write proper conventional commit messages for each.

**Real-world AI use case:** Good commit messages help teammates understand what changed in the AI pipeline without reading code. They also power automated changelogs.

**Concept being tested:** Conventional commit format, descriptive messaging

**Hint:** Format: `<type>(<scope>): <description>`. Types: feat, fix, perf, docs, test, refactor, chore.

**Expected Output:**
```
1. "Added BM25 hybrid search to RAG pipeline"
   → feat(rag): add BM25 hybrid search alongside vector similarity

2. "Fixed bug where embeddings were computed twice"
   → fix(embeddings): prevent duplicate computation on cached documents

3. "Improved prompt that increased accuracy from 78% to 85%"
   → perf(prompts): optimize system prompt, accuracy 78% → 85%

4. "Updated README with setup instructions"
   → docs: add setup instructions and API examples to README

5. "Added tests for the chunking module"
   → test(chunking): add unit tests for overlap and boundary cases

6. "Updated LangChain from 0.1 to 0.2"
   → chore(deps): upgrade langchain 0.1 → 0.2
```

---

## Problem 04 🧩

**Difficulty:** 🟢 Easy

### Undo Mistakes Safely

**Problem:** Practice 5 common "undo" scenarios: unstage a file, undo last commit (keep changes), discard file changes, remove a file from tracking, and revert a pushed commit.

**Real-world AI use case:** You accidentally committed an API key, staged a 2GB model file, or committed to the wrong branch — you need to know how to fix these fast.

**Concept being tested:** `git reset`, `git checkout`, `git revert`, `git rm --cached`

**Hint:** `git reset HEAD <file>` to unstage, `git reset --soft HEAD~1` to undo commit, `git revert <hash>` for pushed commits.

**Expected Output:**
```bash
# Scenario 1: Unstage .env file
$ git reset HEAD .env

# Scenario 2: Undo last commit, keep changes  
$ git reset --soft HEAD~1

# Scenario 3: Discard changes to config.py
$ git checkout -- config.py

# Scenario 4: Stop tracking chroma_db/ (already committed)
$ git rm -r --cached chroma_db/

# Scenario 5: Revert a pushed commit safely
$ git revert abc1234
```

---

## Problem 05 🧩

**Difficulty:** 🟡 Medium

### Resolve a Merge Conflict

**Problem:** Create a merge conflict scenario where two branches modify the same function differently, then resolve it manually.

**Real-world AI use case:** When one engineer updates the chunking strategy while another updates the chunk size in the same config, merge conflicts are inevitable.

**Concept being tested:** Merge conflicts, manual resolution, conflict markers

**Hint:** Create two branches from the same commit, edit the same line in both, try to merge. Resolve by editing the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).

**Expected Output:**
```bash
# Conflict in config.py
<<<<<<< HEAD
CHUNK_SIZE = 500
=======
CHUNK_SIZE = 1000
>>>>>>> feature/larger-chunks

# Resolution
CHUNK_SIZE = 750  # Compromise between both approaches

$ git add config.py
$ git commit -m "fix: resolve chunk size conflict, use 750 as compromise"
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### Git Hooks for AI Projects

**Problem:** Create a pre-commit hook that prevents committing files containing API keys, files larger than 10MB, and files with incorrect Python syntax.

**Real-world AI use case:** Pre-commit hooks are your last line of defense against leaking secrets or committing broken code to the AI app repository.

**Concept being tested:** Git hooks, shell scripting, file validation

**Hint:** Create `.git/hooks/pre-commit` (must be executable). Use `grep -r` for secrets, `find` for large files, and `python -m py_compile` for syntax.

**Expected Output:**
```bash
$ git commit -m "test commit"
🔍 Checking for API keys...
❌ BLOCKED: Found potential API key in src/config.py:
   Line 5: OPENAI_KEY = "sk-abc123..."
   
💡 Move secrets to .env file and use os.getenv()
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### GitHub Actions: Auto-Lint on PR

**Problem:** Write a GitHub Actions workflow file that automatically runs `ruff` (Python linter), `mypy` (type checker), and `pytest` on every pull request.

**Real-world AI use case:** CI/CD pipelines automatically validate code quality before merging to main, preventing bugs in the AI pipeline from reaching production.

**Concept being tested:** GitHub Actions YAML, CI/CD concepts, automated testing

**Hint:** Create `.github/workflows/ci.yml`. Use `on: pull_request`, set up Python with `actions/setup-python@v5`, install dependencies from `requirements.txt`.

**Expected Output:**
```yaml
# .github/workflows/ci.yml should produce:
✅ Lint (ruff) — passed
✅ Type check (mypy) — passed  
✅ Tests (pytest) — 12 passed, 0 failed
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Manage Experiment Branches

**Problem:** Create a Git workflow for managing ML experiments: create experiment branches with standardized names, track results in commit messages, and use tags for best-performing experiments.

**Real-world AI use case:** AI engineers run many experiments (different prompts, models, chunk sizes). A proper branching strategy keeps experiments organized and reproducible.

**Concept being tested:** Branching strategies, tags, git log filtering

**Hint:** Naming: `experiment/<date>-<description>`. Use `git tag -a v1-best-rag -m "RAG accuracy: 92%"` for milestone tagging.

**Expected Output:**
```bash
$ git branch | grep experiment
  experiment/2024-01-15-gpt4-vs-claude
  experiment/2024-01-16-chunk-size-comparison
  experiment/2024-01-17-hybrid-search
  
$ git tag -l
  baseline-v1
  best-rag-v2 (accuracy: 92%)
  
$ git log --oneline experiment/2024-01-15-gpt4-vs-claude
  abc1234 experiment: GPT-4 accuracy=85%, latency=2.1s, cost=$0.03/query
  def5678 experiment: Claude accuracy=82%, latency=1.8s, cost=$0.02/query
```

---

## Problem 09 🧩

**Difficulty:** 🟡 Medium

### Create a Complete GitHub Repository

**Problem:** Set up a complete GitHub repository for an AI project with: README with badges, LICENSE, CONTRIBUTING.md, PR template, issue templates (bug report, feature request), and `.github/workflows/ci.yml`.

**Real-world AI use case:** Professional repositories need proper structure for open-source collaboration. This is exactly what companies evaluate when hiring.

**Concept being tested:** GitHub repository best practices, templates, markdown

**Hint:** Use `.github/ISSUE_TEMPLATE/` directory for issue templates, `.github/pull_request_template.md` for PR template.

**Expected Output:**
```
my-ai-project/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── workflows/
│   │   └── ci.yml
│   └── pull_request_template.md
├── .gitignore
├── CONTRIBUTING.md
├── LICENSE
├── README.md
└── requirements.txt
```

---

## Problem 10 🧩

**Difficulty:** 🔴 Hard

### Rewrite History to Remove Secrets

**Problem:** You accidentally committed an `.env` file with API keys 5 commits ago. Remove it from the entire Git history using `git filter-branch` or `git filter-repo`, then verify it's completely gone.

**Real-world AI use case:** Leaked API keys cost money and compromise security. Knowing how to clean history is a critical skill. GitHub even flags repos with exposed secrets.

**Concept being tested:** `git filter-repo`, history rewriting, secret scanning

**Hint:** Use `git filter-repo --path .env --invert-paths` or `git filter-branch --tree-filter 'rm -f .env' HEAD`. Then force-push and rotate all compromised keys.

**Expected Output:**
```bash
# Before: file exists in history
$ git log --all --full-history -- .env
commit abc1234 Added env configuration  # BAD!

# After cleanup
$ git log --all --full-history -- .env
# (no output — file removed from all history)

# Verify
$ git rev-list --all | xargs git grep "sk-" | wc -l
0  # No API keys found anywhere in history
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### Git Bisect to Find a Bug

**Problem:** Your RAG pipeline's accuracy dropped from 92% to 74% somewhere in the last 20 commits. Use `git bisect` to find the exact commit that introduced the regression.

**Real-world AI use case:** When an AI system's performance degrades, `git bisect` helps you efficiently find which code change caused it by binary searching through commits.

**Concept being tested:** `git bisect`, debugging with version control, binary search

**Hint:** `git bisect start`, `git bisect bad` (current), `git bisect good <commit>` (known good), then test each suggested commit and mark as good/bad.

**Expected Output:**
```bash
$ git bisect start
$ git bisect bad HEAD
$ git bisect good HEAD~20
Bisecting: 10 revisions left to test
[abc1234] refactor: change chunking overlap from 50 to 0

# After testing...
$ git bisect bad
$ git bisect good
# ... (5 more steps) ...

abc1234 is the first bad commit
commit abc1234
Author: Developer <dev@example.com>
Date: Mon Jan 15

    refactor: change chunking overlap from 50 to 0
    
# Found it! Zero overlap broke the RAG pipeline.
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### Monorepo Git Workflow

**Problem:** Design a Git workflow for a monorepo containing multiple AI microservices: `rag-service/`, `agent-service/`, `embedding-service/`, and `gateway/`. Handle independent versioning, selective CI/CD, and code ownership.

**Real-world AI use case:** Large AI platforms run multiple services in a monorepo. Changes to the embedding service should only trigger its tests and deployment, not the entire system.

**Concept being tested:** Monorepo patterns, CODEOWNERS, path-based CI/CD, semantic versioning

**Hint:** Use `CODEOWNERS` file for ownership, GitHub Actions path filters (`paths: ['rag-service/**']`), and tags like `rag-v1.2.3` for service-specific versions.

**Expected Output:**
```
ai-platform/
├── CODEOWNERS
├── rag-service/          # @team-rag
│   ├── CHANGELOG.md
│   └── ...
├── agent-service/        # @team-agents  
├── embedding-service/    # @team-ml
└── gateway/              # @team-platform

# CODEOWNERS
/rag-service/      @team-rag
/agent-service/    @team-agents
/embedding-service/ @team-ml

# Tagging: rag-v1.2.3, agent-v2.0.0
# CI: only runs tests for changed services
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 1](../README.md) · [🏠 Home](../../README.md)

</div>
