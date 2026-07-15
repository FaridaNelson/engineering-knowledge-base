# Memory

Memory allows an agent to preserve useful context across steps, conversations, or sessions.

In LangGraph, memory is closely related to state and persistence.

Memory should not be treated as one unlimited conversation transcript. Different kinds of information require different storage, retention, and retrieval strategies.

## Memory Categories

```text
Memory
├── Working state
├── Short-term conversation memory
├── Long-term memory
└── External source data
```

## Working State

Working state exists during one graph execution.

Examples:

- current user request
- tool outputs
- retrieved documents
- validation results
- current workflow step
- draft response

```python
class GraphState(TypedDict):
    question: str
    analytics: dict
    retrieved_notes: list[str]
    final_answer: str
```

Working state is not automatically long-term memory.

## Short-Term Memory

Short-term memory preserves context within a conversation or thread.

Examples:

- recent user messages
- previous assistant messages
- earlier tool results
- clarification already provided

```text
Message 1
   ↓
Message 2
   ↓
Tool call
   ↓
Tool result
   ↓
Message 3
```

This allows follow-up questions such as:

> “How does that compare with the previous month?”

The system can understand what “that” refers to.

## Checkpointing

A checkpointer persists graph state at execution steps.

Conceptually:

```text
Graph executes
     ↓
State checkpoint saved
     ↓
Process pauses or fails
     ↓
Graph resumes from saved state
```

Checkpointing supports:

- conversational threads
- pause and resume
- fault recovery
- human-in-the-loop workflows
- inspection of prior state
- long-running execution

A graph is commonly invoked with a thread identifier so that the correct conversation state can be recovered.

## Long-Term Memory

Long-term memory persists across separate conversations.

Examples:

- stable user preferences
- previously generated student insights
- completed exam-cycle summaries
- recurring learning patterns
- teacher-approved observations

Long-term memory usually lives in an external store rather than only in the current graph state.

Possible stores include:

- MongoDB collections
- semantic-search indexes
- structured profile records
- generated insight documents

## Source Data vs. Memory

Not all persistent data should be described as agent memory.

StudioPulse source data includes:

- practice logs
- attendance
- current tempo
- goal tempo
- scores
- teacher notes
- assignments
- exam cycles

These are authoritative application records.

Derived memory may include:

- exam-cycle analytics summaries
- recurring technical patterns
- long-term progress summaries
- teacher-approved AI observations

The distinction matters:

```text
Source data
    ↓
Deterministic analysis
    ↓
Derived insight
    ↓
Long-term searchable memory
```

## Semantic Memory

Semantic memory stores knowledge in a form that can be retrieved by meaning.

Example summary:

> Across three exam cycles, the student showed rapid early tempo improvement followed by plateaus near 85% of goal tempo.

A user could later ask:

> “Has this student experienced similar tempo plateaus before?”

Semantic search can retrieve the relevant summary even when the wording differs.

## Episodic Memory

Episodic memory represents specific past events.

Examples:

- a lesson where wrist tension appeared
- an exam-cycle completion
- a missed week of practice
- a teacher intervention that improved rhythm

These memories should retain:

- source identifiers
- dates
- student scope
- exam-cycle scope
- provenance
- authorization metadata

## Procedural Memory

Procedural memory represents instructions or rules for how the system should behave.

Examples:

- teacher evaluation rubric
- exam-readiness criteria
- safe response policies
- preferred summary format
- authorization rules

Security and authorization rules should remain deterministic application code rather than editable LLM memory.

## Memory Retrieval

Long-term memory should be retrieved selectively.

Poor approach:

```text
Load all student history
        ↓
Send everything to the LLM
```

Better approach:

```text
User question
     ↓
Apply authorized student filter
     ↓
Search relevant summaries or notes
     ↓
Retrieve top evidence
     ↓
Generate grounded answer
```

This reduces:

- token usage
- irrelevant context
- privacy risk
- hallucination risk
- latency

## Memory Compression

Conversation history and long-term records can grow indefinitely.

Possible strategies include:

- retaining only recent messages
- summarizing older conversations
- retrieving past context on demand
- storing important facts separately
- preserving source links
- discarding low-value transient content

A summary must not replace authoritative source records.

## Memory Quality

Long-term memory should include metadata such as:

```json
{
  "studentId": "...",
  "examCycleId": "...",
  "summary": "...",
  "generatedAt": "...",
  "summaryVersion": 1,
  "sourceUpdatedAt": "...",
  "reviewStatus": "unreviewed"
}
```

Useful metadata includes:

- creation time
- source revision
- generating model
- prompt version
- approval status
- expiration or regeneration conditions

## Memory Risks

### Stale memory

An old summary may no longer reflect current source data.

### Incorrect generated memory

An LLM-generated observation may contain an error.

### Duplicated memory

Repeated summaries may describe the same event.

### Unauthorized retrieval

A memory search may cross student, teacher, or organization boundaries.

### Context overload

Too much retrieved memory can reduce answer quality.

## StudioPulse Memory Layers

### Layer 1: Current request state

```text
question
authorized student
selected exam cycle
retrieved evidence
```

### Layer 2: Conversation memory

```text
recent messages
prior clarification
earlier tool results
```

### Layer 3: Exam-cycle memory

```text
cycle analytics
cycle summary
teacher observations
readiness evidence
```

### Layer 4: Long-term student memory

```text
cross-cycle trends
recurring strengths
recurring challenges
practice patterns
tempo progression patterns
```

## Human Oversight

Educational conclusions may influence teachers, parents, and students.

Generated memory should therefore support:

- teacher review
- correction
- source inspection
- regeneration
- deletion
- version tracking

The agent should distinguish between:

```text
Teacher-confirmed observation
AI-generated hypothesis
Deterministic calculated fact
```

## Key Principle

> State supports the current workflow. Checkpoints preserve execution. Long-term memory stores selected, traceable knowledge across sessions.
