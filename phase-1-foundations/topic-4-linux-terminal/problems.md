<div align="center">

# 🧩 Linux & Terminal — Practice Problems

![Problems](https://img.shields.io/badge/Problems-12-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > [Linux & Terminal](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Project Directory Setup Script

**Problem:** Write a bash script that creates a complete AI project directory structure with all necessary subdirectories and placeholder files.

**Real-world AI use case:** Standardized project structures ensure consistency across team members and make CI/CD pipelines predictable.

**Concept being tested:** `mkdir -p`, `touch`, shell scripting basics

**Hint:** Use heredoc (`cat << 'EOF' > file`) to create files with content.

**Expected Output:**
```bash
$ bash create_project.sh my-rag-app
📁 Creating project: my-rag-app
  ✅ src/
  ✅ src/models/
  ✅ src/routes/
  ✅ tests/
  ✅ data/
  ✅ .env.example
  ✅ requirements.txt
  ✅ Dockerfile
  ✅ README.md
🎉 Project my-rag-app created!
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### Log Analysis One-Liners

**Problem:** Given an application log file, write one-liner shell commands to: count errors, find the most common error message, extract timestamps of 500 errors, and calculate average response time.

**Real-world AI use case:** When your AI API is returning errors, fast log analysis helps identify if it's rate limiting, token limits, or model timeouts.

**Concept being tested:** `grep`, `awk`, `sort`, `uniq`, pipes

**Hint:** Chain commands with `|`. Use `grep -c` for counting, `sort | uniq -c | sort -rn` for frequency.

**Expected Output:**
```bash
# Count errors
$ grep -c "ERROR" app.log
47

# Most common error
$ grep "ERROR" app.log | awk -F'ERROR: ' '{print $2}' | sort | uniq -c | sort -rn | head -3
  23 Rate limit exceeded
  15 Token limit exceeded
   9 Connection timeout

# Average response time
$ grep "response_time=" app.log | awk -F'response_time=' '{sum+=$2; n++} END {print sum/n "ms"}'
245.3ms
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Environment Variable Manager

**Problem:** Write a bash script that loads environment variables from a `.env` file, validates required variables exist, and prints a status report.

**Real-world AI use case:** AI applications require multiple API keys and configuration values. A validation script catches missing config before the app crashes.

**Concept being tested:** `source`, environment variables, conditional checks

**Hint:** Use `[ -z "$VAR" ]` to check if a variable is empty. Use `source .env` to load variables.

**Expected Output:**
```bash
$ bash check_env.sh
🔍 Checking environment variables...
  ✅ OPENAI_API_KEY     — set (sk-***...***xyz)
  ✅ DATABASE_URL       — set (postgresql://***@localhost/db)
  ❌ REDIS_URL          — NOT SET
  ✅ MODEL_NAME         — set (gpt-4)
  ❌ LANGSMITH_API_KEY  — NOT SET

⚠️  2 required variables are missing!
```

---

## Problem 04 🧩

**Difficulty:** 🟢 Easy

### Find and Clean Project Artifacts

**Problem:** Write commands to find and clean common Python/AI project artifacts: `__pycache__`, `.pyc` files, `.egg-info`, old log files, and dangling Docker images.

**Real-world AI use case:** AI projects accumulate cache files, old model checkpoints, and Docker artifacts that waste disk space.

**Concept being tested:** `find`, `rm`, `du`, disk management

**Hint:** Use `find . -type d -name "__pycache__" -exec rm -rf {} +` pattern.

**Expected Output:**
```bash
$ bash clean_project.sh
🧹 Cleaning project artifacts...
  Removed 12 __pycache__ directories (45 MB)
  Removed 3 .egg-info directories (2 MB)
  Removed 156 .pyc files
  Removed 5 log files older than 7 days (120 MB)
  Total freed: 167 MB
```

---

## Problem 05 🧩

**Difficulty:** 🟡 Medium

### System Health Monitor Script

**Problem:** Write a bash script that monitors CPU, memory, disk usage, and running Python processes, with alerts when thresholds are exceeded.

**Real-world AI use case:** AI services can be memory-hungry (embedding models, large contexts). Monitoring prevents OOM kills and identifies resource leaks.

**Concept being tested:** `top`, `free`, `df`, process monitoring, conditional alerts

**Hint:** Use `free -m | awk '/Mem:/ {print $3/$2 * 100}'` for memory percentage.

**Expected Output:**
```bash
$ bash monitor.sh
═══════════════════════════════════
  System Health Report — 2024-01-15 10:30:00
═══════════════════════════════════
  CPU Usage:     45% ✅
  Memory Usage:  78% ⚠️ (threshold: 80%)
  Disk Usage:    62% ✅
  
  Python Processes: 3
  PID 1234 — uvicorn main:app          — 256 MB
  PID 5678 — python worker.py          — 1.2 GB ⚠️
  PID 9012 — python embedding_server.py — 890 MB
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### JSON Log Processor with jq

**Problem:** Given a JSON log file (one JSON object per line), use `jq` to extract, filter, and aggregate data.

**Real-world AI use case:** Modern AI services output structured JSON logs. Using `jq` to analyze these in real-time is faster than loading them into a database.

**Concept being tested:** `jq` queries, JSON processing, data aggregation

**Hint:** Use `jq -s` (slurp) to treat all lines as an array, then aggregate.

**Expected Output:**
```bash
# Given log entries like:
# {"timestamp":"2024-01-15T10:00:00","model":"gpt-4","tokens":150,"cost":0.003,"status":"success"}

# Total cost by model
$ cat llm_logs.jsonl | jq -s 'group_by(.model)| map({model: .[0].model, total_cost: (map(.cost) | add), calls: length})'
[
  {"model": "gpt-4", "total_cost": 2.45, "calls": 120},
  {"model": "gpt-3.5-turbo", "total_cost": 0.23, "calls": 450}
]
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### Cron Job for AI Model Evaluation

**Problem:** Set up a cron job that runs an AI model evaluation script every day at 2 AM, logs results, rotates old logs, and sends a summary notification.

**Real-world AI use case:** Production AI systems need automated evaluation to detect model drift, accuracy degradation, or increased latency over time.

**Concept being tested:** Cron syntax, log rotation, scheduled tasks

**Hint:** Cron format: `minute hour day month weekday`. Use `logrotate` or manual rotation with `find -mtime`.

**Expected Output:**
```bash
# Crontab entry
0 2 * * * /opt/ai-service/scripts/evaluate.sh >> /var/log/ai-eval.log 2>&1

# evaluate.sh produces:
[2024-01-15 02:00:01] Starting daily evaluation...
[2024-01-15 02:05:23] Results:
  Accuracy: 91.2% (baseline: 90.0%) ✅
  Latency p95: 1.8s (threshold: 2.0s) ✅
  Cost/query: $0.025 (budget: $0.03) ✅
[2024-01-15 02:05:23] All metrics within bounds.
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Multi-Service Start/Stop Script

**Problem:** Write a bash script that starts/stops/restarts multiple services (FastAPI app, Redis, embedding worker) with proper process management.

**Real-world AI use case:** Local development of AI systems often requires multiple services running simultaneously (API server, database, embedding server, etc.).

**Concept being tested:** Process management, PID files, signal handling

**Hint:** Store PIDs in files (`echo $! > service.pid`), use `kill $(cat service.pid)` to stop. Implement `start`, `stop`, `restart`, `status` subcommands.

**Expected Output:**
```bash
$ bash services.sh start
🚀 Starting services...
  ✅ FastAPI server    — PID 1234 (port 8000)
  ✅ Redis             — PID 1235 (port 6379)
  ✅ Embedding worker  — PID 1236

$ bash services.sh status
📊 Service Status:
  FastAPI server    — ✅ Running (PID 1234, uptime: 5m)
  Redis             — ✅ Running (PID 1235, uptime: 5m)
  Embedding worker  — ✅ Running (PID 1236, uptime: 5m)

$ bash services.sh stop
🛑 Stopping services...
  ✅ All services stopped.
```

---

## Problem 09 🧩

**Difficulty:** 🟡 Medium

### Data Pipeline Shell Script

**Problem:** Write a shell script that downloads a dataset, validates its format, extracts specific columns, cleans the data, and splits it into train/test sets.

**Real-world AI use case:** Data preprocessing is often done with shell scripts for speed, especially when dealing with large CSV/JSONL files that are too big for Python-based loading.

**Concept being tested:** `curl`/`wget`, `awk`, `sed`, `split`, data processing pipes

**Hint:** Use `shuf` to shuffle data, `head`/`tail` to split, `awk -F','` for CSV column extraction.

**Expected Output:**
```bash
$ bash prepare_data.sh
📥 Downloading dataset...
  Downloaded: qa_dataset.csv (50,000 rows, 125 MB)
  
🔍 Validating format...
  ✅ Columns: question, answer, context, source
  ✅ No null values in required columns
  
🧹 Cleaning...
  Removed 234 duplicate rows
  Removed 12 rows with empty answers
  
✂️ Splitting...
  Train: 39,803 rows (80%) → data/train.csv
  Test:  9,951 rows (20%)  → data/test.csv
  
✅ Data pipeline complete!
```

---

## Problem 10 🧩

**Difficulty:** 🔴 Hard

### Tmux Session Manager for AI Development

**Problem:** Write a script that creates a preconfigured tmux session with multiple panes: API server, log viewer, database shell, and a coding pane.

**Real-world AI use case:** AI engineers juggle multiple terminals — API server, log viewer, database queries, and code editing. A tmux setup script automates this.

**Concept being tested:** `tmux`, session management, automated layout

**Hint:** Use `tmux new-session -d -s name`, `tmux split-window`, `tmux send-keys 'command' C-m`.

**Expected Output:**
```
┌────────────────────────┬────────────────────────┐
│                        │                        │
│   FastAPI Server       │   Log Viewer           │
│   uvicorn main:app     │   tail -f app.log      │
│   --reload             │                        │
│                        │                        │
├────────────────────────┼────────────────────────┤
│                        │                        │
│   Database Shell       │   Code / Testing       │
│   psql -d ai_db        │   (bash)               │
│                        │                        │
│                        │                        │
└────────────────────────┴────────────────────────┘
tmux session: ai-dev
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### Deployment Script with Rollback

**Problem:** Write a deployment script that pulls the latest code, builds a Docker image, runs health checks, and deploys. If health checks fail, automatically roll back to the previous version.

**Real-world AI use case:** Zero-downtime deployment with rollback is essential for production AI services. A failed deployment shouldn't leave the system in a broken state.

**Concept being tested:** Docker, health checks, rollback logic, production deployment

**Hint:** Tag Docker images with commit hashes. Keep the previous image tag. Use `curl` for health check retries.

**Expected Output:**
```bash
$ bash deploy.sh
🚀 Deploying AI Service v2.3.1...
  📥 Pulling latest code...         ✅
  🐳 Building Docker image...       ✅ (ai-service:abc1234)
  🔄 Stopping old container...      ✅
  ▶️  Starting new container...      ✅
  🏥 Running health checks...       
     Attempt 1/5: waiting...        ⏳
     Attempt 2/5: healthy!          ✅
  
✅ Deployment successful! v2.3.1 is live.

# If health check fails:
  🏥 Running health checks...       
     Attempt 1/5: unhealthy         ⚠️
     ...
     Attempt 5/5: unhealthy         ❌
  ⚠️  ROLLING BACK to previous version (ai-service:def5678)...
  ✅ Rollback complete. Previous version restored.
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### Automated Security Audit Script

**Problem:** Write a comprehensive security audit script that checks for exposed secrets, insecure file permissions, open ports, outdated packages, and Docker security issues.

**Real-world AI use case:** AI systems handle sensitive data (API keys, user documents, model weights). Regular security audits catch vulnerabilities before attackers do.

**Concept being tested:** Security best practices, file permissions, network scanning, package auditing

**Hint:** Check for patterns like `sk-`, `api_key=`, `password=` in files. Use `netstat`/`ss` for ports. `pip audit` for Python vulnerabilities.

**Expected Output:**
```bash
$ bash security_audit.sh
🔒 Security Audit Report — 2024-01-15
════════════════════════════════════════

📁 File Permissions:
  ⚠️  .env has permissions 644 — should be 600
  ✅  All SSH keys are 600
  ✅  No world-writable files found

🔑 Secrets Scan:
  ❌  Found potential API key in src/config.py:15
  ❌  Found password in docker-compose.yml:23
  ✅  .env is in .gitignore

🌐 Network:
  ⚠️  Port 5432 (PostgreSQL) exposed on 0.0.0.0
  ✅  Port 8000 (FastAPI) bound to 127.0.0.1
  ✅  Port 6379 (Redis) bound to 127.0.0.1

📦 Dependencies:
  ⚠️  2 vulnerabilities found (run 'pip audit' for details)
  
Score: 6/10 — Action required!
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 1](../README.md) · [🏠 Home](../../README.md)

</div>
