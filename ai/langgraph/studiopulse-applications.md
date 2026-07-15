# StudioPulse Applications

This document explores possible uses of LangGraph and agent orchestration within StudioPulse.

These are architectural possibilities, not immediate implementation requirements.

The initial StudioPulse intelligence platform should prioritize:

- reliable source data
- deterministic analytics
- authorization
- semantic retrieval
- traceable generated insights

Graph-based agents should be introduced only where dynamic orchestration provides clear product value.

## Current Data Foundation

StudioPulse already collects or plans to collect:

### Structured data

- students
- teachers
- parents
- exam cycles
- practice minutes
- task completion
- current tempos
- goal tempos
- attendance
- progress scores
- game sessions
- cumulative game statistics
- days remaining before exams

### Text data

- teacher notes
- student reflections
- practice notes
- task feedback
- technical observations
- cycle summaries
- long-term insight summaries

## Intelligence Architecture

```text
StudioPulse source data
        ↓
Deterministic analytics
        ↓
Generated insights
        ↓
MongoDB Automated Embedding
        ↓
Semantic and hybrid search
        ↓
LLM explanation
        ↓
Future agent workflows
```

## Suggested Implementation Sequence

```text
1. Reliable source data
2. Exam-cycle analytics
3. Insight generation
4. Automated Embedding
5. Semantic search
6. Summary regeneration pipeline
7. Long-term intelligence
8. Agent orchestration where justified
```

## Application 1: Exam-Cycle Progress Assistant

Example question:

> “How is this student progressing toward the exam?”

Required evidence:

- practice consistency
- score trends
- tempo progress
- assignment completion
- days to exam
- teacher observations

A simple initial workflow:

```text
Authorize user
     ↓
Load complete cycle analytics
     ↓
Retrieve relevant teacher notes
     ↓
Generate grounded explanation
```

This may begin as a chain rather than a graph.

A graph becomes useful when the system must decide which evidence is required based on the specific question.

## Application 2: Tempo Progress Analysis

Example question:

> “Why has Piece A stopped progressing toward its goal tempo?”

Possible workflow:

```text
Authorize access
     ↓
Load tempo history
     ↓
Detect plateau
     ↓
Load practice consistency
     ↓
Search relevant teacher notes
     ↓
Enough evidence?
 ┌────────┴─────────┐
No                  Yes
 ↓                   ↓
Retrieve more       Explain likely factors
```

Evidence may include:

- starting tempo
- current tempo
- goal tempo
- weekly tempo changes
- practice minutes
- task outcomes
- teacher comments about tension, fingering, rhythm, or accuracy

## Application 3: Practice Recommendation Assistant

Example question:

> “What should the student focus on this week?”

Possible inputs:

- incomplete assignments
- recent practice frequency
- task outcomes
- current and goal tempos
- teacher priorities
- exam proximity
- recurring technical issues

The assistant should recommend only actions supported by current teacher instructions and student data.

The teacher remains the educational authority.

## Application 4: Parent Progress Assistant

Example question:

> “Is my child making enough progress?”

The parent-facing assistant should:

- use plain language
- avoid unsupported predictions
- distinguish fact from interpretation
- focus only on authorized children
- avoid exposing private teacher-only notes
- provide source dates
- encourage teacher communication where needed

Possible tools:

```text
get_parent_authorized_student_snapshot
get_practice_consistency_summary
get_assignment_completion_summary
get_parent_visible_teacher_feedback
```

## Application 5: Teacher Assistant

Possible teacher workflows:

- summarize student progress
- identify recurring difficulties
- compare recent practice with earlier cycles
- prepare lesson-review notes
- surface students needing attention
- generate draft parent updates
- locate relevant historical observations

The teacher assistant should not replace:

- evaluation
- grading
- repertoire decisions
- pedagogical judgment
- direct communication

## Application 6: Long-Term Student Intelligence

Long-term intelligence compares several completed cycles.

Possible questions:

> “What recurring problems appear near performance tempo?”

> “Which practice patterns lead to the strongest improvement?”

> “Does this student usually plateau before reaching goal tempo?”

Possible evidence:

- cross-cycle tempo progression
- consistency rates
- score trajectories
- teacher observations
- practice distribution
- intervention outcomes

Possible workflow:

```text
Authorize user
     ↓
Identify relevant completed cycles
     ↓
Load structured cycle analytics
     ↓
Search semantic cycle summaries
     ↓
Compare recurring patterns
     ↓
Generate evidence-based explanation
```

