<div align="center">

# 🔨 Mini Project: Smart CLI Chatbot

### Build a terminal chatbot with Ollama, conversation history, and model switching

![Tech](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Tech](https://img.shields.io/badge/Ollama-000000?style=flat-square)
![Tech](https://img.shields.io/badge/Rich-FF6F61?style=flat-square)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > **Mini Project**

---

## 📋 Project Overview

Build a **feature-rich terminal chatbot** using Ollama (free, local) that features conversation history management, runtime model switching, color-coded output, system prompt customization, and conversation export.

---

## 🧠 Concepts Used from This Phase

| Concept | Where It's Used |
|---------|----------------|
| **LLM APIs** | Ollama API for chat completions |
| **Prompt Engineering** | System prompts, conversation context |
| **Streaming** | Token-by-token response display |
| **Memory** | Conversation history management |
| **Model selection** | Runtime model switching |
| **LangChain** | Optional integration for enhanced features |

---

## 🏗️ File Structure

```
smart-cli-chatbot/
├── chatbot.py              # Main chatbot application
├── models.py               # Data models for conversations
├── history.py              # Conversation history manager
├── ui.py                   # Rich terminal UI utilities
├── config.py               # Configuration management
├── requirements.txt
└── README.md
```

---

## 🔧 Step-by-Step Build Instructions

### Step 1: Install Dependencies

```txt
# requirements.txt
requests>=2.31.0
rich>=13.7.0
pydantic>=2.5.0
```

### Step 2: Build the Core Classes

```python
# chatbot.py
import requests
import json
from rich.console import Console
from rich.markdown import Markdown
from rich.panel import Panel
from rich.prompt import Prompt
from datetime import datetime

console = Console()

class SmartChatbot:
    def __init__(self, model: str = "llama3", 
                 system_prompt: str = "You are a helpful AI assistant."):
        self.model = model
        self.system_prompt = system_prompt
        self.history: list[dict] = []
        self.base_url = "http://localhost:11434"
    
    def chat(self, message: str) -> str:
        """Send message and get streaming response."""
        self.history.append({"role": "user", "content": message})
        
        messages = [{"role": "system", "content": self.system_prompt}]
        messages.extend(self.history[-20:])  # Keep last 20 messages
        
        response = requests.post(
            f"{self.base_url}/api/chat",
            json={"model": self.model, "messages": messages, "stream": True},
            stream=True,
        )
        
        full_response = ""
        console.print(f"\n[bold cyan]🤖 {self.model}:[/bold cyan] ", end="")
        
        for line in response.iter_lines():
            if line:
                data = json.loads(line)
                if "message" in data and data["message"]["content"]:
                    token = data["message"]["content"]
                    console.print(token, end="", highlight=False)
                    full_response += token
        
        console.print()
        self.history.append({"role": "assistant", "content": full_response})
        return full_response
    
    def switch_model(self, model: str):
        """Switch to a different model at runtime."""
        self.model = model
        console.print(f"\n[bold green]✅ Switched to {model}[/bold green]\n")
    
    def clear_history(self):
        """Clear conversation history."""
        self.history = []
        console.print("\n[bold yellow]🗑️ History cleared[/bold yellow]\n")
    
    def export_conversation(self, filename: str = None):
        """Export conversation to markdown file."""
        if not filename:
            filename = f"chat_{datetime.now().strftime('%Y%m%d_%H%M%S')}.md"
        
        with open(filename, "w") as f:
            f.write(f"# Chat Export — {datetime.now().strftime('%Y-%m-%d %H:%M')}\n\n")
            f.write(f"**Model:** {self.model}\n\n---\n\n")
            for msg in self.history:
                role = "👤 You" if msg["role"] == "user" else "🤖 AI"
                f.write(f"**{role}:** {msg['content']}\n\n")
        
        console.print(f"\n[bold green]💾 Conversation saved to {filename}[/bold green]\n")

def main():
    console.print(Panel.fit(
        "[bold cyan]🤖 Smart CLI Chatbot[/bold cyan]\n"
        "Powered by Ollama • Type /help for commands",
        border_style="cyan"
    ))
    
    bot = SmartChatbot()
    
    commands = {
        "/help": "Show available commands",
        "/model <name>": "Switch model (e.g., /model mistral)",
        "/clear": "Clear conversation history",
        "/export": "Export conversation to markdown",
        "/system <prompt>": "Change system prompt",
        "/history": "Show conversation history",
        "/quit": "Exit the chatbot",
    }
    
    while True:
        try:
            user_input = Prompt.ask("\n[bold green]👤 You[/bold green]")
            
            if user_input.startswith("/"):
                cmd = user_input.split()[0]
                args = user_input[len(cmd):].strip()
                
                if cmd == "/quit":
                    console.print("\n[bold]👋 Goodbye![/bold]")
                    break
                elif cmd == "/help":
                    for c, d in commands.items():
                        console.print(f"  [cyan]{c}[/cyan] — {d}")
                elif cmd == "/model":
                    bot.switch_model(args or "llama3")
                elif cmd == "/clear":
                    bot.clear_history()
                elif cmd == "/export":
                    bot.export_conversation()
                elif cmd == "/system":
                    bot.system_prompt = args
                    console.print(f"[green]✅ System prompt updated[/green]")
                elif cmd == "/history":
                    for msg in bot.history[-10:]:
                        role = "👤" if msg["role"] == "user" else "🤖"
                        console.print(f"  {role} {msg['content'][:80]}...")
                continue
            
            bot.chat(user_input)
        
        except KeyboardInterrupt:
            console.print("\n\n[bold]👋 Goodbye![/bold]")
            break

if __name__ == "__main__":
    main()
```

---

## ▶️ How to Run

```bash
# 1. Make sure Ollama is running
ollama serve

# 2. Pull a model
ollama pull llama3

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the chatbot
python chatbot.py
```

---

## 🚀 How to Extend It

1. **Add RAG** → Search local documents before answering (Phase 4 preview)
2. **Add voice input** → Use `speech_recognition` library
3. **Add image input** → Use `llava` model for multimodal
4. **Add web search** → Integrate web search tool
5. **Add conversation database** → Store chats in SQLite/PostgreSQL
6. **Add streaming markdown** → Render markdown as it streams

---

## 📝 What to Add to Your Resume

> **Smart CLI Chatbot** — Built a feature-rich terminal chatbot using Ollama for local LLM inference with streaming responses, conversation history management (last 20 messages for context), runtime model switching between Llama 3/Mistral/Phi-3, Rich library for color-coded UI, system prompt customization, and markdown conversation export. Zero API cost using local models.

---

<div align="center">

[← Topic 5: Ollama](../topic-5-ollama-local-llms/) · [🏠 Home](../../README.md) · [📋 Phase 3](../README.md)

**[Phase 4: RAG Systems →](../../phase-4-rag-systems/)**

</div>
