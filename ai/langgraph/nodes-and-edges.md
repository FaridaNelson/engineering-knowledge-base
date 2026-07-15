# Nodes and Edges

LangGraph workflows are assembled from nodes and edges.

- **Nodes do the work.**
- **Edges determine what runs next.**

```text
State
  ↓
Node
  ↓
State update
  ↓
Edge
  ↓
Next node
```

## Nodes

A node is a function that receives the current state, performs work, and returns an update.

A node may contain:

- ordinary application logic
- an LLM invocation
- a database lookup
- a tool execution
- retrieval
- validation
- authorization
- transformation
- human approval logic

A node does not have to use AI.

## Basic Node

```python
def calculate_progress(state):
    current_tempo = state["current_tempo"]
    goal_tempo = state["goal_tempo"]

    progress_percentage = (
        current_tempo / goal_tempo * 100
        if goal_tempo > 0
        else 0
    )

    return {
        "tempo_progress_percentage": progress_percentage
    }
```

This is deterministic application code inside a graph node.

## LLM Node

```python
def agent_node(state):
    response = model.invoke(state["messages"])

    return {
        "messages": [response]
    }
```

The node asks the model to respond or decide whether a tool is required.

## Retrieval Node

```python
def retrieve_teacher_notes(state):
    notes = search_teacher_notes(
        student_id=state["student_id"],
        exam_cycle_id=state["exam_cycle_id"],
        query=state["question"]
    )

    return {
        "teacher_notes": notes
    }
```

## Authorization Node

```python
def authorize_student_access(state):
    authorized = can_access_student(
        user_id=state["user_id"],
        user_role=state["user_role"],
        student_id=state["student_id"]
    )

    return {
        "authorized": authorized
    }
```

Authorization should be performed by deterministic backend logic, not delegated to the LLM.

## Adding Nodes

```python
from langgraph.graph import StateGraph

builder = StateGraph(GraphState)

builder.add_node("authorize", authorize_student_access)
builder.add_node("analytics", load_cycle_analytics)
builder.add_node("retrieve_notes", retrieve_teacher_notes)
builder.add_node("answer", generate_answer)
```

The string is the node name. The second argument is the function executed by that node.

## Edges

Edges define how execution moves through the graph.

There are two main categories:

- fixed edges
- conditional edges

## Fixed Edges

A fixed edge always routes to the same next node.

```python
from langgraph.graph import START, END

builder.add_edge(START, "authorize")
builder.add_edge("analytics", "retrieve_notes")
builder.add_edge("retrieve_notes", "answer")
builder.add_edge("answer", END)
```

Conceptually:

```text
START
  ↓
Authorize
  ↓
Analytics
  ↓
Retrieve Notes
  ↓
Answer
  ↓
END
```

## Conditional Edges

Conditional edges select the next node based on current state.

```python
def route_after_authorization(state):
    if state["authorized"]:
        return "analytics"

    return "deny"
```

```python
builder.add_conditional_edges(
    "authorize",
    route_after_authorization,
    {
        "analytics": "analytics",
        "deny": "deny"
    }
)
```

Conceptually:

```text
Authorize
   ↓
Authorized?
 ┌───────┴───────┐
Yes              No
 ↓                ↓
Analytics        Deny
```

## Tool-Calling Route

An agent may return a tool request.

A routing function examines the latest model message:

```python
def route_after_agent(state):
    last_message = state["messages"][-1]

    if last_message.tool_calls:
        return "tools"

    return "end"
```

```python
builder.add_conditional_edges(
    "agent",
    route_after_agent,
    {
        "tools": "tools",
        "end": END
    }
)
```

The tools node can then return execution to the agent:

```python
builder.add_edge("tools", "agent")
```

This creates a loop:

```text
Agent
  ↓
Needs tool?
 ┌───────┴───────┐
Yes              No
 ↓                ↓
Tools            END
 ↓
Agent
```

## START and END

`START` is the virtual entry point into the graph.

```python
builder.add_edge(START, "authorize")
```

`END` marks termination.

```python
builder.add_edge("answer", END)
```

They are not ordinary business-logic nodes.

## Compiling the Graph

After defining state, nodes, and edges, the graph must be compiled.

```python
graph = builder.compile()
```

Compilation validates the graph structure and prepares it for execution.

```python
result = graph.invoke(initial_state)
```

## Cycles

Unlike a simple chain, a graph may revisit nodes.

```text
Agent
  ↓
Tool
  ↓
Agent
  ↓
Tool
  ↓
Agent
  ↓
END
```

Cycles are useful when the next step cannot be fully known before execution.

They also create risks:

- endless loops
- excessive tool calls
- increased latency
- increased model cost

A production graph should have clear stopping conditions and execution limits.

## Node Granularity

A node should represent a meaningful unit of work.

Too small:

```text
Load student name
Load lesson date
Load one score
Load one note
```

This may create unnecessary graph complexity.

Too large:

```text
Perform all authorization, retrieval, analysis,
generation, persistence, and notification
inside one node
```

This reduces observability and makes failures harder to isolate.

A useful balance is:

```text
Authorize request
Load structured analytics
Retrieve semantic evidence
Generate grounded answer
Validate response
```

## Node Design Guidelines

A good node should have:

- one clear responsibility
- explicit inputs
- predictable outputs
- limited side effects
- retry-safe behavior
- meaningful error handling

Example:

```python
def load_cycle_analytics(state):
    analytics = analytics_service.calculate_exam_cycle(
        student_id=state["student_id"],
        exam_cycle_id=state["exam_cycle_id"]
    )

    return {
        "cycle_analytics": analytics
    }
```

## StudioPulse Example

```python
builder.add_node("authorize", authorize_student_access)
builder.add_node("classify_question", classify_question)
builder.add_node("load_analytics", load_cycle_analytics)
builder.add_node("search_notes", search_teacher_notes)
builder.add_node("generate_answer", generate_grounded_answer)
builder.add_node("deny", generate_access_denied_response)
```

Possible routing:

```text
START
  ↓
Authorize
  ↓
Authorized?
 ┌───────┴────────┐
No                Yes
 ↓                 ↓
Deny        Classify Question
 ↓                 ↓
END        ┌────────┴────────┐
           Analytics       Notes
              ↓              ↓
              └──────┬───────┘
                     ↓
               Generate Answer
                     ↓
                    END
```

## Key Principle

> Nodes encapsulate work. Edges encode orchestration.
