# Hybrid Search

Hybrid search combines multiple retrieval signals instead of relying on only one search method.

The most common combination is:

- lexical or full-text search
- vector or semantic search
- structured metadata filters

```text
User query
   ├── Keyword search
   ├── Vector search
   └── Structured filters
            ↓
       Score combination
            ↓
       Optional reranking
            ↓
       Final results
```

## Why Hybrid Search?

Keyword and vector search solve different problems.

### Keyword search is strong at:

- exact terminology
- names
- identifiers
- codes
- quoted phrases
- rare words
- field-specific matches

### Vector search is strong at:

- paraphrases
- conceptual similarity
- intent
- related meaning
- natural-language questions

Hybrid search uses both.

## Example

Query:

> dotted rhythm problem in Piece B

Keyword search may find documents containing:

```text
dotted rhythm
Piece B
```

Vector Search may find:

```text
The student rushes long-short patterns in the second repertoire piece.
```

Hybrid search can rank both as relevant.

## Retrieval Pipeline

```text
Query
  ↓
┌───────────────────────┐
│ Lexical retrieval     │
│ Semantic retrieval    │
└───────────┬───────────┘
            ↓
      Normalize scores
            ↓
        Fuse rankings
            ↓
      Apply business rules
            ↓
       Optional reranker
            ↓
        Return results
```

## Structured Filters

Hybrid search should still apply exact filters.

Example:

```javascript
{
  studentId: authorizedStudentId,
  examCycleId: currentExamCycleId,
  visibility: {
    $in: ["parent", "shared"]
  }
}
```

Filters can enforce:

- student access
- teacher ownership
- organization tenancy
- exam-cycle scope
- date range
- content visibility
- content type
- active status

## Score Fusion

Lexical and vector scores are not always directly comparable.

Possible strategies include:

- score normalization
- weighted score combination
- reciprocal rank fusion
- learned ranking
- reranking

## Weighted Combination

Conceptually:

```text
finalScore =
    lexicalScore × lexicalWeight
  + vectorScore × vectorWeight
```

Example:

```text
lexical weight: 0.4
vector weight: 0.6
```

Weights should be tuned using actual queries and relevance judgments.

## Reciprocal Rank Fusion

Reciprocal rank fusion combines rankings rather than raw scores.

A document receives value based on its rank in each result list.

This can be useful when the underlying score ranges are different.

Conceptually:

```text
Keyword rank: 2
Vector rank: 5
      ↓
Combined rank contribution
```

## Reranking

After retrieving candidates from keyword and vector search, a reranker can evaluate query-document relevance more deeply.

```text
Keyword results
Vector results
      ↓
Merged candidate set
      ↓
Reranker
      ↓
Final top documents
```

Reranking is useful when:

- the initial candidate set is noisy
- fine-grained relevance matters
- several chunks appear similar
- retrieval quality matters more than minimal latency

## Hybrid Search vs. Metadata Filtering

These are related but distinct.

### Metadata filtering

Restricts which documents are eligible.

```text
Only this student
Only this cycle
Only parent-visible records
```

### Hybrid retrieval

Combines lexical and semantic relevance.

```text
Exact phrase relevance
+
Semantic meaning
```

A production search often needs both.

## StudioPulse Example

Question:

> What has the teacher said about left-hand tension near performance tempo?

### Structured filter

```text
studentId = authorized student
examCycleId = selected cycle
contentType = teacher observation
```

### Keyword search

Looks for:

```text
left hand
tension
tempo
```

### Vector search

May retrieve:

```text
The wrist becomes rigid once the student exceeds 84 BPM.
```

### Structured analytics

Provides:

```text
Current tempo: 86 BPM
Goal tempo: 96 BPM
```

### Final answer

> The current tempo is 86 BPM toward a 96 BPM goal. Teacher notes from the last three lessons indicate that the wrist becomes rigid above approximately 84 BPM, which corresponds with the recent slowdown in tempo progression.

## Hybrid Search for Generated Insights

Cycle summaries can combine exact and semantic signals.

Example query:

> Find previous cycles with a similar late-stage plateau.

Filters:

```text
studentId = current student
cycleStatus = completed
```

Semantic search:

```text
rapid early improvement followed by a plateau near goal tempo
```

Keyword or exact conditions:

```text
tempoGoalProgress between 80% and 95%
```

This is a hybrid intelligence query.

## Security

Hybrid search must not retrieve unauthorized content and filter it afterward.

Prefer:

```text
Authorized candidate set
       ↓
Search
```

Avoid:

```text
Search all records
       ↓
Remove unauthorized records later
```

Pre-filtering is safer and more efficient.

## Evaluation

Hybrid search quality should be tested with realistic questions.

Example evaluation set:

```json
{
  "query": "Why is the student's tempo plateauing?",
  "expectedSources": [
    "tempo-history-cycle-17",
    "teacher-note-2026-06-12",
    "teacher-note-2026-06-19"
  ]
}
```

Measure:

- precision
- recall
- ranking quality
- irrelevant-result rate
- latency
- cost
- authorization correctness

## Tuning Questions

- Does lexical search receive too much weight?
- Does semantic search retrieve overly broad notes?
- Are filters too restrictive?
- Is chunk size hurting context?
- Should more candidates be reranked?
- Are exact names and repertoire titles preserved?
- Are old or stale summaries ranking too highly?

## Key Principle

> Hybrid search combines exact language, semantic meaning, and structured scope to produce stronger retrieval than any one method alone.
