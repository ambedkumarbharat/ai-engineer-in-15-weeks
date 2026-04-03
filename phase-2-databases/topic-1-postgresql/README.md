<div align="center">

# 🐘 Topic 1: PostgreSQL

![Difficulty](https://img.shields.io/badge/Difficulty-🟢_Beginner-brightgreen)
![Time](https://img.shields.io/badge/Time-3_Days-blue)
![Phase](https://img.shields.io/badge/Phase-2_Databases-purple)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 2](../README.md) > **PostgreSQL**

---

## 🎯 Why This Matters for AI Systems Engineering

PostgreSQL is the **primary relational database** for AI systems. You'll store:
- **User data** — accounts, preferences, API keys
- **Conversation history** — chat messages, sessions
- **Evaluation results** — accuracy metrics, A/B test data
- **Job queues** — background processing tasks
- **Model metadata** — versions, configs, performance logs

PostgreSQL also has **pgvector** extension for vector similarity search, making it a one-stop solution for many AI applications.

---

## 📚 Core Concepts

### 1. Schema Design for AI Applications

```sql
-- Create database for an AI application
CREATE DATABASE ai_platform;

-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    api_key VARCHAR(64) UNIQUE,
    tier VARCHAR(20) DEFAULT 'free' CHECK (tier IN ('free', 'pro', 'enterprise')),
    tokens_used INTEGER DEFAULT 0,
    tokens_limit INTEGER DEFAULT 10000,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Conversations table
CREATE TABLE conversations (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255),
    model VARCHAR(50) NOT NULL DEFAULT 'gpt-4',
    total_tokens INTEGER DEFAULT 0,
    total_cost DECIMAL(10, 6) DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Messages table
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    conversation_id INTEGER REFERENCES conversations(id) ON DELETE CASCADE,
    role VARCHAR(20) NOT NULL CHECK (role IN ('user', 'assistant', 'system')),
    content TEXT NOT NULL,
    tokens INTEGER DEFAULT 0,
    model VARCHAR(50),
    latency_ms INTEGER,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Create indexes for performance
CREATE INDEX idx_messages_conversation ON messages(conversation_id);
CREATE INDEX idx_conversations_user ON conversations(user_id);
CREATE INDEX idx_messages_created ON messages(created_at DESC);
CREATE INDEX idx_users_api_key ON users(api_key);

-- Updated_at trigger
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER users_updated_at
    BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at();
```

### 2. Python + psycopg2 (Direct Connection)

```python
import psycopg2
from psycopg2.extras import RealDictCursor
from contextlib import contextmanager

DATABASE_URL = "postgresql://user:password@localhost:5432/ai_platform"

@contextmanager
def get_db_connection():
    """Context manager for database connections."""
    conn = psycopg2.connect(DATABASE_URL)
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise
    finally:
        conn.close()

def create_user(email: str, name: str) -> dict:
    """Create a new user."""
    import secrets
    api_key = f"ak_{secrets.token_hex(16)}"
    
    with get_db_connection() as conn:
        with conn.cursor(cursor_factory=RealDictCursor) as cur:
            cur.execute(
                """
                INSERT INTO users (email, name, api_key)
                VALUES (%s, %s, %s)
                RETURNING id, email, name, api_key, tier, created_at
                """,
                (email, name, api_key)
            )
            return dict(cur.fetchone())

def get_user_conversations(user_id: int, limit: int = 10) -> list[dict]:
    """Get a user's recent conversations with message count."""
    with get_db_connection() as conn:
        with conn.cursor(cursor_factory=RealDictCursor) as cur:
            cur.execute(
                """
                SELECT c.id, c.title, c.model, c.total_tokens, c.total_cost,
                       COUNT(m.id) as message_count,
                       MAX(m.created_at) as last_message_at
                FROM conversations c
                LEFT JOIN messages m ON c.id = m.conversation_id
                WHERE c.user_id = %s
                GROUP BY c.id
                ORDER BY c.updated_at DESC
                LIMIT %s
                """,
                (user_id, limit)
            )
            return [dict(row) for row in cur.fetchall()]

# Usage
user = create_user("dev@example.com", "AI Developer")
print(f"Created user: {user['email']} with API key: {user['api_key']}")
```

### 3. SQLAlchemy ORM (Production Approach)

```python
from sqlalchemy import create_engine, Column, Integer, String, Text, ForeignKey, DateTime, Numeric
from sqlalchemy.orm import declarative_base, relationship, sessionmaker
from sqlalchemy.sql import func

DATABASE_URL = "postgresql://user:password@localhost:5432/ai_platform"

engine = create_engine(DATABASE_URL, pool_size=10, max_overflow=20)
SessionLocal = sessionmaker(bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String(255), unique=True, nullable=False, index=True)
    name = Column(String(100), nullable=False)
    api_key = Column(String(64), unique=True, index=True)
    tier = Column(String(20), default="free")
    tokens_used = Column(Integer, default=0)
    tokens_limit = Column(Integer, default=10000)
    created_at = Column(DateTime, server_default=func.now())
    
    # Relationships
    conversations = relationship("Conversation", back_populates="user", cascade="all, delete-orphan")
    
    def __repr__(self):
        return f"<User(id={self.id}, email='{self.email}')>"

class Conversation(Base):
    __tablename__ = "conversations"
    
    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id", ondelete="CASCADE"))
    title = Column(String(255))
    model = Column(String(50), default="gpt-4")
    total_tokens = Column(Integer, default=0)
    total_cost = Column(Numeric(10, 6), default=0)
    created_at = Column(DateTime, server_default=func.now())
    
    user = relationship("User", back_populates="conversations")
    messages = relationship("Message", back_populates="conversation", cascade="all, delete-orphan")

class Message(Base):
    __tablename__ = "messages"
    
    id = Column(Integer, primary_key=True, index=True)
    conversation_id = Column(Integer, ForeignKey("conversations.id", ondelete="CASCADE"))
    role = Column(String(20), nullable=False)
    content = Column(Text, nullable=False)
    tokens = Column(Integer, default=0)
    created_at = Column(DateTime, server_default=func.now())
    
    conversation = relationship("Conversation", back_populates="messages")

# Usage with FastAPI dependency injection
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# Example queries
def get_usage_stats(db, user_id: int) -> dict:
    """Get token usage statistics for a user."""
    from sqlalchemy import func as f
    
    result = db.query(
        f.count(Conversation.id).label("total_conversations"),
        f.sum(Conversation.total_tokens).label("total_tokens"),
        f.sum(Conversation.total_cost).label("total_cost"),
    ).filter(Conversation.user_id == user_id).first()
    
    return {
        "total_conversations": result.total_conversations or 0,
        "total_tokens": result.total_tokens or 0,
        "total_cost": float(result.total_cost or 0),
    }
```

### 4. Indexing Strategies for AI Workloads

```sql
-- B-tree index (default) — equality and range queries
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_messages_created ON messages(created_at DESC);

-- Partial index — only index active conversations
CREATE INDEX idx_active_conversations 
    ON conversations(user_id, updated_at DESC) 
    WHERE total_tokens > 0;

-- GIN index — full-text search on message content
CREATE INDEX idx_messages_content_search 
    ON messages USING GIN (to_tsvector('english', content));

-- Full-text search query
SELECT content, ts_rank(to_tsvector('english', content), query) AS rank
FROM messages, plainto_tsquery('english', 'machine learning models') AS query
WHERE to_tsvector('english', content) @@ query
ORDER BY rank DESC
LIMIT 10;

-- EXPLAIN ANALYZE to check query performance
EXPLAIN ANALYZE
SELECT * FROM messages 
WHERE conversation_id = 42 
ORDER BY created_at DESC 
LIMIT 20;
```

---

## 📺 Best Free Resources

| Resource | Type | Link |
|----------|------|------|
| **PostgreSQL Tutorial** | 📄 Docs | [postgresqltutorial.com](https://www.postgresqltutorial.com/) |
| **SQLAlchemy 2.0 Tutorial** | 📄 Docs | [docs.sqlalchemy.org/en/20/tutorial](https://docs.sqlalchemy.org/en/20/tutorial/) |
| **PostgreSQL for Python Devs** | 📹 YouTube | [TechWorld with Nana - PostgreSQL](https://www.youtube.com/watch?v=qw--VYLpxG4) |
| **Database Design Course** | 📹 YouTube | [FreeCodeCamp - Database Design](https://www.youtube.com/watch?v=ztHopE5Wnpc) |
| **pgvector for AI** | 📄 Docs | [github.com/pgvector/pgvector](https://github.com/pgvector/pgvector) |

---

## ⚠️ Common Mistakes Beginners Make

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| No indexes on foreign keys | Joins become very slow | Always index FKs and query columns |
| Storing JSON for structured data | Can't index or validate | Use proper columns for known fields |
| Not using connection pooling | Connection overhead per request | Use SQLAlchemy `pool_size` or pgBouncer |
| N+1 query problem | Hundreds of queries instead of one | Use `joinedload()` or explicit joins |
| Not using transactions | Partial updates leave bad state | Wrap related operations in transactions |
| Storing embeddings as JSON | Can't do similarity search | Use `pgvector` extension |

---

## 📋 Quick Reference Cheatsheet

| Operation | SQL | SQLAlchemy |
|-----------|-----|------------|
| Create | `INSERT INTO t VALUES (...)` | `db.add(obj)` |
| Read | `SELECT * FROM t WHERE id = 1` | `db.query(M).filter_by(id=1).first()` |
| Update | `UPDATE t SET col = val WHERE id = 1` | `obj.col = val; db.commit()` |
| Delete | `DELETE FROM t WHERE id = 1` | `db.delete(obj); db.commit()` |
| Join | `SELECT ... FROM a JOIN b ON ...` | `db.query(A).join(B)` |
| Aggregate | `SELECT COUNT(*), SUM(col) FROM t` | `db.query(func.count(), func.sum(M.col))` |
| Index | `CREATE INDEX idx ON t(col)` | `Column(..., index=True)` |
| Transaction | `BEGIN; ...; COMMIT;` | `with db.begin():` |

---

> 💡 **Pro Tip:** In AI systems, PostgreSQL with the `pgvector` extension can handle both structured data AND vector similarity search. For smaller applications (< 1M vectors), you might not even need a separate vector database. Start with pgvector and graduate to Pinecone/Weaviate only when you need to scale beyond what PostgreSQL can handle.

---

<div align="center">

[🏠 Home](../../README.md) · [📋 Phase 2](../README.md)

**[▶ Problems](problems.md)** · [Topic 2: Redis →](../topic-2-redis/)

</div>
