# ADR-G004: Use LangGraph for Multi-Agent AI Systems

**Status:** Accepted  
**Date:** 2025-01-15  
**Author:** EEIK AI Engineering Guild  
**Applies To:** All multi-agent AI implementations using Claude

---

## Context

When building multi-agent AI systems, teams need a framework for:
- Defining agent state
- Routing between agents
- Human-in-the-loop interruptions
- Persistent memory across sessions
- Tracing and debugging agent flows

Multiple frameworks exist: LangGraph, CrewAI, AutoGen, AWS Bedrock Agents.

## Decision

**LangGraph** (Python) is the primary framework for multi-agent AI systems in EEIK projects.

For Java-native agent implementations: use **Spring AI** with custom orchestration.

## Rationale

| Factor | LangGraph | CrewAI | AutoGen | Bedrock Agents |
|--------|-----------|--------|---------|----------------|
| Graph control flow | ✅ Explicit | ⚠️ Role-based | ⚠️ Conversation-based | ⚠️ Managed |
| Human-in-the-loop | ✅ First-class | ⚠️ Limited | ⚠️ Basic | ⚠️ Limited |
| Persistent memory | ✅ PostgresSaver, Redis | ⚠️ Manual | ⚠️ Manual | ✅ Managed |
| Debuggability | ✅ LangSmith traces | ⚠️ Verbose | ⚠️ Verbose | ⚠️ CloudWatch |
| AWS integration | ✅ Bedrock models | ✅ | ✅ | ✅ Native |
| Claude support | ✅ First-class | ✅ | ✅ | ✅ Native |

LangGraph's explicit graph model makes control flow predictable and auditable — critical for regulated domain deployments.

## Consequences

**Positive:**
- Deterministic routing — supervisor pattern prevents infinite loops
- LangSmith integration for production tracing
- Interruption points for human approval in regulated workflows
- `MemorySaver` / `PostgresSaver` for persistent state

**Negative:**
- Python-only (for LangGraph) — Java teams need Spring AI or Py4J bridge
- LangGraph has a steeper learning curve than CrewAI
- Vendor dependency on LangChain ecosystem

## Implementation Standards

- Use `TypedDict` with `Annotated` for all state schemas
- Always set `recursion_limit` on compiled graphs
- Log every supervisor routing decision
- Use `interrupt()` for human-in-the-loop checkpoints in regulated flows
- Store evaluation baselines in `tests/evaluation/`
