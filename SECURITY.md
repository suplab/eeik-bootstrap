# Security Model

## Purpose

Define security principles and controls for EEIK.

---

# Security Principles

1. Least Privilege

2. Secure By Default

3. Defense In Depth

4. Human Oversight

5. Traceability

---

# Repository Security

Required:

- Branch Protection
- Pull Request Reviews
- Signed Commits (Recommended)
- Secret Scanning

---

# Agent Security

Agents must:

- Operate within defined scope
- Declare permissions
- Declare constraints
- Escalate when uncertain

---

# Prompt Security

Prompts must not:

- Expose secrets
- Expose credentials
- Bypass governance

---

# Secret Management

Secrets must never be stored in:

```text
Prompts

Agents

Templates

Documentation
```

Use approved secret management systems.

---

# Knowledge Security

Knowledge assets must be classified.

Recommended classifications:

```text
Public

Internal

Confidential

Restricted
```

---

# Governance Controls

Security-sensitive actions require:

- Human review
- Audit logging
- Traceability

---

# Auditability

Every generated artifact should be traceable to:

- Manifest
- Capability Pack
- Generator
- Version
