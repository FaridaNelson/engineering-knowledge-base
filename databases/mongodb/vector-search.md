# MongoDB Vector Search

MongoDB Vector Search retrieves documents based on semantic similarity rather than only exact keyword matches.

A vector embedding represents the meaning of text as an array of numbers.

```text
Text
  ↓
Embedding model
  ↓
Vector
  ↓
Vector index
  ↓
Similarity search
```

## Semantic Search

Traditional keyword search looks for matching terms.

Query:

> problems maintaining a steady beat

Document:

> The student rushes dotted rhythms and loses pulse in longer phrases.

The words are different, but the meaning is related.

Vector Search can retrieve the document because their embeddings are close in vector space.

## Vector Embeddings

Conceptually:

```json
{
  "text": "The student rushes dotted rhythms.",
  "embedding": [0.018, -0.117, 0.402]
}
```

Real embedding vectors usually contain hundreds or thousands of dimensions.

Each individual number is generally not meaningful by itself. The full vector represents semantic relationships learned by the embedding model.

## Index-Time and Query-Time Embeddings

### Index time

Document text is converted into vectors.

```text
Document text
      ↓
Embedding model
      ↓
Stored or internally managed vector
      ↓
Vector Search index
```

### Query time

The user’s question is converted with a compatible embedding model.

```text
User query
    ↓
Embedding model
    ↓
Query vector
    ↓
Nearest-neighbor search
```

The index and query embeddings must be compatible.

## Manual Embedding Pattern

With manually managed embeddings, the application stores a vector field.

```json
{
  "_id": "...",
  "studentId": "...",
  "teacherNote": "Left-hand tension appears above 80 BPM.",
  "teacherNoteEmbedding": [0.012, -0.083, 0.221]
}
```

A Vector Search index references the vector field.

Conceptually:

```json
{
  "fields": [
    {
      "type": "vector",
      "path": "teacherNoteEmbedding",
      "numDimensions": 1024,
      "similarity": "dotProduct"
    },
    {
      "type": "filter",
      "path": "studentId"
    }
  ]
}
```

Manual management gives the application control over:

- embedding providers
- model versioning
- vector storage
- batching
- backfills
- retry logic
- re-embedding
- custom preprocessing

It also creates more infrastructure to maintain.

## Automated Embedding Pattern

With Automated Embedding, MongoDB generates and manages vectors for a selected text field.

```json
{
  "fields": [
    {
      "type": "autoEmbed",
      "modality": "text",
      "path": "teacherNote",
      "model": "voyage-4"
    },
    {
      "type": "filter",
      "path": "studentId"
    }
  ]
}
```

The application writes ordinary text. MongoDB manages the embeddings separately.

See [Automated Embedding](./automated-embedding.md).

## Query Example

Conceptually:

```javascript
db.teacherNotes.aggregate([
  {
    $vectorSearch: {
      index: "teacher-notes-semantic-search",
      path: "teacherNote",
      query: "difficulty maintaining a steady rhythm",
      filter: {
        studentId: authorizedStudentId,
      },
      limit: 10,
    },
  },
  {
    $project: {
      studentId: 1,
      teacherNote: 1,
      lessonDate: 1,
      score: {
        $meta: "vectorSearchScore",
      },
    },
  },
]);
```

The exact syntax depends on whether embeddings are manually managed or generated through Automated Embedding.

## Similarity Functions

Similarity functions measure how close vectors are.

### Cosine similarity

Measures the angle between vectors.

Useful when vector direction matters more than magnitude.

### Dot product

Measures alignment while also incorporating vector magnitude.

MongoDB recommends dot product for full-fidelity or scalar-quantized embeddings.

### Euclidean distance

Measures the distance between vector endpoints.

MongoDB recommends Euclidean similarity for binary-quantized vectors.

## Number of Dimensions

The number of dimensions is the length of the vector.

Higher-dimensional embeddings may represent more semantic detail, but they also increase:

- storage
- memory requirements
- index size
- compute cost
- possible query latency

The required dimension count depends on the model.

## Approximate Nearest-Neighbor Search

Vector databases commonly use approximate nearest-neighbor search.

Instead of comparing the query vector with every stored vector exactly, the index searches efficiently for likely nearest matches.

This improves speed but introduces tuning decisions.

A common parameter is `numCandidates`.

Conceptually:

```text
Larger candidate pool
    ↓
Potentially better recall
    ↓
More computation and latency
```

The returned `limit` is usually smaller than the candidate pool.

## Filtering

Vector Search can combine semantic similarity with structured filters.

Example:

```javascript
filter: {
  studentId: authorizedStudentId,
  examCycleId: selectedExamCycleId,
  visibility: "parent"
}
```

Filtering is essential for:

- authorization
- tenant isolation
- student scoping
- date ranges
- document types
- lifecycle status

## Metadata

Useful searchable metadata may include:

```json
{
  "studentId": "...",
  "teacherId": "...",
  "examCycleId": "...",
  "lessonDate": "...",
  "contentType": "teacher-note",
  "visibility": "teacher",
  "status": "active"
}
```

The text embedding captures meaning. Metadata provides exact scope.

## Retrieval Scores

Vector Search returns a similarity score.

A higher score generally indicates stronger semantic similarity, but the score is not a universal probability.

Avoid interpreting:

```text
0.88 = 88% correct
```

The score should be evaluated relative to:

- the selected model
- the dataset
- the query type
- index configuration
- retrieval thresholds

## Reranking

Initial vector retrieval may return a broad candidate set.

A reranker can reorder those candidates using a deeper relevance model.

```text
Vector Search
      ↓
Top candidate documents
      ↓
Reranker
      ↓
Most relevant documents
```

Reranking can improve precision when:

- documents are semantically similar
- fine distinctions matter
- the initial retrieval set is larger
- high-quality context is important

## Source Attribution

Retrieved results should retain:

- document ID
- source type
- student scope
- exam-cycle scope
- date
- author
- score
- source revision

This enables the final response to remain traceable.

## StudioPulse Example

Question:

> Why has Piece A stopped progressing?

Structured retrieval can provide:

- current tempo
- goal tempo
- weekly tempo history
- practice minutes
- task outcomes

Vector Search can retrieve:

- teacher notes mentioning tension
- fingering inconsistency
- rhythmic instability
- accuracy loss at faster tempos

Combined answer:

> Piece A has remained at 84 BPM for two weeks, while the goal is 96 BPM. Practice frequency also declined during the same period. Teacher observations repeatedly mention left-hand tension and uneven rhythm above 80 BPM.

## What Vector Search Should Not Replace

Vector Search should not replace exact logic for:

- authorization
- numeric calculations
- date comparisons
- totals
- averages
- count queries
- uniqueness constraints
- transactional updates

## Key Principle

> Vector Search retrieves meaning. MongoDB queries and aggregations preserve exactness.
