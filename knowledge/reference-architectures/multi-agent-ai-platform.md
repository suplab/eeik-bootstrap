# Reference Architecture: Multi-Agent AI Platform

**Manifest Match:**
```yaml
technology.backend.language: python
technology.backend.framework: fastapi
ai.enabled: true
ai.pattern: multi-agent
ai.framework: langgraph
cloud.provider: aws
```

**Validated:** PoC and staging environments  
**ADRs:** ADR-G004

---

## Architecture Overview

```
                    ┌─────────────────────────────────────────┐
                    │            AWS Account                    │
                    │                                           │
User/System ──▶ API Gateway ──▶ FastAPI (ECS Fargate)          │
                    │               │                           │
                    │               ▼                           │
                    │     ┌──────────────────┐                 │
                    │     │  Supervisor Graph  │                │
                    │     │   (LangGraph)      │                │
                    │     └────────┬───────────┘               │
                    │              │ routes to                  │
                    │    ┌─────────┼──────────┐               │
                    │    ▼         ▼          ▼               │
                    │ Research   Code       Archive            │
                    │  Agent    Agent       Agent              │
                    │    │         │          │                │
                    │    └────┬────┘          │                │
                    │         ▼               ▼                │
                    │    Bedrock (Claude)   DynamoDB           │
                    │    Knowledge Base     (Checkpoints)      │
                    │    (Amazon Kendra /                      │
                    │     OpenSearch)                          │
                    └─────────────────────────────────────────┘
```

## CDK Stack Breakdown

| Stack | Resources | Purpose |
|-------|-----------|---------|
| NetworkStack | VPC, subnets, NAT | Network isolation |
| AgentStack | ECS Fargate, ALB, task def | FastAPI + LangGraph host |
| KnowledgeStack | S3 (docs), Kendra/OpenSearch | RAG knowledge base |
| MemoryStack | DynamoDB (checkpoints) | LangGraph persistent memory |
| ObservabilityStack | CloudWatch, X-Ray, dashboards | Monitoring |

## Key Design Decisions

### Model Selection
- **Supervisor routing:** `claude-haiku-4-5` — fast, cheap, routing-only
- **Research agent:** `claude-sonnet-4-6` — balanced quality for retrieval + synthesis
- **Code agent:** `claude-sonnet-4-6` — strong code generation
- **Complex reasoning:** `claude-opus-4-6` — reserve for high-stakes decisions only

### Memory Strategy
- **In-session:** `MemorySaver` (no persistence, PoC only)
- **Production:** `PostgresSaver` or DynamoDB checkpointer
- **RAG:** Amazon Kendra (managed) or OpenSearch (self-managed)

### Human-in-the-Loop
For regulated domains: use LangGraph `interrupt()` at high-risk decision points.

```python
from langgraph.types import interrupt

def review_node(state: AgentState) -> AgentState:
    if state["risk_level"] == "HIGH":
        human_decision = interrupt({"message": "High-risk action requires approval", "action": state["proposed_action"]})
        if not human_decision["approved"]:
            return {"next": "ABORT"}
    return {"next": "EXECUTE"}
```

## Observability

Required metrics for this architecture:
- `agent.supervisor.routing_decision` — which worker was selected (tag by worker_name)
- `agent.tool.invocation_count` — tool calls per session
- `agent.graph.recursion_depth` — how many hops before END
- `agent.session.token_usage` — total tokens consumed per session
- `rag.retrieval.recall_at_5` — retrieval quality metric (target: > 0.8)
- `rag.response.faithfulness` — hallucination rate (target: > 0.85)

## Limitations

- LangGraph Python only — Java services need a sidecar or Spring AI alternative
- Bedrock regional availability may limit model choices outside eu-west-1
- DynamoDB checkpointer does not support TTL-based cleanup natively
