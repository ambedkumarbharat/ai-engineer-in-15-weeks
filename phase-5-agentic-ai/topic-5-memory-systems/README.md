<div align="center">

# 🧠 Topic 5: Memory Systems

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow) ![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **Memory Systems**

---

## 🎯 Why This Matters

Without memory, agents are goldfish — they forget everything between calls. Memory systems enable agents to remember conversations, learn from past interactions, and maintain context across sessions.

---

## 📚 Core Concepts

### Memory Types for AI Agents

```python
# 1. SHORT-TERM MEMORY — Current conversation context
# Stored in: list of messages
# Lifetime: Single session
# Example: "The user asked about RAG, then about chunking"

short_term_memory = [
    {"role": "user", "content": "What is RAG?"},
    {"role": "assistant", "content": "RAG is..."},
    {"role": "user", "content": "How do I chunk documents?"},
]

# 2. LONG-TERM MEMORY — Persistent knowledge across sessions
# Stored in: Vector database (ChromaDB, Pinecone)
# Lifetime: Permanent
# Example: "User prefers GPT-4 over Claude, works at Company X"

import chromadb
memory_db = chromadb.PersistentClient(path="./agent_memory")
long_term = memory_db.get_or_create_collection("user_preferences")

long_term.add(
    documents=["User prefers Python code examples", "User works on RAG systems"],
    ids=["mem_1", "mem_2"],
    metadatas=[{"user": "user_42", "type": "preference"}, {"user": "user_42", "type": "context"}],
)

# Before responding, search memory
relevant_memories = long_term.query(
    query_texts=["What coding language should I use?"],
    n_results=3, where={"user": "user_42"}
)

# 3. EPISODIC MEMORY — Past task summaries
# Stored in: PostgreSQL or MongoDB
# Lifetime: Configurable
# Example: "Last week, I helped the user debug their RAG pipeline"

# 4. WORKING MEMORY — Current task state (scratchpad)
# Stored in: Agent state dict
# Lifetime: Current task only
working_memory = {
    "current_task": "Research AI agents",
    "steps_completed": ["searched web", "found 5 articles"],
    "intermediate_results": {"articles_found": 5},
}
```

### Memory-Augmented Agent

```python
class MemoryAgent:
    def __init__(self, user_id: str):
        self.user_id = user_id
        self.short_term: list[dict] = []
        self.memory_db = chromadb.PersistentClient(path="./memory")
        self.long_term = self.memory_db.get_or_create_collection("memories")
    
    def remember(self, content: str, memory_type: str = "fact"):
        """Store a memory for later retrieval."""
        self.long_term.add(
            documents=[content],
            ids=[f"mem_{self.user_id}_{self.long_term.count()}"],
            metadatas=[{"user": self.user_id, "type": memory_type}],
        )
    
    def recall(self, query: str, n: int = 3) -> list[str]:
        """Recall relevant memories."""
        results = self.long_term.query(
            query_texts=[query], n_results=n,
            where={"user": self.user_id}
        )
        return results["documents"][0] if results["documents"] else []
    
    def chat(self, message: str) -> str:
        memories = self.recall(message)
        context = "\n".join(f"- {m}" for m in memories) if memories else "No relevant memories."
        
        self.short_term.append({"role": "user", "content": message})
        
        system = f"Your memories about this user:\n{context}\n\nUse these to personalize your response."
        # Call LLM with system prompt + short-term memory...
        return "Response based on memories"
```

---

> 💡 **Pro Tip:** Start with short-term memory (conversation buffer). Add long-term memory when you need personalization. Add episodic memory for agents that should learn from past tasks. Don't over-engineer memory — even ChatGPT uses simple conversation context.

---

<div align="center">

[← Topic 4](../topic-4-multi-agent-crewai/) · [📋 Phase 5](../README.md) · [Topic 6 →](../topic-6-agent-evaluation/)

</div>
