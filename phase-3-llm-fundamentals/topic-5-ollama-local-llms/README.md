<div align="center">

# 🦙 Topic 5: Ollama & Local LLMs

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-2_Days-blue)
![Phase](https://img.shields.io/badge/Phase-3_LLM_Fundamentals-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > **Ollama & Local LLMs**

---

## 🎯 Why This Matters for AI Systems Engineering

Running LLMs locally with **Ollama** is a game-changer:
- **Free** → No API costs during development (saves $100+ per month)
- **Private** → Data never leaves your machine
- **Fast iteration** → No rate limits, instant testing
- **Offline** → Works without internet
- **OpenAI-compatible** → Same API format, easy to swap in production

---

## 📚 Core Concepts

### 1. Getting Started with Ollama

```bash
# Install Ollama (macOS/Linux)
curl -fsSL https://ollama.com/install.sh | sh

# Install on Windows: download from https://ollama.com/download

# Pull a model
ollama pull llama3          # Meta's Llama 3 (8B, ~4.7GB)
ollama pull mistral         # Mistral 7B (~4.1GB)
ollama pull phi3            # Microsoft Phi-3 Mini (~2.2GB)
ollama pull codellama       # Code-specialized model

# List downloaded models
ollama list

# Run a model interactively
ollama run llama3
>>> What is RAG?
# ... model responds ...
>>> /bye

# Ollama serves an API on http://localhost:11434
```

### 2. Python Integration

```python
import requests
import json

def chat_with_ollama(prompt: str, model: str = "llama3", 
                     system: str = "", stream: bool = False) -> str:
    """Call Ollama's local API."""
    url = "http://localhost:11434/api/chat"
    
    messages = []
    if system:
        messages.append({"role": "system", "content": system})
    messages.append({"role": "user", "content": prompt})
    
    payload = {
        "model": model,
        "messages": messages,
        "stream": stream,
    }
    
    if stream:
        response = requests.post(url, json=payload, stream=True)
        full_response = ""
        for line in response.iter_lines():
            if line:
                data = json.loads(line)
                if "message" in data:
                    token = data["message"]["content"]
                    print(token, end="", flush=True)
                    full_response += token
        print()
        return full_response
    else:
        response = requests.post(url, json=payload)
        return response.json()["message"]["content"]

# Usage
response = chat_with_ollama(
    prompt="Explain embeddings in 3 sentences.",
    model="llama3",
    system="You are a concise AI tutor."
)
print(response)
```

### 3. OpenAI-Compatible API (Drop-in Replacement)

```python
from openai import OpenAI

# Point OpenAI client at Ollama — SAME CODE works!
client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama",  # Required but unused
)

response = client.chat.completions.create(
    model="llama3",
    messages=[
        {"role": "system", "content": "You are a helpful AI tutor."},
        {"role": "user", "content": "What is vector similarity search?"},
    ],
    temperature=0.7,
)

print(response.choices[0].message.content)
# Works exactly like OpenAI's API — same code, zero cost!
```

### 4. LangChain + Ollama

```python
from langchain_community.llms import Ollama
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = Ollama(model="llama3")

prompt = ChatPromptTemplate.from_template(
    "Explain {concept} in simple terms for a software engineer."
)

chain = prompt | llm | StrOutputParser()
result = chain.invoke({"concept": "retrieval augmented generation"})
print(result)
```

### 5. Model Management

```bash
# Pull specific versions
ollama pull llama3:8b        # 8 billion parameters
ollama pull llama3:70b       # 70 billion (needs 40GB+ RAM)

# Create custom model with system prompt
cat > Modelfile << 'EOF'
FROM llama3
SYSTEM "You are an AI Systems Engineering expert. Always provide Python code examples with type hints. Be concise."
PARAMETER temperature 0.3
PARAMETER num_ctx 4096
EOF

ollama create ai-tutor -f Modelfile
ollama run ai-tutor

# Delete a model
ollama rm phi3

# Show model details
ollama show llama3
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Ollama Official** | 📄 Docs | [ollama.com](https://ollama.com/) |
| **Ollama GitHub** | 📄 GitHub | [github.com/ollama/ollama](https://github.com/ollama/ollama) |
| **Run LLMs Locally** | 📹 YouTube | [NetworkChuck - Ollama](https://www.youtube.com/watch?v=Wjrdr0NU4Sk) |
| **Ollama Model Library** | 📄 Docs | [ollama.com/library](https://ollama.com/library) |

---

## 📋 Quick Reference Cheatsheet

| Command | What It Does |
|---------|-------------|
| `ollama pull model` | Download a model |
| `ollama run model` | Interactive chat |
| `ollama list` | List downloaded models |
| `ollama rm model` | Delete a model |
| `ollama show model` | Model details |
| `ollama create name -f Modelfile` | Create custom model |
| `ollama serve` | Start API server |
| API: `localhost:11434/api/chat` | Chat endpoint |
| API: `localhost:11434/v1/chat/completions` | OpenAI-compatible |

---

> 💡 **Pro Tip:** Use Ollama for ALL development and testing. Only switch to OpenAI/Anthropic when you need specific capabilities (like GPT-4's reasoning or Claude's 200K context). A typical development workflow: build with Ollama (free) → test with GPT-3.5 (cheap) → deploy with GPT-4 (quality). This alone saves $100+/month in API costs.

---

<div align="center">

[← Topic 4: LangChain Basics](../topic-4-langchain-basics/) · [📋 Phase 3](../README.md)

[🔨 Mini Project →](../mini-project/)

</div>
