<div align="center">

# 🦜 Topic 4: LangChain Basics

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-3_LLM_Fundamentals-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > **LangChain Basics**

---

## 🎯 Why This Matters for AI Systems Engineering

LangChain is the most popular framework for building LLM applications. It provides:
- **Chains** → Composable sequences of LLM calls and transformations
- **Memory** → Conversation history management
- **Output Parsers** → Structured output from LLMs
- **LCEL** → LangChain Expression Language for declarative pipelines
- **Integration** → 100+ integrations with databases, APIs, and tools

---

## 📚 Core Concepts

### 1. Basic Chain with LCEL

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# Initialize LLM
llm = ChatOpenAI(model="gpt-4", temperature=0.7)

# Create prompt template
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful AI tutor for {subject}."),
    ("user", "{question}"),
])

# Create chain with LCEL (pipe operator)
chain = prompt | llm | StrOutputParser()

# Invoke
result = chain.invoke({
    "subject": "AI Systems Engineering",
    "question": "What is the difference between RAG and fine-tuning?"
})
print(result)
```

### 2. Output Parsers — Get Structured Data

```python
from langchain_core.output_parsers import JsonOutputParser
from langchain_core.prompts import ChatPromptTemplate
from pydantic import BaseModel, Field

class CodeReview(BaseModel):
    """Structured code review output."""
    quality_score: int = Field(description="Code quality 1-10")
    issues: list[str] = Field(description="List of issues found")
    suggestions: list[str] = Field(description="Improvement suggestions")
    is_production_ready: bool = Field(description="Whether code is production-ready")

parser = JsonOutputParser(pydantic_object=CodeReview)

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a senior code reviewer. Review the following code.\n{format_instructions}"),
    ("user", "Review this code:\n```python\n{code}\n```"),
])

chain = prompt | llm | parser

result = chain.invoke({
    "code": "def add(a, b): return a + b",
    "format_instructions": parser.get_format_instructions(),
})
print(result)  # {'quality_score': 6, 'issues': [...], ...}
```

### 3. Conversation Memory

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.chat_history import InMemoryChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

llm = ChatOpenAI(model="gpt-4")

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful AI assistant."),
    MessagesPlaceholder(variable_name="history"),
    ("user", "{input}"),
])

chain = prompt | llm

# Session-based memory
store = {}

def get_session_history(session_id: str):
    if session_id not in store:
        store[session_id] = InMemoryChatMessageHistory()
    return store[session_id]

with_memory = RunnableWithMessageHistory(
    chain, get_session_history,
    input_messages_key="input",
    history_messages_key="history",
)

# First message
config = {"configurable": {"session_id": "user_42"}}
response1 = with_memory.invoke({"input": "My name is Raj"}, config=config)
print(response1.content)

# Second message — remembers context
response2 = with_memory.invoke({"input": "What's my name?"}, config=config)
print(response2.content)  # "Your name is Raj"
```

### 4. Chains with Multiple Steps

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

# Multi-step chain: Generate → Critique → Improve
generate_prompt = ChatPromptTemplate.from_template(
    "Write a short explanation of {topic} for beginners."
)

critique_prompt = ChatPromptTemplate.from_template(
    "Critique this explanation and list 3 improvements:\n\n{explanation}"
)

improve_prompt = ChatPromptTemplate.from_template(
    "Improve this explanation based on the critique:\n\nOriginal:\n{explanation}\n\nCritique:\n{critique}"
)

# Chain them together
chain = (
    {"topic": RunnablePassthrough()}
    | generate_prompt | llm | StrOutputParser()
    | (lambda x: {"explanation": x})
    | RunnablePassthrough.assign(
        critique=critique_prompt | llm | StrOutputParser()
    )
    | improve_prompt | llm | StrOutputParser()
)

result = chain.invoke("vector databases")
print(result)
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **LangChain Docs** | 📄 Docs | [python.langchain.com](https://python.langchain.com/) |
| **LangChain Crash Course** | 📹 YouTube | [Patrick Loeber](https://www.youtube.com/watch?v=LbT1yp6quS8) |
| **LCEL Guide** | 📄 Docs | [python.langchain.com/docs/expression_language](https://python.langchain.com/docs/concepts/lcel/) |
| **LangChain Templates** | 📄 GitHub | [langchain-ai/langchain](https://github.com/langchain-ai/langchain) |

---

## 📋 Quick Reference Cheatsheet

| Concept | Code | Use Case |
|---------|------|----------|
| Chat model | `ChatOpenAI(model="gpt-4")` | LLM interactions |
| Prompt template | `ChatPromptTemplate.from_messages()` | Reusable prompts |
| LCEL chain | `prompt \| llm \| parser` | Composable pipelines |
| String parser | `StrOutputParser()` | Plain text output |
| JSON parser | `JsonOutputParser()` | Structured output |
| Memory | `RunnableWithMessageHistory()` | Conversation state |
| Passthrough | `RunnablePassthrough()` | Pass data through |
| Batch | `chain.batch([inputs])` | Process multiple |

---

> 💡 **Pro Tip:** LangChain's LCEL (pipe operator `|`) is the modern way to build chains. Avoid the legacy `LLMChain` and `SequentialChain` classes. LCEL chains support streaming, batching, and async out of the box. Also, always use `langchain-openai` (not `langchain`) for model integrations — packages are now split.

---

<div align="center">

[← Topic 3: Prompt Engineering](../topic-3-prompt-engineering/) · [📋 Phase 3](../README.md)

[Topic 5: Ollama & Local LLMs →](../topic-5-ollama-local-llms/)

</div>
