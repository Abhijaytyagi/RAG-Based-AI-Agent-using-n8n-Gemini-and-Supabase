# RAG-Based AI Agent using n8n, Gemini, and Supabase

This project demonstrates an end-to-end **Retrieval-Augmented Generation (RAG)** system built using **n8n**, **Google Gemini embeddings**, and **Supabase vector search**.

The system allows users to ask natural language questions via a chat interface. The workflow retrieves relevant knowledge from a document corpus stored in Supabase and generates grounded answers using an AI model.

---

## 🚀 Key Features

- Chat-based user interface (n8n Chat Trigger)
- Semantic search using vector embeddings
- Supabase `pgvector`-based similarity search
- RAG context construction
- AI-generated, grounded responses
- Modular, extensible agent-style workflow

---

## 🧠 Architecture Overview

**High-level flow:**

1. User enters a query via chat
2. Query is converted into embeddings (Gemini)
3. Embedding is used to search relevant documents in Supabase
4. Retrieved chunks are formatted into a RAG context
5. AI model generates a final answer based only on retrieved context



---

## 🛠️ Tech Stack

- **n8n** – Workflow orchestration
- **Google Gemini** – Text embeddings & response generation
- **Supabase (PostgreSQL + pgvector)** – Vector storage and similarity search
- **JavaScript** – Context formatting logic

---

## 📂 Repository Contents

| Path | Description |
|----|----|
| `n8n/rag-workflow.json` | Exported n8n workflow |
| `supabase/vector_search_function.sql` | SQL function for vector similarity |
| `samples/sample_query.json` | Example user query |
| `samples/sample_response.json` | Example AI response |
| `.env.example` | Environment variable template |

---

## 🧩 Supabase Vector Search Function

The project uses a SQL function for similarity matching:

```sql
create or replace function match_documents (
  query_embedding vector(768),
  match_count int default 5
)
returns table (
  id uuid,
  content text,
  source text,
  similarity float
)
language sql stable
as $$
  select
    d.id,
    d.content,
    d.source,
    1 - (d.embedding <=> query_embedding) as similarity
  from documents d
  order by d.embedding <=> query_embedding
  limit match_count;
$$;
