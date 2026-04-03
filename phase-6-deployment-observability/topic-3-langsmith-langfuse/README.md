<div align="center">

# 🔍 Topic 3: LangSmith & Langfuse
![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow) ![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > **LangSmith & Langfuse**

---

## 🎯 Why This Matters

LLM observability is how you debug, optimize, and monitor AI systems in production. LangSmith and Langfuse let you trace every LLM call, see inputs/outputs, measure latency, and track costs.

---

## 📚 Core Concepts

### LangSmith Setup

`python
import os
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = "ls_your_key"
os.environ["LANGCHAIN_PROJECT"] = "my-rag-app"

# That's it! All LangChain calls are now traced automatically.
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4")
response = llm.invoke("What is RAG?")
# View trace at: smith.langchain.com
`

### Langfuse Setup (Open Source Alternative)

`python
from langfuse import Langfuse
from langfuse.decorators import observe

langfuse = Langfuse(public_key="pk-...", secret_key="sk-...", host="https://cloud.langfuse.com")

@observe()
def rag_query(question: str) -> str:
    # All LLM calls inside are automatically traced
    context = retrieve_context(question)
    answer = generate_answer(question, context)
    return answer
`

### What to Monitor

| Metric | Why | Tool |
|--------|-----|------|
| LLM latency | User experience | LangSmith/Langfuse |
| Token usage | Cost control | LangSmith/Langfuse |
| Error rate | Reliability | Application logs |
| Retrieval quality | RAG accuracy | Custom eval |
| User satisfaction | Product quality | Feedback widget |

---

> 💡 **Pro Tip:** Use LangSmith (free tier: 5K traces/mo) for LangChain projects. Use Langfuse (open source, self-host) for non-LangChain projects or when you need full data ownership.

---

<div align="center">

[← Topic 2](../topic-2-cloud-deployment/) · [📋 Phase 6](../README.md) · [Topic 4 →](../topic-4-cost-latency-optimization/)

</div>
