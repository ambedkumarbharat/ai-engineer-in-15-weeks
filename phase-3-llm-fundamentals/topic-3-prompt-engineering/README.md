<div align="center">

# 📝 Topic 3: Prompt Engineering

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-3_LLM_Fundamentals-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > **Prompt Engineering**

---

## 🎯 Why This Matters for AI Systems Engineering

Prompt engineering is the difference between an AI system that works 50% of the time and one that works 95% of the time. As an AI Systems Engineer, you'll design prompts that are:
- **Reliable** → Consistent outputs across thousands of requests
- **Structured** → Return parseable JSON, not unpredictable text
- **Efficient** → Use fewer tokens while maintaining quality
- **Testable** → Can be evaluated and iterated on

---

## 📚 Core Concepts

### 1. System Prompts — Setting the Foundation

```python
# A great system prompt has: Role, Context, Rules, Output Format
system_prompt = """You are an AI Systems Engineering tutor.

## Your Role
- Help students learn about RAG, agents, and LLM applications
- Provide practical, production-ready code examples in Python 3.10+
- Use real-world AI engineering scenarios

## Rules
1. Always include runnable code with type hints
2. Explain WHY, not just HOW
3. Mention common pitfalls
4. Keep responses under 500 words unless asked for detail
5. Use markdown formatting with code blocks

## Output Format
- Start with a one-sentence direct answer
- Then provide explanation with code
- End with a "Pro Tip" callout
"""
```

### 2. Few-Shot Prompting

```python
few_shot_prompt = """Classify the following customer support ticket into a category.

Categories: billing, technical, feature_request, bug_report

Example 1:
Ticket: "I was charged twice for my subscription this month"
Category: billing

Example 2:
Ticket: "The API returns 500 error when I send more than 10 requests"
Category: bug_report

Example 3:
Ticket: "Can you add support for PDF parsing in the RAG pipeline?"
Category: feature_request

Now classify:
Ticket: "{user_ticket}"
Category:"""
```

### 3. Chain-of-Thought (CoT)

```python
cot_prompt = """Analyze whether this RAG system architecture will work for the given requirements.

Requirements:
- 10,000 documents, each ~5 pages
- 100 queries per minute
- Sub-2-second response time
- 95% accuracy on retrieval

Proposed Architecture:
- ChromaDB for vector storage
- OpenAI text-embedding-3-small for embeddings
- GPT-4 for generation
- Chunk size: 1000 tokens with 200 overlap

Think step by step:

Step 1: Calculate total chunks
- 10,000 docs × 5 pages × ~500 words/page = 25M words
- ~500 words per 1000-token chunk = 50,000 chunks
- ChromaDB handles this easily (designed for < 1M vectors) ✅

Step 2: Calculate embedding cost
- 50,000 chunks × ~500 tokens avg = 25M tokens
- text-embedding-3-small: $0.02/1M tokens = $0.50 one-time ✅

Step 3: Evaluate query latency
- Embedding query: ~50ms
- ChromaDB search: ~20ms for 50K vectors
- GPT-4 generation: ~1-3 seconds ⚠️
- Total: ~1.5-3.5 seconds — RISKY for sub-2s requirement

Step 4: Recommendation
- Use GPT-3.5-turbo for generation (faster, cheaper) OR
- Use streaming to show results immediately
- Add Redis caching for repeated queries
"""
```

### 4. Output Formatting Prompts

```python
json_prompt = """Extract structured information from the following job description.

Job Description:
{job_description}

Return a JSON object with this exact structure:
```json
{{
    "title": "string",
    "company": "string",
    "location": "string",
    "salary_range": {{"min": number, "max": number, "currency": "string"}},
    "required_skills": ["string"],
    "nice_to_have_skills": ["string"],
    "experience_years": number,
    "remote": boolean
}}
```

Rules:
- If salary is not mentioned, set min and max to null
- Extract ALL technical skills mentioned
- Set remote to true if "remote" or "WFH" is mentioned
"""
```

### 5. Prompt Templates with Variables

```python
from string import Template

class PromptLibrary:
    """Reusable prompt templates for production AI systems."""
    
    RAG_QA = Template("""Use the following context to answer the user's question.
If the answer is not in the context, say "I don't have enough information."

Context:
$context

Question: $question

Answer:""")
    
    SUMMARIZE = Template("""Summarize the following text in $length.
Focus on: $focus_areas

Text:
$text

Summary:""")
    
    CLASSIFY = Template("""Classify the following text into one of these categories: $categories

Text: $text

Category:""")

# Usage
prompt = PromptLibrary.RAG_QA.substitute(
    context="RAG uses vector similarity to find relevant documents...",
    question="How does RAG find relevant documents?"
)
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **Prompt Engineering Guide** | 📄 Docs | [promptingguide.ai](https://www.promptingguide.ai/) |
| **OpenAI Prompt Engineering** | 📄 Docs | [platform.openai.com/docs/guides/prompt-engineering](https://platform.openai.com/docs/guides/prompt-engineering) |
| **Anthropic Prompt Engineering** | 📄 Docs | [docs.anthropic.com/claude/docs/prompt-engineering](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering) |
| **DeepLearning.AI Course** | 📹 Course | [ChatGPT Prompt Engineering](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/) |

---

## 📋 Quick Reference Cheatsheet

| Technique | When to Use | Example |
|-----------|------------|---------|
| Zero-shot | Simple tasks | "Classify this as positive/negative" |
| Few-shot | Need consistent format | Show 3 examples first |
| Chain-of-thought | Complex reasoning | "Think step by step" |
| System prompt | Set behavior for all msgs | Role + rules + format |
| JSON mode | Need structured output | `response_format={"type": "json_object"}` |
| Delimiter | Separate sections | Use `###` or `"""` or `---` |
| Role playing | Domain expertise | "You are a senior ML engineer" |
| Constraint | Control output | "Answer in exactly 3 bullet points" |

---

> 💡 **Pro Tip:** The best prompts are tested, versioned, and evaluated like code. Create a `prompts/` directory in your project, store each prompt as a separate file or constant, and track changes in Git. Measure prompt quality with evaluation metrics (accuracy, format compliance, latency) across a test set of 50+ examples.

---

<div align="center">

[← Topic 2: APIs](../topic-2-openai-anthropic-api/) · [📋 Phase 3](../README.md)

[Topic 4: LangChain Basics →](../topic-4-langchain-basics/)

</div>
