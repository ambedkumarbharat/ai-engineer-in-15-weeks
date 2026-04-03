<div align="center">

# 🧩 How LLMs Work — Practice Problems

![Problems](https://img.shields.io/badge/Problems-10-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 3](../README.md) > [How LLMs Work](README.md) > **Problems**

---

## Problem 01 🧩
**Difficulty:** 🟢 Easy
### Token Counter
**Problem:** Build a token counter that takes a text input and returns token count, estimated cost, and the actual tokens using `tiktoken` for GPT-3.5 and GPT-4.
**Hint:** Use `tiktoken.encoding_for_model()` and compare costs between models.

---

## Problem 02 🧩
**Difficulty:** 🟢 Easy
### Temperature Experiment
**Problem:** Call an LLM with the same prompt at temperatures 0.0, 0.5, 1.0, and 1.5 (10 times each). Measure output diversity (unique responses) and length variance at each temperature.
**Hint:** Use Ollama for free experiments. Track unique outputs with a set.

---

## Problem 03 🧩
**Difficulty:** 🟢 Easy
### Context Window Calculator
**Problem:** Build a tool that calculates how much of a model's context window is available for the response, given a system prompt and user message. Support GPT-3.5 (16K), GPT-4 (128K), and Claude 3 (200K).
**Hint:** `available = context_window - system_tokens - user_tokens - safety_margin`.

---

## Problem 04 🧩
**Difficulty:** 🟡 Medium
### Tokenization Visualizer
**Problem:** Build a CLI tool that shows how text is tokenized: display each token with its ID, highlight multi-token words, and show token boundaries. Compare tokenization between GPT-3.5 and GPT-4 encodings.
**Hint:** Use `tiktoken` with `cl100k_base` and `p50k_base` encodings. Color-code tokens using `rich`.

---

## Problem 05 🧩
**Difficulty:** 🟡 Medium
### Cost Estimation Dashboard
**Problem:** Build a cost estimator that takes a dataset of prompts and estimates total API cost across different models (GPT-3.5, GPT-4, GPT-4-turbo, Claude 3). Show cost breakdown and recommend the best model.
**Hint:** Count input tokens, estimate output tokens (1.5x input), apply per-model pricing.

---

## Problem 06 🧩
**Difficulty:** 🟡 Medium
### Attention Visualizer
**Problem:** Build a simplified attention visualizer that shows which words in a sentence "attend to" which other words, using word overlap as a proxy for attention scores.
**Hint:** Create an NxN matrix where score[i][j] = word similarity between word i and word j. Display as a heatmap.

---

## Problem 07 🧩
**Difficulty:** 🟡 Medium
### Prompt Length vs Quality Experiment
**Problem:** Design an experiment that tests how prompt length affects output quality. Use the same question with 3 prompt lengths (short, medium, detailed) and evaluate answer quality on 20 test questions.
**Hint:** Rate outputs on accuracy (0-1), completeness (0-1), and conciseness (0-1). Average across test set.

---

## Problem 08 🧩
**Difficulty:** 🔴 Hard
### Simple Text Generator
**Problem:** Build a simple next-word predictor using frequency-based n-grams. Train on a text corpus, then generate text word by word with temperature-based sampling.
**Hint:** Build a dictionary of `{(word_n-2, word_n-1): {next_word: count}}`. Apply temperature to probabilities before sampling.

---

## Problem 09 🧩
**Difficulty:** 🔴 Hard
### Model Comparison Benchmark
**Problem:** Build a benchmarking system that compares multiple LLMs (via Ollama) on: response quality, latency, token efficiency, and consistency across 50 standardized test prompts.
**Hint:** Run each prompt 3 times per model. Track p50/p95 latency. Use LLM-as-judge for quality scoring.

---

## Problem 10 🧩
**Difficulty:** 🔴 Hard
### Syllable Counter & Readability Scorer
**Problem:** Implement the Flesch-Kincaid readability formula from scratch. Then use it to analyze LLM outputs at different temperature settings and prove that lower temperature produces more readable text.
**Hint:** Syllable counting: count vowel groups in each word. FK Grade = 0.39 × (words/sentences) + 11.8 × (syllables/words) - 15.59.

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 3](../README.md) · [🏠 Home](../../README.md)

</div>
