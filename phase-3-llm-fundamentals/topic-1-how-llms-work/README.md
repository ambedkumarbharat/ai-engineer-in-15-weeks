<div align="center">

# 🧠 Topic 1: How LLMs Work

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-2_Days-blue)
![Phase](https://img.shields.io/badge/Phase-3_LLM_Fundamentals-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > **How LLMs Work**

---

## 🎯 Why This Matters for AI Systems Engineering

Understanding how LLMs work isn't just academic — it directly impacts your engineering decisions:
- **Token limits** → Why you need chunking strategies for long documents
- **Attention mechanism** → Why context window size matters for RAG
- **Temperature** → How to control creativity vs precision in outputs
- **Tokenization** → Why "tiktoken" matters for cost estimation
- **Inference** → Why latency varies with prompt length

You don't need a PhD in ML, but you need to understand enough to make informed architecture decisions.

---

## 📚 Core Concepts

### 1. The Transformer Architecture (Simplified)

```
┌─────────────────────────────────────────────┐
│           Transformer Architecture           │
├─────────────────────────────────────────────┤
│                                             │
│  Input: "The cat sat on the"                │
│           ↓                                 │
│  ┌─────────────────┐                        │
│  │  Tokenization   │  "The" → 464           │
│  │  Text → Numbers │  "cat" → 9246          │
│  └────────┬────────┘  "sat" → 3290          │
│           ↓                                 │
│  ┌─────────────────┐                        │
│  │   Embeddings    │  Each token →          │
│  │  Numbers → Vecs │  768-dim vector        │
│  └────────┬────────┘                        │
│           ↓                                 │
│  ┌─────────────────┐                        │
│  │ Self-Attention  │  Each token looks at   │
│  │ (the magic!)    │  all other tokens      │
│  └────────┬────────┘                        │
│           ↓  (× many layers)               │
│  ┌─────────────────┐                        │
│  │  Feed Forward   │  Process attended      │
│  │  Network        │  information           │
│  └────────┬────────┘                        │
│           ↓                                 │
│  ┌─────────────────┐                        │
│  │  Output Layer   │  Probability over      │
│  │  (Softmax)      │  all tokens            │
│  └────────┬────────┘                        │
│           ↓                                 │
│  Output: "mat" (highest probability)        │
│                                             │
└─────────────────────────────────────────────┘
```

### 2. Tokenization — Why It Matters

```python
# Understanding tokenization is CRITICAL for cost estimation
import tiktoken

def analyze_tokens(text: str, model: str = "gpt-4"):
    """Analyze how text is tokenized."""
    encoding = tiktoken.encoding_for_model(model)
    tokens = encoding.encode(text)
    
    print(f"Text: '{text}'")
    print(f"Token count: {len(tokens)}")
    print(f"Tokens: {tokens}")
    
    # Decode each token to see the breakdown
    for i, token in enumerate(tokens):
        decoded = encoding.decode([token])
        print(f"  Token {i}: {token} → '{decoded}'")
    
    return len(tokens)

# Common text
analyze_tokens("Hello, how are you?")
# Token count: 5
# Token 0: 9906 → 'Hello'
# Token 1: 11 → ','
# Token 2: 1268 → ' how'
# Token 3: 527 → ' are'
# Token 4: 499 → ' you?'

# Technical text (more tokens due to rare words)
analyze_tokens("LangChain's LCEL enables composable LLM pipelines")

# Cost estimation
def estimate_cost(prompt: str, response_length: int = 500, 
                  model: str = "gpt-4") -> dict:
    """Estimate API call cost."""
    encoding = tiktoken.encoding_for_model(model)
    input_tokens = len(encoding.encode(prompt))
    output_tokens = response_length  # Estimate
    
    # Pricing (as of 2024, GPT-4 Turbo)
    prices = {
        "gpt-4": {"input": 0.03, "output": 0.06},      # per 1K tokens
        "gpt-4-turbo": {"input": 0.01, "output": 0.03},
        "gpt-3.5-turbo": {"input": 0.0005, "output": 0.0015},
    }
    
    price = prices.get(model, prices["gpt-4"])
    input_cost = (input_tokens / 1000) * price["input"]
    output_cost = (output_tokens / 1000) * price["output"]
    
    return {
        "input_tokens": input_tokens,
        "estimated_output_tokens": output_tokens,
        "input_cost": f"${input_cost:.4f}",
        "output_cost": f"${output_cost:.4f}",
        "total_cost": f"${input_cost + output_cost:.4f}",
    }

cost = estimate_cost("Explain how RAG works in detail", response_length=800, model="gpt-4")
print(cost)
```

### 3. Key Parameters You Control

```python
# Temperature — Controls randomness
# 0.0 = Deterministic (same input → same output). Best for: factual answers, code generation
# 0.7 = Balanced (default). Best for: general conversation, creative writing
# 1.5+ = Very creative/random. Best for: brainstorming, creative tasks

# Max Tokens — Controls response length
# 100 tokens ≈ 75 words (short answer)
# 500 tokens ≈ 375 words (paragraph)  
# 2000 tokens ≈ 1500 words (full article)

# Top-P (nucleus sampling) — Alternative to temperature
# 0.1 = Only consider top 10% probability tokens (focused)
# 0.9 = Consider top 90% probability tokens (diverse)

# Context Window — How much text the model can "see"
# GPT-3.5-turbo: 16K tokens (~12K words)
# GPT-4-turbo:   128K tokens (~96K words)
# Claude 3:      200K tokens (~150K words)
# Llama 3:       8K-128K tokens
```

### 4. Self-Attention — The Key Innovation

```python
def simplified_attention(query: str, keys: list[str], values: list[str]) -> str:
    """Simplified demonstration of how attention works.
    
    In reality:
    - Query, Key, Value are all vectors (not strings)
    - Attention scores are computed via dot product
    - Results are weighted sums of value vectors
    """
    
    # Simulate: "How relevant is each key to the query?"
    relevance_scores = {}
    for key, value in zip(keys, values):
        # In real transformers, this is a dot product of vectors
        shared_words = set(query.lower().split()) & set(key.lower().split())
        score = len(shared_words) / max(len(query.split()), 1)
        relevance_scores[key] = (score, value)
    
    # Sort by relevance (like softmax in real attention)
    sorted_results = sorted(relevance_scores.items(), key=lambda x: x[1][0], reverse=True)
    
    print(f"Query: '{query}'")
    print(f"Attention scores:")
    for key, (score, value) in sorted_results:
        bar = "█" * int(score * 20)
        print(f"  [{score:.2f}] {bar} '{key}' → '{value}'")
    
    # Return highest-attention value
    return sorted_results[0][1][1]

# Demo
simplified_attention(
    query="What is machine learning?",
    keys=["machine learning basics", "cooking recipes", "learning algorithms", "weather forecast"],
    values=["ML is a subset of AI", "How to make pasta", "Algorithms that learn from data", "Rain expected tomorrow"]
)
```

### 5. How LLMs Generate Text (Auto-regressive)

```python
import random

def simulate_llm_generation(prompt: str, max_tokens: int = 20, temperature: float = 0.7) -> str:
    """Simulate how LLMs generate text one token at a time."""
    
    # Simplified "vocabulary" with probabilities
    next_token_probs = {
        "What is": {"RAG": 0.3, "AI": 0.25, "machine": 0.2, "the": 0.15, "a": 0.1},
        "RAG": {"?": 0.2, "stands": 0.3, "is": 0.25, "pipeline": 0.15, "system": 0.1},
        "stands": {"for": 0.9, "out": 0.1},
        "for": {"Retrieval": 0.8, "something": 0.1, "the": 0.1},
        "Retrieval": {"Augmented": 0.95, "based": 0.05},
        "Augmented": {"Generation": 0.95, "search": 0.05},
    }
    
    tokens = prompt.split()
    generated = list(tokens)
    
    print(f"Prompt: '{prompt}'")
    print(f"Generating (temperature={temperature}):")
    
    for step in range(max_tokens):
        last_token = generated[-1] if generated else ""
        last_two = " ".join(generated[-2:]) if len(generated) >= 2 else last_token
        
        # Get probabilities for next token
        probs = next_token_probs.get(last_two, next_token_probs.get(last_token, {"[END]": 1.0}))
        
        if "[END]" in probs and probs["[END]"] == 1.0:
            break
        
        # Apply temperature
        adjusted = {}
        for token, prob in probs.items():
            adjusted[token] = prob ** (1 / max(temperature, 0.01))
        total = sum(adjusted.values())
        adjusted = {t: p/total for t, p in adjusted.items()}
        
        # Sample
        chosen = random.choices(list(adjusted.keys()), list(adjusted.values()))[0]
        generated.append(chosen)
        
        print(f"  Step {step+1}: chose '{chosen}' (p={probs.get(chosen, 0):.2f})")
    
    return " ".join(generated)

result = simulate_llm_generation("What is", max_tokens=10, temperature=0.3)
print(f"\nFinal: {result}")
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **3Blue1Brown — Transformers** | 📹 YouTube | [But what is a GPT?](https://www.youtube.com/watch?v=wjZofJX0v4M) |
| **Andrej Karpathy — Let's build GPT** | 📹 YouTube | [Let's build GPT from scratch](https://www.youtube.com/watch?v=kCc8FmEb1nY) |
| **Jay Alammar — The Illustrated Transformer** | 📄 Blog | [jalammar.github.io](https://jalammar.github.io/illustrated-transformer/) |
| **Hugging Face NLP Course** | 📄 Course | [huggingface.co/learn](https://huggingface.co/learn/nlp-course/) |
| **Attention Is All You Need (Paper)** | 📄 Paper | [arxiv.org/abs/1706.03762](https://arxiv.org/abs/1706.03762) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Thinking LLMs "understand" | They predict next tokens, not reason | Treat them as powerful pattern matchers |
| Ignoring token limits | API errors or truncated context | Always count tokens before calling |
| Same temperature for everything | Wrong trade-off for the task | Use 0 for facts, 0.7 for creative |
| Not considering latency | Users wait too long | Know that latency scales with output length |
| Ignoring costs | Surprise bills | Track token usage, set budgets |

---

## 📋 Quick Reference Cheatsheet

| Concept | What It Is | Why It Matters |
|---------|-----------|----------------|
| Transformer | Neural network architecture | Powers all modern LLMs |
| Tokenization | Text → number sequences | Determines cost and limits |
| Attention | Tokens "look at" each other | Enables context understanding |
| Context window | Max tokens model can process | Limits input + output size |
| Temperature | Randomness control (0-2) | Controls output creativity |
| Top-P | Alternative sampling method | Fine-tune output diversity |
| Inference | Running the model | Where latency comes from |
| Fine-tuning | Training on custom data | Customize for your domain |

---

> 💡 **Pro Tip:** You don't need to understand every mathematical detail of transformers. Focus on the practical implications: how tokenization affects cost, why context windows limit RAG designs, and how temperature affects output quality. These are the decisions you'll make daily as an AI Systems Engineer.

---

<div align="center">

[🏠 Home](../../README.md) · [📋 Phase 3](../README.md)

[Topic 2: OpenAI & Anthropic APIs →](../topic-2-openai-anthropic-api/)

</div>
