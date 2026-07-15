# Graph State

Graph state is the shared data structure that represents the current snapshot of a LangGraph workflow.

Every node can read relevant values from the state and return partial updates. LangGraph applies those updates according to the reducer configured for each state field.

```text
Initial State
     ↓
Node reads state
     ↓
Node performs work
     ↓
Node returns partial update
     ↓
LangGraph applies update
     ↓
Next node receives updated state
```

## Why State Matters

Without shared state, each step would need to manually pass individual values to the next step.

State gives the workflow a consistent place to store:

- conversation messages
- user input
- retrieved documents
- tool results
- validation status
- identifiers
- workflow progress
- final output

State allows a workflow to pause, resume, branch, loop, and preserve context across multiple nodes.

## Defining State

A common way to define state is with `TypedDict`.

```python
from typing_extensions import TypedDict

class GraphState(TypedDict):
    user_query: str
    student_id: str
    retrieved_notes: list[str]
    analytics: dict
    final_answer: str
```

The graph is then created with that state schema:

```python
from langgraph.graph import StateGraph

builder = StateGraph(GraphState)
```

## Nodes Return Partial Updates

A node does not need to return the entire state.

```python
def load_notes(state: GraphState):
    notes = [
        "Piece A tempo improved.",
        "Left-hand tension remains."
    ]

    return {
        "retrieved_notes": notes
    }
```

Only `retrieved_notes` is updated. Other state fields remain available.

## Default Update Behavior

Without a custom reducer, a returned value replaces the existing value.

Initial state:

```python
{
    "retrieved_notes": ["Earlier note"]
}
```

Node update:

```python
{
    "retrieved_notes": ["New note"]
}
```

Result:

```python
{
    "retrieved_notes": ["New note"]
}
```

## Reducers

Reducers determine how a new value is combined with the current state value.

A reducer receives:

```text
current value + node update → new value
```

For example, list values can be accumulated instead of replaced.

```python
from operator import add
from typing import Annotated
from typing_extensions import TypedDict

class GraphState(TypedDict):
    retrieved_notes: Annotated[list[str], add]
```

Initial state:

```python
{
    "retrieved_notes": ["Note A"]
}
```

Node update:

```python
{
    "retrieved_notes": ["Note B"]
}
```

Result:

```python
{
    "retrieved_notes": ["Note A", "Note B"]
}
```

## Message State

Agent workflows commonly store conversation messages in state.

LangGraph provides `MessagesState` and the `add_messages` reducer for this purpose.

```python
from langgraph.graph import MessagesState

def agent_node(state: MessagesState):
    response = model.invoke(state["messages"])

    return {
        "messages": [response]
    }
```

Conceptually:

```text
User message
     ↓
Stored in state
     ↓
LLM response
     ↓
Added to state
     ↓
Tool result
     ↓
Added to state
     ↓
Final response
```

## Input, Internal, and Output State

A graph may use different schemas for different purposes.

### Input state

Values accepted when the graph begins.

```python
class InputState(TypedDict):
    user_query: str
    student_id: str
```

### Internal state

Values used while the workflow runs.

```python
class InternalState(TypedDict):
    user_query: str
    student_id: str
    practice_data: dict
    teacher_notes: list[str]
    analytics: dict
    draft_answer: str
```

### Output state

Values returned to the caller.

```python
class OutputState(TypedDict):
    final_answer: str
```

This separation can reduce accidental exposure of internal values.

## State Design Guidelines

### Store workflow data, not arbitrary application data

State should contain information required by the current workflow.

Avoid copying entire database documents into state when only a few fields are needed.

### Prefer identifiers over duplicated objects

Instead of storing an entire student document:

```python
{
    "student": {
        "...": "large document"
    }
}
```

prefer:

```python
{
    "student_id": "..."
}
```

and retrieve only the authorized fields required by the workflow.

### Keep sensitive data scoped

Only add information that the current user and workflow are authorized to access.

### Separate source facts from generated text

```python
class GraphState(TypedDict):
    analytics: dict
    teacher_notes: list[str]
    generated_summary: str
```

This keeps exact data distinguishable from AI-generated interpretation.

### Design for retry safety

A node may be re-executed after interruption or failure.

State updates and side effects should therefore be:

- deterministic where possible
- idempotent
- safe to retry
- traceable

## StudioPulse Example

```python
from typing_extensions import TypedDict

class StudentProgressState(TypedDict):
    user_id: str
    user_role: str
    student_id: str
    exam_cycle_id: str
    question: str

    authorized: bool

    practice_analytics: dict
    tempo_analytics: dict
    teacher_notes: list[str]
    relevant_insights: list[str]

    final_answer: str
```

Possible execution:

```text
Input state
   ↓
Authorize access
   ↓
Load cycle analytics
   ↓
Retrieve teacher observations
   ↓
Generate answer
   ↓
Return final output
```

## Key Principle

> State is the evolving source of workflow context. Nodes read it, nodes update it, and edges use it to decide what happens next.
