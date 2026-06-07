# Dependency Resolution Prompt

## Purpose

Resolve the complete dependency graph for a manifest before generation begins.

---

## Resolution Algorithm

```
1. Read manifest → extract all capability requirements
2. Load capability-matrix.yaml → resolve required packs
3. For each pack, load dependencies.yaml
4. Build dependency graph (DAG)
5. Topological sort → establish assembly order
6. Flag missing or planned packs
7. Output resolved dependency manifest
```

---

## Input

`project-manifest.yaml` values that drive resolution:

```yaml
technology.backend.language    → java-pack | python-pack
technology.backend.framework   → (informs standards selection within pack)
cloud.provider                 → aws-pack | azure-pack | gcp-pack
ai.enabled                     → ai-engineering-pack
ai.framework                   → (informs agent selection within pack)
governance.profile             → governance-pack
project.domain                 → domain-pack (if not generic)
modernization.enabled          → modernization-pack
```

---

## Output: Resolved Dependency Manifest

```yaml
resolution:
  timestamp: "2025-01-01T10:00:00Z"
  manifest: project-manifest.yaml
  
  packs_required:
    - id: architecture-pack
      status: built
      priority: 1
      reason: "foundational — always required"
      
    - id: java-pack
      status: built
      priority: 2
      reason: "technology.backend.language = java"
      depends_on: [architecture-pack]
      
    - id: aws-pack
      status: built
      priority: 3
      reason: "cloud.provider = aws"
      depends_on: [architecture-pack]
      
    - id: ai-engineering-pack
      status: built
      priority: 4
      reason: "ai.enabled = true"
      depends_on: [architecture-pack]
      
    - id: governance-pack
      status: built
      priority: 5
      reason: "governance.profile = standard"
      depends_on: [architecture-pack]
      
  packs_skipped:
    - id: banking-pack
      status: planned
      reason: "project.domain = banking — pack not yet built"
      impact: "Domain-specific overlays not applied"
      
  assembly_order:
    - architecture-pack
    - java-pack
    - aws-pack
    - ai-engineering-pack
    - governance-pack
```
