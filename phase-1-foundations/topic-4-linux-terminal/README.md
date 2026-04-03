<div align="center">

# 🐧 Topic 4: Linux & Terminal

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-2_Days-blue)
![Phase](https://img.shields.io/badge/Phase-1_Foundations-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > **Linux & Terminal**

---

## 🎯 Why This Matters for AI Systems Engineering

AI systems run on **Linux servers**. Your Docker containers, cloud VMs, and Kubernetes clusters all run Linux. You need terminal skills for:
- **Deploying AI services** → SSH into servers, manage processes
- **Docker debugging** → Inspecting containers, viewing logs
- **Data wrangling** → Processing large files with `awk`, `grep`, `sed`
- **Automation** → Bash scripts for training, evaluation, deployment
- **Monitoring** → Checking resource usage, log analysis

---

## 📚 Core Concepts

### 1. Essential File Navigation

```bash
# Navigation
pwd                         # Print working directory
ls -la                      # List all files with details
ls -lh *.py                 # List Python files with human-readable sizes
cd ~/projects/ai-app        # Change directory
cd ..                       # Go up one level
cd -                        # Go to previous directory

# File operations
mkdir -p src/models/rag     # Create nested directories
touch src/__init__.py       # Create empty file
cp -r src/ src_backup/      # Copy directory recursively
mv old_name.py new_name.py  # Rename/move
rm -rf __pycache__/         # Remove directory (⚠️ CAREFUL!)

# Viewing files
cat config.yaml             # Print entire file
head -20 large_dataset.csv  # First 20 lines
tail -f logs/app.log        # Follow log file in real-time (Ctrl+C to stop)
less huge_file.txt          # Paginated viewing (q to quit)
wc -l data/*.csv            # Count lines in all CSV files
```

### 2. Text Processing for Data & Logs

```bash
# grep — Search for patterns (crucial for log analysis)
grep "ERROR" logs/app.log                    # Find all errors
grep -r "api_key" --include="*.py" src/      # Find api_key in Python files
grep -c "rate_limit" logs/app.log            # Count rate limit occurrences
grep -n "def " src/main.py                   # Find functions with line numbers
grep -v "DEBUG" logs/app.log                 # Exclude debug lines

# awk — Extract columns (great for CSV/log analysis)
awk -F',' '{print $1, $3}' results.csv       # Print columns 1 and 3
awk '/ERROR/ {count++} END {print count}' app.log  # Count errors
awk -F',' 'NR>1 {sum+=$3} END {print sum/NR}' metrics.csv  # Average of column 3

# sed — Stream editing
sed 's/gpt-3.5/gpt-4/g' config.yaml         # Replace model name
sed -n '10,20p' large_file.txt               # Print lines 10-20
sed '/^#/d' config.yaml                      # Remove comment lines

# sort & uniq — Dedup and count
cat logs/app.log | grep "model=" | sort | uniq -c | sort -rn
# Output: how many times each model was called

# jq — JSON processing (essential for API responses)
curl -s http://localhost:8000/health | jq '.'  # Pretty print JSON
cat response.json | jq '.results[].score'      # Extract scores
cat response.json | jq '[.results[] | select(.score > 0.8)]'  # Filter by score
```

### 3. Process Management

```bash
# Running processes
python main.py &                  # Run in background
nohup python train.py > train.log 2>&1 &  # Run even after terminal closes
jobs                              # List background jobs
fg %1                             # Bring job 1 to foreground

# Monitoring
ps aux | grep python              # Find Python processes
top -p $(pgrep -f "uvicorn")      # Monitor specific process
htop                              # Interactive process viewer
nvidia-smi                        # GPU usage (for ML workloads)

# Resource usage
df -h                             # Disk usage
du -sh models/                    # Size of models directory
free -h                           # Memory usage

# Killing processes
kill $(pgrep -f "uvicorn")        # Kill uvicorn server
kill -9 <PID>                     # Force kill (last resort)
pkill -f "python worker.py"       # Kill by command pattern
```

### 4. Environment Variables & Configuration

```bash
# Setting variables
export OPENAI_API_KEY="sk-your-key-here"
export MODEL_NAME="gpt-4"
export DATABASE_URL="postgresql://user:pass@localhost/db"

# Using in commands
echo $OPENAI_API_KEY
python -c "import os; print(os.getenv('MODEL_NAME'))"

# .env files (used with python-dotenv)
cat > .env << 'EOF'
OPENAI_API_KEY=sk-your-key-here
MODEL_NAME=gpt-4
TEMPERATURE=0.7
DATABASE_URL=postgresql://user:pass@localhost/db
REDIS_URL=redis://localhost:6379
EOF

# Load .env in shell
set -a; source .env; set +a

# Check if variable is set
if [ -z "$OPENAI_API_KEY" ]; then
    echo "❌ OPENAI_API_KEY is not set!"
    exit 1
fi
```

### 5. Shell Scripting for AI Workflows

```bash
#!/bin/bash
# setup.sh — AI project setup script

set -euo pipefail  # Exit on error, undefined vars, pipe failures

echo "🚀 Setting up AI project..."

# Check prerequisites
command -v python3 >/dev/null 2>&1 || { echo "❌ Python3 required"; exit 1; }
command -v docker >/dev/null 2>&1 || { echo "❌ Docker required"; exit 1; }

# Create virtual environment
echo "📦 Creating virtual environment..."
python3 -m venv venv
source venv/bin/activate

# Install dependencies
echo "📥 Installing dependencies..."
pip install -r requirements.txt

# Check for .env file
if [ ! -f .env ]; then
    echo "⚠️  No .env file found. Creating from template..."
    cp .env.example .env
    echo "📝 Please edit .env with your API keys"
fi

# Run database migrations
echo "🗄️  Running database setup..."
python -c "from src.db import init_db; init_db()"

# Verify setup
echo "✅ Running verification..."
python -m pytest tests/ -v --tb=short

echo ""
echo "🎉 Setup complete! Run: uvicorn src.main:app --reload"
```

### 6. SSH & Remote Server Management

```bash
# Connect to a server
ssh user@your-server.com

# SSH with key (more secure)
ssh -i ~/.ssh/ai-server-key.pem ubuntu@ec2-instance.amazonaws.com

# Copy files to/from server
scp model.pt user@server:/models/         # Upload
scp user@server:/logs/app.log ./          # Download
rsync -avz src/ user@server:~/app/src/    # Sync directory

# SSH config (~/.ssh/config)
# Host ai-server
#   HostName 12.34.56.78
#   User ubuntu
#   IdentityFile ~/.ssh/ai-server.pem

# Then just: ssh ai-server

# Port forwarding (access remote service locally)
ssh -L 8000:localhost:8000 ai-server
# Now http://localhost:8000 accesses the server's port 8000
```

### 7. Permissions & Security

```bash
# File permissions
chmod +x setup.sh              # Make script executable
chmod 600 .env                 # Only owner can read/write (secrets!)
chmod 755 deploy.sh            # Owner: all, others: read+execute

# Understanding permissions: rwx = read(4) + write(2) + execute(1)
ls -la .env
# -rw------- 1 user user 256 Jan 15 10:00 .env  ← Good! Only owner

# Change ownership
chown -R appuser:appgroup /opt/ai-service/
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Linux Command Line Basics** | 📹 YouTube | [FreeCodeCamp - Linux for Beginners](https://www.youtube.com/watch?v=ZtqBQ68cfJc) |
| **Bash Scripting Tutorial** | 📹 YouTube | [NetworkChuck - Bash in 100 Seconds](https://www.youtube.com/watch?v=I4EWvMFj37g) |
| **The Missing Semester (MIT)** | 📹 Course | [missing.csail.mit.edu](https://missing.csail.mit.edu/) |
| **Linux Journey** | 🎮 Interactive | [linuxjourney.com](https://linuxjourney.com/) |
| **jq Tutorial** | 📄 Docs | [stedolan.github.io/jq/tutorial](https://stedolan.github.io/jq/tutorial/) |
| **ExplainShell** | 🔧 Tool | [explainshell.com](https://explainshell.com/) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Using `rm -rf` without checking | Deletes permanently, no recovery | Always `ls` first, then `rm` |
| Running as root | Security risk, accidental damage | Use `sudo` only when needed |
| Hardcoding paths | Breaks on other machines | Use `$HOME`, relative paths, env vars |
| Not using `set -e` in scripts | Script continues after errors | Always `set -euo pipefail` |
| Ignoring file permissions on .env | Anyone can read your API keys | `chmod 600 .env` |
| Not checking command existence | Script fails with cryptic error | Use `command -v` checks |

---

## 📋 Quick Reference Cheatsheet

| Command | What It Does | Example |
|---------|-------------|---------|
| `ls -la` | List all files with details | `ls -la src/` |
| `grep -r` | Search recursively | `grep -r "api_key" src/` |
| `find` | Find files | `find . -name "*.py" -mtime -1` |
| `awk` | Process columns | `awk '{print $2}' log.txt` |
| `sed` | Stream edit | `sed 's/old/new/g' file.txt` |
| `jq` | Process JSON | `cat data.json \| jq '.items[]'` |
| `tail -f` | Follow a file | `tail -f logs/app.log` |
| `chmod` | Change permissions | `chmod +x script.sh` |
| `export` | Set env variable | `export API_KEY="key"` |
| `nohup` | Run after disconnect | `nohup python app.py &` |
| `scp` | Copy over SSH | `scp file.txt user@host:/path/` |
| `du -sh` | Directory size | `du -sh models/` |

---

> 💡 **Pro Tip:** Learn keyboard shortcuts: `Ctrl+R` (reverse search history), `Ctrl+A`/`Ctrl+E` (start/end of line), `Ctrl+W` (delete word), `!!` (repeat last command), `!$` (last argument). These save hours over a career.

---

<div align="center">

[← Topic 3: Git & GitHub](../topic-3-git-github/) · [🏠 Home](../../README.md) · [📋 Phase 1](../README.md)

**[▶ Problems](problems.md)** · [Topic 5: Docker →](../topic-5-docker/)

</div>
