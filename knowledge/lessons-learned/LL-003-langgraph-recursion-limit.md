# LL-003: LangGraph Infinite Loop Without recursion_limit

**Category:** AI/Agents  
**Severity:** CRITICAL — runaway agent consumed $40 in API costs in 8 minutes  
**Captured:** 2025-01-20  
**Project Phase:** AI Integration  

---

## What Happened

A supervisor agent graph was compiled without a `recursion_limit`. A poorly constructed prompt caused the supervisor to keep routing to the research agent, which kept returning incomplete results, which caused the supervisor to route again — indefinitely.

## Root Cause

LangGraph does not enforce a default recursion limit. Without it, a graph can execute indefinitely if:
- A conditional edge never evaluates to `END`
- A supervisor cannot decide "FINISH" from the current state
- Two agents route to each other in a cycle

## Fix

Always compile graphs with a `recursion_limit`:

```python
# ✅ Always set recursion_limit
app = graph.compile(
    checkpointer=MemorySaver(),
    recursion_limit=25          # ← non-negotiable
)
```

Also add a failsafe in the supervisor:

```python
def supervisor_node(state: SupervisorState) -> SupervisorState:
    turn_count = len([m for m in state["messages"] if m["role"] == "assistant"])
    if turn_count >= 10:
        return {"next": "FINISH"}   # Force termination
    
    # ... normal routing logic
```

## Prevention

This is now enforced in the **agent-standard.md** — all LangGraph graphs must:
1. Set `recursion_limit` at compile time (maximum: 50 for production)
2. Implement a turn-count failsafe in the supervisor node
3. Add a test that verifies the graph terminates within N steps on all test inputs

## Cost Impact

$40 in 8 minutes. For a production workload, this would be catastrophic.

**Always set spending limits in your AWS Bedrock or Anthropic account before testing agents.**

## Time Lost

30 minutes to detect + 2 hours to understand billing + lesson written
