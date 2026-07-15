# MongoDB Vocabulary

## Database

Container for collections.

---

## Collection

Equivalent to a SQL table.

---

## Document

Equivalent to a a row in a SQL table.

Documents are stored in BSON format.

---

## BSON

Binary JSON.

MongoDB's internal storage format.

---

## CRUD

The four fundamental database operations:

- Create
- Read
- Update
- Delete

---

## Query

A request to retrieve documents from a collection.

---

## Filter

Conditions used to match documents in a query.

Example:

```javascript
db.users.find({ age: { $gte: 18 } });
```

---

## Projection

Selecting which fields should be returned by a query.

Example:

```javascript
db.users.find({}, { name: 1, email: 1 });
```

---

## Sorting

Ordering query results.

Example:

```javascript
db.users.find().sort({ createdAt: -1 });
```

---

## Limit

Restricting the number of documents returned.

Example:

```javascript
db.users.find().limit(10);
```

---

## countDocuments()

Efficient method for counting documents that match a filter.

Example:

```javascript
db.users.countDocuments({ active: true });
```

---

## Index

A data structure that improves query performance.

---

## Unique Index

An index that prevents duplicate values.

Example:

```javascript
unique: true;
```

---

## ObjectId

MongoDB's default unique document identifier.

---

## Cursor

The object returned by `find()` that allows iteration through query results.

---

## Embedded Document

A document stored inside another document.

Example:

```json
{
  "student": {
    "name": "John",
    "age": 12
  }
}
```

---

## Array

An ordered list stored within a document.

Example:

```json
{
  "skills": ["piano", "voice", "guitar"]
}
```

---

## Operator

A special MongoDB command beginning with `$`.

Examples:

- `$set`
- `$push`
- `$lt`
- `$gte`
- `$or`

---

# Vector Search

## Semantic Search

Search based on meaning rather than exact keywords.

Example:

- "prison escape movie"
- "movie about escaping prison"

can return similar results.

---

## Lexical Search

Search based on exact words, phrases, or keywords.

Examples:

- Full-text search
- Autocomplete
- Fuzzy search

---

## Vector Embedding

A numerical representation of data that captures semantic meaning.

Example:

```python
[0.12, -0.34, 0.88, ...]
```

---

## Embedding Model

An AI model that converts data into vector embeddings.

Examples:

- OpenAI Embeddings
- Voyage AI
- Cohere

---

## Dimensions

The number of values contained in an embedding vector.

Example:

```python
[0.1, 0.2, 0.3]
```

contains 3 dimensions.

---

## Similarity Function

The method used to compare vectors.

Examples:

- cosine
- euclidean
- dotProduct

---

## Cosine Similarity

Measures similarity using the angle between vectors.

Ignores vector magnitude.

---

## Euclidean Distance

Measures straight-line distance between vectors.

---

## Dot Product

Measures similarity using both angle and magnitude.

---

## Vector Search Index

A specialized MongoDB index used for semantic similarity search.

---

## HNSW

Hierarchical Navigable Small World graph.

A graph-based indexing algorithm used by MongoDB Vector Search to perform efficient approximate nearest neighbor searches.

---

## Approximate Nearest Neighbor (ANN)

A search technique that finds vectors that are likely to be the closest matches without comparing every vector.

---

## numCandidates

The number of candidate vectors considered during an ANN search before returning the final results.

---

## Pre-filtering

Applying filters before vector similarity search to reduce the search space.

---

## Quantization

A technique that compresses vectors to reduce storage and memory requirements.

---

## Scalar Quantization

Compresses vectors while preserving most semantic information.

---

## Binary Quantization

Aggressively compresses vectors with some loss of accuracy.

---

## Dedicated Search Nodes

MongoDB Atlas nodes dedicated to search and vector workloads.

---

## Retrieval-Augmented Generation (RAG)

An AI architecture that retrieves relevant information before generating a response.

---

## Agent Memory

Persistent storage that AI agents can retrieve from and reason over.
