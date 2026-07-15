# MongoDB Automated Embedding

Automated Embedding allows MongoDB Vector Search to generate and manage embeddings for selected text fields.

MongoDB handles:

- embedding generation
- initial backfill
- new documents
- updated documents
- query embeddings
- synchronization
- generated vector storage

The application does not need to store an embedding field in its normal document schema.

## Manual Pipeline

Without Automated Embedding:

```text
Application saves text
       ↓
Application or trigger detects change
       ↓
Embedding API request
       ↓
Receive vector
       ↓
Store vector
       ↓
Maintain version and retry logic
       ↓
Vector Search
```

Responsibilities include:

- provider integration
- API keys
- batching
- retries
- rate limits
- error status
- backfills
- model changes
- re-embedding
- vector-field storage

## Automated Pipeline

With Automated Embedding:

```text
Application saves text
       ↓
MongoDB detects indexed text
       ↓
MongoDB generates embedding asynchronously
       ↓
MongoDB stores vector internally
       ↓
Vector Search becomes available
```

At query time:

```text
Natural-language query
       ↓
MongoDB uses compatible Voyage AI model
       ↓
Query embedding
       ↓
Vector Search
```

## Index Definition

Automated Embedding is enabled with the `autoEmbed` index field type.

```json
{
  "fields": [
    {
      "type": "autoEmbed",
      "modality": "text",
      "path": "summary",
      "model": "voyage-4"
    },
    {
      "type": "filter",
      "path": "studentId"
    },
    {
      "type": "filter",
      "path": "examCycleId"
    }
  ]
}
```

### Important fields

- `type`: `autoEmbed`
- `modality`: currently text for text embedding
- `path`: document text field
- `model`: Voyage AI embedding model

Filter fields can be added for pre-filtering.

## Query

Conceptually:

```javascript
db.examCycleInsights.aggregate([
  {
    $vectorSearch: {
      index: "exam-cycle-insights",
      path: "summary",
      query: "cycles where tempo improved quickly and then plateaued",
      model: "voyage-4",
      filter: {
        studentId: authorizedStudentId,
      },
      limit: 5,
    },
  },
  {
    $project: {
      summary: 1,
      examCycleId: 1,
      generatedAt: 1,
      score: {
        $meta: "vectorSearchScore",
      },
    },
  },
]);
```

MongoDB embeds the natural-language query using a model compatible with the index-time model.

## Existing Data

When the index is created, MongoDB generates embeddings for existing indexed documents.

This removes the need for a custom one-time backfill script in many use cases.

Initial index generation may still require:

- time
- model token usage
- sufficient cluster resources
- rate-limit capacity
- monitoring

## New and Updated Data

MongoDB automatically generates embeddings when:

- a new document is inserted
- the indexed text field changes
- a relevant document is updated

Embedding generation is asynchronous.

This means a newly saved document may not become semantically searchable at the exact same instant as the database write.

Applications requiring immediate consistency should account for this delay.

## Storage

Generated embeddings are not added to the ordinary application document.

MongoDB persists them in an internal generated embeddings collection on the same cluster.

Each Automated Embedding index has a corresponding generated embeddings collection.

Application document:

```json
{
  "_id": "...",
  "studentId": "...",
  "summary": "Tempo improved steadily before plateauing at 88 BPM."
}
```

Not:

```json
{
  "_id": "...",
  "summary": "...",
  "embedding": [0.012, -0.081]
}
```

This keeps the source schema cleaner.

## Supported Atlas Tiers

Automated Embedding supports:

- Free `M0`
- Flex
- dedicated `M10+`

Dedicated clusters require storage and cluster-tier auto-scaling.

For initial large index builds, MongoDB may temporarily need to scale resources upward.

## Dedicated-Cluster Requirements

For `M10` or `M20`:

- configure a maximum tier of at least `M30`

For `M30` or higher:

- configure a maximum tier above the current tier

For NVMe clusters:

- enable scaling when storage runs low

These requirements should be rechecked before production implementation because platform capabilities can change.

## Available Automated Embedding Models

Current documented options include:

| Model            | Primary use                                |
| ---------------- | ------------------------------------------ |
| `voyage-4-lite`  | high volume and lower cost                 |
| `voyage-4`       | balanced general text retrieval            |
| `voyage-4-large` | maximum accuracy for complex relationships |
| `voyage-code-3`  | code and technical-document search         |

See [Voyage AI Models](./voyage-ai-models.md).

## Quantization

Quantization reduces embedding precision to reduce storage and memory requirements.

### `float`

- 32-bit float values
- highest storage use
- full-fidelity representation

### `scalar`

- compresses each dimension to 8 bits
- lower memory and storage
- potential accuracy trade-off

### `binary`

- compresses dimensions to individual bits
- rescoring is applied to top results

### `binaryNoRescore`

- binary representation
- no rescoring
- fastest or smallest option in some workloads
- potentially larger quality trade-off

Quantization should be evaluated using application-specific retrieval tests.

## Costs

Automated Embedding does not make embeddings free.

Cost may come from:

- indexing existing text
- indexing new text
- re-indexing changed text
- embedding query text
- storage
- cluster resources

Token volume depends on:

- source-text length
- number of documents
- update frequency
- query volume
- model price

## Rate Limits

Embedding providers enforce rate limits such as:

- requests per minute
- tokens per minute

Large backfills and high query traffic may compete for capacity.

Self-managed configurations may use separate API keys for indexing and query operations so indexing does not negatively affect live search traffic.

## What Automated Embedding Removes

It can remove or reduce the need for:

- custom embedding fields
- Atlas Triggers for embedding generation
- queue workers
- embedding API wrappers
- routine backfill scripts
- direct vector persistence
- change-detection logic

## What It Does Not Remove

The application must still decide:

- which fields deserve embeddings
- how text is cleaned
- whether documents should be chunked
- what metadata is indexed
- authorization filters
- retrieval thresholds
- evaluation strategy
- summary-generation logic
- source provenance
- model and cost trade-offs

## When Manual Embeddings May Still Be Better

Manual embedding management may be preferable when the application requires:

- a provider not supported by Automated Embedding
- custom preprocessing
- multiple fields combined into one embedding
- custom chunk construction
- explicit vector portability
- application-controlled model versioning
- embedding reuse across databases
- synchronous availability
- specialized multimodal pipelines
- custom caching or batching

## StudioPulse Candidate Fields

Potential direct Automated Embedding fields:

```text
teacherNote
taskNote
studentReflection
technicalObservation
examCycleInsight.summary
studentLongTermInsight.summary
```

Fields that should normally remain structured:

```text
practiceMinutes
currentTempo
goalTempo
score
attendance
daysToExam
studentId
examCycleId
```

Structured fields can still influence embedded summaries.

## Example Derived Insight

```json
{
  "studentId": "...",
  "examCycleId": "...",
  "analytics": {
    "averageWeeklyMinutes": 92,
    "currentTempo": 88,
    "goalTempo": 96,
    "tempoGoalProgress": 91.7
  },
  "summary": "The student practiced an average of 92 minutes per week. Piece A progressed from 72 to 88 BPM toward a 96 BPM goal, but improvement slowed during the last two weeks. Teacher notes repeatedly mention tension above 84 BPM."
}
```

The numeric fields remain exact.

The `summary` field becomes semantically searchable.

## Key Principle

> Automated Embedding removes vector-pipeline maintenance, but it does not replace data modeling, retrieval design, authorization, or evaluation.
