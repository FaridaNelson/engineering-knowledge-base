# Tool Calling

Tool calling allows an LLM to request that application code perform an operation.

The LLM does not directly execute database queries, APIs, calculations, or other side effects. It produces a structured tool request, and the application decides whether and how to execute it.

```text
User question
    ↓
LLM examines available tools
    ↓
LLM requests a tool
    ↓
Application validates and executes it
    ↓
Tool result is returned to the LLM
    ↓
LLM continues or produces an answer
```

## What Is a Tool?

A tool is a function exposed to the model with:

- a name
- a description
- an input schema
- executable application logic

Example:

```python
def get_exam_cycle_analytics(
    student_id: str,
    exam_cycle_id: str
) -> dict:
    ...
```

A model may request:

```json
{
  "name": "get_exam_cycle_analytics",
  "args": {
    "student_id": "student-123",
    "exam_cycle_id": "cycle-456"
  }
}
```

The application executes the actual function.

## Binding Tools to a Model

Conceptually:

```python
tools = [
    get_exam_cycle_analytics,
    search_teacher_notes
]

model_with_tools = model.bind_tools(tools)
```

The agent node invokes the model:

```python
def agent_node(state):
    response = model_with_tools.invoke(state["messages"])

    return {
        "messages": [response]
    }
```

The response may contain either:

- a direct answer
- one or more structured tool calls

## Tool Node

A tool node executes requested tools.

```python
from langgraph.prebuilt import ToolNode

tool_node = ToolNode(tools)
```

```python
builder.add_node("tools", tool_node)
```

The graph routes to this node when the latest model message contains tool calls.

```text
Agent
  ↓
Tool requested?
 ┌───────┴───────┐
Yes              No
 ↓                ↓
Tool Node        END
 ↓
Agent
```

## Fine-Grained Tools

Fine-grained tools perform small operations.

Examples:

```text
get_student()
get_practice_logs()
get_tempo_history()
get_teacher_notes()
calculate_weekly_average()
```

Advantages:

- reusable
- composable
- flexible
- useful for unanticipated questions

Trade-offs:

- more model decisions
- more tool calls
- greater latency
- higher cost
- more complex execution traces
- greater risk of loops or incomplete data collection

Fine-grained tools often benefit from a graph because the model may repeatedly decide what information is still missing.

## Coarse-Grained Tools

Coarse-grained tools perform complete domain operations.

Examples:

```text
get_authorized_exam_cycle_snapshot()
generate_practice_consistency_report()
get_student_readiness_evidence()
```

A robust tool may internally:

- validate authorization
- query multiple collections
- calculate aggregates
- normalize data
- return a complete structured result

Advantages:

- predictable
- easier to test
- fewer model calls
- lower latency
- stronger security boundaries

Trade-offs:

- less flexible
- more application code
- new workflows may require new tools

Coarse-grained tools can often be used in a simple chain without a complex graph.

## Choosing Tool Granularity

A useful principle from the MongoDB AI Agents webinar:

> Fine-grained tools require smarter orchestration. Robust domain tools can support simpler chains.

Use fine-grained tools when:

- the workflow is exploratory
- the required data is not known in advance
- tool reuse is important
- the model must adaptively collect evidence

Use coarse-grained tools when:

- the workflow is well defined
- predictable execution matters
- authorization is complex
- exact calculations are required
- latency and cost must be limited

## Tool Security

The model must never be treated as the authorization layer.

Unsafe:

```python
def find_student(student_id: str):
    return Student.find_by_id(student_id)
```

The model could request any student identifier.

Safer:

```python
def get_authorized_student_progress(
    requesting_user_id: str,
    student_id: str
):
    access = authorize_student_access(
        requesting_user_id,
        student_id
    )

    if not access:
        raise PermissionError("Access denied")

    return load_safe_student_progress(student_id)
```

Even better, derive the requesting user from trusted runtime context rather than allowing the model to supply it.

## Avoid Arbitrary Database Tools

Do not expose unrestricted tools such as:

```text
run_mongodb_query()
execute_aggregation_pipeline()
find_any_document()
```

Prefer domain-specific tools:

```text
get_exam_cycle_progress()
get_practice_summary()
search_authorized_teacher_feedback()
```

This reduces:

- unauthorized access
- prompt injection risk
- accidental destructive actions
- malformed queries
- unnecessary data exposure

## Validate Tool Arguments

Tool inputs should be validated before execution.

```python
from pydantic import BaseModel, Field

class ExamCycleAnalyticsInput(BaseModel):
    student_id: str
    exam_cycle_id: str
    weeks: int = Field(default=8, ge=1, le=52)
```

Validation should cover:

- identifiers
- allowed ranges
- enum values
- date boundaries
- maximum result sizes
- user authorization

## Tool Results

Tool output should be structured and concise.

Prefer:

```json
{
  "practice": {
    "totalMinutes": 420,
    "weeklyAverage": 70,
    "consistencyRate": 0.78
  },
  "tempo": {
    "startTempo": 72,
    "currentTempo": 88,
    "goalTempo": 96,
    "goalProgressPercentage": 91.7
  }
}
```

Avoid returning entire database documents with irrelevant or sensitive fields.

## Side Effects

Some tools only read data.

```text
get_progress()
search_notes()
calculate_analytics()
```

Other tools change state outside the graph.

```text
save_summary()
send_notification()
update_assignment()
```

Write tools require stronger safeguards:

- explicit authorization
- validation
- idempotency
- confirmation for high-impact actions
- audit logging

## StudioPulse Tool Strategy

Initial StudioPulse AI features should favor robust domain tools.

Examples:

```text
get_authorized_exam_cycle_snapshot
get_practice_consistency_analysis
get_tempo_progress_analysis
search_authorized_teacher_notes
get_exam_readiness_evidence
```

The backend should own:

- authentication
- authorization
- MongoDB queries
- aggregations
- numeric calculations
- sensitive-field filtering
- source attribution

The LLM should primarily:

- select an appropriate domain tool
- interpret structured results
- explain evidence
- identify uncertainty
- ask for clarification when necessary

## Example StudioPulse Loop

```text
Parent asks:
"Why has the tempo stopped improving?"

        ↓

Agent chooses:
get_tempo_progress_analysis

        ↓

Tool returns:
- current tempo history
- goal tempo
- plateau period
- practice consistency

        ↓

Agent chooses:
search_authorized_teacher_notes

        ↓

Tool returns:
- observations about tension
- fingering inconsistency

        ↓

Agent produces grounded explanation
```

## Key Principle

> The LLM chooses tools, but trusted application code validates, authorizes, and executes them.
