# Domain Glossary

> **Bootstrap Template** — Replace with terms specific to your project's business domain.
> Precise definitions prevent Claude Code agents from applying generic meanings to domain-specific vocabulary.

---

## How to Use This Glossary

When Claude Code encounters a business term, it checks this glossary first.

To add terms: `/memory-update "new term: definition"`

---

## EEIK Platform Terms

These terms are defined within the EEIK bootstrap context.

| Term | Definition | NOT to be confused with |
|------|-----------|------------------------|
| **Manifest** | The `project-manifest.yaml` file — the source of truth for all EEIK tooling | Not a deployment manifest or Kubernetes manifest |
| **Capability Pack** | A self-contained bundle of agents, standards, templates, and commands for a technology domain | Not an NPM package or Maven artifact |
| **Blueprint** | An agent-generator template that produces project-specific agents | Not a Terraform blueprint |
| **Governance Profile** | One of: basic / standard / regulated / enterprise — determines mandatory review gates | Not an IAM role or security profile |
| **Knowledge Repository** | The `knowledge/` directory — accumulated organizational learnings | Not a Git repository |
| **Outbox Event** | A domain event stored in the `outbox_events` table for reliable async delivery | Not an email outbox |
| **ADR** | Architecture Decision Record — a document capturing a significant technical decision | Not an Architectural Design Review (that's an ARB review) |
| **ARB** | Architecture Review Board — the formal governance body for enterprise decisions | Not the ARB microcontroller |
| **Bounded Context** | A DDD concept — a logical boundary within which a specific domain model applies | Not a microservice (one service can contain multiple contexts; one context can span services) |
| **Strangler Fig** | A modernization pattern — incrementally replace legacy functionality while keeping the legacy system running | Named after a plant that grows around a host tree |

---

## Project-Specific Terms

> Add your domain terms here after running `/bootstrap`

```
Example format:

| **Claim** | A request by a policyholder to receive compensation for a covered loss | NOT a generic assertion or test claim |
| **Policy** | An insurance contract between the insurer and policyholder | NOT a Spring Security policy or IAM policy |
| **Underwriting** | The process of evaluating risk and determining premium pricing | NOT software versioning or code signing |
```

<!-- TODO: Add project-specific domain terms -->
