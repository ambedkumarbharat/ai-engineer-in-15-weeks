<div align="center">

# 📊 Topic 5: RAG Evaluation

![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red)
![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **RAG Evaluation**

---

## 🎯 Why This Matters

Building a RAG system is easy. Building a **good** RAG system requires measuring quality. You need metrics to know if changes improve or degrade performance.

---

## 📚 Core Concepts

### 1. RAG Evaluation Metrics

```
┌─────────────────────────────────────────────┐
│           RAG Quality Dimensions             │
├─────────────────────────────────────────────┤
│                                             │
│  RETRIEVAL QUALITY:                         │
│  • Precision@K — Are retrieved docs relevant?│
│  • Recall@K — Did we find all relevant docs? │
│  • MRR — How high is the first relevant doc? │
│                                             │
│  GENERATION QUALITY:                        │
│  • Faithfulness — Does answer match context? │
│  • Answer Relevance — Does it answer the Q?  │
│  • Groundedness — Is it based on retrieved   │
│    docs, not hallucinated?                  │
│                                             │
└─────────────────────────────────────────────┘
```

### 2. RAGAS Framework

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_precision
from datasets import Dataset

# Prepare evaluation dataset
eval_data = {
    "question": ["What is RAG?", "How do embeddings work?"],
    "answer": ["RAG stands for...", "Embeddings convert text to vectors..."],
    "contexts": [
        ["RAG is a technique that combines retrieval with generation..."],
        ["Embeddings are dense vector representations of text..."],
    ],
    "ground_truth": ["RAG is Retrieval Augmented Generation...", "Embeddings map text to numerical vectors..."],
}

dataset = Dataset.from_dict(eval_data)
results = evaluate(dataset, metrics=[faithfulness, answer_relevancy, context_precision])
print(results)
# {'faithfulness': 0.92, 'answer_relevancy': 0.88, 'context_precision': 0.85}
```

### 3. Custom Evaluation

```python
def evaluate_rag(pipeline, test_cases: list[dict]) -> dict:
    """Custom RAG evaluation with multiple metrics."""
    results = {"correct": 0, "total": len(test_cases), "scores": []}
    
    for case in test_cases:
        answer = pipeline.query(case["question"])
        
        # Check if expected keywords are in the answer
        keywords_found = sum(
            1 for kw in case["expected_keywords"]
            if kw.lower() in answer["answer"].lower()
        )
        keyword_score = keywords_found / len(case["expected_keywords"])
        
        # Check source citation
        source_cited = any(s in str(answer["sources"]) for s in case.get("expected_sources", []))
        
        results["scores"].append({
            "question": case["question"],
            "keyword_score": keyword_score,
            "source_cited": source_cited,
        })
        
        if keyword_score >= 0.5:
            results["correct"] += 1
    
    results["accuracy"] = results["correct"] / results["total"]
    return results

# Test cases
test_cases = [
    {
        "question": "What is RAG?",
        "expected_keywords": ["retrieval", "augmented", "generation", "context"],
        "expected_sources": ["rag_guide.md"],
    },
]
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **RAGAS Docs** | 📄 Docs | [docs.ragas.io](https://docs.ragas.io/) |
| **RAG Evaluation Guide** | 📄 Blog | [LangChain Blog](https://blog.langchain.dev/evaluating-rag-pipelines-with-ragas/) |

> 💡 **Pro Tip:** Create 50+ test question-answer pairs for your domain. Run evaluation after every change to your chunking, embedding, or prompt strategy. A 1% improvement in retrieval precision often translates to 5%+ improvement in answer quality.

---

<div align="center">

[← Topic 4](../topic-4-rag-pipeline-design/) · [📋 Phase 4](../README.md) · [Topic 6 →](../topic-6-hybrid-search/)

</div>
