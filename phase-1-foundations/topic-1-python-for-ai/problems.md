<div align="center">

# 🧩 Python for AI — Practice Problems

![Problems](https://img.shields.io/badge/Problems-15-blue)
![Difficulty](https://img.shields.io/badge/Mix-Easy_→_Hard-gradient)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 1](../README.md) > [Python for AI](README.md) > **Problems**

---

## Problem 01 🧩

**Difficulty:** 🟢 Easy

### Build a Document Metadata Extractor

**Problem:** Create a Pydantic model `DocumentMetadata` that validates and extracts metadata from raw text documents used in a RAG pipeline.

**Real-world AI use case:** In RAG systems, every document needs structured metadata (title, word count, language, etc.) before being stored in a vector database.

**Concept being tested:** Pydantic models, field validators, computed properties

**Requirements:**
- Fields: `title` (str), `content` (str), `author` (str, optional), `tags` (list of str)
- Validators: title must be non-empty, content must have at least 10 words, tags must be lowercase
- Computed property: `word_count` that returns the number of words in content
- Method: `to_embedding_input()` → returns `f"{title}. {content[:500]}"`

**Hint:** Use `@field_validator` for individual field validation and `@property` for computed fields.

**Expected Output:**
```python
doc = DocumentMetadata(
    title="RAG Tutorial",
    content="Retrieval Augmented Generation is a technique that enhances LLM responses by providing relevant context from external knowledge bases",
    author="AI Engineer",
    tags=["RAG", "LLM", "Tutorial"]
)
print(doc.word_count)  # 18
print(doc.tags)  # ['rag', 'llm', 'tutorial']
print(doc.to_embedding_input()[:50])  # "RAG Tutorial. Retrieval Augmented Generation is a "
```

---

## Problem 02 🧩

**Difficulty:** 🟢 Easy

### Type-Safe Configuration Loader

**Problem:** Create a configuration system for an AI application using dataclasses and type hints.

**Real-world AI use case:** AI systems need configuration for model selection, temperature settings, API keys, and retry policies. Type-safe configs prevent runtime errors.

**Concept being tested:** Dataclasses, type hints, default values, `__post_init__`

**Requirements:**
- `LLMConfig` dataclass with: `model` (str), `temperature` (float, default 0.7), `max_tokens` (int, default 1000), `api_key` (str)
- `AppConfig` dataclass that contains `LLMConfig` plus: `debug` (bool), `log_level` (str)
- `__post_init__` validation: temperature must be 0.0–2.0, max_tokens must be 1–4096
- Method `mask_api_key()` → returns key with only first 4 and last 4 chars visible

**Hint:** Use `field(default_factory=...)` for mutable defaults and raise `ValueError` in `__post_init__`.

**Expected Output:**
```python
config = LLMConfig(model="gpt-4", api_key="sk-abc123xyz789")
print(config.mask_api_key())  # "sk-a********z789"
print(config.temperature)  # 0.7
```

---

## Problem 03 🧩

**Difficulty:** 🟢 Easy

### Batch Processing Generator

**Problem:** Write a generator function that yields batches of items from a large list, with configurable batch size and optional padding.

**Real-world AI use case:** When sending documents to an embedding API, you batch them (e.g., 100 at a time) to stay under rate limits and optimize throughput.

**Concept being tested:** Generators, `yield`, memory efficiency

**Requirements:**
- Function `batchify(items, batch_size, pad_last=False, pad_value=None)`
- Yields lists of `batch_size` items
- If `pad_last=True`, pad the final batch to batch_size with `pad_value`
- Must work lazily (don't load all batches at once)

**Hint:** Use a for loop with range and slicing, then yield each slice.

**Expected Output:**
```python
items = list(range(7))
for batch in batchify(items, batch_size=3, pad_last=True, pad_value=-1):
    print(batch)
# [0, 1, 2]
# [3, 4, 5]
# [6, -1, -1]
```

---

## Problem 04 🧩

**Difficulty:** 🟢 Easy

### Retry Decorator with Exponential Backoff

**Problem:** Build a decorator that retries a function on failure with exponential backoff.

**Real-world AI use case:** LLM API calls frequently fail due to rate limits or timeouts. Automatic retries with backoff are essential in production AI systems.

**Concept being tested:** Decorators, closures, exception handling, `functools.wraps`

**Requirements:**
- Decorator `retry(max_attempts=3, base_delay=1.0, exponential=True)`
- On each failure, wait `base_delay * 2^attempt` seconds (if exponential)
- Log each retry attempt with attempt number and error
- Re-raise the last exception if all attempts fail
- Preserve original function metadata with `functools.wraps`

**Hint:** Use `time.sleep()` for delays and nest three functions (decorator factory → decorator → wrapper).

**Expected Output:**
```python
@retry(max_attempts=3, base_delay=0.1, exponential=True)
def unstable_api_call():
    import random
    if random.random() < 0.7:
        raise ConnectionError("API timeout")
    return "Success!"

result = unstable_api_call()  # Retries up to 3 times
# Attempt 1 failed: API timeout. Retrying in 0.1s...
# Attempt 2 failed: API timeout. Retrying in 0.2s...
# Success on attempt 3!
```

---

## Problem 05 🧩

**Difficulty:** 🟢 Easy

### Text Statistics Calculator

**Problem:** Create a class that computes various text statistics useful for document analysis.

**Real-world AI use case:** Before chunking documents for RAG, you need to understand text properties (length, reading level, language) to choose optimal chunk sizes.

**Concept being tested:** Classes, properties, static methods, string processing

**Requirements:**
- Class `TextAnalyzer` with the text as input
- Properties: `word_count`, `sentence_count`, `avg_word_length`, `avg_sentence_length`
- Method: `keyword_density(keyword) -> float` (percentage of words matching keyword)
- Method: `reading_time(wpm=200) -> float` (minutes to read)
- Static method: `is_english(text) -> bool` (check if >80% of chars are ASCII letters/spaces)

**Hint:** Use `@property` for computed attributes and split on `.!?` for sentences.

**Expected Output:**
```python
analyzer = TextAnalyzer("AI is transforming software engineering. Machine learning is powerful.")
print(analyzer.word_count)  # 9
print(analyzer.sentence_count)  # 2
print(f"{analyzer.keyword_density('is'):.1%}")  # 22.2%
print(f"{analyzer.reading_time():.2f} min")  # 0.04 min
```

---

## Problem 06 🧩

**Difficulty:** 🟡 Medium

### Async LLM Client with Semaphore

**Problem:** Build an async LLM client that limits concurrent API calls using `asyncio.Semaphore`.

**Real-world AI use case:** When processing 1000 documents through an LLM, you can't send all 1000 requests at once — you'll hit rate limits. A semaphore limits concurrency.

**Concept being tested:** `asyncio`, `Semaphore`, concurrent programming, rate limiting

**Requirements:**
- Class `AsyncLLMClient` with `max_concurrent` parameter
- Method `async call(prompt: str) -> str` that respects the semaphore
- Method `async batch_call(prompts: list[str]) -> list[str]` that processes all prompts concurrently (but limited)
- Track total calls made and total time
- Simulated API latency of 0.5 seconds per call

**Hint:** Create `asyncio.Semaphore(max_concurrent)` in `__init__` and use `async with self.semaphore:` in the call method.

**Expected Output:**
```python
client = AsyncLLMClient(max_concurrent=3)
prompts = [f"Question {i}" for i in range(9)]
results = await client.batch_call(prompts)
# With max_concurrent=3 and 9 prompts: ~1.5s instead of ~4.5s
print(f"Processed {len(results)} prompts")
print(f"Total calls: {client.total_calls}")
```

---

## Problem 07 🧩

**Difficulty:** 🟡 Medium

### Document Chunking Pipeline

**Problem:** Build a pipeline that reads text, splits it into chunks with overlap, and returns structured chunk objects.

**Real-world AI use case:** This is the exact chunking step in every RAG system. Documents must be split into chunks that fit within an LLM's context window while maintaining semantic coherence.

**Concept being tested:** Generators, dataclasses, method chaining, text processing

**Requirements:**
- `Chunk` dataclass: `text`, `chunk_id`, `source`, `start_idx`, `end_idx`, `metadata`
- `ChunkingPipeline` class with configurable `chunk_size` (in words) and `overlap` (in words)
- Method `chunk_text(text, source) -> Generator[Chunk]`
- Method `chunk_file(filepath) -> Generator[Chunk]` (reads and chunks a file)
- Each chunk gets a unique ID: `{source}::chunk_{n}`

**Hint:** Use word-based splitting with overlap: `words[i:i+chunk_size]` with `i += chunk_size - overlap`.

**Expected Output:**
```python
pipeline = ChunkingPipeline(chunk_size=50, overlap=10)
text = "Word " * 120  # 120 words
chunks = list(pipeline.chunk_text(text, source="test.txt"))
print(f"Total chunks: {len(chunks)}")  # 3 chunks
print(f"First chunk ID: {chunks[0].chunk_id}")  # test.txt::chunk_0
print(f"First chunk words: {len(chunks[0].text.split())}")  # 50
```

---

## Problem 08 🧩

**Difficulty:** 🟡 Medium

### Prompt Template Engine

**Problem:** Build a prompt template system that supports variables, conditionals, and composition.

**Real-world AI use case:** Production AI systems use template engines to generate prompts dynamically based on user input, context, and system configuration.

**Concept being tested:** String manipulation, classes, template patterns, composition

**Requirements:**
- Class `PromptTemplate` initialized with a template string containing `{variable}` placeholders
- Method `render(**kwargs) -> str` that fills in variables
- Method `validate()` → checks all required variables are listed
- Support for optional sections: `{{#if variable}}content{{/if}}`
- Class method `compose(*templates) -> PromptTemplate` to chain templates together

**Hint:** Use regex `re.findall(r'\{(\w+)\}', template)` to extract variables and `str.replace()` for rendering.

**Expected Output:**
```python
template = PromptTemplate(
    "You are a {role}. Answer the question: {question}\n"
    "{{#if context}}Use this context: {context}{{/if}}"
)
print(template.required_variables)  # ['role', 'question']
result = template.render(role="AI tutor", question="What is RAG?", context="RAG is...")
print(result)
# You are a AI tutor. Answer the question: What is RAG?
# Use this context: RAG is...
```

---

## Problem 09 🧩

**Difficulty:** 🟡 Medium

### Custom Collection with Iteration Protocol

**Problem:** Create a `DocumentStore` class that implements Python's iteration protocol and supports filtering, mapping, and reducing over documents.

**Real-world AI use case:** In AI pipelines, you often need to iterate over document collections with filtering (e.g., only PDFs), mapping (e.g., extract text), and aggregation (e.g., total tokens).

**Concept being tested:** `__iter__`, `__len__`, `__getitem__`, protocol implementation

**Requirements:**
- `DocumentStore` implements `__iter__`, `__len__`, `__getitem__`, `__contains__`
- Method `filter(predicate) -> DocumentStore` — returns new store with filtered docs
- Method `map(func) -> list` — applies function to each doc
- Method `add(doc)`, `remove(doc_id)`, `get(doc_id)`
- Support for `doc in store` syntax

**Hint:** Store documents in a dict keyed by `doc_id`. Implement `__iter__` to yield values.

**Expected Output:**
```python
store = DocumentStore()
store.add({"doc_id": "1", "content": "AI is great", "type": "txt"})
store.add({"doc_id": "2", "content": "ML basics", "type": "pdf"})
store.add({"doc_id": "3", "content": "Deep learning", "type": "txt"})

txt_docs = store.filter(lambda d: d["type"] == "txt")
print(len(txt_docs))  # 2

word_counts = store.map(lambda d: len(d["content"].split()))
print(word_counts)  # [3, 2, 2]
```

---

## Problem 10 🧩

**Difficulty:** 🟡 Medium

### Pipeline Builder with Method Chaining

**Problem:** Create a fluent pipeline builder for text preprocessing steps, where each step can be chained.

**Real-world AI use case:** Before feeding text to LLMs or embedding models, you apply multiple preprocessing steps (lowercase, remove URLs, strip whitespace, etc.). A chainable pipeline makes this clean and reusable.

**Concept being tested:** Method chaining, builder pattern, functional composition

**Requirements:**
- `TextPipeline` class with methods that return `self` for chaining
- Steps: `lowercase()`, `strip_whitespace()`, `remove_urls()`, `remove_special_chars()`, `truncate(max_words)`
- Method `build() -> Callable` — returns a function that applies all steps
- Method `process(text) -> str` — applies pipeline to text immediately

**Hint:** Store steps as a list of callables. Each chainable method appends a lambda and returns `self`.

**Expected Output:**
```python
pipeline = (
    TextPipeline()
    .lowercase()
    .remove_urls()
    .strip_whitespace()
    .truncate(max_words=10)
)

text = "  Check out https://example.com for AI  TUTORIALS!  "
result = pipeline.process(text)
print(result)  # "check out for ai tutorials!"

# Also works as reusable function
processor = pipeline.build()
print(processor("ANOTHER   https://test.com  TEXT"))
```

---

## Problem 11 🧩

**Difficulty:** 🔴 Hard

### Async Event-Driven Pipeline

**Problem:** Build an async event-driven system where pipeline stages communicate via an async event bus.

**Real-world AI use case:** Production AI systems use event-driven architectures. When a document is uploaded, events trigger embedding generation, indexing, and notification — all asynchronously.

**Concept being tested:** AsyncIO, event patterns, pub/sub, decoupled architecture

**Requirements:**
- `EventBus` class with `subscribe(event_type, handler)`, `publish(event_type, data)`
- Handlers are async functions
- `AIEventPipeline` that connects: `document_uploaded` → `embedding_generated` → `index_updated`
- Each stage processes and publishes the next event
- Track event history for debugging

**Hint:** Use `defaultdict(list)` for event handlers and `asyncio.gather()` to run multiple handlers.

**Expected Output:**
```python
pipeline = AIEventPipeline()
await pipeline.process_document("AI is the future of software engineering")
# [document_uploaded] Processing document (7 words)
# [embedding_generated] Generated 384-dim embedding
# [index_updated] Document indexed successfully
print(f"Events processed: {len(pipeline.event_history)}")  # 3
```

---

## Problem 12 🧩

**Difficulty:** 🔴 Hard

### LRU Cache with TTL for Embeddings

**Problem:** Implement an LRU (Least Recently Used) cache with TTL (Time To Live) for storing computed embeddings.

**Real-world AI use case:** Computing embeddings is expensive (~$0.0001/call for OpenAI). Caching avoids recomputing embeddings for frequently accessed documents. TTL ensures stale embeddings are refreshed.

**Concept being tested:** Data structures, caching strategies, time-based expiration, `OrderedDict`

**Requirements:**
- Class `EmbeddingCache(max_size, ttl_seconds)`
- Methods: `get(key)`, `put(key, embedding)`, `invalidate(key)`, `clear()`
- LRU eviction when max_size is reached
- TTL-based expiration (entries expire after `ttl_seconds`)
- Properties: `hit_rate`, `size`, `stats`

**Hint:** Use `collections.OrderedDict` for LRU ordering and store `(embedding, timestamp)` tuples as values.

**Expected Output:**
```python
cache = EmbeddingCache(max_size=3, ttl_seconds=2)
cache.put("doc1", [0.1, 0.2, 0.3])
cache.put("doc2", [0.4, 0.5, 0.6])
print(cache.get("doc1"))  # [0.1, 0.2, 0.3]
print(cache.size)  # 2

import time
time.sleep(3)
print(cache.get("doc1"))  # None (expired)
print(f"Hit rate: {cache.hit_rate:.1%}")
```

---

## Problem 13 🧩

**Difficulty:** 🔴 Hard

### Abstract Base Class for LLM Providers

**Problem:** Design an abstract base class system for multiple LLM providers with a unified interface.

**Real-world AI use case:** Production systems support multiple LLM providers (OpenAI, Anthropic, Ollama) behind a single interface. This allows switching providers without changing application code.

**Concept being tested:** ABC, abstract methods, factory pattern, inheritance, Protocol

**Requirements:**
- `BaseLLMProvider` ABC with abstract methods: `generate(prompt, **kwargs)`, `embed(text)`, `count_tokens(text)`
- Concrete implementations: `OpenAIProvider`, `AnthropicProvider`, `OllamaProvider` (simulated)
- `LLMFactory.create(provider_name, **config) -> BaseLLMProvider`
- Each provider has its own pricing and token counting logic
- Unified `LLMRouter` that tries providers in order of priority

**Hint:** Use `ABC` and `@abstractmethod` from `abc` module. The factory can use a dict mapping names to classes.

**Expected Output:**
```python
# Factory pattern
openai = LLMFactory.create("openai", api_key="sk-xxx", model="gpt-4")
anthropic = LLMFactory.create("anthropic", api_key="sk-ant-xxx", model="claude-3")

# Unified interface
for provider in [openai, anthropic]:
    result = provider.generate("What is RAG?")
    tokens = provider.count_tokens("What is RAG?")
    print(f"{provider.name}: {tokens} tokens, response: {result[:50]}")

# Router with fallback
router = LLMRouter([openai, anthropic])
response = router.generate("Explain AI agents")  # Tries openai first, falls back to anthropic
```

---

## Problem 14 🧩

**Difficulty:** 🔴 Hard

### Concurrent Document Processor with Progress Tracking

**Problem:** Build an async document processor that processes files concurrently with real-time progress tracking and error recovery.

**Real-world AI use case:** When ingesting thousands of documents into a RAG system, you need concurrent processing with progress bars, error handling, and the ability to resume after failures.

**Concept being tested:** AsyncIO, `asyncio.Semaphore`, `asyncio.Queue`, progress tracking, error recovery

**Requirements:**
- `DocumentProcessor` with configurable concurrency limit
- Process documents concurrently with progress callback
- Error recovery: failed documents are retried, then skipped with logging
- Results collected in order (not random order)
- Statistics: total processed, failed, retried, time elapsed

**Hint:** Use `asyncio.Queue` for the work queue and `asyncio.Semaphore` for concurrency limiting. Track progress with a counter.

**Expected Output:**
```python
processor = DocumentProcessor(max_concurrent=5, max_retries=2)

docs = [f"document_{i}.txt" for i in range(20)]
results = await processor.process_all(docs, callback=print_progress)
# Processing: [████████████████████] 20/20 (100%)
# ✅ Processed: 18 | ❌ Failed: 2 | 🔄 Retried: 3
# ⏱️ Total time: 2.1s | Avg: 0.10s/doc
```

---

## Problem 15 🧩

**Difficulty:** 🔴 Hard

### Plugin System with Dynamic Loading

**Problem:** Build a plugin system that dynamically discovers and loads text processing plugins from a directory.

**Real-world AI use case:** AI systems need extensibility. A plugin system allows adding new document loaders, embedding providers, or preprocessing steps without modifying core code.

**Concept being tested:** Dynamic imports, `importlib`, abstract classes, plugin architecture, `__init_subclass__`

**Requirements:**
- `PluginBase` abstract class with `name`, `version`, `process(text) -> str`
- `PluginManager` that discovers plugins via `__init_subclass__` hook
- Support registering plugins via decorator: `@register_plugin`
- Method to list all available plugins, get plugin by name, chain plugins
- Each plugin processes text differently (uppercase, reverse, summarize, etc.)

**Hint:** Use `__init_subclass__` to auto-register subclasses or maintain a registry dict. The decorator approach uses a module-level dict.

**Expected Output:**
```python
@register_plugin
class UppercasePlugin(PluginBase):
    name = "uppercase"
    version = "1.0"
    def process(self, text: str) -> str:
        return text.upper()

@register_plugin
class ReversePlugin(PluginBase):
    name = "reverse"
    version = "1.0"
    def process(self, text: str) -> str:
        return text[::-1]

manager = PluginManager()
print(manager.list_plugins())  # ['uppercase', 'reverse']

result = manager.chain(["uppercase", "reverse"], "hello world")
print(result)  # "DLROW OLLEH"
```

---

<div align="center">

[📘 Back to Topic](README.md) · [📋 Phase 1](../README.md) · [🏠 Home](../../README.md)

</div>
