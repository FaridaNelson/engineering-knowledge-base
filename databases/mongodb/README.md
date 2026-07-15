# MongoDB AI Search and Retrieval

This directory documents MongoDB capabilities used to build semantic search, hybrid retrieval, Retrieval-Augmented Generation (RAG), AI-agent memory, and long-term application intelligence.

MongoDB can store structured application data and unstructured text in the same document model. This allows an application to combine:

- exact database queries
- aggregation pipelines
- full-text search
- vector search
- semantic retrieval
- metadata filtering
- generated summaries
- AI-agent memory

## Core Architecture

```text
Application data
      │
      ├── Structured fields
      │     ├── identifiers
      │     ├── dates
      │     ├── scores
      │     ├── practice minutes
      │     └── tempos
      │
      └── Text fields
            ├── teacher notes
            ├── observations
            ├── feedback
            └── generated summaries

                    ↓

          MongoDB Search Layer

      ┌─────────────┴─────────────┐
      │                           │
Keyword / filter search     Vector search
      │                           │
      └─────────────┬─────────────┘
                    ↓
              Hybrid retrieval
                    ↓
              Optional reranking
                    ↓
                    LLM
```

## Documents in This Directory

### [Vector Search](./vector-search.md)

Covers:

- embeddings
- semantic similarity
- vector indexes
- query vectors
- similarity functions
- filtering
- retrieval architecture

### [Automated Embedding](./automated-embedding.md)

Covers:

- the `autoEmbed` index type
- index-time embedding
- query-time embedding
- generated embeddings storage
- cluster requirements
- model selection
- lifecycle management

### [Hybrid Search](./hybrid-search.md)

Covers:

- combining lexical and semantic retrieval
- metadata filters
- score fusion
- reranking
- authorization boundaries
- StudioPulse examples

### [Chunking](./chunking.md)

Covers:

- why documents are split
- chunk size
- chunk overlap
- semantic chunking
- parent-document retrieval
- generated summaries
- contextualized embeddings

### [Voyage AI Models](./voyage-ai-models.md)

Covers:

- general-purpose embedding models
- cost and accuracy trade-offs
- code embeddings
- contextualized retrieval
- reranking models
- model-selection criteria

## Structured Data and Semantic Data

Not all data should be handled in the same way.

### Structured data

Examples:

- `studentId`
- `teacherId`
- `examCycleId`
- practice minutes
- current tempo
- goal tempo
- score
- lesson date
- attendance
- task status

These fields are best handled through:

- normal MongoDB queries
- indexes
- aggregation pipelines
- exact filters

### Semantic text

Examples:

- teacher observations
- student reflections
- technical comments
- assignment notes
- cycle summaries
- long-term trend summaries

These fields may benefit from:

- embeddings
- Vector Search
- semantic retrieval
- reranking

### Derived intelligence

Structured records can produce higher-level insight documents.

```text
Practice minutes
Current tempo
Goal tempo
Scores
Attendance
Teacher notes
       ↓
Aggregation and analysis
       ↓
Exam-cycle analytics
       ↓
Narrative summary
       ↓
Automated Embedding
       ↓
Long-term semantic retrieval
```

The structured source records remain authoritative. Generated summaries form an interpretation and retrieval layer.

## StudioPulse Application

Potential StudioPulse uses include:

- semantic search over teacher notes
- exam-cycle summaries
- tempo-progression analysis
- practice-consistency analysis
- recurring technical-pattern retrieval
- cross-cycle comparisons
- parent-facing explanations
- teacher-assistant workflows
- AI-agent memory

## Security Principle

Vector Search does not replace authorization.

Every search must be scoped to records the authenticated user is allowed to access.

```text
Authenticated user
       ↓
Backend resolves role
       ↓
Backend resolves authorized student scope
       ↓
Vector or hybrid search with filters
       ↓
Authorized results only
```

The LLM must not choose or validate access boundaries.

## Design Principle

> Use structured queries for exact facts, semantic search for meaning, and hybrid retrieval when both are required.
