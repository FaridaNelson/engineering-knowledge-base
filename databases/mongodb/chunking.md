# Chunking

Chunking divides long content into smaller searchable units before embedding or retrieval.

```text
Full document
     ↓
Chunking strategy
     ↓
Chunk 1
Chunk 2
Chunk 3
     ↓
One embedding per chunk
```

## Why Chunk Documents?

A single embedding for a long document represents many topics at once.

Example lesson note:

```text
Piece A reached 88 BPM.
Measures 17–24 remain uneven.
D-major scale fingering is inconsistent.
Sight-reading has improved.
The family will be away next week.
The exam is in 28 days.
```

One vector must represent all of these ideas.

A query about tempo may retrieve the full note, including unrelated content.

Chunking creates more focused representations.

```text
Chunk 1:
Piece A reached 88 BPM.
Measures 17–24 remain uneven.

Chunk 2:
D-major scale fingering is inconsistent.

Chunk 3:
Sight-reading has improved.

Chunk 4:
The family will be away next week.
The exam is in 28 days.
```

## Benefits

Chunking may improve:

- retrieval precision
- semantic specificity
- reranking quality
- source attribution
- token efficiency
- context-window usage

## Chunk Size Trade-Off

### Chunks that are too large

Risks:

- multiple topics share one embedding
- irrelevant text is retrieved
- more tokens are sent to the LLM
- fine-grained matches may be diluted

### Chunks that are too small

Risks:

- important context is lost
- pronouns or references become ambiguous
- retrieved text may be incomplete
- too many vectors are created
- ranking may become noisy

## Fixed-Length Chunking

Split text by character count or token count.

```text
Every 500 tokens
```

Advantages:

- easy to implement
- predictable
- efficient

Trade-offs:

- may split sentences
- may separate related concepts
- ignores document structure

## Recursive Chunking

Attempt larger boundaries first, then smaller ones.

Example preference:

```text
section
  ↓
paragraph
  ↓
sentence
  ↓
token boundary
```

This better preserves natural structure.

## Semantic Chunking

Split based on meaning or topic change rather than fixed length.

Example:

```text
Repertoire progress
Technique
Sight-reading
Scheduling
Exam planning
```

Advantages:

- conceptually coherent chunks
- stronger retrieval specificity

Trade-offs:

- more processing
- more complex implementation
- boundaries may vary unpredictably

## Domain-Aware Chunking

Applications can split content according to business structure.

For StudioPulse:

```text
Lesson record
├── Piece A observation
├── Piece B observation
├── Scales observation
├── Sight-reading observation
├── Aural observation
└── General lesson summary
```

This may be better than arbitrary token boundaries because the data already has educational meaning.

## Chunk Overlap

Overlap repeats some content between adjacent chunks.

```text
Chunk 1:
The student should reach 96 BPM by next lesson.
Measures 17–24 remain inconsistent.

Chunk 2:
Measures 17–24 remain inconsistent.
Use slow dotted-rhythm practice before increasing tempo.
```

Overlap prevents information at boundaries from being lost.

Trade-offs:

- more tokens embedded
- higher storage cost
- duplicate search results
- repeated context in LLM prompts

Overlap should be large enough to preserve continuity but not so large that chunks become near duplicates.

## Parent-Document Retrieval

Each chunk can reference the original document.

```json
{
  "_id": "chunk-2",
  "parentDocumentId": "lesson-42",
  "studentId": "...",
  "examCycleId": "...",
  "text": "Measures 17–24 remain inconsistent.",
  "chunkIndex": 2
}
```

Vector Search retrieves a chunk.

The application may then load:

- nearby chunks
- the complete lesson
- the lesson summary
- metadata from the parent document

## Small-to-Big Retrieval

A useful pattern is:

```text
Search small chunks
       ↓
Identify relevant document
       ↓
Retrieve larger parent context
```

This provides:

- precise matching
- broader answer context

## Full Document for Summarization

A chunk may identify the relevant source, but it may not be enough to summarize an entire lesson or cycle.

Possible design:

```text
Chunk search
    ↓
Relevant lesson ID
    ↓
Load full lesson
    ↓
Generate summary
```

## Summary Embeddings

A document may contain or reference a generated summary.

```json
{
  "lessonId": "...",
  "summary": "Piece A improved in tempo, but left-hand tension remains. Scale fingering is inconsistent.",
  "chunks": ["..."]
}
```

The summary supports broad retrieval.

Chunks support detailed retrieval.

## Hierarchical Retrieval

```text
Student long-term summary
        ↓
Exam-cycle summary
        ↓
Lesson summary
        ↓
Relevant chunk
        ↓
Full source document
```

This allows retrieval to move from broad to specific.

## Contextualized Chunks

A contextualized embedding represents a chunk while considering broader document context.

An isolated chunk:

```text
It improved after the second attempt.
```

is ambiguous.

With document context:

```text
Piece A tempo improved after the second attempt.
```

the meaning is clearer.

Contextualized embedding models can reduce the loss of meaning caused by independent chunking.

## Chunk Metadata

Each chunk should retain useful metadata.

```json
{
  "studentId": "...",
  "examCycleId": "...",
  "lessonId": "...",
  "contentType": "teacher-note",
  "topic": "piece-a-tempo",
  "lessonDate": "...",
  "visibility": "teacher",
  "chunkIndex": 3,
  "sourceUpdatedAt": "..."
}
```

Metadata supports:

- filtering
- authorization
- reconstruction
- source citations
- stale-data detection

## Duplicate Results

Overlapping chunks may cause several near-identical results.

Possible mitigation:

- deduplicate by parent document
- group chunks by lesson
- limit chunks per source
- rerank merged passages
- merge neighboring chunks
- select the strongest chunk plus context

## StudioPulse Recommendation

Do not automatically chunk every short text field.

A concise teacher note may already be one meaningful retrieval unit.

Chunk when:

- a lesson note contains multiple topics
- a cycle summary becomes long
- curriculum documents contain many sections
- imported educational resources exceed practical context size

Prefer domain-aware boundaries:

```text
student
exam cycle
lesson
task category
repertoire item
technical observation
```

## Example StudioPulse Structure

```json
{
  "_id": "lesson-42-piece-a",
  "parentLessonId": "lesson-42",
  "studentId": "...",
  "examCycleId": "...",
  "section": "pieceA",
  "pieceName": "...",
  "text": "Piece A reached 88 BPM, but measures 17–24 remain uneven.",
  "lessonDate": "2026-07-10"
}
```

## Evaluation

Chunking should be tested rather than selected by habit.

Questions:

- Does the retrieved chunk answer the question?
- Does it preserve enough context?
- Are many irrelevant words included?
- Are multiple duplicate chunks returned?
- Can the parent source be reconstructed?
- Does the answer retain source attribution?
- Does chunking increase cost without improving retrieval?

## Key Principle

> Search with the smallest unit that preserves meaning, then retrieve broader context when the task requires it.