## Application 7: Exam Readiness Evidence

The system may organize evidence relevant to readiness.

Possible evidence:

- completion percentage
- current scores
- score stability
- current tempo versus goal tempo
- recent consistency
- teacher observations
- days remaining

The system should not produce an authoritative readiness decision unless explicitly designed and reviewed by qualified educators.

Prefer:

> “Here is the current evidence related to readiness.”

Avoid:

> “The student will definitely pass.”

## Application 8: Student Marketplace Intelligence

GameProgress data may later support:

- skill-specific performance summaries
- repeated error patterns
- practice-duration analysis
- mode comparisons
- progress across sessions

Structured GameProgress fields should be analyzed deterministically.

Generated summaries may then become semantic-searchable insight documents.

Example:

```text
Raw sessions
    ↓
Cumulative analytics
    ↓
Skill-pattern summary
    ↓
Long-term student intelligence
```

## Proposed Tools

### Structured analytics tools

```text
get_exam_cycle_analytics
get_practice_consistency_analysis
get_tempo_progress_analysis
get_score_progress_analysis
get_game_progress_analysis
```

### Semantic retrieval tools

```text
search_teacher_notes
search_exam_cycle_insights
search_long_term_student_insights
```

### Composite domain tools

```text
get_authorized_student_progress_snapshot
get_exam_readiness_evidence
get_parent_visible_progress_summary
```

## Example Graph State

```python
from typing_extensions import TypedDict

class StudioPulseAssistantState(TypedDict):
    user_id: str
    user_role: str
    question: str

    student_id: str
    exam_cycle_id: str | None

    authorized: bool
    intent: str

    structured_analytics: dict
    semantic_evidence: list[dict]

    evidence_complete: bool
    final_answer: str
```

## Example Graph

```text
START
  ↓
Authorize
  ↓
Authorized?
 ┌───────┴────────┐
No                Yes
 ↓                 ↓
Deny          Classify Intent
 ↓                 ↓
END       ┌────────┼─────────┐
          Practice Tempo   General
             ↓       ↓        ↓
          Analytics Analytics Snapshot
             └───────┼────────┘
                     ↓
              Retrieve Notes
                     ↓
              Evidence Complete?
                ┌────┴────┐
               No         Yes
                ↓          ↓
           Retrieve More  Generate
                              ↓
                           Validate
                              ↓
                             END
```

## Authorization Requirements

Every tool must enforce existing StudioPulse access rules.

The graph must never trust:

- a model-generated `studentId`
- a frontend-supplied role without authentication
- semantic-search results without authorization filtering
- unrestricted cross-student retrieval

Authorization should remain inside trusted Express services and middleware.

```text
Authenticated user
       ↓
Backend resolves role and access
       ↓
Authorized student scope
       ↓
Tool or search execution
```

## Structured and Semantic Data

### Structured source data

Use normal MongoDB queries and aggregations for:

- practice minutes
- current tempo
- goal tempo
- attendance
- score values
- dates
- completion counts

### Direct semantic data

Use semantic retrieval for selected text such as:

- teacher observations
- student reflections
- practice notes
- technical feedback

### Derived semantic data

Generate and embed:

- exam-cycle summaries
- tempo-progression summaries
- practice-pattern summaries
- long-term trend summaries

```text
Numeric source data
      ↓
Aggregation
      ↓
Calculated insight
      ↓
Narrative summary
      ↓
Automated Embedding
      ↓
Semantic retrieval
```

## Human-in-the-Loop Opportunities

Possible review points include:

- teacher approval of cycle summaries
- correction of AI-generated observations
- review before sending parent communications
- approval before storing long-term insights
- confirmation before modifying assignments

## Observability Requirements

Agent workflows should record:

- graph execution path
- tools called
- tool arguments after safe redaction
- source records used
- retrieved insight identifiers
- model and prompt versions
- failures and retries
- final response
- user role and authorized scope

Do not log sensitive student content unnecessarily.

## Initial Recommendation

Do not begin by creating a fully autonomous StudioPulse agent.

Start with:

```text
Deterministic analytics services
        ↓
Authorized retrieval services
        ↓
One-step grounded summaries
        ↓
Simple tool calling
        ↓
Conditional graphs only when needed
```

This preserves reliability while allowing the intelligence platform to grow incrementally.

## Key Principle

> StudioPulse agents should orchestrate trusted educational services, not replace them.
