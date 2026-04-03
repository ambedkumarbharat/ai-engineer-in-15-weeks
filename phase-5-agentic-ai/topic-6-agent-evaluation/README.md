<div align="center">

# 📊 Topic 6: Agent Evaluation

![Difficulty](https://img.shields.io/badge/Difficulty-🔴_Advanced-red) ![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 5](../README.md) > **Agent Evaluation**

---

## 🎯 Why This Matters

Agents are non-deterministic — they make different decisions each run. You need systematic evaluation to measure if they actually accomplish tasks correctly, efficiently, and safely.

---

## 📚 Core Concepts

### Evaluation Metrics

| Metric | Description | How to Measure |
|--------|-------------|----------------|
| **Task Completion** | Did the agent finish the task? | Binary: yes/no |
| **Accuracy** | Is the final output correct? | Compare to ground truth |
| **Steps Taken** | How many actions to complete? | Count tool calls |
| **Cost** | Total LLM tokens used | Track usage |
| **Latency** | Time from start to finish | Wall clock time |
| **Tool Usage** | Were the right tools used? | Compare to expected |
| **Safety** | No harmful actions taken | Review action log |

### Evaluation Framework

```python
from dataclasses import dataclass
from datetime import datetime
import time

@dataclass
class EvalResult:
    task: str
    success: bool
    steps: int
    total_tokens: int
    cost: float
    latency_seconds: float
    tools_used: list[str]
    final_output: str
    errors: list[str]

def evaluate_agent(agent, test_cases: list[dict]) -> list[EvalResult]:
    """Run agent on test cases and collect metrics."""
    results = []
    for case in test_cases:
        start = time.time()
        try:
            output = agent.run(case["task"])
            success = case["validator"](output)
            errors = []
        except Exception as e:
            output = ""
            success = False
            errors = [str(e)]
        
        results.append(EvalResult(
            task=case["task"], success=success,
            steps=agent.step_count, total_tokens=agent.total_tokens,
            cost=agent.total_cost, latency_seconds=time.time() - start,
            tools_used=agent.tools_used, final_output=output, errors=errors,
        ))
    
    # Print summary
    successes = sum(1 for r in results if r.success)
    print(f"Results: {successes}/{len(results)} passed")
    print(f"Avg steps: {sum(r.steps for r in results)/len(results):.1f}")
    print(f"Avg cost: ${sum(r.cost for r in results)/len(results):.4f}")
    return results

# Test cases
test_cases = [
    {"task": "What is the capital of France?", "validator": lambda x: "paris" in x.lower()},
    {"task": "Calculate 15% of 230", "validator": lambda x: "34.5" in x},
]
```

---

> 💡 **Pro Tip:** Build an evaluation suite of 50+ test cases before optimizing your agent. Run evaluations after every change. Track metrics over time to see if changes help or hurt.

---

<div align="center">

[← Topic 5](../topic-5-memory-systems/) · [📋 Phase 5](../README.md) · [🔨 Mini Project →](../mini-project/)

</div>
