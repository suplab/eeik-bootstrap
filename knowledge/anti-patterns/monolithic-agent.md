# Anti-Pattern: Monolithic Agent

**Severity:** HIGH  
**Domain:** Agentic AI  
**Commonly Found In:** First-generation LangGraph or LLM agent implementations

---

## What It Looks Like

```python
# ❌ One agent doing everything
system_prompt = """
You are a helpful assistant. You can:
- Search the web
- Write code
- Review code
- Query databases
- Generate reports
- Send emails
- Book meetings
- Manage tickets
- Answer questions
- Summarise documents
...
"""
agent = create_react_agent(model, tools=[
    search, write_code, review_code, query_db, 
    generate_report, send_email, book_meeting, ...
])
```

## Why It Fails

- **Context window exhaustion:** Too many tools confuse the model about when to use what
- **Unpredictable behaviour:** Agent picks wrong tool for the task with high frequency
- **No specialisation:** Generalist agent is mediocre at everything
- **Testing impossible:** Cannot unit-test individual capabilities
- **Cost:** Long system prompts on every invocation
- **Debugging:** When something goes wrong, the failure is hard to attribute

## Correct Alternative

Split by domain responsibility. Use a Supervisor to route.

```python
# ✅ Specialist agents with narrow scope
research_agent = create_react_agent(model, tools=[search, fetch_url])
code_agent     = create_react_agent(model, tools=[write_code, run_tests])
data_agent     = create_react_agent(model, tools=[query_db, generate_report])

# Supervisor routes to specialists
supervisor = build_supervisor_graph([research_agent, code_agent, data_agent])
```

**Rule:** Each agent should have at most 5–7 tools. If you need more, split the agent.

## Detection

- Agent definition has more than 7 tools
- System prompt exceeds 1000 tokens
- Agent is described as "can do X, Y, Z, and W" in its description
