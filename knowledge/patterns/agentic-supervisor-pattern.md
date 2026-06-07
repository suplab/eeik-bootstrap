# Pattern: LangGraph Supervisor Pattern

**Status:** Approved  
**Domain:** Agentic AI  
**Technology:** LangGraph / Python / Claude  
**Validated:** AI PoC and multi-agent projects

---

## Context

In multi-agent systems, routing user requests to the correct specialist agent is a recurring challenge. A naive approach (hardcoded `if/elif` chains) breaks as agents are added. A fully decentralised approach (agents deciding their own handoffs) leads to loops and unpredictable paths.

## Solution

A **Supervisor Agent** sits at the top of the graph. It receives all messages, decides which specialist to invoke next, and receives control back after each specialist responds — deciding whether to continue or end.

```
User → Supervisor
         ↓
    Route to worker: [research | code | review | archive]
         ↓
    Worker executes
         ↓
    Return to Supervisor
         ↓
    Route to next worker or END
```

## Example Implementation

```python
from typing import Annotated, Literal
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.prebuilt import create_react_agent
import operator

# State
class SupervisorState(TypedDict):
    messages: Annotated[list, operator.add]
    next: str

# Worker agents
research_agent = create_react_agent(model, tools=[search_tool])
code_agent = create_react_agent(model, tools=[code_tool])

def supervisor_node(state: SupervisorState) -> SupervisorState:
    """Supervisor decides which worker runs next."""
    system_prompt = """You are a supervisor routing tasks to workers.
    Workers: research, code, FINISH
    Based on the conversation, decide who should act next.
    Respond with just the worker name."""
    
    response = model.invoke([
        {"role": "system", "content": system_prompt},
        *state["messages"]
    ])
    return {"next": response.content.strip()}

def route_after_supervisor(state: SupervisorState) -> Literal["research", "code", END]:
    return END if state["next"] == "FINISH" else state["next"]

# Graph
graph = StateGraph(SupervisorState)
graph.add_node("supervisor", supervisor_node)
graph.add_node("research", research_agent)
graph.add_node("code", code_agent)

graph.set_entry_point("supervisor")
graph.add_conditional_edges("supervisor", route_after_supervisor)
graph.add_edge("research", "supervisor")   # Return to supervisor after each worker
graph.add_edge("code", "supervisor")

app = graph.compile()
```

## Trade-offs

| Pro | Con |
|-----|-----|
| Clean separation of concerns | Supervisor adds latency per turn |
| Easy to add new workers | Supervisor prompt needs careful tuning |
| Predictable control flow | Risk of routing loops if supervisor prompt is poor |
| Works well with Claude as supervisor | Supervisor context grows with conversation |

## Guardrails

- Set `recursion_limit` to prevent infinite loops (`graph.compile(recursion_limit=10)`)
- Log every supervisor decision for debugging
- Add `FINISH` as an explicit terminal route — never rely on implicit termination
- Test with adversarial inputs that might confuse routing

## When to Use Alternatives

- **Single-agent ReAct**: When task is well-scoped and one agent with tools suffices
- **Parallel fan-out**: When subtasks are independent and can run concurrently
- **Pipeline (linear)**: When steps are always in fixed sequence
