<div align="center">

# 📄 Topic 3: Document Loaders & Chunking

![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow)
![Time](https://img.shields.io/badge/Time-3_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 4](../README.md) > **Document Loaders & Chunking**

---

## 🎯 Why This Matters

Chunking strategy is the **#1 factor** that determines RAG quality. Bad chunks = bad retrieval = bad answers. You'll learn to load documents from various sources and split them optimally.

---

## 📚 Core Concepts

### 1. Document Loaders

```python
from langchain_community.document_loaders import (
    PyPDFLoader, TextLoader, WebBaseLoader, 
    DirectoryLoader, CSVLoader,
)

# Load PDF
pdf_loader = PyPDFLoader("research_paper.pdf")
pdf_docs = pdf_loader.load()
print(f"Pages: {len(pdf_docs)}, Content: {pdf_docs[0].page_content[:100]}")

# Load from web
web_loader = WebBaseLoader("https://python.langchain.com/docs/concepts/")
web_docs = web_loader.load()

# Load entire directory
dir_loader = DirectoryLoader("./documents/", glob="**/*.txt", loader_cls=TextLoader)
all_docs = dir_loader.load()
print(f"Loaded {len(all_docs)} documents")
```

### 2. Chunking Strategies

```python
from langchain.text_splitter import (
    RecursiveCharacterTextSplitter,
    CharacterTextSplitter,
    TokenTextSplitter,
)

# RecursiveCharacterTextSplitter — BEST DEFAULT CHOICE
# Tries to split by: \n\n → \n → " " → "" (preserves structure)
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,       # Max characters per chunk
    chunk_overlap=50,     # Overlap between chunks
    length_function=len,
    separators=["\n\n", "\n", ". ", " ", ""],
)

text = "Your long document text here..." * 100
chunks = splitter.split_text(text)
print(f"Chunks: {len(chunks)}, Avg size: {sum(len(c) for c in chunks)/len(chunks):.0f} chars")

# Token-based splitting (better for LLM context limits)
token_splitter = TokenTextSplitter(
    chunk_size=200,       # Max tokens per chunk
    chunk_overlap=20,     # Token overlap
    encoding_name="cl100k_base",  # GPT-4 tokenizer
)
```

### 3. Custom Chunking Pipeline

```python
from dataclasses import dataclass

@dataclass
class Chunk:
    content: str
    source: str
    chunk_index: int
    metadata: dict

def smart_chunk(text: str, source: str, chunk_size: int = 500, 
                overlap: int = 50) -> list[Chunk]:
    """Production chunking with metadata tracking."""
    splitter = RecursiveCharacterTextSplitter(
        chunk_size=chunk_size, chunk_overlap=overlap)
    
    raw_chunks = splitter.split_text(text)
    
    return [
        Chunk(
            content=chunk,
            source=source,
            chunk_index=i,
            metadata={
                "chunk_size": len(chunk),
                "total_chunks": len(raw_chunks),
                "has_overlap": i > 0,
            }
        )
        for i, chunk in enumerate(raw_chunks)
    ]
```

### 4. Chunking Best Practices

| Parameter | Small Docs (< 5 pages) | Medium (5-50 pages) | Large (50+ pages) |
|-----------|----------------------|--------------------|--------------------|
| `chunk_size` | 300-500 chars | 500-1000 chars | 1000-2000 chars |
| `chunk_overlap` | 50 chars | 100 chars | 200 chars |
| Strategy | Recursive | Recursive | Semantic |

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **LangChain Text Splitters** | 📄 Docs | [python.langchain.com](https://python.langchain.com/docs/how_to/recursive_text_splitter/) |
| **Chunking Strategies Guide** | 📹 YouTube | [Greg Kamradt](https://www.youtube.com/watch?v=8OJC21T2SL4) |

> 💡 **Pro Tip:** Test your chunking by manually reviewing 20 chunks. If a chunk starts mid-sentence or splits a code block, your strategy needs tuning. The overlap parameter is crucial — too little and you lose context at boundaries; too much and you waste embedding calls.

---

<div align="center">

[← Topic 2](../topic-2-chromadb-pinecone/) · [📋 Phase 4](../README.md) · [Topic 4 →](../topic-4-rag-pipeline-design/)

</div>
