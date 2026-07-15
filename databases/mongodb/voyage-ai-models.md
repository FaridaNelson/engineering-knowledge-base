# Voyage AI Models

Voyage AI provides embedding and reranking models optimized for retrieval.

MongoDB Vector Search integrates selected Voyage AI models directly through Automated Embedding.

Model selection affects:

- retrieval accuracy
- semantic detail
- token cost
- throughput
- latency
- storage
- supported domain

## Automated Embedding Models

Current documented models include:

| Model            | Primary use                                        | Price per 1M tokens |
| ---------------- | -------------------------------------------------- | ------------------: |
| `voyage-4-lite`  | high-volume, cost-sensitive search                 |               $0.02 |
| `voyage-4`       | balanced general-purpose text search               |               $0.06 |
| `voyage-4-large` | complex semantic relationships and maximum quality |               $0.12 |
| `voyage-code-3`  | source code and technical documentation            |               $0.18 |

Pricing and availability may change and should be checked before implementation.

## `voyage-4-lite`

Designed for:

- high document volume
- high query volume
- cost-sensitive applications
- early prototypes
- lower-value retrieval tasks

Advantages:

- lowest listed token price
- useful for testing at scale
- suitable when approximate semantic matching is enough

Trade-offs:

- may provide less nuanced retrieval than larger models
- may be weaker on subtle semantic relationships

Potential StudioPulse use:

- early semantic-search prototype
- high-volume student reflections
- low-cost experimentation

## `voyage-4`

MongoDB identifies this as the recommended balanced model for general text search.

Designed for:

- ordinary natural-language retrieval
- semantic search
- RAG
- notes
- documentation
- general knowledge collections

Potential StudioPulse use:

- teacher notes
- practice observations
- cycle summaries
- long-term student insights
- parent-visible progress summaries

This is the likely starting point for StudioPulse unless evaluation shows another model performs better.

## `voyage-4-large`

Designed for:

- maximum retrieval accuracy
- subtle semantic distinctions
- complex conceptual relationships
- high-value search experiences

Advantages:

- strongest listed general retrieval quality

Trade-offs:

- higher token cost
- potentially higher resource requirements
- quality gains may not justify cost for every dataset

Potential StudioPulse use:

- complex cross-cycle comparisons
- high-value teacher intelligence
- advanced educational research
- nuanced technical-pattern retrieval

It should be selected based on measured improvement, not model size alone.

## `voyage-code-3`

Specialized for:

- source-code search
- technical documentation
- software repositories
- API references
- engineering knowledge bases

Potential use:

- `engineering-knowledge-base`
- StudioPulse technical documentation
- OpenClaw notes
- code-oriented RAG
- internal developer assistant

It is not the obvious choice for ordinary music-lesson notes.

## Embedding Models vs. Generative Models

Embedding models do not generate prose answers.

They convert input into vectors.

```text
Teacher note
     ↓
Embedding model
     ↓
Vector
```

A generative LLM later receives retrieved context and produces the answer.

```text
User question
     ↓
Embedding and retrieval
     ↓
Relevant documents
     ↓
Generative LLM
     ↓
Answer
```

## Embedding Models vs. Rerankers

Embedding models support fast candidate retrieval.

Rerankers reevaluate a smaller set of candidate documents more deeply.

```text
Embedding retrieval
       ↓
Top 50 candidates
       ↓
Reranker
       ↓
Top 5 results
```

Use reranking when retrieval quality justifies added:

- latency
- token cost
- model calls

## Model Compatibility

Index-time and query-time embeddings must be compatible.

Automated Embedding normally uses the model configured in the index for query embedding.

A different query model may only be used when MongoDB identifies it as compatible with the index-time model.

Do not assume any two embedding models can be mixed.

## Model Changes

Changing an embedding model may require rebuilding or regenerating embeddings.

Vectors from different model families are generally not directly comparable.

Model migration may involve:

```text
Create new index
      ↓
Generate new embeddings
      ↓
Evaluate retrieval
      ↓
Switch query traffic
      ↓
Remove old index later
```

Avoid overwriting a production retrieval system without rollback capability.

## Cost Estimation

Approximate indexing cost depends on:

```text
Total source tokens
×
Price per million tokens
```

Query cost depends on:

```text
Query tokens
×
Number of queries
×
Model price
```

Additional costs may include:

- cluster storage
- search compute
- reranking
- generation LLM calls
- re-embedding after updates

## Selecting a Model

Evaluate models using actual application questions.

### Example query set

```text
Why did Piece A stop improving?
What technical issue keeps recurring?
How did this exam cycle compare with the previous one?
Which notes mention tension at faster tempos?
What practice pattern preceded the strongest score increase?
```

### Evaluation criteria

- expected source retrieved
- ranking position
- irrelevant-result count
- paraphrase handling
- exact-name handling
- latency
- token cost
- consistency

## Model Evaluation Table

| Criterion               |     Lite |                    General |          Large |           Code |
| ----------------------- | -------: | -------------------------: | -------------: | -------------: |
| Cost efficiency         |     High |                     Medium |          Lower |          Lower |
| General text quality    |     Good |                     Strong |      Strongest |    Specialized |
| Subtle relationships    | Moderate |                     Strong |      Strongest |   Code-focused |
| High-volume use         |   Strong |                       Good | More expensive | More expensive |
| Lesson notes            | Possible | Recommended starting point | Evaluate later |  Not preferred |
| Code and technical docs | Possible |                   Possible |       Possible |    Recommended |

## Contextualized Retrieval Models

Voyage AI also develops models that can represent chunks using broader document context.

This addresses a common chunking problem:

```text
Isolated chunk:
"It improved after the second attempt."
```

Without context, the subject is unclear.

Context-aware retrieval can use surrounding document information so the representation better reflects:

```text
Piece A tempo improved after the second attempt.
```

The exact availability of contextualized models must be checked against:

- Voyage AI’s current catalog
- MongoDB Automated Embedding support
- direct Voyage AI API support

Do not assume every Voyage model is available through every MongoDB integration.

## StudioPulse Initial Recommendation

For an initial StudioPulse experiment:

```text
General lesson and insight search
→ voyage-4

Cost-sensitive prototype
→ voyage-4-lite

Advanced accuracy comparison
→ voyage-4-large

Engineering knowledge base
→ voyage-code-3
```

Run an evaluation before production selection.

## Version Tracking

Even when MongoDB manages embeddings, application documentation should record:

```json
{
  "indexName": "exam-cycle-insights-v1",
  "sourceField": "summary",
  "model": "voyage-4",
  "createdAt": "...",
  "evaluationDatasetVersion": 1
}
```

This supports:

- reproducibility
- migration
- debugging
- cost analysis
- retrieval comparison

## Key Principle

> Choose an embedding model using representative retrieval tests, not only benchmark scores or model size.
