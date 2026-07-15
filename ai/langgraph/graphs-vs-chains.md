# Graphs vs. Chains

Chains and graphs are two ways to orchestrate LLM application workflows.

A chain follows a mostly predefined sequence.

A graph supports branching, cycles, stateful routing, and repeated execution.

## Simple Chain

```text
Input
  ↓
Retrieve
  ↓
Generate
  ↓
Output
```

The execution order is known in advance.

Example:

```python
def answer_question(question):
    documents = retrieve(question)
    return generate_answer(question, documents)
```

## Graph

```text
Input
  ↓
Agent
  ↓
Need a tool?
 ┌───────┴────────┐
Yes               No
 ↓                 ↓
Tool              END
 ↓
Agent
```

The next step is selected during execution.

## Key Difference

A chain asks:

> What steps should always happen?

A graph asks:

> Based on the current state, what should happen next?

## When a Chain Is Better

Use a chain when:

- the workflow is linear
- the required steps are known
- one tool returns a complete answer
- deterministic behavior is important
- latency should be minimized
- the task has few branches
- failure handling is simple

Example:

```text
User requests cycle summary
        ↓
Authorize access
        ↓
Load complete cycle snapshot
        ↓
Generate explanation
        ↓
Return response
```

This workflow does not necessarily need agentic looping.

## When a Graph Is Better

Use a graph when:

- the workflow can branch
- different questions require different evidence
- the model may call several tools
- the number of steps is not known in advance
- human approval may interrupt execution
- the system must pause and resume
- retries or recovery are important
- multiple specialized agents or subgraphs are involved

Example:

```text
User question
     ↓
Classify intent
 ┌──────┼───────────┐
Practice Tempo   Teacher Notes
   ↓        ↓            ↓
Analytics  Trend Tool   Search
   └────────┴──────┬─────┘
                   ↓
             Enough evidence?
              ┌────┴────┐
             No         Yes
              ↓          ↓
         Call tool     Answer
```

## Tool Granularity and Architecture

The granularity of tools strongly affects whether a graph is useful.

### Fine-grained tools

```text
get_student
get_cycle
get_practice_logs
get_tempo_history
search_notes
```

The model may need to call several tools, inspect results, and decide what to retrieve next.

A graph can manage this loop.

### Coarse-grained tool

```text
get_complete_exam_cycle_analysis
```

The tool returns a complete answer-ready result.

A simple chain may be enough.

## Comparison

| Concern                | Chain                     | Graph               |
| ---------------------- | ------------------------- | ------------------- |
| Execution order        | Mostly predefined         | Dynamic             |
| Branching              | Limited                   | Native              |
| Loops                  | Usually manual            | Native              |
| Shared state           | Often passed step to step | Explicit            |
| Tool calls             | Usually fixed or limited  | Adaptive            |
| Debugging              | Simpler                   | More complex        |
| Cost predictability    | Higher                    | Lower               |
| Latency predictability | Higher                    | Lower               |
| Human interruption     | Possible but custom       | First-class pattern |
| Long-running workflows | Less natural              | Strong fit          |
| Best use               | Stable workflow           | Adaptive workflow   |

## Graph Does Not Mean Better

A graph introduces additional complexity:

- more code
- more runtime decisions
- more execution paths
- harder testing
- more observability requirements
- possible infinite loops
- increased token and tool usage

Do not introduce a graph only because the application uses an LLM.

A plain function may be enough:

```python
def create_progress_summary(student_id, exam_cycle_id):
    analytics = calculate_analytics(
        student_id,
        exam_cycle_id
    )

    notes = retrieve_notes(
        student_id,
        exam_cycle_id
    )

    return generate_summary(
        analytics,
        notes
    )
```

This remains a valid AI-enabled workflow.

## Decision Checklist

Prefer a chain when most answers are `yes`:

- Are the steps known before execution?
- Does one backend function provide complete evidence?
- Is the workflow short?
- Is predictable cost important?
- Is dynamic routing unnecessary?

Prefer a graph when most answers are `yes`:

- Can the required steps vary by question?
- Must the model decide which evidence is missing?
- Can tools be called repeatedly?
- Does the workflow need branching?
- Must execution pause and resume?
- Is human approval part of the process?
- Are multiple workflows being coordinated?

## StudioPulse Recommendation

For early StudioPulse intelligence features, begin with chains and robust backend services.

Example:

```text
Request
  ↓
Authorize
  ↓
ExamCycle Analytics Service
  ↓
Insight Retrieval Service
  ↓
LLM Explanation
  ↓
Response
```

Use a graph later when the assistant needs to adaptively decide among:

- structured practice analytics
- tempo progression
- score history
- attendance
- teacher notes
- semantic search
- long-term cycle summaries
- clarification questions
- human review

## Evolution Path

```text
Phase 1
Deterministic services
        ↓
Phase 2
Simple LLM chain
        ↓
Phase 3
Tool calling
        ↓
Phase 4
Conditional graph
        ↓
Phase 5
Long-running agent workflows
```

The system should become more agentic only when the product requirements justify the additional complexity.

## Key Principle

> Use a chain when the path is known. Use a graph when the path must be discovered during execution.
