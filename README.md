# Law Simplified
![Python](https://img.shields.io/badge/Python-3.12-blue)
![LLM](https://img.shields.io/badge/LLM-Powered-green)
![RAG](https://img.shields.io/badge/RAG-Enabled-orange)
![Elastic-search](https://img.shields.io/badge/ES-red)
![Status](https://img.shields.io/badge/Status-Completed-success)
# ElasticSearch-Rag# ES-RAG Grievance Retrieval System

An LLM-integrated retrieval pipeline that converts natural language queries into structured Elasticsearch queries, combined with a RAG-based semantic caching and retrieval layer — built to replace manual grievance search on a government portal.

## Overview

Government grievance portals hold large volumes of unstructured complaint records spanning states, districts, ministries, departments, and dates. Staff previously had to manually filter through this data to find relevant cases. ES-RAG lets users query in plain natural language and returns the top-k most relevant grievance records, cutting manual filtering and search time by **~50%**.

**Example query:**
> "Find complaints from [state] in [district] under [ministry] / [department] filed after [date] regarding [complaint type]."

## How It Works

1. **Natural Language Query (NLQ) Input** — User submits a query in plain English describing what they're looking for (location, ministry, department, date range, complaint type, etc.).

2. **LLM-Based Query Translation** — The NLQ is sent to an LLM, which parses intent and entities (state, district, ministry, department, date, complaint category) and constructs a structured Elasticsearch query.

3. **Semantic Cache Check** — The system generates vector embeddings of the incoming query and checks a database for embeddings of previously answered queries.
   - **Cache hit:** if a semantically similar query already exists, its cached results are retrieved directly — skipping redundant search.
   - **Cache miss:** the pipeline proceeds to full retrieval.

4. **Hybrid Retrieval (Semantic + Keyword)** — On a cache miss, the system searches the database using a combination of:
   - **Keyword search** via Elasticsearch (structured fields — state, district, ministry, department, date).
   - **Semantic search** via vector embeddings (contextual/free-text similarity).

5. **Top-K Retrieval** — The hybrid search returns the top **10** most relevant grievance records ranked by combined semantic + keyword relevance.

6. **RAG-Based Response Generation** — Retrieved records are passed to an LLM to generate a context-aware, human-readable response instead of a raw list of records.

## Architecture

```
User NLQ
   │
   ▼
LLM Query Parser  ──────────────►  Structured Elasticsearch Query
   │                                (state, district, ministry, dept, date)
   ▼
Generate Query Embedding
   │
   ▼
Semantic Cache Lookup ── hit ──► Return Cached Results
   │
  miss
   ▼
┌───────────────────────────────┐
│      Hybrid Retrieval          │
│ ┌────────────┐ ┌─────────────┐ │
│ │  Keyword    │ │  Semantic   │ │
│ │ (Elastic-   │ │  (Vector    │ │
│ │  search)    │ │  Embeddings)│ │
│ └──────┬─────┘ └──────┬──────┘ │
│        └───────┬───────┘       │
└────────────────┼───────────────┘
                  ▼
          Top-10 Ranked Results
                  │
                  ▼
      RAG Response Generation (LLM)
                  │
                  ▼
        Context-Aware Output
```

## Key Features

- **NLQ-to-Structured-Query Translation** — Users query in plain language; the LLM handles entity extraction and Elasticsearch query construction.
- **Semantic Caching** — Embeddings of prior queries are stored and checked before running a fresh search, avoiding redundant retrieval for repeated/similar queries.
- **Hybrid Search** — Combines exact-match keyword filtering (structured metadata: state, district, ministry, department, date) with semantic similarity search (free-text complaint context).
- **Top-K Ranking** — Returns the 10 most relevant records by combined relevance score.
- **RAG-Based Generation** — Converts retrieved records into a coherent, context-grounded response.
- **LLM** - Uses many shot prompting examples for reference.

## Tech Stack

- **Language:** Python
- **Search:** Elasticsearch
- **Retrieval:** Vector Embeddings, Semantic Cache DB
- **Generation:** LLM (query parsing + RAG response generation)
- **Core Techniques:** Hybrid Search (Semantic + Keyword), Query Caching, Intent/Entity Extraction

## Impact

| Metric | Result |
|---|---|
| Manual filtering & search time | **↓ ~50%** |
| Redundant searches | Reduced via semantic query caching |
| Retrieval | Top-10 relevant records via hybrid ranking |

## Use Case

Built for a government grievance platform where cases are tagged across state, district, ministry, department, and date. Instead of manually filtering structured fields and reading through complaint text, users query naturally (e.g. *"UP complaints from Lucknow district, Ministry of X, after March 2025, about Y"*) and get ranked, relevant results with a generated summary.

## Future Improvements

- Multi-lingual NLQ support for regional grievance filing
- Feedback loop to improve cache-hit relevance over time
- Expand structured entity extraction to cover more field types

---

*Part of AI/ML work on government-facing document intelligence systems.*
